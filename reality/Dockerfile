# builder
FROM golang:alpine AS builder
ARG XRAY_VERSION

#ENV GOPROXY=https://goproxy.cn,direct
WORKDIR /app

RUN apk add --no-cache --no-scripts git && \
    test -n "$XRAY_VERSION" && \
    git clone https://github.com/XTLS/Xray-core.git . && \
    git checkout "$XRAY_VERSION" && \
    go mod download -x && \
    go build -v -o xray /app/main/

# runner
FROM alpine:latest AS runner
LABEL maintainer="wulabing <wulabing@gmail.com>"
LABEL version="0.1.4"


ENV UUID=""
ENV DEST=""
ENV SERVERNAMES=""
ENV PRIVATEKEY=""
ENV SHORTIDS=""
ENV MIN_CLIENT_VER=""
ENV NETWORK=""
ENV XHTTP_PATH=""
ENV PROXY=""
ENV DOMAIN=""
ENV ACME_EMAIL=""
ENV HOSTMODE_PORT=""
ENV TZ=Asia/Shanghai

WORKDIR /

COPY ./entrypoint.sh /
COPY ./config.json /
COPY ./Caddyfile /etc/caddy/Caddyfile
COPY ./www/ /srv/www/

COPY --from=builder /app/xray /

RUN apk add --no-cache --no-scripts tzdata ca-certificates jq curl libqrencode-tools caddy && \
    mkdir -p /var/log/xray && \
    wget -O /geosite.dat https://github.com/v2fly/domain-list-community/releases/latest/download/dlc.dat && \
    wget -O /geoip.dat https://github.com/v2fly/geoip/releases/latest/download/geoip.dat && \
    chmod +x /entrypoint.sh


ENTRYPOINT ["./entrypoint.sh"]
EXPOSE 80 443

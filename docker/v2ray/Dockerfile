# Dockerfile for v2ray based alpine
# Copyright (C) 2019 - 2020 Teddysun <i@teddysun.com>
# Reference URL:
# https://github.com/v2ray/v2ray-core
# https://github.com/v2ray/geoip
# https://github.com/v2ray/domain-list-community

FROM golang:alpine AS builder
RUN set -ex \
	&& apk add --no-cache git unzip \
	&& mkdir -p /go/src/github.com/v2ray \
	&& cd /go/src/github.com/v2ray \
	&& git clone https://github.com/v2ray/v2ray-core.git \
	&& cd v2ray-core \
	&& go get -d \
	&& cd main \
	&& go build -o /go/bin/v2ray \
	&& cd ../infra/control/main \
	&& go build -o /go/bin/v2ctl \
	&& wget -O /go/bin/geosite.dat https://github.com/v2ray/domain-list-community/releases/latest/download/dlc.dat \
	&& wget -O /go/bin/geoip.dat https://github.com/v2ray/geoip/releases/latest/download/geoip.dat

FROM alpine:latest
LABEL maintainer="Teddysun <i@teddysun.com>"

RUN set -ex \
	&& apk add --no-cache tzdata ca-certificates \
	&& rm -rf /var/cache/apk/* \
	&& mkdir -p /etc/v2ray \
	&& mkdir -p /var/log/v2ray

COPY --from=builder /go/bin/v2ray /usr/bin
COPY --from=builder /go/bin/v2ctl /usr/bin
COPY --from=builder /go/bin/geoip.dat /usr/bin
COPY --from=builder /go/bin/geosite.dat /usr/bin
COPY config.json /etc/v2ray/config.json
VOLUME /etc/v2ray

ENV TZ=Asia/Shanghai

CMD [ "v2ray", "-config", "/etc/v2ray/config.json" ]

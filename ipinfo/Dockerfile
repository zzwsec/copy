FROM alpine:latest AS builder
ARG NGX_LVER
ARG GEOIP2_TAG
COPY ./*.mmdb /GeoLite2/
RUN ln -s /var/cache/apk /etc/apk/cache
RUN --mount=type=cache,target=/var/cache/apk --mount=type=cache,target=/etc/apk/cache \
    set -ex \
    && apk add --virtual .build-deps build-base git libmaxminddb-dev pcre-dev zlib-dev \
    && cd /tmp \
    && wget --tries=5 -qO nginx-${NGX_LVER}.tar.gz "https://nginx.org/download/nginx-${NGX_LVER}.tar.gz" \
    && tar -zxf nginx-${NGX_LVER}.tar.gz \
    && git clone --depth=1 --branch ${GEOIP2_TAG} "https://github.com/leev/ngx_http_geoip2_module.git" \
    && cd nginx-${NGX_LVER} \
    && ./configure --with-compat \
        --add-dynamic-module=../ngx_http_geoip2_module \
    && make modules \
    && apk del .build-deps

FROM nginx:stable-alpine AS dist
ARG NGX_LVER
COPY --from=builder /tmp/nginx-${NGX_LVER}/objs/*.so /usr/lib/nginx/modules/
COPY --from=builder /GeoLite2/*.mmdb /usr/share/GeoIP/
COPY nginx/nginx.conf /etc/nginx/nginx.conf
COPY nginx/conf.d/* /etc/nginx/conf.d/
RUN ln -s /var/cache/apk /etc/apk/cache
RUN --mount=type=cache,target=/var/cache/apk --mount=type=cache,target=/etc/apk/cache \
    set -ex \
    && apk upgrade \
    && apk add libmaxminddb \
    && rm -f /etc/nginx/conf.d/default.conf \
    && wget --no-check-certificate -qO /usr/share/nginx/html/favicon.ico "https://cdn.skyimg.net/up/2025/5/29/e8c32aa5.webp"
HEALTHCHECK --timeout=10s --start-period=5s CMD curl -fs http://127.0.0.1:80/health || exit 1

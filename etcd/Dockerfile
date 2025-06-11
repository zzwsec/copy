FROM alpine
ARG TARGETOS TARGETARCH ETCD_LATEST_VERSION
RUN --mount=type=cache,target=/var/cache/apk \
    --mount=type=cache,target=/etc/apk/cache \
    set -ex \
    && apk update \
    && apk add ca-certificates tzdata \
    && mkdir /var/etcd /var/lib/etcd/ \
    && wget "https://github.com/etcd-io/etcd/releases/download/v${ETCD_LATEST_VERSION}/etcd-v${ETCD_LATEST_VERSION}-${TARGETOS}-${TARGETARCH}.tar.gz" -P /tmp \
    && tar xf /tmp/etcd-v${ETCD_LATEST_VERSION}-${TARGETOS}-${TARGETARCH}.tar.gz -C /usr/local/bin/ \
        --strip-components=1 \
        etcd-v${ETCD_LATEST_VERSION}-${TARGETOS}-${TARGETARCH}/etcd \
        etcd-v${ETCD_LATEST_VERSION}-${TARGETOS}-${TARGETARCH}/etcdctl \
        etcd-v${ETCD_LATEST_VERSION}-${TARGETOS}-${TARGETARCH}/etcdutl \
    && rm -rf /tmp/etcd-v${ETCD_LATEST_VERSION}-${TARGETOS}-${TARGETARCH}.tar.gz
ENV TZ=Asia/Shanghai
EXPOSE 2379 2380
CMD ["/usr/local/bin/etcd"]
FROM --platform=${BUILDPLATFORM} golang:1.27.2 AS builder

ARG TARGETOS
ARG TARGETARCH
ARG TARGETVARIANT
ARG GOARM=${TARGETVARIANT#v}
ARG TESLA_BLE_UPSTREAM_COMMIT=d7dfe8143477e5f79f5ec32e5de83654db959c1c

# Build upstream PR #180 from a pinned commit so this app can ship the
# sleeping-car cache fix before it is released upstream.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git ca-certificates \
    && rm -rf /var/lib/apt/lists/*
WORKDIR /go/src/wimaha/teslaBleHttpProxy
RUN git init . \
    && git remote add origin https://github.com/JKSProds/TeslaBleHttpProxy.git \
    && git fetch --depth=1 origin ${TESLA_BLE_UPSTREAM_COMMIT} \
    && git checkout --detach FETCH_HEAD
COPY patches/serve-stale-for-evcc.patch /tmp/serve-stale-for-evcc.patch
RUN git apply --unidiff-zero /tmp/serve-stale-for-evcc.patch
RUN CGO_ENABLED=0 GOOS=${TARGETOS} GOARCH=${TARGETARCH} GOARM=${GOARM} make build-docker
RUN mkdir -p /go/bin/key

# Keep the runtime small. /key points at Home Assistant's persistent app config
# so the pairing key stays available across app updates and backups.
FROM busybox:1.37.0-musl
COPY --from=builder /usr/share/zoneinfo /usr/share/zoneinfo
ENV TZ=Europe/Berlin
COPY --from=builder /go/bin/teslaBleHttpProxy /teslaBleHttpProxy
RUN mkdir -p /config && ln -s /config /key
ENV TESLA_BLE_PROXY_SERVE_STALE_WHEN_ASLEEP=true
EXPOSE 8080
ENTRYPOINT ["/teslaBleHttpProxy"]

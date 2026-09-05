FROM wimaha/tesla-ble-http-proxy:latest AS upstream

# The upstream image is intentionally built from scratch (no shell). Copy its
# statically linked Go binary into a tiny base image so /key can point to the
# Home Assistant app's persistent, backup-aware /config directory.
FROM busybox:1.37.0-musl
COPY --from=upstream /teslaBleHttpProxy /teslaBleHttpProxy
RUN mkdir -p /config && ln -s /config /key
EXPOSE 8080
ENTRYPOINT ["/teslaBleHttpProxy"]

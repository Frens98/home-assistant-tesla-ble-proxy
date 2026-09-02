FROM wimaha/tesla-ble-http-proxy:latest

# The upstream proxy stores its BLE private keys in /key. Home Assistant mounts
# this add-on's persistent, backup-aware data directory at /config.
RUN rm -rf /key && ln -s /config /key

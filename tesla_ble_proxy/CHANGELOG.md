# Changelog

## 0.1.4

- Build upstream PR #180 and serve bounded cached vehicle data when the proxy confirms the Tesla is asleep, preventing EVCC sleep-related 503 responses.
- Keep stale fallback limited to cached data no older than 24 hours; ordinary BLE failures still return errors.

## 0.1.3

- Publish a Home Assistant app update that rebuilds the wrapper from the latest upstream Tesla BLE Proxy image.
- Keep Tesla BLE keys in the existing persistent app configuration directory.

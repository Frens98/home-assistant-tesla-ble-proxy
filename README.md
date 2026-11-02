# Tesla BLE Proxy – Home Assistant app

This repository packages [TeslaBleHttpProxy](https://github.com/wimaha/TeslaBleHttpProxy)
as a Home Assistant app. Its purpose is to let a Home Assistant OS device — for
example a Raspberry Pi — control a Tesla's charging over local Bluetooth Low
Energy (BLE), rather than through Tesla's paid cloud Fleet API.

It is especially useful together with [evcc](https://evcc.io/): evcc reads the
house's solar surplus and tells the car, via this local proxy, to start, stop,
or adjust charging. This also works with a Tesla Wall Connector Gen 3, which
can measure charging but cannot itself be directly switched by evcc.

## What this app does

- Runs the upstream Tesla BLE HTTP Proxy on the Home Assistant host.
- Exposes it to evcc at `http://HOME_ASSISTANT_IP:8080`.
- Uses a **Charging Manager** BLE key, authorized once by tapping the Tesla
  key card on the centre console.
- Stores generated key material locally in the app's persistent configuration
  directory, so it is retained in Home Assistant backups.

No Tesla account password, API token, VIN, vehicle data, or generated BLE key
is committed to this repository.

## Install in Home Assistant

1. In **Settings → Apps → App store**, add this repository URL:
   `https://github.com/Frens98/home-assistant-tesla-ble-proxy`.
2. Install **Tesla BLE Proxy** and start it.
3. Open `http://HOME_ASSISTANT_IP:8080/dashboard`.
4. Generate a **Charging Manager** key, enter the VIN, wake the car, and tap
   the physical Tesla key card on the centre console when requested.
5. Configure the `tesla-ble` vehicle in evcc with the Pi's IP address and port
   `8080`.

## Updating

Because this is a Home Assistant app repository, a newer release is shown in
the Home Assistant app store after a version change is pushed here. Update it
there in the usual way; pairing keys remain on the Pi.

## Tesla asleep and EVCC 503 responses

This app builds upstream TeslaBleHttpProxy from the pinned commit used by
[upstream PR #180](https://github.com/wimaha/TeslaBleHttpProxy/pull/180), plus
a small wrapper patch that enables its stale-cache behavior for EVCC. EVCC
doesn't currently send the PR's `allow_stale=true` query parameter itself.

When the proxy positively detects that the Tesla is asleep, it can return
cached `vehicle_data` up to 24 hours old instead of HTTP 503. The response is
marked `stale` and includes its cache age, but EVCC may ignore those fields and
show the last SOC/status as current. The cache is in memory and is lost when
the app restarts. Missing cache, expired cache, or ordinary BLE failures (for
example, the car being out of range) still return an error; they are not
treated as a sleeping car.

## Scope

This is a small packaging wrapper around the upstream project. Tesla BLE
protocol support and core proxy behavior are maintained at
[wimaha/TeslaBleHttpProxy](https://github.com/wimaha/TeslaBleHttpProxy); this
app currently pins PR #180 and applies a small wrapper patch so EVCC can use
its bounded stale-cache behavior without sending a custom query parameter.

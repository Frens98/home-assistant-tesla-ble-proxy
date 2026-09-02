# Tesla BLE Proxy

This app runs [TeslaBleHttpProxy](https://github.com/wimaha/TeslaBleHttpProxy)
locally on the Home Assistant host. It offers the vehicle's Bluetooth charging
controls to evcc at `http://HOME_ASSISTANT_IP:8080`.

## First use

1. Start the app and open `http://HOME_ASSISTANT_IP:8080/dashboard`.
2. Generate a **Charging Manager** key.
3. Wake the Tesla, enter its VIN, then tap the NFC key card on the centre
   console to authorize the key.
4. In evcc, use the `tesla-ble` vehicle template with the Pi's IP address and
   port `8080`.

The generated private key remains in the app's persistent configuration data
and is included in Home Assistant backups.

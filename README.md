# Mbed CE Quectel BG770A-GL EVKIT Example App

Reference application that drives the Quectel BG770A-GL Cat-M1 / NB-IoT
modem through the
[`cellular_test`](https://github.com/61ca52/mbed-ce-cellular-test) library to
exercise UDP, TCP, TLS, DTLS and HTTP(S) end-to-end against a configurable
echo server.

- **MCU target:** `NUCLEO_L4A6ZG` (default), also supported: `NUCLEO_L496ZG`
- **Modem driver:** [`bg770agl/`](https://github.com/61ca52/mbed-ce-bg770agl) (Mbed CE cellular framework)
- **Mode:** AT sockets, module-IP (PPP disabled)

## Build

```powershell
cmake -S . -B build/NUCLEO_L4A6ZG-Debug -G Ninja `
      -DMBED_BOARD=NUCLEO_L4A6ZG -DCMAKE_BUILD_TYPE=Debug
cmake --build build/NUCLEO_L4A6ZG-Debug --target MbedCE_BG770AGL_EVAL_KIT_APP
```

In VS Code: run task **Build MbedCE_BG770AGL_EVAL_KIT_APP**, debug with
**Debug MbedCE_BG770AGL_EVAL_KIT_APP NUCLEO_L4A6ZG Debug** (uses
`ST-LINK_gdbserver` on `127.0.0.1:23331`, hex auto-loaded).

## Operator configuration

Patch `mbed_app.json5` under `target_overrides."*"` for the SIM in use.

### 1NCE (default)

```json5
"nsapi.default-cellular-plmn": "\"26201\"",
"nsapi.default-cellular-apn": "\"iot.1nce.net\"",
"quectel-bg770agl.provide-default": true,
"quectel-bg770agl.bands": "\"8\"",
```

### Vodafone

```json5
"nsapi.default-cellular-plmn": "\"26202\"",
"nsapi.default-cellular-apn": "\"lpwa.vodafone.iot\"",
"quectel-bg770agl.provide-default": true,
```

### Proximus (BE)

```json5
"nsapi.default-cellular-apn": "\"m2minternet.proximus.be\"",
"quectel-bg770agl.provide-default": true,
```

Restrict the modem to NB-IoT only (skip Cat-M1 scan):

```json5
"cellular.radio-access-technology": 9,
```

## Selecting a test procedure

`cellular-test.test-procedure` selects exactly one path; only that one
compiles in to keep flash low.

| value | path         |
|------:|--------------|
| 0     | UDP echo     |
| 1     | TCP echo     |
| 2     | TLS echo     |
| 3     | fw-TLS echo  |
| 4     | DTLS echo    |
| 5     | fw-DTLS echo |
| 6     | HTTP GET     |
| 7     | HTTPS GET    |

Echo-server endpoints, ports, certificate-verify mode, ping-pong cycles
and timeouts live under the `cellular-test.*` block in `mbed_app.json5`.
See [`cellular_test`](https://github.com/61ca52/mbed-ce-cellular-test) for
the full parameter list.

## Layout

```text
.
├── main.cpp                 // app entry; calls cellular_test
├── mbed_app.json5           // per-target / per-operator config
├── cellular_test/           // protocol echo-test library
├── bg770agl/                // Quectel BG770A-GL Cat-M1 / NB-IoT modem driver
└── mbed-os/                 // Mbed OS sources (read-only)
```

# ESP32 Technical Build Documentation
## Caneatech ISLS — LilyGO T-A7670X-S3-Standard

---

## 1. Bill of Materials

### Per Node (Regular Node)

| Qty | Component | Model | Notes |
|-----|-----------|-------|-------|
| 1 | Main board | LilyGO T-A7670X-S3-Standard | ESP32-S3 + A7670G onboard |
| 1 | LoRa module | DX-LR02-433T22D | ASR6601, 433 MHz, +22 dBm |
| 1 | Light sensor | BH1750FVI | I2C, GY-302 breakout |
| 1 | Temp/humidity | DHT22 (AM2302) | Single-wire, 3.3V-5V |
| 1 | Current sensor | INA219 | I2C, GY-219 breakout |
| 1 | RTC module | DS3231 | I2C, with CR2032 battery |
| 1 | Relay module | 5V single-channel relay | Opto-isolated, active-HIGH |
| 1 | SD card module | Micro SD SPI breakout | If not using onboard TF slot |
| 1 | Solar charge controller | MPPT (e.g. CN3791) | Sized to panel/battery |
| 1 | Battery | 12V 7Ah SLA or LiFePO4 | Outdoor-rated |
| 1 | Solar panel | 20W–40W mono, 18V | Sized for local irradiance |
| 1 | 5V buck regulator | LM2596 or MP2307 | Powers the 3.3V rail via board |
| 1 | Pull-up resistor | 10 kΩ | For DHT22 data line |
| 1 | Enclosure | IP65 junction box | Pole-mount rated |
| — | JST-PH cables, terminal blocks, wiring | — | Per install |

### Per Gateway Node (add to Regular Node BOM)

| Qty | Component | Notes |
|-----|-----------|-------|
| 1 | SIM card | 4G LTE data SIM (IoT plan) |
| 1 | 4G LTE antenna | SMA, matches A7670G connector on LilyGO |
| 1 | GNSS antenna | uFL, attaches to LilyGO GNSS connector |

> The A7670G modem and TF card slot are **already onboard** the LilyGO board. No extra modem hardware is needed for the gateway.

---

## 2. Pin Assignments

### LilyGO T-A7670X-S3-Standard — Known Onboard Pins

> Verify these against the official schematic at:  
> https://github.com/Xinyuan-LilyGO/LilyGO-T-A76XX

| Signal | GPIO | Description |
|--------|------|-------------|
| MODEM_TX | 17 | ESP32 → A7670G |
| MODEM_RX | 18 | A7670G → ESP32 |
| MODEM_PWRKEY | 4 | Modem power key |
| MODEM_RST | 5 | Modem reset |
| MODEM_FLIGHT | 6 | Modem flight mode |
| MODEM_STATUS | 3 | Modem status indicator |
| MODEM_DTR | 2 | Modem DTR |
| MODEM_RI | 1 | Modem ring indicator |
| BOARD_LED | 12 | Onboard LED |
| BAT_ADC | 35 | Battery voltage ADC |
| SD_MISO | 2 | Onboard TF card (SPI) |
| SD_MOSI | 11 | Onboard TF card (SPI) |
| SD_SCLK | 14 | Onboard TF card (SPI) |
| SD_CS | 13 | Onboard TF card (SPI) |

### External Peripheral Pin Assignments

| Component | Signal | GPIO | Notes |
|-----------|--------|------|-------|
| I2C Bus (shared) | SDA | 8 | BH1750, INA219, DS3231 all share |
| I2C Bus (shared) | SCL | 9 | |
| DHT22 | DATA | 38 | Pull up to 3.3V via 10 kΩ |
| Relay | IN | 40 | HIGH = relay on = light on |
| DX-LR02 LoRa | TX (ESP→LoRa) | 16 | UART2 |
| DX-LR02 LoRa | RX (LoRa→ESP) | 15 | UART2 |
| DX-LR02 LoRa | VCC | 3.3V or 5V | Check module supply spec |
| DX-LR02 LoRa | GND | GND | |

> GPIO 8 and 9 are the default I2C pins on ESP32-S3 but can be reassigned in `Wire.begin(SDA, SCL)`.

---

## 3. Wiring Diagrams

### 3.1 I2C Bus (BH1750 + INA219 + DS3231)

```
ESP32-S3
GPIO 8 (SDA) ──────┬──────────────┬──────────────┐
                   │              │              │
               [BH1750]       [INA219]       [DS3231]
                SDA pin         SDA pin        SDA pin

GPIO 9 (SCL) ──────┬──────────────┬──────────────┐
                   │              │              │
               [BH1750]       [INA219]       [DS3231]
                SCL pin         SCL pin        SCL pin

3.3V ──────────────┴──────────────┴──────────────┘
GND  ──────────────┴──────────────┴──────────────┘
```

I2C Addresses:
- BH1750: `0x23` (ADDR pin LOW) or `0x5C` (ADDR pin HIGH)
- INA219: `0x40` (default, A0=GND A1=GND)
- DS3231: `0x68` (fixed)

### 3.2 INA219 — Streetlight Current Measurement

The INA219 shunt resistor goes **in series** with the streetlight load on the relay output:

```
Battery (+) ──→ Relay (COM) ──→ Relay (NO) ──→ INA219 VIN+ ──→ INA219 VIN- ──→ Lamp (+)
                                                   │
                                              [0.1 Ω shunt]  ← internal to GY-219 module
                                                   │
                                               Lamp (-)  ──→ Battery (-)
```

### 3.3 DHT22

```
ESP32-S3          DHT22
GPIO 38 ──────── DATA  ──[10kΩ]── 3.3V
3.3V  ──────────  VCC
GND   ──────────  GND
```

### 3.4 Relay Module (Opto-isolated, Active-HIGH)

```
ESP32-S3          Relay Module
GPIO 40 ────────  IN
3.3V    ────────  VCC  (some modules need 5V — check your module)
GND     ────────  GND

Relay COM  ────  Load power supply (+)
Relay NO   ────  Streetlight lamp (+)
Streetlight (-)  ──── Load power supply (-)
```

> Use an opto-isolated relay to protect the ESP32 from mains voltage transients.

### 3.5 DX-LR02 LoRa Module (UART AT Commands)

```
ESP32-S3          DX-LR02
GPIO 16 (TX) ─── RXD
GPIO 15 (RX) ─── TXD
3.3V or 5V   ─── VCC  (check your module — DX-LR02 accepts 3.3V–5.5V)
GND          ─── GND
```

Default UART baud rate: **9600**.

### 3.6 Power System

```
Solar Panel (18V)
      │
[MPPT Charge Controller]
      │
[12V SLA / LiFePO4 Battery]
      │
[5V Buck Regulator]
      │
[LilyGO VIN / USB-C 5V input]
      │
[LilyGO 3.3V rail] ──→ All 3.3V sensors
```

> The LilyGO board regulates 5V input down to 3.3V internally. Do not exceed 5V on the VIN pin.

---

## 4. Development Environment Setup

### 4.1 Recommended Toolchain

**PlatformIO** is strongly recommended over Arduino IDE for this project due to:
- Dependency management (`platformio.ini`)
- Multiple build environments (node vs gateway) in a single project
- Serial monitor with timestamps
- OTA support built-in

---

### 4.2 Setup — Linux (Docker)

This approach requires no local toolchain install. All compilation happens inside a container.

#### Prerequisites
- Docker Engine installed: `sudo apt install docker.io`
- USB access to the board

#### Step 1 — Add your user to the `dialout` group (for USB serial)

```bash
sudo usermod -aG dialout $USER
newgrp dialout
```

#### Step 2 — Find the board's port

Plug in the LilyGO via the **ESP-USB** port (left USB-C). Run:

```bash
ls /dev/ttyUSB* /dev/ttyACM*
```

It will typically appear as `/dev/ttyACM0` or `/dev/ttyUSB0`.

#### Step 3 — Pull the PlatformIO Docker image

```bash
docker pull infiniteclouds/platformio:latest
# or use the official CLI image:
docker pull platformio/platformio-core:latest
```

#### Step 4 — Build the firmware

From the root of the firmware project directory:

```bash
docker run --rm \
  -v "$(pwd)":/workspace \
  -w /workspace \
  platformio/platformio-core:latest \
  platformio run --environment gateway
```

Replace `gateway` with `node` to build for a regular node.

#### Step 5 — Flash the firmware

```bash
docker run --rm \
  --device=/dev/ttyACM0 \
  -v "$(pwd)":/workspace \
  -w /workspace \
  platformio/platformio-core:latest \
  platformio run --environment gateway --target upload --upload-port /dev/ttyACM0
```

#### Step 6 — Monitor serial output

```bash
docker run --rm \
  --device=/dev/ttyACM0 \
  -v "$(pwd)":/workspace \
  -w /workspace \
  platformio/platformio-core:latest \
  platformio device monitor --port /dev/ttyACM0 --baud 115200
```

#### Optional — Docker Compose for repeated use

Create `docker-compose.yml` in the firmware project root:

```yaml
version: "3.9"
services:
  pio:
    image: platformio/platformio-core:latest
    volumes:
      - .:/workspace
    working_dir: /workspace
    devices:
      - /dev/ttyACM0:/dev/ttyACM0
    stdin_open: true
    tty: true
```

Then use:
```bash
docker compose run pio platformio run --environment node
docker compose run pio platformio run --environment gateway --target upload
```

---

### 4.3 Setup — Windows

#### Step 1 — Install VS Code

Download from https://code.visualstudio.com/

#### Step 2 — Install PlatformIO IDE Extension

1. Open VS Code
2. Go to Extensions (`Ctrl+Shift+X`)
3. Search for **PlatformIO IDE**
4. Click Install
5. Restart VS Code when prompted

#### Step 3 — Install the CP2102 / CH340 driver

The LilyGO uses either a CP2102 or CH340 USB-to-serial chip. Download and install the appropriate driver:
- CP2102: https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers
- CH340: https://www.wch-ic.com/downloads/CH341SER_EXE.html

After driver install, plug in the LilyGO via **ESP-USB** port. It should appear as `COMx` in Device Manager.

#### Step 4 — Open the firmware project

1. In VS Code, go to `File → Open Folder`
2. Select the firmware project folder
3. PlatformIO will auto-detect `platformio.ini` and install the ESP32-S3 toolchain

#### Step 5 — Select the build environment

In the PlatformIO toolbar at the bottom of VS Code:
- Click the environment selector (shows `Default`)
- Choose `gateway` or `node`

#### Step 6 — Build and upload

- Build: Click the **checkmark** icon in the PlatformIO toolbar (`Ctrl+Alt+B`)
- Upload: Click the **arrow** icon (`Ctrl+Alt+U`)
- Monitor: Click the **plug** icon (`Ctrl+Alt+S`)

---

## 5. PlatformIO Project Configuration

### `platformio.ini`

```ini
[env]
platform = espressif32
board = esp32-s3-devkitc-1
framework = arduino
monitor_speed = 115200
lib_deps =
    clydedacruz/ESPSoftwareSerial@^8.0.1
    adafruit/DHT sensor library@^1.4.6
    adafruit/Adafruit BusIO@^1.16.1
    claws/BH1750@^1.3.0
    adafruit/Adafruit INA219@^1.2.3
    adafruit/RTClib@^2.1.4
    bblanchon/ArduinoJson@^7.1.0
    knolleary/PubSubClient@^2.8

[env:gateway]
build_flags =
    -DIS_GATEWAY=true
    -DUSE_SD_CARD=true

[env:node]
build_flags =
    -DIS_GATEWAY=false
    -DUSE_SD_CARD=true

[env:node_no_sd]
build_flags =
    -DIS_GATEWAY=false
    -DUSE_SD_CARD=false
```

---

## 6. Project File Structure

```
firmware/
├── platformio.ini
├── src/
│   ├── main.cpp                  # Entry point — calls setup() and loop()
│   ├── config.h                  # All pin definitions and compile-time constants
│   ├── secrets.h                 # MQTT credentials, APN — DO NOT COMMIT
│   ├── sensors/
│   │   ├── BH1750Sensor.h/.cpp
│   │   ├── DHT22Sensor.h/.cpp
│   │   ├── INA219Sensor.h/.cpp
│   │   └── RTCModule.h/.cpp
│   ├── actuators/
│   │   └── RelayController.h/.cpp
│   ├── comms/
│   │   ├── LoRaHandler.h/.cpp     # DX-LR02 AT command wrapper
│   │   ├── ModemHandler.h/.cpp    # A7670G AT command wrapper (gateway only)
│   │   └── MqttClient.h/.cpp      # MQTT publish/subscribe (gateway only)
│   ├── storage/
│   │   └── SDCardLogger.h/.cpp    # Log writes and config reads
│   ├── commands/
│   │   └── CommandHandler.h/.cpp  # Parse and execute received commands
│   └── utils/
│       ├── PayloadBuilder.h/.cpp  # Build JSON telemetry and alert payloads
│       └── Watchdog.h/.cpp        # Hardware watchdog wrapper
├── data/                          # Files to upload to SD card
│   └── config.json                # Node config (see section 8)
└── test/
    └── ...
```

---

## 7. Configuration Variables (`config.h`)

```cpp
// ─── Node Identity ───────────────────────────────────────────────
#define HARDWARE_ID         "node-001"          // Must match NodeEntity.hardwareId
#define CLUSTER_ID          "cluster-uuid-here"
#define ZONE                "ring-road"
#define CITY                "accra"
#define COUNTRY             "ghana"

// ─── Mode Flags (overridden by platformio.ini build_flags) ───────
#ifndef IS_GATEWAY
  #define IS_GATEWAY        false
#endif
#ifndef USE_SD_CARD
  #define USE_SD_CARD       true
#endif

// ─── Pin Definitions ─────────────────────────────────────────────
#define I2C_SDA             8
#define I2C_SCL             9
#define DHT22_PIN           38
#define RELAY_PIN           40

// Onboard modem (A7670G) — gateway only
#define MODEM_TX            17
#define MODEM_RX            18
#define MODEM_PWRKEY        4
#define MODEM_RST           5
#define MODEM_FLIGHT        6
#define MODEM_STATUS        3

// Onboard TF card
#define SD_MISO             2
#define SD_MOSI             11
#define SD_SCLK             14
#define SD_CS               13

// External LoRa (DX-LR02)
#define LORA_TX             16      // ESP → LoRa RXD
#define LORA_RX             15      // LoRa TXD → ESP
#define LORA_BAUD           9600

// ─── Intervals ───────────────────────────────────────────────────
#define TELEMETRY_INTERVAL_SEC   60
#define HEARTBEAT_INTERVAL_SEC   300     // 5 minutes
#define LORA_ACK_TIMEOUT_MS      5000

// ─── LoRa Config ─────────────────────────────────────────────────
#define LORA_CHANNEL        6            // 433 MHz channel
#define LORA_TX_POWER       22           // dBm (max for DX-LR02)
#define LORA_ENCRYPT_KEY    "0123456789ABCDEF"   // AES-128, change per deployment

// ─── Misc ────────────────────────────────────────────────────────
#define WATCHDOG_TIMEOUT_SEC   120
#define LOG_RETENTION_DAYS     30
#define BOARD_LED              12
```

---

## 8. SD Card — `config.json`

This file is loaded on startup. If `USE_SD_CARD=false`, these values must be hardcoded in `secrets.h`.

```json
{
  "hardwareId": "node-001",
  "clusterId": "cluster-uuid-here",
  "zone": "ring-road",
  "city": "accra",
  "country": "ghana",
  "isGateway": false,
  "telemetryIntervalSec": 60,
  "heartbeatIntervalSec": 300,
  "schedule": {
    "enabled": true,
    "onTime": "18:30",
    "offTime": "06:00"
  },
  "lora": {
    "channel": 6,
    "txPower": 22,
    "encryptKey": "0123456789ABCDEF",
    "encryptEnabled": true
  },
  "mqtt": {
    "broker": "your-hivemq-cloud-url.s1.eu.hivemq.cloud",
    "port": 8883,
    "username": "your-mqtt-user",
    "password": "your-mqtt-password",
    "clientId": "gw-node-001"
  },
  "apn": {
    "name": "internet",
    "user": "",
    "pass": ""
  }
}
```

> Keep `config.json` out of version control. Add `data/config.json` to `.gitignore`.

---

## 9. Telemetry Payload Format

Published to: `ssli/{country}/{city}/{zone}/{clusterId}/{hardwareId}/telemetry`

```json
{
  "hardwareId": "node-001",
  "clusterId": "cluster-uuid-here",
  "timestamp": 1714920000,
  "battery": 87.5,
  "light": 320.0,
  "motion": false,
  "temperature": 28.4,
  "humidity": 71.2,
  "power": 45.8,
  "current": 0.38,
  "voltage": 12.1,
  "relayState": true,
  "rssi": -78,
  "snr": 9.5
}
```

| Field | Unit | Source |
|-------|------|--------|
| `battery` | % (0–100) | ADC voltage divider → calculated |
| `light` | lux | BH1750 |
| `motion` | boolean | PIR / future sensor (false if not fitted) |
| `temperature` | °C | DHT22 |
| `humidity` | % RH | DHT22 |
| `power` | W | INA219 (V × I) |
| `current` | A | INA219 |
| `voltage` | V | INA219 (load voltage) |
| `relayState` | boolean | Current relay state |
| `rssi` | dBm | LoRa link quality (nodes) / modem signal (gateway) |
| `snr` | dB | LoRa SNR (nodes only) |
| `timestamp` | Unix epoch | DS3231 RTC |

---

## 10. Alert Payload Format

Published to: `ssli/{country}/{city}/{zone}/{clusterId}/{hardwareId}/alert`

```json
{
  "hardwareId": "node-001",
  "clusterId": "cluster-uuid-here",
  "timestamp": 1714920000,
  "alertType": "MOTION_DETECTED",
  "severity": "INFO",
  "data": {}
}
```

| `alertType` | Severity | Trigger |
|-------------|----------|---------|
| `MOTION_DETECTED` | INFO | PIR/motion sensor triggered |
| `LOW_BATTERY` | WARNING | Battery below 20% |
| `CRITICAL_BATTERY` | CRITICAL | Battery below 10% |
| `LAMP_FAULT` | WARNING | INA219 reads 0A when relay is ON |
| `SENSOR_FAULT` | WARNING | Any sensor fails to read |
| `LORA_TIMEOUT` | WARNING | Gateway: no heartbeat from node in 10 min |
| `OVERHEAT` | WARNING | Temperature > 70°C (enclosure temp) |
| `RELAY_FAULT` | CRITICAL | Relay state mismatch detected |

---

## 11. Command Payload Format

Received on: `ssli/{country}/{city}/{zone}/{clusterId}/{hardwareId}/cmd`

```json
{
  "commandId": "cmd-uuid-from-backend",
  "type": "SET_BRIGHTNESS",
  "payload": {
    "level": 75
  },
  "issuedAt": 1714920000
}
```

### ACK Payload

Published to: `ssli/{country}/{city}/{zone}/{clusterId}/{hardwareId}/ack`

```json
{
  "commandId": "cmd-uuid-from-backend",
  "hardwareId": "node-001",
  "status": "ACKNOWLEDGED",
  "timestamp": 1714920010,
  "message": "Brightness set to 75%"
}
```

---

## 12. LoRa AT Command Reference (DX-LR02)

The DX-LR02 is controlled via UART AT commands at 9600 baud. No LoRa stack programming is needed.

### Initialization Sequence

```
AT                          → OK  (check module alive)
AT+RESET                    → reboot module
AT+MODE=0                   → Set to normal TX/RX mode (0=normal, 1=wake-on-radio, 2=power save)
AT+CHANNEL=6                → Set channel (0–127, channel 6 = ~433.875 MHz)
AT+TXPOWER=22               → Set TX power in dBm
AT+DATARATE=5               → Spreading factor (SF7–SF12, higher = longer range, slower)
AT+BANDWIDTH=125            → Bandwidth in kHz (125/250/500)
AT+CODERATE=1               → Coding rate (1=4/5, 2=4/6, 3=4/7, 4=4/8)
AT+PREAMBLE=8               → Preamble length
AT+CRC=1                    → Enable CRC
AT+ENCRYPT=1                → Enable AES-128 encryption
AT+KEY=0123456789ABCDEF0123456789ABCDEF  → 16-byte AES key (same on all nodes)
AT+ADDRESS=1                → Set this module's address (unique per node, gateway=0)
AT+SAVE                     → Save settings to flash
```

### Sending a Packet

```
AT+SEND=0,22,{"t":28.4,"h":71}
         │  │  └── Payload (up to 240 bytes)
         │  └───── Payload length
         └──────── Destination address (0 = gateway)
```

Response: `+SEND:OK` or `+SEND:ERR`

### Receiving a Packet

The module sends received packets to UART automatically:

```
+RCV=1,22,{"t":28.4,"h":71},-78,9
       │  │  └── Payload        │ └── SNR (dB)
       │  │                     └──── RSSI (dBm)
       │  └───── Payload length
       └──────── Sender address
```

Parse this in the `serialEvent()` handler.

### Useful Query Commands

```
AT+VER        → Firmware version
AT+UID        → Module UID
AT+RSSI?      → Last packet RSSI
AT+CHANNEL?   → Current channel
AT+ADDRESS?   → Current address
AT+SLEEP      → Enter sleep mode
AT+WAKEUP     → Wake from sleep
```

---

## 13. A7670G Modem AT Command Reference (Gateway Only)

The A7670G is controlled via UART on GPIO 17/18. Use the `TinyGSM` library or send raw AT commands.

### Power On Sequence

```cpp
// Pull PWRKEY LOW for 1 second to power on the modem
pinMode(MODEM_PWRKEY, OUTPUT);
digitalWrite(MODEM_PWRKEY, HIGH);
delay(100);
digitalWrite(MODEM_PWRKEY, LOW);
delay(1000);
digitalWrite(MODEM_PWRKEY, HIGH);
delay(5000);  // Wait for modem to boot
```

### Key AT Commands

```
AT                          → OK
AT+CPIN?                    → SIM status (READY / NOT INSERTED)
AT+CSQ                      → Signal quality (RSSI, BER)
AT+COPS?                    → Current operator
AT+CREG?                    → Network registration status
AT+CGDCONT=1,"IP","<APN>"   → Set APN
AT+CGACT=1,1                → Activate PDP context (connect to data network)
AT+CIFSR                    → Get IP address

// MQTT (A7670G has built-in MQTT stack)
AT+CMQTTSTART               → Start MQTT service
AT+CMQTTACCQ=0,"clientId"  → Acquire client handle
AT+CMQTTCONNECT=0,"tcp://broker:1883",60,1,"user","pass"  → Connect
AT+CMQTTSUB=0,"topic",1     → Subscribe to topic (QoS 1)
AT+CMQTTPUB=0,"topic",1,60  → Begin publish (then send payload + Ctrl+Z)
AT+CMQTTDISC=0              → Disconnect
AT+CMQTTSTOP               → Stop MQTT service
```

### MQTT TLS Connection

```
AT+CMQTTSSLCFG=0,1          → Enable SSL on client 0
AT+CSSLCFG="sslversion",0,3 → TLS 1.2
AT+CMQTTCONNECT=0,"ssl://your-broker.hivemq.cloud:8883",60,1,"user","pass"
```

### Receiving MQTT Messages

The modem sends incoming messages as unsolicited result codes (URCs):

```
+CMQTTRXSTART: 0,22,44
+CMQTTRXTOPIC: 0,22
ssli/ghana/accra/.../cmd
+CMQTTRXPAYLOAD: 0,44
{"commandId":"...","type":"LIGHT_ON","payload":{}}
+CMQTTRXEND: 0
```

Parse these in your serial event handler.

---

## 14. Firmware Logic — `main.cpp` Skeleton

```cpp
#include "config.h"
#include "sensors/BH1750Sensor.h"
#include "sensors/DHT22Sensor.h"
#include "sensors/INA219Sensor.h"
#include "sensors/RTCModule.h"
#include "actuators/RelayController.h"
#include "comms/LoRaHandler.h"
#include "storage/SDCardLogger.h"
#include "commands/CommandHandler.h"
#include "utils/PayloadBuilder.h"
#include "utils/Watchdog.h"

#if IS_GATEWAY
  #include "comms/ModemHandler.h"
  #include "comms/MqttClient.h"
#endif

unsigned long lastTelemetry = 0;
unsigned long lastHeartbeat = 0;

void setup() {
    Serial.begin(115200);
    Watchdog::enable(WATCHDOG_TIMEOUT_SEC);

    Wire.begin(I2C_SDA, I2C_SCL);
    BH1750Sensor::init();
    DHT22Sensor::init(DHT22_PIN);
    INA219Sensor::init();
    RTCModule::init();
    RelayController::init(RELAY_PIN);

#if USE_SD_CARD
    SDCardLogger::init(SD_CS);
    SDCardLogger::loadConfig();     // Loads config.json into runtime config
#endif

    LoRaHandler::init(LORA_TX, LORA_RX, LORA_BAUD);
    LoRaHandler::configure();

#if IS_GATEWAY
    ModemHandler::init();
    ModemHandler::powerOn();
    ModemHandler::connectNetwork();
    MqttClient::connect();
    MqttClient::subscribe();        // Subscribe to cmd topic
#endif

    Serial.println("[BOOT] Node ready. IS_GATEWAY=" + String(IS_GATEWAY));
}

void loop() {
    Watchdog::reset();

    // Read incoming LoRa messages
    if (LoRaHandler::available()) {
        String raw = LoRaHandler::read();
        #if IS_GATEWAY
            MqttClient::publish("telemetry", raw);   // Forward to cloud
        #else
            CommandHandler::handle(raw);             // Was a command for this node
        #endif
    }

#if IS_GATEWAY
    // Read incoming MQTT messages (modem URC parser)
    if (ModemHandler::hasData()) {
        String topic, payload;
        ModemHandler::read(topic, payload);
        String targetId = PayloadBuilder::extractHardwareId(topic);
        if (targetId == HARDWARE_ID) {
            CommandHandler::handle(payload);         // Command is for this gateway
        } else {
            LoRaHandler::send(targetId, payload);    // Forward via LoRa
        }
    }
#endif

    // Periodic telemetry
    if (millis() - lastTelemetry >= TELEMETRY_INTERVAL_SEC * 1000UL) {
        lastTelemetry = millis();

        SensorData data;
        data.light       = BH1750Sensor::read();
        data.temperature = DHT22Sensor::readTemp();
        data.humidity    = DHT22Sensor::readHumidity();
        data.current     = INA219Sensor::readCurrent();
        data.power       = INA219Sensor::readPower();
        data.voltage     = INA219Sensor::readVoltage();
        data.relayState  = RelayController::getState();
        data.timestamp   = RTCModule::now();
        data.battery     = Watchdog::readBatteryPercent();

        String payload = PayloadBuilder::buildTelemetry(data);

        #if IS_GATEWAY
            MqttClient::publish("telemetry", payload);
        #else
            LoRaHandler::send(0, payload);           // Address 0 = gateway
        #endif

        #if USE_SD_CARD
            SDCardLogger::log(payload);
        #endif
    }

    // Heartbeat / status
    if (millis() - lastHeartbeat >= HEARTBEAT_INTERVAL_SEC * 1000UL) {
        lastHeartbeat = millis();
        String status = PayloadBuilder::buildStatus();
        #if IS_GATEWAY
            MqttClient::publish("status", status);
        #else
            LoRaHandler::send(0, status);
        #endif
    }

    delay(10);
}
```

---

## 15. SD Card Log File Structure

```
/
├── config.json           ← Loaded on boot
├── logs/
│   ├── 2025-01-01.log    ← One file per day (auto-rotated)
│   ├── 2025-01-02.log
│   └── ...
├── commands/
│   └── pending.json      ← Commands queued while offline
└── firmware/
    └── update.bin        ← OTA binary (if delivered via SD card)
```

Log file format (one JSON object per line — newline-delimited JSON):
```
{"ts":1714920000,"t":28.4,"h":71,"lux":320,"pwr":45.8,"relay":true}
{"ts":1714920060,"t":28.5,"h":70,"lux":318,"pwr":45.9,"relay":true}
```

---

## 16. OTA Firmware Update

### Via 4G (Gateway Only)

1. Backend publishes `OTA_UPDATE` command with `{"url": "https://...", "version": "1.0.1"}`
2. Modem downloads the binary via HTTP GET
3. ESP32 flashes via `esp_ota_ops.h` (IDF) or `Update.h` (Arduino)
4. On success: reboot, send ACK with new version
5. On failure: remain on current firmware, send NACK

### Via USB

Use PlatformIO upload as described in section 4. Hold `BOOT` button if the board doesn't auto-enter bootloader.

### Via SD Card (field update)

1. Copy new `update.bin` to `/firmware/update.bin` on the SD card
2. Reboot the node
3. Firmware checks for `/firmware/update.bin` on boot and applies it

---

## 17. Testing & Validation

### Sensor Checks

Open serial monitor at 115200 baud. On boot you should see:

```
[BOOT] BH1750 OK — lux: 312.00
[BOOT] DHT22 OK — temp: 28.4°C, hum: 71.2%
[BOOT] INA219 OK — current: 0.38A, power: 45.8W
[BOOT] DS3231 OK — time: 2025-01-01 18:30:00
[BOOT] Relay OK — state: OFF
[BOOT] SD card OK — config loaded
[BOOT] LoRa OK — channel 6, +22dBm, AES ON
[BOOT] Node ready. IS_GATEWAY=false
```

### LoRa Link Test

1. Flash two boards — one as gateway, one as node
2. Open two serial monitors
3. The node should send a telemetry packet every 60 seconds
4. The gateway serial monitor should show `[LORA RX]` lines

### MQTT Connectivity Test (Gateway)

Use MQTT Explorer or the HiveMQ web console to subscribe to:
```
ssli/ghana/accra/ring-road/#
```
You should see telemetry JSON appearing every 60 seconds.

### Relay Test

Send via serial (or MQTT):
```json
{"commandId":"test-001","type":"LIGHT_ON","payload":{}}
```
The relay should click and the streetlight should turn on.

---

## 18. Troubleshooting

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Board not detected on USB | Wrong USB port | Use **ESP-USB** (left port), not Modem-USB |
| Upload fails | Bootloader not entered | Hold BOOT button, press RST, release BOOT |
| Modem not responding | PWRKEY not pulsed | Call `ModemHandler::powerOn()` with correct timing |
| SIM not found | SIM not seated | Remove and reseat SIM, check nano-SIM orientation |
| LoRa TX but no RX | Address mismatch | Check `AT+ADDRESS` on both modules |
| I2C sensor not found | Wrong SDA/SCL pins or address conflict | Run I2C scanner sketch to list detected addresses |
| DHT22 NaN readings | No pull-up resistor | Add 10 kΩ between DATA and 3.3V |
| INA219 reads 0A | Shunt resistor bypassed | Confirm INA219 is in series with load, not parallel |
| SD card not mounted | CS pin wrong or card not FAT32 | Format card as FAT32, verify SD_CS pin |
| MQTT connect refused | Wrong credentials or TLS config | Check `config.json`, confirm broker TLS port (8883) |
| Telemetry stops after 24h | Memory leak or watchdog issue | Enable hardware watchdog, check heap fragmentation |

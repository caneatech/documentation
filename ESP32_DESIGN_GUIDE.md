# ESP32 Node Design Guide
## Caneatech Intelligent Street Lighting System (ISLS)

---

## 1. System Overview

Each physical streetlight in the ISLS is paired with an embedded ESP32 device. These devices form a distributed sensor and control network that communicates upward to the Caneatech backend via a two-tier architecture: **LoRa mesh** at the field level, and **4G LTE (MQTT)** at the cloud level.

The ESP32 device serves as the "brain" of each streetlight node. It reads environmental sensors, controls the light, logs data locally, and participates in the communication network — either as a dumb sensor/actuator (regular node) or as a network coordinator (gateway node).

---

## 2. Node Types

The firmware supports two operating modes, selected at compile time via a boolean flag:

```cpp
const bool IS_GATEWAY = true;  // false for regular node
```

### Regular Node
A regular node is the most common device in the field. It:
- Reads all sensors (light, temperature, humidity, current, motion)
- Controls the streetlight relay (on/off, dimming)
- Communicates **exclusively via LoRa** to the nearest gateway
- Optionally logs data locally to an SD card
- Does not have direct internet access

### Gateway Node
A gateway node is a regular node with additional cloud connectivity. It:
- Does everything a regular node does (it controls its own streetlight)
- Receives LoRa messages from all regular nodes in its cluster
- Forwards telemetry upstream to the Caneatech backend via **4G LTE MQTT**
- Receives commands from the backend and distributes them downstream via LoRa
- Acts as the single point of internet access for an entire cluster

There is **no defined maximum** on how many regular nodes a single gateway can serve. This scales with LoRa range (~5 km line-of-sight) and the MQTT message rate the backend can handle.

---

## 3. Communication Topology

```
[Backend / Cloud]
       |
    (MQTT over 4G LTE)
       |
  [Gateway Node]  ←── controls its own streetlight
       |
    (LoRa 433 MHz)
    /     |     \
[Node] [Node] [Node]  ←── each controls its own streetlight
```

### LoRa (Field Layer)
- **Module:** DX-LR02-433T22D (ASR6601 chip)
- **Band:** 433 MHz
- **Range:** Up to 5 km line-of-sight
- **Protocol:** AT command UART — no custom LoRa stack required
- **Direction:** Bidirectional — nodes send telemetry up, receive commands down

### 4G LTE (Cloud Layer — Gateway Only)
- **Modem:** SimCom A7670G-LLSE (onboard the LilyGO T-A7670X-S3)
- **Protocol:** MQTT over TCP/TLS
- **Broker:** Caneatech HiveMQ Cloud instance
- **Topic pattern:** `ssli/{country}/{city}/{zone}/{clusterId}/{hardwareId}/{msgType}`

The modem is controlled via AT commands over a hardware UART. The ESP32 acts as the MQTT client through the modem — it never touches TCP/IP directly.

---

## 4. Hardware Architecture

### Core Board
**LilyGO T-A7670X-S3-Standard** is the single PCB that houses both the MCU and the 4G modem. Only **gateway nodes** make use of the onboard modem. Regular nodes use the same board but leave the modem unused (or powered down to save energy).

The board provides:
- ESP32-S3-WROOM-1 MCU (dual-core 240 MHz, 4 MB flash, 512 KB SRAM)
- SimCom A7670G modem (4G LTE Cat-1, GNSS)
- Onboard TF (micro SD) card slot
- LiPo battery JST connector + onboard charging circuit
- Dual USB-C ports (one for MCU programming, one for modem)

### External Peripherals

All external sensors and modules connect to the ESP32-S3 GPIO headers.

| Component | Interface | Purpose |
|-----------|-----------|---------|
| BH1750 | I2C | Ambient light level (lux) — determines when to turn on/off |
| DHT22 | Single-wire digital | Ambient temperature & humidity |
| INA219 | I2C | Current and power draw measurement of the streetlight |
| DS3231 RTC | I2C | Hardware real-time clock — keeps accurate time without internet |
| Relay Module | Digital GPIO | Controls mains power to the streetlight (on/off) |
| DX-LR02 LoRa | UART (AT commands) | LoRa radio — field communication layer |
| SD Card Module | SPI (or onboard TF slot) | Local data logging and startup configuration |

### I2C Bus Sharing
BH1750 (0x23), INA219 (0x40), and DS3231 (0x68) all share the same I2C bus. Their addresses do not conflict, so they can coexist on a single SDA/SCL pair without any multiplexing.

---

## 5. Power System Design

### Architecture
```
[Solar Panel]
      |
[Charge Controller (MPPT)]
      |
[LiPo / Lead-Acid Battery]
      |
[5V Buck Regulator]  →  [LilyGO Board + Peripherals]
      |
[Mains AC Line]  →  [Relay]  →  [Streetlight Lamp]
```

### Design Principles

**Solar Charging:**  
A solar panel sized for your regional irradiance charges the battery through an MPPT charge controller (not the LilyGO's onboard charger — that is for bench use only). The onboard JST connector is a backup/bench power path.

**Battery:**  
Use a sealed lead-acid (SLA) or LiFePO4 battery for outdoor deployment due to temperature tolerance and cycle life. LiPo cells degrade rapidly in direct sunlight heat unless thermally managed.

**Low-Power Strategy:**  
- Regular nodes should use ESP32 light sleep between telemetry intervals
- The modem on regular nodes should be kept powered off entirely
- The LoRa module should be put into sleep mode between transmissions
- Target a telemetry interval of 60 seconds minimum (configurable)

**INA219 Measurement:**  
The INA219 shunt resistor is placed on the load side of the relay — it measures the current drawn by the streetlight lamp, not the ESP32 itself. This gives the backend accurate power consumption data per node.

**Voltage Monitoring:**  
The ESP32-S3 ADC reads the battery voltage via a voltage divider. This feeds the `battery` field in the telemetry payload.

---

## 6. Firmware Architecture

### Operating Modes (set at compile time)

```
IS_GATEWAY = false  →  LoRa-only node firmware
IS_GATEWAY = true   →  Gateway firmware (LoRa + 4G MQTT)
USE_SD_CARD = true/false  →  Enable/disable SD card logging
```

### Main Firmware Loop

```
BOOT
 ├── Load config from SD card (if USE_SD_CARD)
 ├── Initialize I2C bus (BH1750, INA219, DS3231)
 ├── Initialize DHT22
 ├── Initialize Relay (default: OFF)
 ├── Initialize LoRa (via AT commands)
 ├── [Gateway only] Initialize 4G modem + MQTT connect
 └── Start main loop:
      ├── Every TELEMETRY_INTERVAL seconds:
      │    ├── Read all sensors
      │    ├── Build telemetry JSON payload
      │    ├── [Node] Send via LoRa to gateway
      │    └── [Gateway] Publish directly to MQTT
      ├── On LoRa RX (gateway only):
      │    ├── Parse incoming telemetry from node
      │    └── Forward to MQTT broker
      ├── On MQTT command received (gateway only):
      │    ├── If command is for self → execute
      │    └── If command is for another node → forward via LoRa
      ├── On LoRa command received (node only):
      │    └── Execute command locally
      ├── On motion detected (interrupt-driven):
      │    └── Send immediate alert payload
      └── Log to SD card (if USE_SD_CARD)
```

### Scheduling & Automation
Nodes support a local schedule (stored in SD card config or received via command) that can automatically turn the light on/off at specific times. The DS3231 RTC provides accurate timekeeping even during power outages or connectivity loss.

---

## 7. Data Flow

### Telemetry (Node → Backend)

```
Sensors → ESP32 reads → JSON payload built → 
  [Node]    → LoRa TX → Gateway receives → MQTT publish → Backend ingests
  [Gateway] → MQTT publish directly → Backend ingests
```

### Commands (Backend → Node)

```
Backend publishes MQTT command →
  Gateway MQTT subscriber receives →
    If targetHardwareId == self → Execute command
    If targetHardwareId != self → LoRa TX to target node →
      Node receives LoRa command → Execute → Send ACK via LoRa →
      Gateway receives ACK → MQTT publish ACK → Backend updates CommandEntity
```

### Alerts (Node → Backend, immediate)

Motion events, fault conditions, or low-battery warnings trigger an out-of-cycle alert payload. These follow the same path as telemetry but use the `alert` message type.

---

## 8. MQTT Topic Structure

The backend expects the following topic pattern (defined in `MqttTopics` constants):

```
ssli/{country}/{city}/{zone}/{clusterId}/{hardwareId}/{msgType}
```

| `{msgType}` | Direction | Description |
|-------------|-----------|-------------|
| `telemetry` | Node → Backend | Periodic sensor readings |
| `alert`     | Node → Backend | Immediate event notifications |
| `ack`       | Node → Backend | Command acknowledgment |
| `status`    | Node → Backend | Node online/offline heartbeat |
| `cmd`       | Backend → Node | Downlink commands |

Example topic for a gateway in Accra:
```
ssli/ghana/accra/ring-road/cluster-uuid-123/gw-hw-001/telemetry
```

---

## 9. Command Set

The following commands are supported by the firmware:

| Command | Payload | Description |
|---------|---------|-------------|
| `LIGHT_ON` | none | Turn streetlight on immediately |
| `LIGHT_OFF` | none | Turn streetlight off immediately |
| `SET_BRIGHTNESS` | `{"level": 0-100}` | PWM dimming (0 = off, 100 = full) |
| `SET_SCHEDULE` | `{"on": "18:30", "off": "06:00"}` | Automatic on/off schedule |
| `GET_STATUS` | none | Request immediate status report |
| `GET_TELEMETRY` | none | Request immediate telemetry reading |
| `SET_TELEMETRY_INTERVAL` | `{"seconds": 60}` | Change reporting interval |
| `ENABLE_MOTION` | none | Enable motion-triggered events |
| `DISABLE_MOTION` | none | Disable motion-triggered events |
| `SYNC_TIME` | `{"epoch": 1234567890}` | Sync RTC to provided Unix timestamp |
| `REBOOT` | none | Soft reboot the ESP32 |
| `OTA_UPDATE` | `{"url": "...", "version": "1.0.1"}` | Trigger OTA firmware update |
| `CLEAR_LOGS` | none | Wipe SD card logs |
| `LOAD_CONFIG` | none | Reload config from SD card |

---

## 10. Security Considerations

- **MQTT TLS:** The 4G modem establishes a TLS-encrypted MQTT connection. Credentials (username, password) are stored in the SD card config file or compiled in as constants — never hardcoded in shared source.
- **LoRa Encryption:** The DX-LR02 supports AES-128 encryption via AT command. Enable this with a shared key between all nodes in a cluster.
- **Physical Security:** The device enclosure should be IP65 or better for outdoor pole mounting. Consider tamper-evident screws.
- **SD Card Config:** The `config.json` file on the SD card contains sensitive credentials. Use file-system permissions where possible, and physically secure the enclosure.

---

## 11. Deployment & Maintenance

- **Firmware updates** are delivered OTA via the 4G modem (gateway) or via USB-C in the field
- **Log rotation** on the SD card is automatic — files older than 30 days are deleted
- **Watchdog timer** is enabled to auto-recover from firmware hangs
- **Heartbeat / status** messages are sent every 5 minutes so the backend can detect offline nodes
- **Factory reset** is triggered by holding a designated GPIO pin low on boot (clears runtime config, reloads from SD card defaults)

---

## 12. Scalability Notes

- A single gateway can serve an entire cluster as defined in the backend (no firmware limit)
- LoRa at 433 MHz with +22 dBm and -138 dBm sensitivity covers up to 5 km line-of-sight — sufficient for most urban street grid segments
- If a cluster spans more than one gateway's radio range, deploy multiple gateways — the backend's cluster/node model supports this natively
- All node `hardwareId` values must be globally unique across the entire deployment

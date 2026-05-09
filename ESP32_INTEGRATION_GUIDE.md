# ESP32 Integration Guide — Caneatech SSLI Backend

This document specifies exactly what the ESP32 firmware must send as telemetry and what it will receive as commands from the backend. All communication happens over MQTT with TLS.

---

## 1. MQTT Broker Connection

| Parameter   | Value                                                              |
|-------------|--------------------------------------------------------------------|
| Broker URL  | `ssl://bce01726b9ac4276b701388186518afa.s1.eu.hivemq.cloud:8883`   |
| Protocol    | MQTT 5 over TLS/SSL                                                |
| Port        | `8883`                                                             |
| Username    | *(provided separately — see project config)*                       |
| Password    | *(provided separately — see project config)*                       |
| QoS Level   | `1` (AT_LEAST_ONCE) for all publish and subscribe                  |

The ESP32 must connect with TLS. Plain-text MQTT on port 1883 is **not** supported.

---

## 2. Device Identity — `hardwareId`

Every ESP32 node has a unique `hardwareId` string (e.g., `NODE_001`, `STREETLIGHT_042`).

- Must be **uppercase**
- Must be **registered in the backend database** before any MQTT messages are processed
- The `hardwareId` is embedded in MQTT topics — the backend uses it to look up which node sent data

If the backend receives a message from an unknown `hardwareId`, it will be ignored.

---

## 3. MQTT Topic Structure

All topics follow this pattern:

```
ssli/{country}/{city}/{zone}/{clusterId}/{hardwareId}/{message-type}
```

Example for a node in Ghana → Accra → Zone-A with clusterId `abc123` and hardwareId `NODE_001`:

```
ssli/ghana/accra/zone-a/abc123/NODE_001/telemetry
ssli/ghana/accra/zone-a/abc123/NODE_001/ack
ssli/ghana/accra/zone-a/abc123/NODE_001/alert
```

Commands from the backend arrive on a **different** topic format:

```
ssli/cmd/{clusterId}/{hardwareId}
```

Example:
```
ssli/cmd/abc123/NODE_001
```

> **The ESP32 must subscribe to its command topic on startup.**

---

## 4. Telemetry Data — What to Send

**Topic:** `ssli/{country}/{city}/{zone}/{clusterId}/{hardwareId}/telemetry`  
**Direction:** ESP32 → Backend  
**Format:** JSON

### Payload

```json
{
  "batteryLevel": 85,
  "lightIntensity": 320.5,
  "motionDetected": false,
  "temperature": 27.3,
  "humidity": 62.1,
  "powerConsumption": 4.8,
  "recordedAt": "2026-05-09T14:30:00"
}
```

### Field Reference

| Field             | Type    | Unit / Range      | Description                                          |
|-------------------|---------|-------------------|------------------------------------------------------|
| `batteryLevel`    | Integer | `0 – 100` (%)     | Battery charge percentage                            |
| `lightIntensity`  | Double  | Lux (or raw ADC)  | Ambient or emitted light sensor reading              |
| `motionDetected`  | Boolean | `true` / `false`  | Whether the PIR/motion sensor triggered              |
| `temperature`     | Double  | Celsius           | Ambient temperature reading                          |
| `humidity`        | Double  | `0 – 100` (%)     | Relative humidity reading                            |
| `powerConsumption`| Double  | Watts (or mA)     | Current power draw of the node                       |
| `recordedAt`      | String  | ISO 8601          | Timestamp when the reading was taken on the device   |

### `recordedAt` Format

Use ISO 8601 local datetime without timezone offset:

```
"2026-05-09T14:30:00"
```

**All fields are required.** Missing fields will cause the backend to reject the message.

### How Battery Level Affects Node Status

The backend automatically derives node health from `batteryLevel`:

| Battery Level | Status        |
|---------------|---------------|
| > 20%         | `ONLINE`      |
| 10 – 20%      | `MAINTENANCE` |
| < 10%         | `FAULT`       |

### Recommended Publish Interval

Publish telemetry every **5 to 30 minutes** depending on how frequently conditions change at the deployment site. The backend retains raw readings for 90 days and aggregates weekly summaries automatically.

---

## 5. Commands — What to Receive

**Topic:** `ssli/cmd/{clusterId}/{hardwareId}`  
**Direction:** Backend → ESP32  
**Format:** JSON

### Payload

```json
{
  "commandId": "550e8400-e29b-41d4-a716-446655440000",
  "type": "TOGGLE_LIGHT",
  "payload": "ON"
}
```

### Field Reference

| Field       | Type   | Description                                                         |
|-------------|--------|---------------------------------------------------------------------|
| `commandId` | String | UUID of this command — **must be echoed back in the ACK**           |
| `type`      | String | The command name (e.g., `TOGGLE_LIGHT`, `SET_BRIGHTNESS`, `REBOOT`) |
| `payload`   | String | Optional command-specific value; may be `null`                      |

The ESP32 must:
1. Parse the incoming JSON.
2. Execute the action described by `type` and `payload`.
3. Send an ACK back to the backend immediately after handling the command.

---

## 6. Acknowledgment (ACK) — Confirming a Command

**Topic:** `ssli/{country}/{city}/{zone}/{clusterId}/{hardwareId}/ack`  
**Direction:** ESP32 → Backend  
**Format:** JSON

After executing a command, publish this payload:

```json
{
  "commandId": "550e8400-e29b-41d4-a716-446655440000",
  "responsePayload": "Light turned ON successfully"
}
```

### Field Reference

| Field             | Type   | Description                                                    |
|-------------------|--------|----------------------------------------------------------------|
| `commandId`       | String | The UUID from the command message — required                   |
| `responsePayload` | String | Optional result/confirmation message; may be `null` or omitted |

The backend will update the command status from `SENT` → `ACKNOWLEDGED` and record the response payload and timestamp.

---

## 7. Alerts — Reporting Faults or Threshold Violations

**Topic:** `ssli/{country}/{city}/{zone}/{clusterId}/{hardwareId}/alert`  
**Direction:** ESP32 → Backend  
**Format:** JSON

Send an alert any time the device detects an abnormal condition (sensor failure, hardware fault, threshold exceeded, etc.):

```json
{
  "message": "Temperature sensor disconnected"
}
```

### Field Reference

| Field     | Type   | Description                                   |
|-----------|--------|-----------------------------------------------|
| `message` | String | Human-readable description of the alert event |

The backend logs the alert and broadcasts it in real time to dashboard clients over WebSocket. Send alerts on-demand — **do not** send them on a fixed interval.

---

## 8. Command Lifecycle

```
Backend creates command  →  PENDING
Backend publishes to MQTT  →  SENT
ESP32 publishes ACK  →  ACKNOWLEDGED
(If MQTT publish fails)  →  FAILED
```

The ESP32 only affects the transition from `SENT` to `ACKNOWLEDGED`. If the ESP32 does not ACK, the command stays in `SENT` state permanently — there is no automatic retry from the backend.

---

## 9. Startup Checklist for the ESP32

1. Connect to MQTT broker over TLS using the provided credentials.
2. Subscribe to the command topic: `ssli/cmd/{clusterId}/{hardwareId}`
3. Set up a timer to publish telemetry on your chosen interval.
4. On receiving a command: parse → execute → publish ACK.
5. On sensor fault or threshold breach: publish an alert.

---

## 10. Complete Example Exchange

### ESP32 publishes telemetry

**Topic:** `ssli/ghana/accra/zone-a/abc123/NODE_001/telemetry`

```json
{
  "batteryLevel": 74,
  "lightIntensity": 215.0,
  "motionDetected": true,
  "temperature": 29.1,
  "humidity": 68.4,
  "powerConsumption": 5.2,
  "recordedAt": "2026-05-09T22:00:00"
}
```

### Backend sends a command

**Topic:** `ssli/cmd/abc123/NODE_001`

```json
{
  "commandId": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "type": "SET_BRIGHTNESS",
  "payload": "75"
}
```

### ESP32 acknowledges the command

**Topic:** `ssli/ghana/accra/zone-a/abc123/NODE_001/ack`

```json
{
  "commandId": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "responsePayload": "Brightness set to 75%"
}
```

### ESP32 sends an alert

**Topic:** `ssli/ghana/accra/zone-a/abc123/NODE_001/alert`

```json
{
  "message": "Light driver overcurrent detected"
}
```

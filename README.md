# ServoFacelock

Team project for real-time face tracking with servo control using a distributed architecture:

- PC vision node detects/tracks faces and publishes movement commands over MQTT.
- ESP8266 edge controller subscribes to commands and actuates a servo.
- Backend service relays MQTT messages to a browser dashboard through WebSocket.

## Architecture

PC Vision Node -> MQTT Broker -> ESP8266 Servo Controller  
MQTT Broker -> Backend WebSocket Service -> Web Dashboard

## Repository Structure

```text
.
|-- src/
|   |-- face_locking.py
|   |-- esp8266_servo_controller.py
|   |-- websocket_backend.py (if used in your deployment)
|   |-- dashboard.html (if used in your deployment)
|   |-- enroll.py
|   |-- detect.py
|   |-- camera.py
|   |-- landmarks.py
|   |-- haar_5pt.py
|   |-- embed.py
|   |-- evaluate.py
|   |-- recognize.py
|   |-- esp8266_servo_controller/
|       |-- esp8266_servo_controller.ino
|-- requirements.txt
|-- requirements_backend.txt
|-- README.md
```

Note: Some scripts support earlier validation and hardware testing workflows (for example `servo_test.py`, `basic_servo_test.ino`, and related files).

## Requirements

### Vision node (PC)

Install dependencies:

```bash
pip install -r requirements.txt
```

`requirements.txt`:
- opencv-python
- mediapipe
- onnxruntime
- numpy
- matplotlib

### Backend service

Install dependencies:

```bash
pip install -r requirements_backend.txt
```

`requirements_backend.txt`:
- websockets==11.0.3
- paho-mqtt==1.6.1
- asyncio-mqtt==0.13.0

## Quick Start

1. Configure team and broker settings in the vision and ESP8266 controller code.
2. Start the backend service (if using dashboard relay).
3. Power/connect ESP8266 to Wi-Fi and MQTT.
4. Run the vision node:

```bash
python src/face_locking.py
```

5. Open the dashboard (if included in your deployment).

## MQTT Topics

Use team-scoped topics to avoid collisions between groups.

Example pattern:

```text
vision/<TEAM_ID>/movement
vision/<TEAM_ID>/heartbeat
```

Example movement payload:

```json
{
  "status": "MOVE_LEFT",
  "confidence": 0.87,
  "timestamp": 1730000000,
  "servo_angle": 45.5,
  "face_position": 320,
  "degrees_from_center": 44.5,
  "direction": "LEFT"
}
```

## Validation Flow (Face Recognition Pipeline)

Run these in order when validating the recognition stack:

```bash
python src/camera.py
python src/detect.py
python src/landmarks.py
python src/haar_5pt.py
python src/embed.py
```

## Notes

- Do not commit private credentials (Wi-Fi passwords, tokens, keys) to the repository.
- Keep broker host, ports, and team id configurable per environment.

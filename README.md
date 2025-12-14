# ESP32-CAM Car Project

## Overview
This project is a WiFi-controlled car based on the ESP32-CAM module. It features live video streaming, camera control (pan/tilt), motor control, and a web-based user interface for remote operation. The project is designed for use with the AI-Thinker ESP32-CAM board and leverages the PlatformIO build system.

## Features
- Live video streaming from the ESP32-CAM
- WebSocket-based real-time control
- Motor control for forward, backward, left, and right movement
- Camera pan/tilt control via servos
- Flashlight (LED) control
- WiFi AP and STA modes with captive portal for easy setup
- Responsive web UI for mobile and desktop

## Directory Structure
```
├── data/           # Minified web UI files for upload to ESP32 (LittleFS)
│   ├── busy.min.html
│   ├── index.min.html
│   ├── script.min.js
│   └── style.min.css
├── lib/            # Source (unminified) web UI files
│   ├── busy.html
│   ├── index.html
│   ├── script.js
│   └── style.css
├── src/            # Main firmware source code
│   ├── Car.h
│   ├── carServer.h
│   ├── config.h
│   ├── customApSuccess.h
│   ├── main.cpp
│   ├── Motor.h
│   └── utils.h
├── platformio.ini  # PlatformIO project configuration
```

## Getting Started

### Prerequisites
- ESP32-CAM (AI-Thinker or compatible)
- PlatformIO (VS Code extension recommended)
- Micro-USB cable

### Build and Upload
1. Clone this repository.
2. Open the project folder in VS Code with PlatformIO installed.
3. Connect your ESP32-CAM to your computer.
4. Build and upload the firmware:
   - Click the PlatformIO "Upload" button, or run:
     ```
     pio run --target upload
     ```
5. Upload the web UI files to the ESP32 filesystem:
   - Click the PlatformIO "Upload File System image" button, or run:
     ```
     pio run --target uploadfs
     ```

### Usage
- On first boot, the ESP32-CAM creates a WiFi access point (AP) named `WiFi Car`.
- Connect to this AP with your phone or computer.
- Open a browser and go to `http://192.168.4.1:82` or `http://car.local:82`.
- Use the web interface to control the car and view the camera stream.
- You can configure the car to connect to your home WiFi using the captive portal.

## Source Code Structure
- `main.cpp`: Main entry point, hardware and WiFi setup, main loop.
- `Car.h`: Car logic, camera and servo control, flash, and movement.
- `Motor.h`: Motor driver abstraction.
- `carServer.h`: HTTP/WebSocket server, command handling, file serving.
- `config.h`: Board and pin configuration, camera model selection.
- `utils.h`: Utility functions (timing, conversions).
- `customApSuccess.h`: Custom captive portal UI for WiFiManager.

## Web UI
- `lib/` contains the source HTML, CSS, and JS for the web interface.
- `data/` contains the minified versions for upload to the ESP32 (LittleFS).
- The UI is mobile-friendly and supports real-time control and video.


## LED Indicator and Connection Guide

### LED Indicator Modes

- **Startup:**
  - 1 long blink — Start of boot process.
- **Boot Error:**
  - 3 very quick blinks — Boot error (e.g., camera or WiFi failed, or hardware issue). The board will auto-restart after 3 seconds.
- **Successful Boot:**
  - 1 long blink — Boot completed successfully.

After a successful boot, the car enters the last saved operating mode:

- **AP Mode (Access Point):**
  - Continuous short blinks — Car is in AP mode. Connect to the `WiFiCar` network.
- **ST Mode (Station):**
  - Continuous long blinks — Car is in Station mode, connected to a router. Connect your device to the same router.

**Client Connected:**
- Regardless of AP or ST mode, when a client is connected, the LED will slowly pulse. This indicates a successful connection and the user will see the web interface and camera stream.

#### Quick Reference

| Event                  | LED Signal                  |
|------------------------|-----------------------------|
| Start of boot          | 1 long blink                |
| Boot error             | 3 quick blinks              |
| Successful boot        | 1 long blink                |
| AP mode                | Continuous short blinks     |
| ST mode                | Continuous long blinks      |
| Client connected       | Slow pulsing                |

---

## Getting Started: Connection Instructions

### First Start
On first boot, the car will operate in AP mode. Connect to the `WiFiCar` access point and open your browser to [http://car.local:82/](http://car.local:82/) (or the backup address [http://192.168.4.1:82/](http://192.168.4.1:82/)). The car's web interface will open.

### Station (ST) Mode
In ST mode, the car will try to connect to the last used WiFi network. If it fails, it will revert to AP mode.

When the car is in ST mode (see LED indicator), connect your device to the same WiFi network as the car and open [http://car.local:82/](http://car.local:82/). ST mode is more convenient and efficient, as both the client and car are on the same network and the client retains internet access. You won't need to switch WiFi networks between the car and your router. ST mode is recommended for regular use.

#### mDNS Notes
- `car.local` uses mDNS in your local network. Some routers may not support mDNS. If so, you must use the car's IP address instead.
- To find the car's IP, check your router's client list for the device and use `http://<IP>:82` in your browser.
- If `car.local` works after connecting the car to the router, your router supports mDNS. However, mDNS records may persist for a few minutes after the car is powered off. If you restart the car quickly, the router may not create a new mDNS record, and the car won't respond at `car.local:82`. If this happens, power cycle the car again after a minute to reset the mDNS record.

---

## Credits
- Based on ESP32-CAM and WiFiManager libraries

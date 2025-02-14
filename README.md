# ESP32 Dual-Core MQTT with NTP (Sri Lanka Time)

This project runs two tasks on the dual-core ESP32, each counting in opposite directions while publishing the values along with the current time (adjusted for Sri Lanka time) to an MQTT broker.

## 🛠 Features
- Uses **ESP32's Dual-Core** feature:
  - **Core 0**: Counts from **1 to 100**
  - **Core 1**: Counts from **100 to 1**
- Publishes real-time data to an **MQTT broker**
- Fetches **NTP time** and adjusts it to **Sri Lanka time (UTC+5:30)**
- **Wi-Fi connectivity** for network communication
- Uses **FreeRTOS** for multitasking

## 🔧 Hardware Requirements
- **ESP32 Development Board**
- **Wi-Fi Network**
- **MQTT Broker** 

## 📜 Dependencies
Ensure you have the following Arduino libraries installed:
- [WiFi](https://www.arduino.cc/reference/en/libraries/wifi/)
- [PubSubClient](https://github.com/knolleary/pubsubclient) (for MQTT)
- [NTPClient](https://github.com/arduino-libraries/NTPClient)
- [wifiUDP]

## ⚙️ Setup & Configuration
1. **Clone this repository**
   ```sh
   git clone https://github.com/yourusername/esp32-mqtt-ntp.git
   ```
2. **Modify Wi-Fi and MQTT Credentials** in the code:
   ```cpp
   const char *ssid = "YourWiFiSSID";
   const char *password = "YourWiFiPassword";
   const char *mqttServer = "YourMQTTBrokerIP";
   const int mqttPort = 1883;
   ```
3. **Upload to ESP32** using the Arduino IDE or PlatformIO
4. **Monitor Serial Output** (Baud rate: `115200`)

## 📡 MQTT Topics
The ESP32 publishes messages to the following MQTT topics:
- `esp32/core0` → `{ "count": <value>, "time": "HH:MM:SS" }`
- `esp32/core1` → `{ "count": <value>, "time": "HH:MM:SS" }`

## 🖥 Example Output
```
WiFi connected!
Connected to MQTT!
Publishing: Core0: 2 Time: 12:34:56
Publishing: Core1: 99 Time: 12:34:56
...
```
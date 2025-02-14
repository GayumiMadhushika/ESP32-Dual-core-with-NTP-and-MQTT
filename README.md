# ESP32-Dual-core-with-NTP-and-MQTT
This project utilizes the dual-core capabilities of the ESP32 to run two independent tasks concurrently:  
Core 1 counts from 1 to 100 Core 2 counts from 100 to 1 The data from both cores is sent via MQTT to a Mosquitto Broker, and the timestamp of each count is verified using NTP (Network Time Protocol)
TECHNOLOGIES USED:
ESP32 (Dual-core processing)
FreeRTOS (Real-time multitasking)
MQTT Protocol (Message communication)
Mosquitto Broker (MQTT message handling)
MQTT Explorer (Monitoring MQTT messages)
NTP (Network Time Protocol) (Timestamp synchronization)
Arduino IDE

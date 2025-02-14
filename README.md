# ESP32-Dual-core-with-NTP-and-MQTT
#include <WiFi.h>
#include <PubSubClient.h>
#include <NTPClient.h>
#include <WiFiUdp.h>

// WiFi credentials
const char *ssid = "Redmi";
const char *password = "12345678";

// MQTT Broker
const char *mqttServer = "192.168.43.23";
const int mqttPort = 1883;
WiFiClient espClient;
PubSubClient client(espClient);

// NTP setup for Sri Lanka Time (UTC +5:30)
WiFiUDP udp;
NTPClient timeClient(udp, "pool.ntp.org", 19800);  // Offset 19800 sec (5hr 30min)

// Task Handles
TaskHandle_t task1Handle = NULL;
TaskHandle_t task2Handle = NULL;

// Global variables to store counts
int count1 = 1;
int count2 = 100;

// Setup MQTT connection
void setupMQTT() {
  client.setServer(mqttServer, mqttPort);
  client.setCallback(mqttCallback);
}

void mqttCallback(char* topic, byte* payload, unsigned int length) {
  // Handle incoming messages (if needed)
}

// Core 0 counting from 1 to 100
void core0Task(void *parameter) {
  for (;;) {  // Infinite loop
    count1++;
    if (count1 > 100) count1 = 1;

    // Get the current time from NTP
    timeClient.update();
    String time = timeClient.getFormattedTime();

    // Send count1 and time to MQTT
    String message = "Core0: " + String(count1) + " Time: " + time;
    client.publish("esp32/core0", message.c_str());

    vTaskDelay(1000 / portTICK_PERIOD_MS);  // Non-blocking delay
  }
}

// Core 1 counting from 100 to 1
void core1Task(void *parameter) {
  for (;;) {  // Infinite loop
    count2--;
    if (count2 < 1) count2 = 100;

    // Get the current time from NTP
    timeClient.update();
    String time = timeClient.getFormattedTime();

    // Send count2 and time to MQTT
    String message = "Core1: " + String(count2) + " Time: " + time;
    client.publish("esp32/core1", message.c_str());

    vTaskDelay(1000 / portTICK_PERIOD_MS);  // Non-blocking delay
  }
}

// Setup Wi-Fi and MQTT
void setup() {
  Serial.begin(115200);

  // Connect to Wi-Fi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("WiFi connected!");

  // Setup MQTT connection
  setupMQTT();

  // Setup NTP client
  timeClient.begin();

  // Create tasks pinned to cores
  xTaskCreatePinnedToCore(core0Task, "Core0Task", 10000, NULL, 1, &task1Handle, 0);  // Core 0
  xTaskCreatePinnedToCore(core1Task, "Core1Task", 10000, NULL, 1, &task2Handle, 1);  // Core 1
}

void loop() {
  // Ensure the MQTT connection is maintained
  if (!client.connected()) {
    while (!client.connected()) {
      Serial.println("Connecting to MQTT...");
      if (client.connect("ESP32Client")) {
        Serial.println("Connected to MQTT!");
      } else {
        delay(1000);
      }
    }
  }

  client.loop();  // Keep the MQTT client running
}

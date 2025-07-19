# IoT_Based_Plant_Health_Monitoring_System

---

### 📜 **Code Overview**

You are building a **Smart Plant Monitoring System** that:
- Connects to Wi-Fi.
- Connects to **Firebase Realtime Database**.
- Reads data from:
  - **DHT11 sensor** for temperature and humidity.
  - **Soil moisture sensor**.
  - **PIR sensor** for motion detection.
- Controls a **solenoid valve** (via a relay) based on:
  - A **physical button**.
  - A **command from Firebase**.
- Continuously **updates** sensor values and **reads solenoid control commands** from Firebase.

![3a6e38db-003b-412c-b275-7f4db071d37c](https://github.com/user-attachments/assets/3ce48658-bd5e-4125-be78-0d921950c15f)

---
### 🛠️ **Libraries Used**
- `WiFi.h`: Connects ESP32 to a Wi-Fi network.
- `FirebaseESP32.h`: Communicates with Firebase Realtime Database.
- `DHT.h`: Reads temperature and humidity from DHT11 sensor.

---

### 🔧 **Key Definitions**

```cpp
#define WIFI_SSID "esp32"
#define WIFI_PASSWORD "Sai@1234"
```
→ Wi-Fi credentials.

```cpp
#define FIREBASE_HOST "https://smart-plant-monitoring-c6110-default-rtdb.firebaseio.com/"
#define FIREBASE_AUTH "95zON7XcZHfLMGd0cxXIN72vBX4zHezDth3CY7UW"
```
→ Firebase Database URL and authentication token.
![WhatsApp Image 2025-07-19 at 4 40 34 PM](https://github.com/user-attachments/assets/c3b7f1f9-63f0-427d-84d1-89200de080d8)

cpp!
```
#define DHTPIN 4
#define DHTTYPE DHT11
```
→ DHT11 is connected to GPIO 4.

```cpp
#define SOIL_PIN 34
#define PIR_PIN 18
#define RELAY_PIN 5
#define BUTTON_PIN 14
```
→ Sensor and actuator pin definitions:
- Soil moisture sensor on **GPIO 34** (Analog input).
- PIR motion sensor on **GPIO 18** (Digital input).
- Relay control pin on **GPIO 5** (Output).
- Push button on **GPIO 14** (Input with pull-up resistor).

---

### 🏗️ **Setup Function (`setup()`)**
1. Start Serial communication for debugging.
2. Initialize sensor and actuator pins.
3. Begin DHT sensor reading.
4. Connect to Wi-Fi:
   - Wait until connected.
5. Configure Firebase connection:
   - Set Firebase host URL and auth token.
   - Begin Firebase service.
-
--

### 🔁 **Main Loop (`loop()`)**
Every **1 second**, the following happens:

#### 1. **Read Sensor Data**
- `dht.readTemperature()` → Temperature in Celsius.
- `dht.readHumidity()` → Relative Humidity (%).
- `analogRead(SOIL_PIN)` → Soil moisture:
  - Raw ADC value mapped to percentage (0–100%).
  - Higher percentage = wetter soil.
- `digitalRead(PIR_PIN)` → Motion detected or not (0 or 1).

---

#### 2. **Manual Solenoid Control (via Button)**
- Reads the **Button** state (with debouncing).
- If button pressed:
  - **Toggle** the relay (ON/OFF).
  - Update the solenoid state **to Firebase**.

---

#### 3. **Remote Solenoid Control (via Firebase)**
- Reads `/plant_monitor/solenoid_status` string from Firebase.
  - If value is `"true"`, turns **solenoid ON** (`relay LOW`).
  - If value is `"false"`, turns **solenoid OFF** (`relay HIGH`).

> 🛠 Small note: In your code, the solenoid relay logic is **active low** (relay turns ON when pin is LOW).

---

#### 4. **Upload Sensor Data to Firebase**
- Uploads temperature, humidity, soil moisture, and motion status to Firebase.

---

#### 5. **Print to Serial Monitor**
Prints real-time status:
```plaintext
→ Temp: 26.00 | Hum: 65.00 | Soil: 43 | Motion: 1 | Solenoid: true
```
Helps you monitor the plant conditions.

---

### 🔥 **Important Code Points**

- **Debouncing** ensures button press is reliable (50ms delay check).
- **Mapping Soil Sensor** data inverses the percentage, because wet soil has lower resistance.
- **Firebase Communication** is two-way:
  - Read `solenoid_status` (remote control).
  - Write sensor data (update plant health).

---

### ✅ **Summary**
Your project achieves:
- **Wireless plant monitoring** (temperature, humidity, soil moisture, motion).
- **Remote control** of a **water solenoid valve** through **Firebase**.
- **Manual control** through a **physical push button**.
- **Real-time updates** both ways (ESP32 ↔ Firebase).

---

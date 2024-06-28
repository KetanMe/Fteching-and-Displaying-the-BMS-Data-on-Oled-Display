# Fetching-and-Displaying-the-BMS-Data-on-Oled-Display

## Table of Content

1. [Introduction]()
2. [Libraries and Constants]()
3. [Setup Function]()
4. [Loop Function]()
5. [Functions]()
   - [`readDataFromFirebase()`]()
   - [`displayDataOnOLED()`]()
   - [`printReceivedValues()`]()

### Introduction
This code integrates WiFi connectivity, Firebase interaction, OLED display management, and serial output to create a system that reads data from a Firebase Realtime Database and displays it on an OLED screen while providing debugging information via Serial Monitor. Adjustments and error handling ensure smooth operation under various conditions, making it suitable for remote monitoring applications.

### Libraries and Constants

```cpp
#include <string.h>
#include <FirebaseESP8266.h>
#include <ESP8266WiFi.h>
#include <U8g2lib.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64

const char *WIFI_SSID = "Airtel-MyWiFi-AMF-311WW-CF14";
const char *WIFI_PASSWORD = "12ac4bc8";
const char *API_KEY = "AIzaSyA1V05Jh53vRDa9SHQO4IdoSk2e0HQts10";
const char *DATABASE_URL = "https://fir-1673d-default-rtdb.firebaseio.com/";

FirebaseData fbdo;
FirebaseAuth auth;
FirebaseConfig config;
bool signupOK = false;

float voltage;
float current;
int SoC;
float maxcv;
float minicv;
int maxT;
int minT;
float capacity;

U8G2_SSD1306_128X64_NONAME_F_HW_I2C display(U8G2_R0, /* reset=*/ U8X8_PIN_NONE);
```

#### Explanation:
- **Libraries**: Includes necessary libraries for Firebase (FirebaseESP8266), WiFi (ESP8266WiFi), and OLED display (U8g2lib).
- **Constants**: Defines WiFi credentials (`WIFI_SSID` and `WIFI_PASSWORD`), Firebase API key (`API_KEY`), and database URL (`DATABASE_URL`).
- **Firebase and Variables**: Declares Firebase client objects (`FirebaseData fbdo`, `FirebaseAuth auth`, `FirebaseConfig config`) and variables to store data received from Firebase.
- **OLED Display**: Initializes an OLED display (`U8G2_SSD1306_128X64_NONAME_F_HW_I2C`) with specific hardware configuration.

### Setup Function

```cpp
void setup() {
  Serial.begin(115200);

  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  Serial.print("Connecting to Wi-Fi");
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    delay(300);
  }
  Serial.println();
  Serial.print("Connected with IP: ");
  Serial.println(WiFi.localIP());
  Serial.println();

  config.api_key = API_KEY;
  config.database_url = DATABASE_URL;

  if (Firebase.signUp(&config, &auth, "", "")) {
    Serial.println("Sign up successful.");
    signupOK = true;
  } else {
    Serial.printf("Sign up failed: %s\n", config.signer.signupError.message.c_str());
  }

  Firebase.begin(&config, &auth);
  Firebase.reconnectWiFi(true);

  // Initialize the OLED display
  display.begin();
  display.clear();
}
```

#### Explanation:
- **Serial Setup**: Starts serial communication for debugging.
- **WiFi Connection**: Connects ESP8266 to WiFi using credentials (`WIFI_SSID` and `WIFI_PASSWORD`).
- **Firebase Setup**: Configures Firebase API key and database URL (`config.api_key` and `config.database_url`). Attempts Firebase authentication (`Firebase.signUp`) and handles success or failure.
- **Firebase Initialization**: Initializes Firebase connection (`Firebase.begin`) and ensures WiFi reconnection (`Firebase.reconnectWiFi`).
- **OLED Initialization**: Initializes and clears the OLED display (`display.begin()` and `display.clear()`).

### Loop Function

```cpp
void loop() {
  // Read data from Firebase
  readDataFromFirebase();

  // Display data on OLED
  displayDataOnOLED();

  // Print received values on Serial Monitor
  printReceivedValues();
  
  // Delay for stability
  delay(1000);
}
```

#### Explanation:
- **Read Data from Firebase**: Calls `readDataFromFirebase()` to fetch data from specific Firebase paths (`/Voltage`, `/Current`, etc.).
- **Display Data on OLED**: Calls `displayDataOnOLED()` to update the OLED display with fetched data.
- **Print to Serial Monitor**: Calls `printReceivedValues()` to print fetched data to the Serial Monitor for debugging.
- **Delay**: Introduces a delay of 1 second (`delay(1000)`) for stability and to control the update rate of data fetching and display.

### Functions (`readDataFromFirebase`, `displayDataOnOLED`, `printReceivedValues`)

These functions are responsible for:
- **`readDataFromFirebase()`**: Reads specific data (voltage, current, SoC, etc.) from Firebase using `Firebase.getFloat` and `Firebase.getInt`.
- **`displayDataOnOLED()`**: Clears the OLED buffer, sets font sizes, positions data (SoC, voltage, temperature), and sends the buffer to the OLED display (`display.sendBuffer()`).
- **`printReceivedValues()`**: Prints fetched data (voltage, current, SoC, etc.) to the Serial Monitor.

### Output
![image](https://github.com/KetanMe/Fteching-and-Displaying-the-BMS-Data-on-Oled-Display/assets/121623546/7dbbeb2c-d0c3-4475-921b-a6489b134fce)



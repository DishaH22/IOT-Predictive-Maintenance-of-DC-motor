# IOT-Predictive-Maintenance-of-DC-motor

## 💡 Tools & Technologies
- Arduino IDE  
- Python  
- IoT Dashboard: Blynk  
- Sensors: Temperature (TMP36), Humidity (DHT22), Voltage, Vibration, Optocoupler, Accelerometer (MMA8451)  
- LOLIN WEMOS microcontroller with inbuilt WiFi  

## 📌 Project Summary
Implemented an IoT-based condition monitoring system for industrial DC motors, capturing real-time operational data (temperature, humidity, voltage, vibration, motor speed, and orientation).  

Applied predictive analytics including FFT-based vibration analysis to anticipate motor faults, reducing downtime and maintenance costs. Developed a cloud-integrated Blynk dashboard for remote monitoring and real-time control of motor direction, enabling data-driven decision-making in industrial environments.

This system demonstrates how low-cost sensors and IoT platforms can transform traditional machines into smart, connected systems capable of predictive maintenance.

## 📈 System Architecture & Workflow
The system operates in **three main stages**:  

1. **Signal Acquisition & Preprocessing**  
   - Analog and digital sensors collect temperature, humidity, voltage, vibration, speed, and orientation.  
   - Multiplexer (MUX) circuit allows multiple analog sensors to connect to a single ADC pin.  
   - TMP36 sensors measure motor and ambient temperature with OPAMP-based scaling and offset.  
   - DHT22 measures ambient temperature and humidity.  
   - Optocoupler + tachometer measures motor RPM.  
   - Accelerometer (MMA8451) tracks motor tilt and orientation.  

2. **Data Analysis & Processing**  
   - FFT analysis on vibration signals to detect faults.  
   - Vibration severity evaluated using ISO 10816 standard.  
   - Fault detection triggers alerts for preventive maintenance.  

3. **System Monitoring & Control**  
   - Data sent via WiFi to Blynk dashboard every second.  
   - Dashboard widgets display motor & ambient temperature, humidity, RPM, and orientation.  
   - Motor direction can be controlled remotely (Forward, Backward, Stop).  
   - Centralized local or cloud server allows real-time monitoring and emergency interventions.  

<img width="1024" height="1536" alt="IOT WORKFOW" src="https://github.com/user-attachments/assets/a18aa0ae-49b9-4c24-a032-8d5844377bad" />


---

## 🖼 Screenshots
### Sensor Setup
<img width="1000" height="1000" alt="image" src="https://github.com/user-attachments/assets/c8dff294-6e37-4582-8628-57da0edeb503" />

<img width="645" height="461" alt="Screenshot 2026-02-09 211444" src="https://github.com/user-attachments/assets/da662732-b35e-4574-8601-1abcabc38d8a" />
<img width="706" height="731" alt="Screenshot 2026-02-09 211433" src="https://github.com/user-attachments/assets/10a2a64f-9a8b-41ae-83e0-d493cf7017f7" />
<img width="603" height="803" alt="Screenshot 2026-02-09 211458" src="https://github.com/user-attachments/assets/75ec3874-c9ad-4a16-8bbd-9c92bfa29509" />

### FFT Analysis
<img width="328" height="744" alt="Screenshot 2026-02-09 211624" src="https://github.com/user-attachments/assets/fbffc406-1a73-4bab-bbd6-d5c7e70072ef" />

### Blynk Dashboard
<img width="495" height="500" alt="Screenshot 2026-02-09 211510" src="https://github.com/user-attachments/assets/bcd7edb1-5708-4c99-b11a-e83a40bd91a7" />


---
## Codes:

// ----------------------------------------------------
// IoT Predictive Maintenance of DC Motor
// Controller: LOLIN D1 Mini Pro (ESP8266)
// Platform: Blynk IoT
// ----------------------------------------------------

// Blynk & ESP8266 Libraries
#define BLYNK_PRINT Serial
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <SimpleTimer.h>

// Sensor Libraries
#include <DHTesp.h>
#include <Wire.h>
#include <Adafruit_Sensor.h>
#include "Adafruit_MMA8451.h"

// Multiplexer Selection Lines
#define MUX_A 13
#define MUX_B 15

// Analog Input
#define ANALOG_INPUT A0

// H-Bridge Pins
const int FIN = D0;
const int RIN = D5;

// DHT Sensor
DHTesp dht;

// Accelerometer
Adafruit_MMA8451 mma = Adafruit_MMA8451();

// Blynk Credentials
char auth[] = "YOUR_BLYNK_TOKEN";
char ssid[] = "YOUR_WIFI";
char pass[] = "YOUR_PASSWORD";

SimpleTimer timer;

// ----------------------------------------------------
// Setup
// ----------------------------------------------------
void setup() {

  Serial.begin(9600);

  Blynk.begin(auth, ssid, pass);

  // Initialize DHT22
  dht.setup(12, DHTesp::DHT22);

  pinMode(FIN, OUTPUT);
  pinMode(RIN, OUTPUT);

  pinMode(MUX_A, OUTPUT);
  pinMode(MUX_B, OUTPUT);

  Serial.println("Starting Predictive Maintenance System");

  if (!mma.begin()) {
    Serial.println("Accelerometer not detected!");
    while (1);
  }

  Serial.println("MMA8451 detected");

  mma.setRange(MMA8451_RANGE_2_G);

  timer.setInterval(1000L, sendData);
}

// ----------------------------------------------------
// Change MUX Channel
// ----------------------------------------------------
void changeMux(int a, int b) {
  digitalWrite(MUX_A, a);
  digitalWrite(MUX_B, b);
}

// ----------------------------------------------------
// Send Sensor Data
// ----------------------------------------------------
void sendData() {

  // Ambient Temperature
  changeMux(LOW, LOW);

  float analogTemp = analogRead(ANALOG_INPUT);
  float voltage = analogTemp * 0.004;
  float temp = (voltage * 100) - 49;

  Serial.print("Ambient Temperature: ");
  Serial.print(temp);
  Serial.println(" °C");

  Blynk.virtualWrite(V0, temp);

  delay(1000);

  // Motor Temperature
  changeMux(LOW, HIGH);

  float motorTemp = analogRead(ANALOG_INPUT);
  float voltage2 = motorTemp * 0.004;
  float tempMotor = (voltage2 * 100) - 49;

  Serial.print("Motor Temperature: ");
  Serial.print(tempMotor);
  Serial.println(" °C");

  Blynk.virtualWrite(V1, tempMotor);

  // DHT Sensor
  TempAndHumidity data = dht.getTempAndHumidity();

  Serial.print("Humidity: ");
  Serial.println(data.humidity);

  Blynk.virtualWrite(V2, data.humidity);

  // Accelerometer Data
  sensors_event_t event;
  mma.getEvent(&event);

  Serial.print("Acceleration X: ");
  Serial.println(event.acceleration.x);

  Serial.print("Acceleration Y: ");
  Serial.println(event.acceleration.y);

  Serial.print("Acceleration Z: ");
  Serial.println(event.acceleration.z);

  Blynk.virtualWrite(V3, event.acceleration.x);
  Blynk.virtualWrite(V4, event.acceleration.y);
  Blynk.virtualWrite(V5, event.acceleration.z);
}

// ----------------------------------------------------
// Loop
// ----------------------------------------------------
void loop() {
  Blynk.run();
  timer.run();
}

---

## 🚀 Future Improvements
- Integrate machine learning algorithms for automatic fault prediction  
- Expand monitoring to multiple motors simultaneously  
- Add automated notifications/alerts for predictive maintenance  
- Include advanced vibration analysis for more accurate fault diagnosis  

---

## 📚 References
2. ISO 10816: Vibration Severity Chart for Industrial Machines  
3. Blynk IoT Platform Documentation  
4. TMP36, DHT22, MMA8451 Sensor Datasheets



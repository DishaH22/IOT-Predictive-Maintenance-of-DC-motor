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





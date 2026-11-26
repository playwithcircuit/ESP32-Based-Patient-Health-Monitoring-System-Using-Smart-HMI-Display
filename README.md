# ESP32-Based Patient Health Monitoring System Using Smart HMI Display

![ESP32-Based Patient Health Monitoring System using Smart HMI Display](https://playwithcircuit.com/wp-content/uploads/2026/10/Patient-Health-Monitoring-System-using-VIEWE-Smart-HMI-Display-Biomedical-Sensors-and-ESP32.webp)

This project demonstrates an ESP32-based patient health monitoring system using a 7-inch ESP32-P4 Smart HMI Display. It combines three sensors—MAX30102, AD8232, and LM35—to collect health-related measurements and display them through an interactive LVGL-based dashboard.

The system uses two separate units:

- **Sensor Unit:** An ESP32 DevKit C collects data from the connected sensors.
- **Display Unit:** An ESP32-P4 Smart HMI Display receives sensor data and presents it through a touchscreen interface.

Both units communicate through RS485, allowing reliable data transmission between the sensor controller and display.

## Project Features

- SpO₂ monitoring using the MAX30102 sensor
- ECG signal acquisition using the AD8232 module
- Heart rate measurement using ECG data
- Respiration rate estimation from ECG data
- Body temperature measurement using the LM35
- Real-time sensor data display
- ECG waveform visualization
- LVGL-based graphical interface
- RS485 communication between the sensor and display units

## Hardware Requirements

| Component | Purpose | Quantity |
|---|---|---:|
| VIEWE ESP32-P4 Smart HMI Display | Main touchscreen display | 1 |
| ESP32 DevKit C | Sensor data acquisition | 1 |
| MAX30102 | Optical SpO₂ sensor | 1 |
| AD8232 ECG Module | ECG signal acquisition | 1 |
| LM35 | Temperature measurement | 1 |
| TTL-to-RS485 Converter | RS485 communication | 2 |
| 4.7 kΩ Resistors | I2C pull-up resistors | 2 |
| ECG Electrodes | ECG signal collection | 3 |
| Breadboard and Jumper Wires | Prototyping connections | As required |

## System Architecture

The project uses a two-controller architecture.

1. The sensor-side ESP32 reads measurements from the MAX30102, AD8232, and LM35.
2. The ESP32 processes the sensor readings and prepares the data for transmission.
3. A TTL-to-RS485 converter transfers the UART data to the RS485 differential communication lines.
4. A second RS485 converter receives the data at the display controller.
5. The ESP32-P4 processes the received information and updates the LVGL dashboard.

This arrangement separates sensor acquisition from graphical display processing.

## Patient Health Monitoring Target Wiring

![Patient Health Monitoring Target Wiring](https://playwithcircuit.com/wp-content/uploads/2026/10/Target-Side-Wiring-Connections-1536x817.webp)
### 1. MAX30102 to ESP32

The MAX30102 communicates with the ESP32 through the I2C interface.

| ESP32 Pin | MAX30102 Pin |
|---|---|
| 3.3V | Vin |
| GND | GND |
| GPIO_22 | SCL |
| GPIO_21 | SDA |

**Important I2C observation:** The MAX30102 module used in this project had onboard SDA and SCL pull-up resistors connected to its internal 1.8V regulator. To resolve the I2C communication issue, the onboard SDA and SCL pull-ups were removed and external 4.7 kΩ pull-up resistors were connected to 3.3V. The INT pin's onboard pull-up was left unchanged because the pin was not used.

### 2. AD8232 to ESP32

The AD8232 provides an analog ECG signal to the ESP32.

| ESP32 Pin | AD8232 Pin |
|---|---|
| 3.3V | 3.3V |
| GND | GND |
| GPIO_34 | Output |
| GPIO_33 | LO- |
| GPIO_32 | LO+ |

The RA, LA, and RL terminals connect to the three ECG electrodes. The SDN pin is not used in this setup.

### 3. LM35 to ESP32

The LM35 provides an analog output proportional to the measured temperature.

| ESP32 Pin | LM35 Pin |
|---|---|
| 5V | Vcc |
| GND | GND |
| GPIO_35 | Out |

### 4. ESP32 to RS485 Converter

The sensor-side ESP32 communicates with the RS485 converter through UART.

| ESP32 Pin | RS485 Converter Pin |
|---|---|
| 3.3V | 3.3V |
| GPIO_16 | Rx |
| GPIO_17 | Tx |
| GND | GND |

## Controller Side Wiring Connections

![Controller Side Wiring Connections](https://playwithcircuit.com/wp-content/uploads/2026/10/Controller-Side-Wiring-Connections-1536x722.webp)

The display controller uses another TTL-to-RS485 converter to receive sensor data.

| ESP32-P4 Pin | RS485 Converter Pin |
|---|---|
| 3.3V | 3.3V |
| GPIO_47 | Rx |
| GPIO_46 | Tx |
| GND | GND |

Connect the A+ and B− terminals of both RS485 converters to their corresponding differential communication lines.

## Sensor Functions and Specifications

| Sensor | Interface | Measurement |
|---|---|---|
| MAX30102 | I2C (0x57) | SpO₂ |
| AD8232 | Analog output | ECG and heart rate |
| LM35 | Analog output | Temperature |

The respiration rate is estimated from the ECG signal acquired through the AD8232.

## RS485 Communication Protocol

The sensor controller uses a simple command-response protocol.

**Command format:**

`[Slave_Address] [Command]`

Example:

`01 F1`

**Response format:**

`[Slave_Address] [Command] [Status] [Response Data byte 1] ... [Response Data byte n]`

### Supported Commands

| Command | Measurement | Data Format |
|---|---|---|
| 0xF1 | Heart rate | 2 bytes, BPM × 10 |
| 0xF2 | Respiration rate | 2 bytes, breaths/min × 10 |
| 0xF3 | SpO₂ | 1 byte, percentage |
| 0xF4 | Temperature | 2 bytes, °C × 10 |
| 0xF5 | ECG samples | 50 signed 16-bit samples |

The display controller requests the required measurement from the sensor-side ESP32. The target controller responds with the corresponding data, which is processed and displayed by the ESP32-P4.

## Display Interface

The ESP32-P4 Smart HMI Display provides a 7-inch touchscreen interface with a 1024 × 600 resolution.

The graphical interface is developed using LVGL and includes:

- Main patient monitoring dashboard
- Heart rate and SpO₂ readings
- Temperature and respiration rate readings
- ECG waveform visualization
- Dedicated graph screens
- Touch-based screen navigation

The display controller also maintains a buffer of the latest 60 seconds of ECG samples for waveform visualization.

## Project Tutorial

The complete project explanation, hardware setup, and implementation details are available on **Play with Circuit**.

## Discussion

For a patient monitoring system with multiple sensors, would you prefer a separate sensor controller communicating with the display over RS485, or would you integrate all the sensors directly with the ESP32-P4? What factors would influence your choice?

## Disclaimer

This project is intended for educational and experimental purposes only. It is not a clinically validated medical device and must not be used for medical diagnosis, treatment, or patient-care decisions.

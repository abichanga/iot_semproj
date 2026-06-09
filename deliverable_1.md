# Flora Farms Greenhouse IoT - Deliverable 1

**Group:** Ohm Sweet Ohm 
**Assigned Flower:** Daisy  
**Project:** Remote greenhouse environmental monitoring system for Flora Farms, Naivasha

---

## 1. Environmental Requirements for Daisy Growth

Below are the target environmental parameters that our embedded devices will need to monitor to support healthy daisy cultivation. These values serve as the reference thresholds for the alerting and analysis logic we'll implement later in the project.

| Parameter | Optimal Range | Notes |
|---|---|---|
| **Optimal Temperature** | 15 °C - 24 °C (60 °F - 75 °F) | Daisies can tolerate cooler temps (down to approximately -6 °C) and warmer conditions (up to 29 °C), but growth slows significantly outside this optimal band. This range aligns well with Flora Farms' greenhouse targets of 18-24 °C. |
| **Optimal Relative Humidity** | 40 % - 60 % | Around 50 % is the sweet spot. Too little humidity causes leaf desiccation, while excessive moisture invites fungal diseases like powdery mildew and botrytis. Adequate ventilation in the greenhouse is essential for maintaining this range. |
| **Recommended Soil Type** | Well-draining loamy or sandy loam, rich in organic matter | An ideal growing medium combines compost, peat moss, and perlite in roughly equal parts. This provides a balance of moisture retention, aeration, and nutrient supply. |
| **Optimal Soil Moisture** | Consistently moist, not waterlogged (~60-70 % of field capacity; roughly 1 inch of water per week) | Daisies are prone to root rot, so the soil must stay moist but never soggy. Given that the greenhouses are irrigated from a local river stream, monitoring soil moisture is critical to avoid overwatering. |
| **Optimal Soil pH** | 6.0 - 7.0 (slightly acidic to neutral) | This range ensures optimal nutrient uptake through the root system. A broader tolerance of about 5.8-7.5 is acceptable, but performance is best within the primary range. |
| **Sunlight Exposure** | 6 - 8 hours of direct sunlight per day | Daisies are full-sun plants; some varieties may need even longer. This can be measured using a light sensor (e.g., BH1750) and integrated over the course of the day. |

---

## 2. Hardware Components

All components are constrained by the greenhouse's existing power supply: a **200 W solar panel** charging a **12 V 100 Ah battery** per greenhouse. The design therefore steps 12 V down to the 5 V and 3.3 V rails that the electronics require.

### 2.1 Microcontroller

| Component | Specification | Role |
|---|---|---|
| ESP32-S DevKit (30-pin) | Dual-core 240 MHz, Wi-Fi + BLE, 34 GPIO, 12-bit ADC | Central processing unit - reads all sensors, drives the OLED display, and communicates data over Wi-Fi to the cloud platform. Two units are needed for Designs B and C. |

### 2.2 Sensors

| Component | Specification | Measurement |
|---|---|---|
| DHT22 (AM2302) | Temperature: -40 to +80 °C (+-0.5 °C); Humidity: 0-100 % RH (+-2-5 %) | Temperature and relative humidity |
| MQ-5 Gas Sensor | Detects LPG (butane/propane), methane, coal gas; 5 V heater, analog output | LPG concentration - critical because the greenhouses use LPG-powered heating systems |
| Capacitive Soil Moisture Sensor v1.2 | Analog output, corrosion-resistant (no exposed metal probes) | Soil moisture content |
| Analog pH Probe + Signal Conditioning Board | 0-14 pH range, BNC connector, analog output | Soil pH |
| BH1750 Light Intensity Sensor | 1-65535 lux, I2C digital output, 16-bit | Sunlight exposure (integrated over hours) |

### 2.3 Display

| Component | Specification | Role |
|---|---|---|
| 1.3" White IIC 128x64 OLED LCD (SH1106) | I2C interface (SDA, SCL), 3.3 V-5 V compatible | Local display for real-time sensor readings at the greenhouse |

### 2.4 Relay

| Component | Specification | Role |
|---|---|---|
| 5V 1-Channel Low-Level Trigger Relay Module (SRD-05VDC-SL-C) | Switching capacity: 10 A @ 250 VAC; trigger: active LOW | Used in Design C to switch power to the second ESP32 + MQ-5 node on demand |

### 2.5 Power Regulation

| Component | Specification | Role |
|---|---|---|
| LM2596 DC-DC Buck Converter Module | Input: 4.5-40 V, Output: adjustable 1.25-37 V, max 3 A | Steps down the 12 V battery supply to a stable 5 V rail for the ESP32's VIN and the MQ-5 heater. The ESP32's onboard AMS1117 then derives the 3.3 V rail. |

### 2.6 Passive Components & Prototyping Tools

| Component | Quantity / Notes |
|---|---|
| 10 kΩ resistor | Pull-up for the DHT22 data line |
| 100 nF ceramic capacitor | Decoupling cap between DHT22 VDD and GND for signal filtering |
| 10 kΩ + 20 kΩ resistors (voltage divider) | Divides the MQ-5 analog output (~5 V max) down to <= 3.3 V for the ESP32's ADC input. Ratio: 20k / (10k + 20k) = ~0.67 |
| Breadboard(s) | Solderless prototyping - at least 830 tie-point full-size boards |
| Jumper wires (M-M, M-F, F-F) | Assorted lengths for breadboard and module interconnections |
| Micro-USB cable | Programming and serial debugging of the ESP32 |
| Multimeter | Verifying voltages, continuity checks, and debugging |

---

## 3. Component Datasheets

Below are the datasheets (or product reference pages) for the five components specified in the deliverable brief. Where the component in question is a breakout-board module, the cited datasheet refers to the underlying IC or sensing element, which is the technically relevant document.

| # | Component | Datasheet / Reference | Source |
|---|---|---|---|
| 1 | **1.3" White IIC 128x64 OLED LCD** | [1.3inch SH1106 OLED Datasheet (PDF)](https://www.waveshare.com/w/upload/e/e3/1.3inch-SH1106-OLED.pdf) | Waveshare - covers the SH1106 driver IC, I2C/SPI interface configuration, pin definitions, and mechanical drawings |
| 2 | **ESP32-S DevKit WiFi + BLE Module (30-Pin)** | [ESP32-WROOM-32 Datasheet v3.6 (PDF)](https://www.espressif.com/sites/default/files/documentation/esp32-wroom-32_datasheet_en.pdf) | Espressif Systems - official datasheet covering the ESP32-WROOM-32 module (the core of the DevKit board), pin configurations, RF characteristics, and power consumption |
| 3 | **DHT22 (AM2302) Temperature & Humidity Sensor** | [DHT22 / AM2302 Datasheet (PDF)](https://cdn.sparkfun.com/assets/f/7/d/9/c/DHT22.pdf) | Aosong Electronics - single-bus communication protocol, electrical specs, and calibration information |
| 4 | **MQ-5 LPG / Natural Gas / Coal Gas Sensor** | [MQ-5 Flammable Gas Sensor Manual v1.5 (PDF)](https://www.winsen-sensor.com/d/files/MQ-5.pdf) | Zhengzhou Winsen Electronics - sensitivity characteristics, heater circuit specs, recommended load resistance (RL = ~20 kΩ), and test circuit |
| 5 | **5V 1-Channel Low-Level Trigger Relay Module** | [SRD-05VDC-SL-C Relay Datasheet (PDF)](https://www.circuitbasics.com/wp-content/uploads/2015/11/SRD-05VDC-SL-C-Datasheet.pdf) | Ningbo Songle Relay (hosted by Circuit Basics) - coil specifications, contact ratings (10 A @ 250 VAC), dimensions, and ordering information |

---

## 4. Schematic Diagrams

Three circuit architectures are presented below, each using the components listed in Sections 2 and 3. All designs include the following standard supporting circuitry:

- **MQ-5 voltage divider:** A 10 kΩ / 20 kΩ resistive divider on the MQ-5 analog output scales the 0-5 V signal down to 0-3.3 V, protecting the ESP32's ADC (which is not 5 V tolerant).
- **DHT22 pull-up and decoupling:** A 10 kΩ pull-up resistor on the data line (required by the single-wire protocol) and a 100 nF capacitor between VDD and GND for signal stability.
- **Common ground bus:** All sub-circuits share a common ground reference, which is critical for reliable analog readings and UART communication.
- **Power path:** 12 V battery -> LM2596 buck converter -> 5 V rail (feeds ESP32 VIN and MQ-5 heater) -> ESP32 onboard 3.3 V regulator (feeds DHT22, OLED, BH1750).

### 4.1 Design A - Single ESP32-S with MQ-5, DHT22, and OLED

In this design, a single ESP32-S board handles all sensing and display duties. The MQ-5 connects to an ADC-capable GPIO through the voltage divider, the DHT22 connects to a digital GPIO with a pull-up, and the 1.3" OLED connects over the I2C bus (GPIO 21 for SDA, GPIO 22 for SCL).

This is the simplest and most compact architecture, ideal for a single greenhouse node where all sensors are co-located.

![Design A - Single-node schematic](./schematics/design_a_single_node.png)

**Pin Assignments (Design A):**

| ESP32 Pin | Connected To | Notes |
|---|---|---|
| VIN (5 V) | LM2596 output, MQ-5 VCC | 5 V power rail |
| 3V3 | DHT22 VDD, OLED VCC | 3.3 V regulated rail |
| GND | Common ground bus | Shared across all components |
| GPIO 34 (ADC1_CH6) | MQ-5 AOUT via voltage divider | Input-only pin, suitable for ADC |
| GPIO 4 | DHT22 Data (with 10 kΩ pull-up to 3.3 V) | Single-wire digital protocol |
| GPIO 21 (SDA) | OLED SDA | I2C data line |
| GPIO 22 (SCL) | OLED SCL | I2C clock line |

---

### 4.2 Design B - Two ESP32-S Boards Interfaced Directly (UART)

Here, each ESP32 handles one sensor: **ESP32-A** is connected to the MQ-5, and **ESP32-B** is connected to the DHT22. The two boards communicate via a direct UART serial link (TX<->RX crossover).

Key considerations for this design:
- Both ESP32 boards operate at 3.3 V logic, so the UART lines connect directly with **no level shifter** required.
- The two boards **must share a common ground**, otherwise the UART signals have no reference and communication will fail.
- This architecture is useful when sensors are physically separated within the greenhouse (e.g., the gas sensor near the LPG heater, the temp/humidity sensor near the flower beds).

![Design B - Dual-node UART schematic](./schematics/design_b_dual_uart.png)

**Pin Assignments (Design B):**

| Board | ESP32 Pin | Connected To | Notes |
|---|---|---|---|
| ESP32-A | VIN | LM2596 output | 5 V power |
| ESP32-A | GPIO 34 | MQ-5 AOUT via voltage divider | ADC input |
| ESP32-A | GPIO 17 (TX2) | ESP32-B GPIO 16 (RX2) | UART TX -> RX |
| ESP32-A | GPIO 16 (RX2) | ESP32-B GPIO 17 (TX2) | UART RX <- TX |
| ESP32-B | VIN | LM2596 output | 5 V power |
| ESP32-B | GPIO 4 | DHT22 Data (with pull-up) | Sensor data line |
| Both | GND | Common ground bus | Essential for UART reference |

---

### 4.3 Design C - ESP32 + DHT22 -> Relay -> ESP32 + MQ-5

This design introduces power-switching logic. **ESP32-A** reads the DHT22 and controls the relay module. The relay's normally-open (NO) contact switches 5 V power to **ESP32-B**, which runs the MQ-5. This means the gas-monitoring node is only powered on when ESP32-A actively decides to turn it on (e.g., when temperature readings suggest the LPG heater is running).

The relay module is **low-level trigger** - pulling the control pin LOW energizes the coil and closes the NO contact. The module includes a built-in flyback diode and optocoupler for isolation.

![Design C - Relay-switched chain schematic](./schematics/design_c_relay_chain.png)

**Pin Assignments (Design C):**

| Board | ESP32 Pin | Connected To | Notes |
|---|---|---|---|
| ESP32-A | VIN | LM2596 output | 5 V power (always on) |
| ESP32-A | GPIO 4 | DHT22 Data (with pull-up) | Sensor data line |
| ESP32-A | GPIO 26 | Relay IN (active LOW) | Pulling LOW energizes the relay coil |
| ESP32-A | 5 V | Relay VCC | Powers the relay module |
| Relay | COM | LM2596 5 V output | Incoming power to switch |
| Relay | NO | ESP32-B VIN | Switched 5 V power to the second node |
| ESP32-B | GPIO 34 | MQ-5 AOUT via voltage divider | ADC input |
| Both | GND | Common ground bus | Shared reference |

**Relay Logic:**
- `GPIO 26 = HIGH` (default) -> relay coil de-energized -> NO contact open -> ESP32-B is unpowered
- `GPIO 26 = LOW` (triggered) -> relay coil energized -> NO contact closes -> ESP32-B boots and begins MQ-5 readings

---


## 5. Evidence of Groupwork

**Group Members:**
* [Arthur Asava] - [156972]
* [Silver Mbai] - [103023]
* [Isaac Njuguna] - [169647]
* [Sudheysi Ibrahim] - [166344]
* [Anita Bichanga] - [167752]

**Group Minutes from 4/06/26**

Venue: Gazebo outside STMB
Present: Arthur, Silver, Isaac, Sudheysi, Anita
Duration: 40 minutes
Summary:
Read through the Deliverable 1 brief together and broke it down into four sections.
Isaac asked about the MQ-5 needing 5 V while the ESP32 ADC only handles 3.3 V — agreed we'd need a voltage divider in the schematics.
Sudheysi brought up whether we need soil sensors now or later. Consensus: not required for this deliverable but worth keeping in mind.
Silver offered to check the Makerspace for what components are already available.

**Task Split**
| Task | Assigned To |
|---|---|
| Environmental research (daisy growth conditions) |Sudheysi |
| Hardware component list | Isaac |
| Datasheet sourcing | Silver  |
| Schematic diagrams (A, B, C) | Arthur, Isaac & Anita |
| Markdown compilation & GitHub submission | Anita |
---


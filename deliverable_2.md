# ICS 4111: Embedded Systems & IoT - Semester Project: Deliverable 2

## A. Prototype A: 1 ESP32S connected to 1 MQ-5, 1 DHT22 and Display

### Simulated Prototype
* **Wokwi Project Link:** [Simulation A Link](https://wokwi.com/projects/468343920186370049)
* **Simulation Schematic:**
![Simulation Schematic A](design_a.png)

### Physical Prototype
* **Initial Physical Implementation:**
![Physical Prototype Overview](simulation_a_proto.jpeg)


### Challenges Encountered and Resolutions
**Technical Prototyping Issue:** During the physical prototyping phase for this architecture, the initial LCD display (visible in the initial prototype image) was not functioning correctly and failed to display the expected output from the sensors.

**Resolution/Workaround:**
To resolve this issue, the team swapped the malfunctioning LCD for an OLED display. This hardware change was successful, and the OLED properly outputted the sensor readings (Temperature, Humidity, and Gas levels).

* **OLED Output Implementation (Working State):**
![OLED Output 1](simulation_a_1.jpeg)
![OLED Output 2](simulation_a_2.jpeg)

---

## B. Prototype B: 1 ESP32S connected to 1 MQ-5 interfaced directly with another ESP32S connected to 1 DHT22[cite: 1]

### Implementation Strategy
For this device architecture, our team chose to develop a **physical model**[cite: 1].

### Device Schematic
![Prototype B Schematic](design_b.jpeg)

### Physical Prototype Implementation
* **Hardware Setup:**
![Physical Prototype B Setup](insert_physical_prototype_b_image_link_here)

* **Hardware Output:**
![Physical Prototype B Output](insert_output_b_image_link_here)

---

## C. Prototype C: 1 ESP32S connected to 1 DHT22 connected to 1 relay which is connected to another ESP32S connected to 1 MQ-5[cite: 1]

### Implementation Strategy
For this device architecture, our team chose to develop a **simulated model** using Wokwi[https://wokwi.com/projects/468171968422865921]. 

### Device Schematic
![Prototype C Schematic](design_c.jpeg)

### Simulated Prototype
* **Wokwi Project Link:** [Wokwi Simulation C](https://wokwi.com/projects/468171968422865921)
* **Wokwi Implementation Diagram:**
![Simulation C Overview](simulation_c_overview.png)

### Simulation Outputs
* **Output from First ESP32:**
![First ESP32 Output](node_1_output.png)

* **Output from Second ESP32:**
![Second ESP32 Output](node_2_output.png)

---

## Evidence of Groupwork
*(Placeholder: Insert your team meeting notes, GitHub commit history screenshots, or collaboration evidence here)*


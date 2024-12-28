
# ESP32-S3-DevKitC-1 PCB Project Documentation

## Overview
This project involves the design and implementation of a custom PCB based on the ESP32-S3-WROOM-1 module. The primary goal was to take the ESP32-S3-WROOM-1 module and replicate the ESP32-S3-DevKitC-1 development board. Starting from scratch, I designed each circuit, researched its connections, and selected appropriate values for all component parameters to achieve the desired functionality. This documentation details the schematic design and system overview of the project.

---

## Features
- **Microcontroller**: ESP32-S3-WROOM-1 module with Wi-Fi and Bluetooth capabilities.
- **Power Regulation**: 5V to 3.3V conversion using a linear voltage regulator.
- **USB to UART Conversion**: Facilitates serial communication and programming.
- **Auto Programming Circuit**: Automates the programming process with control over boot and reset pins.
- **Pin Headers**: Access to all GPIO pins, power, and ground for prototyping and external interfacing.
- **RGB LED**: Addressable LED for status indication or decorative purposes.

---

## System Overview (Master Sheet)
![Master Sheet](https://i.imgur.com/HvE0amj.png)
The Master Sheet provides a top-level view of the PCB design, showing how all the subsystems connect:


- **Subsystems**:
  - USB Interface
  - USB to UART Converter
  - Auto Programming Circuit
  - ESP32-S3 Module
  - Pin Headers
  - RGB LED
- **Connections**:
  - Power Distribution: VCC_5V and VCC_3.3V supply power to all subsystems.
  - GPIO Pins: Provide control signals and interfaces for peripherals.
  - Communication: UART handles data exchange between the module and external devices.
 
---

## Schematics

### 1. **ESP USB Circuit**
![ESP USB Circuit](https://i.imgur.com/YaYjxaE.png)

- **Purpose**: Connects the ESP32-S3 module to a USB interface for power and data transfer.
- **Key Components**:
  - **Micro USB Connector**: Serves as the USB input for the circuit, providing both power and data signals.
  - **Protection Diodes**:
    - **D200, D204**: A Schottky diode that prevents reverse voltage from damaging the circuit by allowing current flow in only one direction, chosen for its low forward voltage drop, minimizing power loss (370 mV @ 1 mA).
    - **D201, D202, D203, D205, D206, D207**: TVS (Transient Voltage Suppressor) diodes used to protect the USB data lines and the VBUS line against transient voltage spikes and ESD (Electrostatic Discharge). TVS diodes are chosen for their fast response time and ability to handle high-energy transients effectively.
  - **GPIO19 and GPIO20**: These GPIO pins are used for USB communication, providing the module with direct USB interface capability.
  - **USB_UART_N and USB_UART_P**: Differential signal lines used for UART communication, ensuring high-speed and reliable data transmission between the USB interface and the ESP32-S3 module. These lines are essential for bridging USB to serial communication.
 - **Differential pair**: Routing is designed with a 90-ohm impedance to match the USB standard for signal integrity.


### 2. **USB to UART Converter Circuit**
![USB to UART Converter Circuit](https://i.imgur.com/GUHuDvu.png)
- **Purpose**: Converts USB signals to UART for serial communication.
- **Key Components**:
  - USB to UART Bridge IC (U400): Manages USB to UART signal conversion, connecting the USB interface to the ESP32-S3 module.
  - Resistors:
    - **R400** (22.1 kΩ): Pull-up resistor for the USB input line.
    - **R401** (4.7 kΩ): Current-limiting resistor on the signal line.
    - **R402** (1 kΩ): Pull-up resistor for stability.
    - **R403** and **R404** (0 Ω): Configurable jumpers for default paths.
  - Capacitors:
    - **C400** (10 µF): Input capacitor for smoothing the 3.3V power supply.
    - **C401** (1 µF): Decoupling capacitor for VBUS stabilization.
  - Connections:
    - USB_UART_N and USB_UART_P lines for differential USB communication.
    - TXD and RXD lines for UART communication with the ESP32-S3 module.
    - DTR and RTS lines for control signals to the Auto Programming Circuit.

### 3. **Power Circuit**
![Power Circuit](https://i.imgur.com/XX94xeS.png)

- **Purpose**: Regulates 5V input to 3.3V for the ESP32-S3 module.
- **Key Components**:
  - **U300 (Linear Voltage Regulator)**: Converts the 5V input to a stable 3.3V output with a maximum current of 1A. This ensures reliable power for the ESP32-S3 module and other 3.3V peripherals.
  - **C300 (10 µF)**: Input smoothing capacitor to stabilize the input voltage and filter any high-frequency noise.
  - **C301 (0.1 µF)**: Decoupling capacitor to reduce noise and voltage ripple on the input line.
  - **C302 (0.1 µF) & C303 (10 µF)**: Output capacitors to stabilize the 3.3V line, providing a smooth power supply to connected components.
  - **R300 (3.4 kΩ)**: Current-limiting resistor for the power indicator LED. It is calculated to provide 0.5 mA current to the LED, balancing visibility and power efficiency.
  - **D300 (Red LED)**: Indicates that the 3.3V output is active, allowing visual confirmation of power status.

- **Why These Values?**
  - **Capacitors**: The 10 µF capacitors (C300, C303) handle bulk decoupling for low-frequency noise, while the 0.1 µF capacitors (C301, C302) filter out high-frequency noise, ensuring a clean and stable power supply.
  - **Resistor (R300)**: The resistance is calculated using Ohm's Law:
    
    \[ R = \frac{(3.3V - 1.6V)}{0.5 \text{ mA}} = 3.4 \text{ kΩ} \]
    
    where 1.6V is the forward voltage of the LED, and 0.5 mA is the desired LED current.

### 4. **Enable Pin Circuit**
![Enable Pin Circuit](https://i.imgur.com/KmD4qEK.png)

- **Purpose**: Provides the ability to enable or reset the ESP32-S3 module manually.
- **Key Components**:
  - **R301 (10 kΩ)**: Pull-up resistor ensures the EN pin remains high by default, keeping the ESP32-S3 module enabled.
  - **RESET Button**: A momentary push button that grounds the EN pin when pressed, resetting the module.
  - **C304 (0.1 µF)**: Debounce capacitor eliminates noise or false triggering during button presses, ensuring stable operation.

### 5. **Boot Pin Circuit**
![Boot Pin Circuit](https://i.imgur.com/xOQV7yE.png)

- **Purpose**: Allows the ESP32-S3 module to enter bootloader mode for programming purposes.
- **Key Components**:
  - **R302 (10 kΩ)**: Pull-up resistor keeps GPIO0 high by default, ensuring normal operation.
  - **BOOT Button**: A momentary push button that grounds GPIO0 when pressed, placing the module in bootloader mode.
  - **C305 (0.1 µF)**: Debounce capacitor eliminates transient noise, providing stable and consistent signals during button presses.


### 6. **Auto Programming Circuit**
![Auto Programming Circuit](https://i.imgur.com/pRUxmVM.png)

- **Purpose**: Automates the boot and reset processes for programming the ESP32-S3 module.
- **Key Components**:
  - **Q500, Q501 (NSS20101JTI Transistors)**: NPN transistors that control the EN and GPIO0 pins based on the RTS and DTR signals from the UART interface.
  - **R500, R501, R502, R503 (10 kΩ)**: Pull-up resistors ensure stable base signals for the transistors, preventing noise-induced toggling.

- **Operation**:
  - The RTS and DTR signals from the UART interface drive the base of Q500 and Q501 through R501 and R503, respectively.
  - When the transistors are activated, they pull the EN and GPIO0 pins low to reset or enter programming mode.
  - The truth table specifies the state of EN and GPIO0 based on RTS and DTR inputs:
    
    | DTR | RTS | EN | GPIO0 |
    |-----|-----|----|-------|
    |  1  |  1  |  1 |   1   |
    |  0  |  0  |  1 |   1   |
    |  1  |  0  |  1 |   1   |
    |  0  |  1  |  1 |   1   |


### 7. **ESP32-S3-WROOM-1 Module Circuit**
![ESP32-S3-WROOM-1 Module Circuit](https://i.imgur.com/H7SlUaH.png)

- **Purpose**: Central processing unit providing Wi-Fi, Bluetooth, and GPIO functionality.
- **Key Components**:
  - ESP32-S3-WROOM-1 Module
  - Decoupling Capacitors
  - Connections for GPIO, UART, EN, and Power Pins

### 8. **RGB LED Circuit**
![RGB LED Circuit](https://i.imgur.com/1dTStJl.png)

- **Purpose**: Controls an addressable RGB LED for visual feedback.
- **Key Components**:
  - RGB LED
  - Current-Limiting Resistor
  - GPIO38 for Control Signal

### 9. **Pin Headers Circuit**
![Pin Headers Circuit](https://i.imgur.com/IV1PyjW.png)

- **Purpose**: Provides access to GPIO pins, power, and ground for external interfacing.
- **Key Features**:
  - Clearly labeled GPIO Pins
  - Dedicated Power and Ground Pins

---

## PCB Layout Visualizations

- **Top Layer**:
![Top Layer](https://i.imgur.com/ryyUIOh.png)
- **Power Layer**:
![Power Layer](https://i.imgur.com/CQRpa47.png)
- **Ground Layer**: 
![Ground Layer](https://i.imgur.com/O8uv7cl.png)
- **Bottom Layer**:
![Bottom Layer](https://i.imgur.com/VSsLdHn.png)

## 3D Model
![3D Model](https://i.imgur.com/xLreRyp.png)
![3D Model](https://i.imgur.com/qAoSosl.png)
![3D Model](https://i.imgur.com/cCCLOtA.png)


  

# ESP32-S3-DevKitC-1 PCB Project Documentation

  

## Overview

This project involves the design and implementation of a custom PCB based on the ESP32-S3-WROOM-1 module. The goal was to replicate the ESP32-S3-DevKitC-1 development board using this module. Starting from scratch, I designed each circuit, researched connections, and chose the appropriate component values to achieve the desired functionality. To simplify the design process and ensure accuracy, I also created an integrated component library, which helped manage footprints and electrical properties. This documentation covers the schematic design, the role of the component library, and the overall system structure.

---

  

## Features

-  **Microcontroller**: ESP32-S3-WROOM-1 module with Wi-Fi and Bluetooth capabilities.

-  **Power Regulation**: 5V to 3.3V conversion using a linear voltage regulator.

-  **USB to UART Conversion**: Facilitates serial communication and programming.

-  **Auto Programming Circuit**: Automates the programming process with control over boot and reset pins.

-  **Pin Headers**: Access to all GPIO pins, power, and ground for prototyping and external interfacing.

-  **RGB LED**: Addressable LED for status indication or decorative purposes.

  

---

  

## System Overview (Master Sheet)

![Master Sheet](https://i.imgur.com/MODrUup.png)

The Master Sheet gives a top-level view of the PCB design, showing how all the main parts connect and work together.

  

### Subsystems:

-  **USB Interface**: Connects the board to a computer or other USB devices.

-  **USB to UART Converter**: Converts USB signals to UART for communication with the ESP32-S3 module.

-  **Auto Programming Circuit**: Makes programming and resetting the ESP32-S3 module easier.

-  **ESP32-S3 Module**: The main controller that handles processing and communication.

-  **Pin Headers**: Allow easy access to pins for connecting external devices.

-  **RGB LED**: Provides visual feedback for system status or user actions.

  

---

  

## Schematics

  

### 1. **ESP USB Circuit**

![ESP USB Circuit](https://i.imgur.com/5bULbTQ.png)

  

-  **Purpose**: Connects the ESP32-S3 module to a USB interface for both power supply and data transfer. It ensures reliable communication and protects the circuit from voltage spikes and reverse currents.

-  **Key Components**:

	-  **Micro USB Connector**: Acts as the input point for power and data. It connects the PCB to external USB devices.

	-  **Protection Diodes**:

	-  **D200, D204**: Schottky diodes that prevent reverse voltage from damaging the circuit. These are chosen for their low forward voltage drop (220 mV @ 10 mA), which minimizes power loss.

	-  **D201, D202, D203, D205, D206, D207**: TVS (Transient Voltage Suppressor) diodes that protect the USB data lines and VBUS line from voltage spikes and electrostatic discharge (ESD). These diodes respond quickly to high-energy transients, safeguarding the circuit.
	
- **Connections**:
	-  **GPIO19 and GPIO20**: Differential signal lines used for USB communication, these pins provide the ESP32-S3 module with a direct USB interface for data transfer.

	-  **USB_UART_N and USB_UART_P**: Differential signal lines used for UART communication. They ensure high-speed, reliable data transmission between the USB interface and the ESP32-S3 module, enabling seamless USB-to-serial bridging.

	-  **Differential pair**: Routing is designed with a 90-ohm impedance to match the USB standard for signal integrity.

  
  

### 2. **USB to UART Converter Circuit**

![USB to UART Converter Circuit](https://i.imgur.com/RBqhwyW.png)

- **Purpose**: Converts USB signals to UART for serial communication between the USB interface and the ESP32-S3 module.

- **Key Components**:

  - **USB to UART Bridge IC (U400)**: Handles the conversion of USB signals to UART, ensuring smooth communication with the ESP32-S3 module.

  - **Resistors**:  
    - **R400** (1 kΩ): Pull-up resistor to ensure the USB to UART bridge IC is properly enabled.  
    - **R401** and **R402** (0 Ω): Act as zero-ohm jumpers to ensure direct signal paths.  
    
  - **Capacitors**:  
    - **C400** (10 µF): Smooths and stabilizes the 3.3V power supply.  
    - **C401** (1 µF): Decouples and stabilizes the VBUS power line.

- **Connections**:  
  - **USB_UART_N and USB_UART_P**: Differential lines for reliable USB communication.  
  - **TXD and RXD**: UART lines for data transfer between the USB interface and the ESP32-S3 module.  
  - **DTR and RTS**: Control lines used by the Auto Programming Circuit for programming and reset operations.


  

### 3. **Power Circuit**

![Power Circuit](https://i.imgur.com/yweeOLW.png)

  

-  **Purpose**: Regulates 5V input to 3.3V for the ESP32-S3 module.

-  **Key Components**:

	-  **U300 (Linear Voltage Regulator)**: Converts the 5V input to a stable 3.3V output with a maximum current of 1A. This ensures reliable power for the ESP32-S3 module and other 3.3V peripherals.

	-  **C300 (10 µF)**: Input smoothing capacitor to stabilize the input voltage and filter any high-frequency noise.

	-  **C301 (0.1 µF)**: Decoupling capacitor to reduce noise and voltage ripple on the input line.

	-  **C302 (0.1 µF) & C303 (10 µF)**: Output capacitors to stabilize the 3.3V line, providing a smooth power supply to connected components.

	-  **R300 (3.4 kΩ)**: Current-limiting resistor for the power indicator LED. It is calculated to provide 0.5 mA current to the LED, balancing visibility and power efficiency.

	-  **D300 (Red LED)**: Indicates that the 3.3V output is active, allowing visual confirmation of power status.

  

-  **Why These Values?**

	-  **Capacitors**: The 10 µF capacitors (C300, C303) handle bulk decoupling for low-frequency noise, while the 0.1 µF capacitors (C301, C302) filter out high-frequency noise, ensuring a clean and stable power supply.

	-  **Resistor (R300)**: The resistance is calculated using Ohm's Law:

R = (3.3V - 1.6V) / 0.5mA = 3.4 kΩ

where 1.6V is the forward voltage of the LED, and 0.5 mA is the desired LED current.

  

### 4. **Enable Pin Circuit**

![Enable Pin Circuit](https://i.imgur.com/KmD4qEK.png)

  

-  **Purpose**: Provides the ability to enable or reset the ESP32-S3 module manually.

-  **Key Components**:

	-  **R301 (10 kΩ)**: Pull-up resistor ensures the EN pin remains high by default, keeping the ESP32-S3 module enabled.

	-  **RESET Button**: A momentary push button that grounds the EN pin when pressed, resetting the module.

	-  **C304 (0.1 µF)**: Debounce capacitor eliminates noise or false triggering during button presses, ensuring stable operation.

  

### 5. **Boot Pin Circuit**

![Boot Pin Circuit](https://i.imgur.com/xOQV7yE.png)

  

-  **Purpose**: Allows the ESP32-S3 module to enter bootloader mode for programming purposes.

-  **Key Components**:

	-  **R302 (10 kΩ)**: Pull-up resistor keeps GPIO0 high by default, ensuring normal operation.

	-  **BOOT Button**: A momentary push button that grounds GPIO0 when pressed, placing the module in bootloader mode.

	-  **C305 (0.1 µF)**: Debounce capacitor eliminates transient noise, providing stable and consistent signals during button presses.

  
  

### 6. **Auto Programming Circuit**

![Auto Programming Circuit](https://i.imgur.com/pRUxmVM.png)

  

-  **Purpose**: Automates the boot and reset processes for programming the ESP32-S3 module.

-  **Key Components**:

	-  **Q500, Q501 (NSS20101JTI Transistors)**: NPN transistors that control the EN and GPIO0 pins based on the RTS and DTR signals from the UART interface.

	-  **R500, R501, R502, R503 (10 kΩ)**: Pull-up resistors ensure stable base signals for the transistors, preventing noise-induced toggling.

  

-  **Operation**:

	- The RTS and DTR signals from the UART interface drive the base of Q500 and Q501 through R501 and R503, respectively.

	- When the transistors are activated, they pull the EN and GPIO0 pins low to reset or enter programming mode.

	- The truth table specifies the state of EN and GPIO0 based on RTS and DTR inputs:

| DTR | RTS | EN | GPIO0 |

|-----|-----|----|-------|

| 1 | 1 | 1 | 1 |

| 0 | 0 | 1 | 1 |

| 1 | 0 | 1 | 1 |

| 0 | 1 | 1 | 1 |

  
  

### 7. **ESP32-S3-WROOM-1 Module Circuit**

![ESP32-S3-WROOM-1 Module Circuit](https://i.imgur.com/7QrJKMd.png)

  

-  **Purpose**: Central processing unit providing Wi-Fi, Bluetooth, and GPIO functionality.

-  **Key Components**:

	- ESP32-S3-WROOM-1 Module

	- Decoupling Capacitors

	- Connections for GPIO, UART, EN, and Power Pins

  

### 8. **RGB LED Circuit**

![RGB LED Circuit](https://i.imgur.com/TU4nTpz.png)

  

- **Purpose**: Controls an addressable RGB LED, providing visual feedback for system status or user interactions.

- **Key Components**:

  - **RGB LED**:  
    An addressable LED capable of displaying various colors based on the input data signal. It requires a 5V power supply and a data signal for control.

  - **Current-Limiting Resistor (R700, 1 kΩ)**:  
    Protects the GPIO pin and ensures a stable data signal by limiting the current flow to the level translator.

  - **Bidirectional Level Translator (B00)**:  
    Converts the 3.3V signal from the ESP32-S3 GPIO38 pin to a 5V signal required by the RGB LED. The **DIR** pin is tied to 3.3V to ensure data flows from the ESP32-S3 to the RGB LED.

  - **GPIO38**:  
    Provides the control signal to drive the data input (DIN) of the RGB LED through the level translator, enabling color and brightness adjustments.


  

### 9. **Pin Headers Circuit**

![Pin Headers Circuit](https://i.imgur.com/IV1PyjW.png)

  

-  **Purpose**: Provides access to GPIO pins, power, and ground for external interfacing.

-  **Key Features**:

	- Clearly labeled GPIO Pins

	- Dedicated Power and Ground Pins

  

---

## PCB Layout: 4-Layer Impedance Control Stackup

### Purpose
The PCB layout was designed using a **4-layer impedance-controlled stackup** to ensure signal integrity and reliable high-speed data transmission. The target impedance for critical signal traces was set to **50 ohms**, following the **Single-Ended (Non-Coplanar)** configuration. To achieve this, the **trace width** and **spacing** were calculated using JLCPCB’s **Impedance Calculator**. The signal layer (L1) uses **L2 (Ground Layer)** as the reference plane to minimize signal reflection and ensure high signal integrity.

### Stackup Details
The stackup was designed according to **JLCPCB’s 4-layer stackup specifications** with the following layers:

1. **L1 (Top Layer)**:  
   - Material: Outer Copper (1 oz)  
   - Thickness: 0.035 mm (1.38 mil)  
   - Used for high-speed signal routing with a target impedance of **50 ohms**.

2. **Prepreg Layer**:  
   - Material: 3313 RC57%  
   - Thickness: 0.092 mm (3.62 mil)  
   - Provides insulation between L1 and L2.

3. **L2 (Inner Layer 1 - Ground)**:  
   - Material: Inner Copper  
   - Thickness: 0.030 mm (1.18 mil)  
   - Serves as the reference ground plane for controlled impedance.

4. **Core Layer**:  
   - Material: FR-4 with 1 oz copper  
   - Thickness: 1.230 mm (48.43 mil)  
   - Acts as a structural and insulating layer.

5. **L3 (Inner Layer 2 - Power)**:  
   - Material: Inner Copper  
   - Thickness: 0.030 mm (1.18 mil)  
   - Used as a power plane.

6. **Prepreg Layer**:  
   - Material: 3313 RC57%  
   - Thickness: 0.092 mm (3.62 mil)  
   - Provides insulation between L3 and L4.

7. **L4 (Bottom Layer)**:  
   - Material: Outer Copper (1 oz)  
   - Thickness: 0.035 mm (1.38 mil)  
   - Used for routing less critical signals and for components.



## PCB Layout Visualizations

  

-  **Full Layout**:

![Full Layout](https://i.imgur.com/X2QKVIt.png)

-  **Top Layer**:

![Top Layer](https://i.imgur.com/KXxkCbZ.png)

-  **Bottom Layer**:

![Bottom Layer](https://i.imgur.com/Nd1TilR.png)

## 3D Model

![3D Model](https://i.imgur.com/HvLU5jp.png)

![3D Model](https://i.imgur.com/VzZx1rp.png)

![3D Model](https://i.imgur.com/TimVGAZ.png)
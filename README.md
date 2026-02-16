# 🤖 ROS2 + Arduino Integration

This repository contains the **Arduino firmware and ROS2 nodes** to control a robot via ROS2 topics, including:

- LED control
- Sensor data acquisition
- Servo/motor control

It demonstrates how ROS2 can act as the "brain" while Arduino handles the actuators and sensors.

---

## 📌 Prerequisites

Make sure you have the following installed:

- 🐧 **Ubuntu 22.04** or later  
- 🌐 **ROS2 Humble** (or appropriate ROS2 distro)  
  👉 Installation instructions: [ROS2 Installation Guide](https://docs.ros.org/en/humble/Installation.html)  
- ⚡ **Arduino IDE** (or VS Code with Arduino extension)  
- 🔌 Arduino board connected via USB  
-  🐍 Python 3 with **pyserial** library  

---

Install **pyserial** using pip:

```bash
python3 -m pip install pyserial
```


## 🔌 Check Arduino Port and Permissions

Before running the ROS2 serial transmitter node, make sure you know which port your Arduino is connected to and that you have permission to access it.

### 1️⃣ Identify the Arduino port

Plug in your Arduino and run:

```bash
ls /dev/ttyACM*
# or
ls /dev/ttyUSB*
```

### Typical outputs: 
```bash
/dev/ttyACM0 
/dev/ttyUSB0
```
> ⚠️ Tip: The exact name depends on your board and USB port.

### 2️⃣ Check permissions

You may need access to the serial port. Test with:
```bash
ls -l /dev/ttyACM0
```

Example output:
```bash
crw-rw---- 1 root dialout 166, 0 Feb 16 12:00 /dev/ttyACM0
```

- The group is usually ```dialout ```
- If your user is not in the group, add it: 

```bash
sudo usermod -aG dialout $USER
```

## ⚙️ Installation 🚀

### 1️⃣ Create a ROS2 workspace
```bash
mkdir -p ~/arduino_ws
cd ~/arduino_ws
```

### 2️⃣ Clone the repository 📥
```bash
git clone https://github.com/rahul-r-joshua/ros2-arduino-robot.git .
```

### 3️⃣ Build the workspace 🏗️
```bash
colcon build --symlink-install
```

## 4️⃣ Source the workspace ⚡

```bash
source install/setup.bash
```

----- 

## 5️⃣ Make Python scripts executable 🛠️
Navigate to the firmware folder:
```bash
cd ~/arduino_ws/src/arduino_firmware/arduino_firmware

chmod +x *.py
```

----- 


# 💡 ROS2 + Arduino LED Blink

**Setup:** ROS2 publishes commands to a topic  
**Arduino:** Subscribes to the topic  
**Action:** Turn an LED ON/OFF based on messages from ROS2  
**Purpose:** Demonstrates ROS2 sending control commands to Arduino

## Upload the Arduino Code

Navigate to the Arduino sketch folder:

```bash
cd ~/arduino_ws/src/arduino_firmware/firmware/simple_serial_receiver
```
1. Open the sketch in Arduino IDE (or VS Code with Arduino extension).

2. Select the correct board and port in the IDE.

3. Click Upload to flash the code to the Arduino.

> ⚠️ **Warning:** After uploading the code, close the Arduino IDE before running the ROS2 terminal commands. (The Arduino IDE may lock the serial port.)


## Wiring / LED Selection

You can connect an external LED to **Digital Pin 5**, or use the **built-in LED**.

If you use an external LED:

- Connect LED **+** to **D5** through a 150Ω resistor.
- Connect LED **-** to **GND**.

---


## 🖥️ Terminal 1

```bash
source install/setup.bash

# Run the Arduino firmware (adjust port if needed)
ros2 run arduino_firmware simple_serial_transmitter --ros-args -p port:=/dev/ttyACM1
```

## 🖥️ Terminal 2

```bash
ros2 topic list

ros2 topic pub /serial_transmitter std_msgs/msg/String "data: '0'"

ros2 topic pub /serial_transmitter std_msgs/msg/String "data: '1'"
```

### LED Commands

- `0` → LED OFF
- `1` → LED ON  

--- 
# 🌡️ ROS2 + Arduino Sensor Data (Simulated)

**Setup:** Arduino generates (mimics) analog sensor data  
**Arduino:** Publishes data to ROS2 via micro-ROS  
**Action:** ROS2 subscribes to the topic and displays the simulated sensor data  
**Purpose:** Demonstrates Arduino sending data to ROS2 for processing without a physical sensor  

## Upload the Arduino Code

The Arduino firmware generates simulated analog values (e.g., 0–1023) and publishes them to ROS2.

Navigate to the Arduino sketch folder:

```bash
cd ~/arduino_ws/src/arduino_firmware/firmware/simple_serial_trasmitter
```

1. Open the sketch in Arduino IDE (or VS Code with Arduino extension).

2. Select the correct board and port in the IDE.

3. Click Upload to flash the code to the Arduino.

> ⚠️ **Warning:** After uploading the code, close the Arduino IDE before running the ROS2 terminal commands. (The Arduino IDE may lock the serial port.)



## Wiring / Sensor Selection

You can connect a sensor such as a **temperature sensor**, **ultrasonic sensor**, or **potentiometer**.  

Example (for a simple analog sensor):

- Connect sensor **VCC** to **5V**  
- Connect sensor **GND** to **GND**  
- Connect sensor **Signal** to **A0** (Analog pin 0)  

> ⚠️ **Warning:** Make sure the sensor voltage matches the Arduino input specifications to avoid damage.

---

## 🖥️ Terminal 1

```bash
source install/setup.bash

# Run the Arduino firmware (adjust port if needed)
ros2 run arduino_firmware simple_serial_receiver --ros-args -p port:=/dev/ttyACM1
```

## 🖥️ Terminal 2

```bash
ros2 topic list

ros2 topic echo /serial_receiver 
```

# ⚙️ ROS2 + Arduino Motor Control

**Setup:** ROS2 publishes motor commands  
**Arduino:** Subscribes to commands  
**Action:** Controls motor accordingly  
**Purpose:** Demonstrates ROS2 (the “brain”) controlling actuators (Arduino + motors)  

---

## Upload the Arduino Code

Navigate to the Arduino sketch folder:

```bash
cd ~/arduino_ws/src/arduino_firmware/firmware/simple_servo_control
```


1. Open the sketch in Arduino IDE (or VS Code with Arduino extension).

2. Select the correct board and port in the IDE.

3. Click Upload to flash the code to the Arduino.

> ⚠️ **Warning:** After uploading the code, close the Arduino IDE before running the ROS2 terminal commands. (The Arduino IDE may lock the serial port.)



## Wiring / Servo Selection

You can connect a standard servo to your Arduino to control its angle via ROS2.  

Example wiring for a single servo:

- Connect servo **VCC** (usually red wire) to **5V**  
- Connect servo **GND** (usually black/brown wire) to **GND**  
- Connect servo **Signal** (usually yellow/white wire) to **Digital Pin 9**  

> ⚠️ **Warning:** Make sure the servo voltage matches the Arduino specifications. Do not exceed 5V for typical hobby servos. 

> ⚠️ Avoid drawing too much current from the Arduino; use an external power supply for multiple or high-torque servos.


---

## 🖥️ Terminal 1

```bash
source install/setup.bash

# Run the Arduino firmware (adjust port if needed)
ros2 run arduino_firmware simple_serial_transmitter --ros-args -p port:=/dev/ttyACM1
```

## 🖥️ Terminal 2

```bash
ros2 topic list

#To see the topic 
ros2 topic echo /serial_transmitter 
```

## Terminal 3: send servo angles (0 to 180)

``` bash
ros2 topic pub /serial_transmitter std_msgs/msg/String "data: '0'"

ros2 topic pub /serial_transmitter std_msgs/msg/String "data: '90'"

ros2 topic pub /serial_transmitter std_msgs/msg/String "data: '180'"
```
### Servo Angle Commands

- `0` → Move servo to 0°
- `90` → Move servo to 90°
- `180` → Move servo to 180°

## 🛠️ Troubleshooting & Common Issues

If you encounter errors while connecting your ROS2 nodes to the Arduino hardware, refer to the table below for quick fixes.

| Issue | Solution |
| :--- | :--- |
| **`SerialException: [Errno 13] Permission denied`** | Your user lacks access to the serial port. Run `sudo usermod -aG dialout $USER` and **restart your PC** (or log out and back in) to apply changes. |
| **`SerialException: Port busy`** | The serial port is locked by another process. Ensure the **Arduino IDE Serial Monitor**, Plotter, or any other terminal instance is closed. |
| **Data looks like gibberish / Strange characters** | This is usually a **Baud Rate mismatch**. Ensure `Serial.begin(115200);` in your Arduino `.ino` file matches the baud rate defined in your ROS2 Python/C++ node. |
| **`Package 'arduino_firmware' not found`** | Your current terminal environment doesn't know where the workspace is. Run `source install/setup.bash` in every new terminal tab you open. |

---

# 🔄 Bi-Directional Communication Summary

By combining these two nodes, you establish a complete control loop between your high-level ROS2 environment and your low-level hardware.

### 1. ROS2 Transmitter → Arduino Receiver
* **Direction:** PC to Microcontroller.
* **Function:** ROS2 acts as the **Controller**.
* **Use Cases:** * Toggling LEDs.
    * Moving Servos to specific angles.
    * Driving DC Motors or Stepper motors.
    * Activating Relays.

### 2. ROS2 Receiver ← Arduino Transmitter
* **Direction:** Microcontroller to PC.
* **Function:** ROS2 acts as the **Monitor/Brain**.
* **Use Cases:**
    * Reading Potentiometer or Analog values.
    * Monitoring Temperature/Humidity sensors.
    * Getting Ultrasonic distance data.
    * Receiving Encoder feedback for closed-loop control.

---

## 🚀 Conclusion

With this setup, you now have the power to **control physical devices** and **process real-world data** directly from the ROS2 ecosystem. This architecture allows you to run heavy computations (like Computer Vision or Navigation) on your PC while the Arduino handles the precise timing of hardware interactions.

**Happy Hacking!** 🤖🔌

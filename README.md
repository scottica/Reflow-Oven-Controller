# Smart Reflow Oven Controller

## 📌 Overview
An automated, closed-loop Reflow Oven Controller engineered using CV-8052 Assembly on the Intel DE10-Lite/DE1-SoC FPGA platform. This system converts a standard 1500W toaster oven into a precision PCB manufacturing tool using a solid-state relay (SSR). Beyond the core requirements, this system features a heavily expanded feature set including a custom UI, active cooling mechanics, and optical safety interlocks.

## ✨ Key Features & Expansions
* **Precision Thermal Control:** Reads real-time temperatures from a K-type thermocouple (with cold junction compensation) and uses Pulse Width Modulation (PWM) to regulate the SSR, guiding the oven through distinct Ramp-to-Soak, Soak, Ramp-to-Reflow, and Cooling states.
* **Custom UI & Options Menu:** Engineered a robust, multi-level options menu displayed on a 16x2 LCD. Users can select between pre-programmed thermal profiles or define custom parameters (time and temperature) for the soak and reflow stages. 
* **Live Telemetry & Plotting:** Streams the current oven temperature via RS-232 serial to a host PC at 1Hz. I designed the PC-side strip chart to dynamically change plot colors based on thermal danger zones (<50°C Green, >50°C Orange, >180°C Red) and overlay the target reference temperature against the live oven data.
* **Active Cooling System:** During the "Cooling" state, a PWM-controlled servo motor physically opens the oven door while an integrated fan actively exhausts hot air to ensure rapid, controlled PCB cooling.
* **Optical Safety Interlocks (IR Sensor):** Features a "Smart Mode" utilizing an infrared sensor. The cycle can automatically start when the door is closed. Crucially, if the IR sensor detects the door opening during an active reflow cycle, it immediately triggers an error flag and aborts the process to prevent thermal hazards or ruined PCBs.

## ⚙️ Technical Implementation
* **Language:** CV-8052 Assembly
* **Hardware:** Intel FPGA (DE10-Lite), 1500W Toaster Oven, Solid State Relay (SSR), K-Type Thermocouple, IR Sensor, Servo Motor, 12V DC Fan.
* **Architecture:** Utilizes multiple hardware timers (`Timer 0` for the 1ms tick/system state, `Timer 1` for 10ms PWM generation, and `Timer 2` for the 115200-baud serial port). The code relies on an extensive flag system (`dbit`) to manage the state machine and handle asynchronous hardware events like button debouncing and sensor trips.

## 🚀 Usage & UI Navigation
1. **Boot:** The LCD displays the current temperature (switchable between °C, °F, and K).
2. **Profile Selection:** Use the `OPTIONS` menu to cycle between `Profile 1`, `Profile 2`, or `Custom Profile`.
3. **Smart Mode:** Toggle Smart Mode via the options menu to enable the IR-sensor auto-start and safety interlocks.
4. **Execution:** Press `Start`. The LCD will display a custom progress bar (`###`) and the active state (e.g., "Ramp-to-Soak"). The RGB LEDs will change color to indicate the current phase.
5. **Safety:** If the oven fails to reach 50°C within 60 seconds or the door is opened (in Smart Mode), the system halts and the speaker sounds a 10-beep error alarm.

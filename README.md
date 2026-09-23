# Smart Reflow Oven Controller

## 📌 Overview
A closed-loop reflow oven controller written in CV-8052 Assembly on an Intel DE10-Lite FPGA soft processor. It turns a standard 1500W toaster oven into a PCB reflow tool by switching the oven through a solid-state relay (SSR), and adds an options menu, active cooling, and an IR door-safety interlock. Built as a team project.

## ✨ Key Features
* **Thermal Control:** Reads oven temperature from a K-type thermocouple with cold-junction compensation and guides the oven through Ramp-to-Soak, Soak, Ramp-to-Reflow, Reflow, and Cooling states. The oven heater is switched on and off through the SSR, and a PWM-controlled fan holds temperature near the soak and reflow setpoints.
* **Validated Accuracy:** Readings were validated against a lab multimeter from 25–240°C, staying within the ±3°C requirement. An initial ~10°C error was traced, by testing each sensing stage in isolation, to an undervoltage supply skewing the LM335 cold-junction reading.
* **Analog Front-End:** OP07 difference amplifier (300x gain) for the thermocouple, an LM335 for cold-junction reference, and an LMC7660 charge pump for the negative supply rail.
* **Options Menu:** A multi-level menu on a 16x2 LCD to select preset profiles or set custom soak and reflow times and temperatures.
* **Live Telemetry:** Sends the oven temperature over UART (115200 baud, via USB-serial) once per second for PC-side strip-chart plotting.
* **Active Cooling:** In the Cooling state, a PWM-driven servo opens the oven door while the fan exhausts hot air.
* **Safety:** Aborts if the oven doesn't reach 50°C within 60 seconds. In Smart Mode, an IR sensor starts the cycle when the door closes and aborts if the door opens mid-cycle, with a 10-beep error alarm.

## ⚙️ Technical Implementation
* **Language:** CV-8052 Assembly
* **Hardware:** Intel DE10-Lite, 1500W toaster oven, SSR, K-type thermocouple, OP07, LM335, LMC7660, IR sensor, servo, 12V DC fan
* **Timers:** `Timer 0` drives the one-second system tick and the speaker tone, `Timer 1` generates the 100 Hz fan and servo PWM, and `Timer 2` generates the serial baud rate. State and asynchronous events are tracked with bit flags (`dbit`).

## 🚀 Usage
1. **Boot:** The LCD shows the current temperature (switchable between °C, °F, and K).
2. **Profile Selection:** Use the `OPTIONS` menu to choose `Profile 1`, `Profile 2`, or `Custom Profile`.
3. **Smart Mode:** Toggle Smart Mode in the options menu to enable the IR auto-start and door interlock.
4. **Run:** Press `Start`. The LCD shows the runtime, the active state, or a progress bar, and RGB LEDs indicate the current phase.
5. **Safety:** On an error, the system switches to cooling and sounds a 10-beep alarm.

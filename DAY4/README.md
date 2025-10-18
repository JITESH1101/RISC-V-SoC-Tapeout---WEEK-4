# DAY4

##  Introduction to Noise Margin 

The fundamental characteristics of a digital inverter and the concept of noise margin were introduced.

* **Ideal vs. Realistic Inverter:**
    * An ideal inverter's behavior was first considered. Its Voltage Transfer Characteristic (VTC), which plots Output Voltage ($V_{out}$) against Input Voltage ($V_{in}$), would theoretically have an **infinite slope**. This means the transition from a high output to a low output would be instantaneous and occur precisely at a switching threshold of $V_{dd}/2$.
    * In contrast, a realistic inverter was shown to have a **finite slope**. This indicates that the transition between logic states is not instantaneous but occurs over a range of input voltages.

* **Defining Logic Levels as Voltage Ranges:**
    * It was established that logic levels are not represented by single, exact voltage values but by a range of voltages.
    * Specifically, the **Output Low Voltage ($V_{OL}$)** was defined. Any output voltage level that falls between 0 volts and the specified $V_{OL}$ value is interpreted by a subsequent logic gate as a valid **logic '0'**. This range provides a buffer, or margin, for the logic level against voltage fluctuations.

***

<img width="3548" height="2034" alt="Screenshot from 2025-10-17 19-57-18" src="https://github.com/user-attachments/assets/3e556538-e5e1-43b6-aa4a-e44d816513e0" />

##  Noise Margin Voltage Parameters 

To properly quantify the robustness of an inverter, a set of key voltage parameters from its actual Input/Output (I/O) characteristic curve were defined.

* **Input and Output Voltage Thresholds:**
    * **$V_{OH}$ (Output High Voltage):** This is the nominal voltage produced by the inverter at its output when representing a logic '1'. It is the maximum output voltage when the input is low.
    * **$V_{OL}$ (Output Low Voltage):** This is the nominal voltage produced by the inverter at its output when representing a logic '0'. It is the minimum output voltage when the input is high.
    * **$V_{IL}$ (Input Low Voltage):** This is the maximum input voltage that the inverter will reliably interpret as a logic '0'.
    * **$V_{IH}$ (Input High Voltage):** This is the minimum input voltage that the inverter will reliably interpret as a logic '1'.

* **Determining $V_{IL}$ and $V_{IH}$ from the VTC:**
    * A critical point was made about how $V_{IL}$ and $V_{IH}$ are precisely determined from the VTC graph. They are not arbitrary points but are defined as the specific input voltages where the gain of the inverter, represented by the slope of the curve ($dV_{out}/dV_{in}$), is equal to **-1**. These points mark the boundaries of the high-gain transition region.

***

<img width="3548" height="2034" alt="Screenshot from 2025-10-17 20-01-08" src="https://github.com/user-attachments/assets/202d7a74-a39f-4d1e-b699-b35e3e718b18" />


##  Noise Margin Equation and Summary 

The previously defined voltage parameters were used to formally derive the equations for noise margin and to summarize its practical importance.

* **Noise Margin Equations:**
    * The noise margin is split into two components, one for each logic level, to quantify how much noise the gate can withstand before its output is compromised.
    * **Noise Margin Low ($NM_L$):** This is the tolerance for noise on a low input signal. It was defined by the equation:
        $$NM_L = V_{IL} - V_{OL}$$
    * **Noise Margin High ($NM_H$):** This is the tolerance for noise on a high input signal. It was defined by the equation:
        $$NM_H = V_{OH} - V_{IH}$$

* **The Undefined Region:**
    * The voltage range on the input between $V_{IL}$ and $V_{IH}$ was designated as the **Undefined Region**. Any input signal that falls into this region does not correspond to a valid logic '0' or logic '1', and the gate's output level becomes indefinite or unpredictable.

* **Noise Immunity in Practice:**
    * A summary graph was presented showing how noise-induced voltage "bumps" or spikes on a signal are handled. This illustrated the real-world impact of noise margins.
    * **(a)** If a noise spike on a logic '0' signal causes the voltage to rise but remain below $V_{IL}$, it is still considered a valid logic '0', and the system operates correctly. The noise is successfully rejected.
    * **(b)** If the noise spike is larger and causes the input voltage to enter the undefined region (between $V_{IL}$ and $V_{IH}$), the output of the gate becomes undefined, potentially leading to errors.
    * **(c)** If the noise spike is very large and causes the input voltage to cross above $V_{IH}$, it will be misinterpreted as a logic '1', causing a logic failure.
    * The key takeaway was that for a signal to be reliably considered a logic '0' or logic '1', any noise present must not cause the voltage to exceed the bounds defined by the $NM_L$ and $NM_H$ ranges, respectively.

***

<img width="3548" height="2034" alt="Screenshot from 2025-10-17 20-02-39" src="https://github.com/user-attachments/assets/5d76c5f0-e8e2-42bd-bec6-6d4f9d69cd65" />


##  Noise Margin Variation with Respect to PMOS Width 

The static behavior and robustness of a CMOS inverter were evaluated by observing how its noise margins change when the relative sizes of the NMOS and PMOS transistors are varied.

* **Experimental Setup:**
    * An analysis was performed where the PMOS transistor's width-to-length ratio ($W_p/L_p$) was kept constant, while the NMOS transistor's ratio ($W_n/L_n$) was progressively increased. The NMOS width was scaled from its base value (`1x`) up to five times its base value (`5x`).

* **Observed Results from Tabulated Data:**
    * The simulation results showed a clear trade-off between the high and low noise margins as the NMOS transistor was made stronger relative to the PMOS.
    * **$NM_H$ Trend:** As the `x.Wn/Ln` value increased, the Noise Margin High ($NM_H$) was observed to improve, increasing from **0.3V** to **0.42V**.
    * **$NM_L$ Trend:** Conversely, the Noise Margin Low ($NM_L$) was observed to degrade, decreasing from **0.3V** to **0.27V**.
    * **Switching Threshold ($V_m$) Shift:** The switching threshold voltage ($V_m$) also shifted significantly. As the NMOS became stronger, it pulled the switching point higher, increasing $V_m$ from **0.99V** to **1.4V**.

* **Graphical Confirmation:**
    * The VTC for the specific case where the NMOS was five times its base width (`5Wn/Ln`) was plotted. This graph visually confirmed the results from the table, showing the calculated noise margins of $NM_H = 0.42$ and $NM_L = 0.27$ for that particular transistor sizing.

***

## Sky130 Noise Margin Lab

* Just as previous labs , we use ngspice and source the file starting with day4 in designs to start noise margin calculation as shown below

<img width="3470" height="2121" alt="noise_margin" src="https://github.com/user-attachments/assets/be838269-7600-46c1-88ca-c8aedfee1311" />

Now the plot is as follows

<img width="3979" height="2500" alt="noise_margin_plot" src="https://github.com/user-attachments/assets/e21505af-70d2-4642-89d9-6a313e7964de" />

By clicking on the slope of logic 1 and logic 0 and we get VOH,VIL,VIH,VOL, so that we can calculate noise margin

<img width="3979" height="2500" alt="noise_margin_calculation" src="https://github.com/user-attachments/assets/44e39603-ae7a-471f-ab25-2383eb2da1d7" />


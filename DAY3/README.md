# DAY3


---

## SPICE Deck Creation for CMOS Inverter

* An understanding of the fundamental components of a SPICE (Simulation Program with Integrated Circuit Emphasis) deck was gained. A SPICE deck is essentially a text-based description of a circuit that the simulator can understand and analyze.
* The creation of a SPICE deck was found to involve three primary steps:
    1.  **Component Connectivity:** Defining how different components are connected to each other.
    2.  **Component Values:** Assigning specific values or models to these components, such as transistor dimensions or voltage levels.
    3.  **Identifying 'Nodes':** The connection points in the circuit are identified and named. These nodes are crucial for defining connectivity.
* The CMOS inverter circuit schematic was examined for this purpose. It consists of a PMOS transistor (M1) and an NMOS transistor (M2), along with voltage sources and a load capacitor.
* The nodes in the circuit were identified and named. For this specific inverter:
    * `in`: The input node, connected to the gates of both M1 and M2.
    * `out`: The output node, connected to the drains of both M1 and M2.
    * `vdd`: The positive supply voltage rail.
    * `0` or `vss`: The ground reference node.
* A **NETLIST** was created based on this connectivity. The netlist is a formal description of each component and its connections. The syntax for a MOSFET was observed to be: `M<name> <drain> <gate> <source> <bulk> <model_name> W=<width> L=<length>`.
    * For the PMOS transistor (M1), the connection line was written as: `M1 out in vdd vdd pmos W=0.375u L=0.25u`.
    * For the NMOS transistor (M2), the line was: `M2 out in 0 0 nmos W=0.375u L=0.25u`.
* Other components were also defined in the SPICE deck:
    * `cload out 0 10f`: A load capacitor named `cload` is connected between the `out` node and ground (`0`), with a value of 10 femtofarads ($10fF$).
    * `Vdd vdd 0 2.5`: The main power supply `Vdd` provides $2.5V$ between the `vdd` node and ground (`0`).
    * `Vin in 0 2.5`: The input voltage source `Vin` is connected between the `in` node and ground (`0`).

---

<img width="3565" height="2032" alt="Screenshot from 2025-10-15 23-52-56" src="https://github.com/user-attachments/assets/206d8e09-1650-4d9b-bfd8-a81d1187a65e" />


## SPICE Simulation for CMOS Inverter

* Once the SPICE deck was constructed, simulation commands were added to perform circuit analysis.
* The `.op` command was noted, which is used for a DC operating point analysis.
* The primary simulation command used for this analysis was the **DC Sweep**: `.dc Vin 0 2.5 0.05`. This command instructs SPICE to perform a DC analysis by sweeping the input voltage source `Vin` from $0V$ to $2.5V$ in steps of $0.05V$.
* The output of this simulation is the **Voltage Transfer Characteristic (VTC)** of the CMOS inverter. This graph plots the output voltage ($V_{out}$) on the Y-axis against the input voltage ($V_{in}$) on the X-axis.
* The resulting waveform was observed. It showed a characteristic inverted 'S' shape. When $V_{in}$ is low (near $0V$), $V_{out}$ is high (near $2.5V$). As $V_{in}$ increases, it crosses a threshold where the output rapidly switches from high to low. When $V_{in}$ is high (near $2.5V$), $V_{out}$ is low (near $0V$).
* The specific simulation was performed for a device where the transistor sizes were symmetrical:
    * Width of NMOS, $W_n = 0.375 \mu m$.
    * Width of PMOS, $W_p = 0.375 \mu m$.
    * Length of both, $L_n = L_p = 0.25 \mu m$.
    * This results in a width-to-length ratio for both transistors of $\frac{W_n}{L_n} = \frac{W_p}{L_p} = 1.5$.

---

## Lab SPICE Simulation for CMOS

* A crucial part of any accurate SPICE simulation is the inclusion of process-specific **MODEL files**. These files contain a vast number of parameters that describe the detailed electrical behavior of transistors for a particular manufacturing technology.
* In the provided SPICE deck, the following lines were used to include these models:
    ```spice
    *** MODEL Descriptions ***
    .include tsmc_025um_model.mod
    .LIB "tsmc_025um_model.mod" CMOS MODELS
    ```
* It was understood that these commands instruct the SPICE simulator to load the transistor models from the file named `tsmc_025um_model.mod`.
* The principle remains the same for any technology (like Sky130): the accuracy of the simulation is entirely dependent on the quality of the model file that represents the target silicon process.
* As we did spice simulations for day 1 and day 2 , now we will do it for Cmos logic
* Cmos - out vs in plot
  
<img width="3545" height="2323" alt="cmos_out_vs_in" src="https://github.com/user-attachments/assets/2c79a20a-f5c4-48b0-8e37-d4ed7ba996a4" />

<img width="3964" height="2507" alt="cmos_out_vs_in_plot" src="https://github.com/user-attachments/assets/b1ced426-ec81-4b8f-8b03-58ad02559b69" />

* Cmos - out vs time in plot

<img width="3545" height="2323" alt="cmos_out_vs_timein" src="https://github.com/user-attachments/assets/b16763c4-1d14-45e5-8530-fd8068cb3802" />

<img width="3981" height="2503" alt="cmos_out_vs_timein_plot" src="https://github.com/user-attachments/assets/958f243f-e5c2-4af6-817e-a236bfd6c145" />


---

## Switching Threshold, Vm

* A key parameter for evaluating the robustness of a CMOS inverter is the **Switching Threshold, $V_m$**.
* $V_m$ was defined as the specific input voltage ($V_{in}$) at which the output voltage ($V_{out}$) is equal to it. That is, the point on the VTC where **$V_{in} = V_{out}$**.
* This point can be visualized on the VTC plot by drawing a line where $Y=X$. The intersection of this line with the VTC curve gives the value of $V_m$.
* A comparative analysis was shown for two different inverter designs:
    1.  **Symmetric Design:** $W_n = W_p = 0.375 \mu m$, resulting in $\frac{W_n}{L_n} = \frac{W_p}{L_p} = 1.5$. For this design, the switching threshold was found to be approximately **$V_m \approx 0.98V$**.
    2.  **Asymmetric Design:** $W_n = 0.375 \mu m$ but $W_p = 0.9375 \mu m$, resulting in a PMOS to NMOS ratio of $\frac{W_p/L_p}{W_n/L_n} = 2.5$. For this design, the switching threshold was observed to be higher, approximately **$V_m \approx 1.2V$**.
* At the switching threshold ($V_{in} = V_m$), both the PMOS and NMOS transistors are operating in the **saturation region**.
* The fundamental condition for the circuit at $V_{in} = V_m$ was identified as:
    $$I_{dsp} = -I_{dsN} \quad \text{or} \quad I_{dsp} + I_{dsN} = 0$$

---

## Analytical Expression of Vm as a function of (W/L)p and (W/L)n

* A mathematical derivation was followed to obtain an analytical expression for $V_m$. The derivation begins with the current equality condition at the switching threshold: $I_{dsp} + I_{dsN} = 0$.
* The general Shockley equation for drain current ($I_d$) in the saturation region was recalled, ignoring the channel length modulation term:
    $$I_d = \mu \cdot C_{ox} \cdot \frac{W}{L} \cdot \frac{(V_{gs} - V_t)^2}{2}$$
* By substituting the specific conditions for the PMOS and NMOS transistors at the switching point and solving for $V_m$, the following expression was derived:
    $$V_m = \frac{R \cdot V_{dd}}{1+R}$$
* Where the ratio $R$ is defined based on the device parameters and W/L ratios:
    $$R = \frac{k_p'(W/L)_p}{k_n'(W/L)_n}$$
* This formula shows that $V_m$ is fundamentally determined by the supply voltage and the ratio of the current-driving capabilities of the PMOS and NMOS transistors.

---

## Analytical Expression of (W/L)p and (W/L)n as a function of Vm

* The derivation was then approached from the opposite direction: to determine the required transistor size ratio, $\frac{(W/L)_p}{(W/L)_n}$, to achieve a *desired* switching threshold, $V_m$.
* The process was initiated again from the saturation current equality equation: $I_{dsp} = -I_{dsn}$.
* Through algebraic manipulation, the equation was rearranged to isolate the ratio of the technology constants and the W/L ratios on one side.
* The final derived expression for the required PMOS/NMOS sizing ratio was found to be:
    $$\frac{(W/L)_p}{(W/L)_n} = \frac{k_n'}{k_p'} \cdot \frac{(V_m - V_{tn})^2}{((V_m - V_{dd}) - V_{tp})^2}$$
* This formula allows a circuit designer to set a target for the switching threshold and calculate the precise W/L ratio needed to achieve it.

---

## Static and Dynamic Simulation of CMOS Inverter

* The analysis was extended from purely static behavior (the VTC) to include **dynamic behavior** (timing).
* Simulation results were presented for the case where the PMOS and NMOS sizes were equal ($\frac{W_p/L_p}{W_n/L_n} = 1$).
* **Static Result:** The switching threshold was measured from the VTC to be **$V_m = 0.99V$**.
* **Dynamic Results:** The propagation delays were measured:
    * **Rise Delay:** 148 picoseconds ($ps$).
    * **Fall Delay:** 71 picoseconds ($ps$).
* It was observed that with equal transistor sizing, the fall delay is significantly shorter than the rise delay. This is because electron mobility (NMOS) is higher than hole mobility (PMOS), making the NMOS a stronger driver for the same dimensions.

---

## Static and Dynamic Simulation of CMOS Inverter with Increased PMOS Width

* To investigate the impact of sizing on performance, a series of simulations were run where the PMOS width was progressively increased. A summary table of the results was analyzed.
* Several key trends were deduced from this data:
    * As the PMOS width increases, the **Rise Delay decreases** significantly.
    * As the PMOS width increases, the **Fall Delay increases** slightly.
    * As the PMOS width increases, the **Switching Threshold ($V_m$) increases**.
* A crucial design goal of achieving **approximately equal rise and fall delays** was highlighted. From the data, this occurs when the PMOS W/L ratio is about **twice** the NMOS W/L ratio, resulting in delays of 80ps and 76ps.

---

## Applications of CMOS Inverter in Clock Network and STA

* The practical application of the CMOS inverter, specifically as a **clock buffer**, was explored in the context of a **clock tree**.
* An **H-Tree** was shown as a common clock distribution network structure, which is built from chains of buffers (cascaded inverters).
* Symmetric rise/fall delays are critical in clock paths to maintain a consistent 50% **duty cycle** and minimize **skew**.
* The physical reason for needing a wider PMOS was explained: The ON-resistance ($R_{ON}$) of a PMOS transistor is inherently higher than that of an equally sized NMOS. For the example 0.25um tech, **$R_{ON}(PMOS) > R_{ON}(NMOS)$ by ~2.5 times**.
* The impact of inverter delays was then connected to **Static Timing Analysis (STA)**.
* A fundamental STA **setup analysis** between a launch flop and a capture flop was examined.
* **SLACK** was defined as the margin by which timing is met: **Slack = Data Required Time - Data Arrival Time**. For a design to work, the slack must be positive or zero.
* The takeaway was that the delays of the inverters and buffers, controlled by transistor sizing, are critical inputs to the STA calculation and are fundamental to designing a functional high-frequency chip (e.g., at 1 GHz, where the clock period is only 1ns).

<img width="3548" height="2034" alt="Screenshot from 2025-10-17 19-47-44" src="https://github.com/user-attachments/assets/d1a1a0b6-1820-44cd-b696-663617c3788d" />

<img width="3548" height="2034" alt="Screenshot from 2025-10-17 19-48-07" src="https://github.com/user-attachments/assets/3bf29e81-7847-4894-90f9-98d6c3a12ee1" />


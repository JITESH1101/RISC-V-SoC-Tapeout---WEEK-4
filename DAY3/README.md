# DAY3


---

## SPICE Deck Creation for CMOS Inverter

- An understanding of the fundamental components of a **SPICE (Simulation Program with Integrated Circuit Emphasis)** deck was gained. A SPICE deck is a text-based description of a circuit that the simulator uses for analysis.
- The creation of a SPICE deck was found to involve three primary steps:
    1.  **Component Connectivity:** Defining how different components are connected to each other.
    2.  **Component Values:** Assigning specific values or models to these components.
    3.  **Identifying 'Nodes':** The connection points in the circuit are identified and named.
- The CMOS inverter circuit schematic was examined, consisting of a PMOS transistor (M1), an NMOS transistor (M2), voltage sources, and a load capacitor.
- The nodes in the circuit were identified and named for creating the netlist:
    - `in`: The input node, connected to the gates of both M1 and M2.
    - `out`: The output node, connected to the drains of both M1 and M2.
    - `vdd`: The positive supply voltage rail.
    - `0` or `vss`: The ground reference node.
- A **NETLIST** was created based on this connectivity. The syntax for a MOSFET was observed as: `M<name> <drain> <gate> <source> <bulk> <model_name> W=<width> L=<length>`.
- The netlist descriptions for the circuit components were written as follows:
  ```spice
  * --- NETLIST Description ---
  M1 out in vdd vdd pmos W=0.375u L=0.25u
  M2 out in 0 0 nmos W=0.375u L=0.25u
  cload out 0 10f
  Vdd vdd 0 2.5
  Vin in 0 2.5
  ```

---

## SPICE Simulation for CMOS Inverter

- Once the SPICE deck was constructed, simulation commands were added to perform circuit analysis.
- The primary command for this analysis was the **DC Sweep**: `.dc Vin 0 2.5 0.05`.
- This command instructs SPICE to perform a DC analysis by sweeping the input voltage source `Vin` from $0V$ to $2.5V$ in steps of $0.05V$.
- The output of this simulation is the **Voltage Transfer Characteristic (VTC)** of the CMOS inverter, which plots the output voltage ($V_{out}$) versus the input voltage ($V_{in}$).
- The resulting waveform showed a characteristic inverted 'S' shape, confirming the inverting behavior of the circuit.
- The simulation was performed for a device with symmetrical transistor sizes:
    - $W_n = W_p = 0.375 \mu m$
    - $L_n = L_p = 0.25 \mu m$
    - This results in a width-to-length ratio for both transistors of $\frac{W_n}{L_n} = \frac{W_p}{L_p} = 1.5$.

---

## Lab SPICE Simulation for CMOS

- It was understood that accurate SPICE simulation requires process-specific **MODEL files**, which contain detailed electrical parameters for the transistors.
- The following commands were used in the SPICE deck to include these models from a TSMC 0.25um process technology file:
  ```spice
  *** MODEL Descriptions ***
  .include tsmc_025um_model.mod
  .LIB "tsmc_025um_model.mod" CMOS MODELS
  ```
- It was noted that simulation accuracy is entirely dependent on the quality of the model file that represents the target silicon process (e.g., Sky130 or the TSMC process in the example).

---

## Switching Threshold, Vm

- The **Switching Threshold ($V_m$)** was identified as a key parameter for evaluating inverter robustness.
- $V_m$ is defined as the input voltage ($V_{in}$) at which the output voltage ($V_{out}$) is equal to it. On the VTC plot, this is the intersection of the curve with a $V_{out} = V_{in}$ line.
- A comparative analysis was shown for two different inverter designs:
    1.  **Symmetric Design:** $(\frac{W_n}{L_n}=\frac{W_p}{L_p} = 1.5)$. The switching threshold was found to be **$V_m \approx 0.98V$**.
    2.  **Asymmetric Design:** $(\frac{W_p/L_p}{W_n/L_n} = 2.5)$. The switching threshold was higher, **$V_m \approx 1.2V$**.
- At the switching threshold ($V_{in} = V_m$), both the PMOS and NMOS transistors operate in the **saturation region**.
- The fundamental condition at this point is that the drain current of the PMOS equals the drain current of the NMOS:
  $$I_{dsp} = -I_{dsN} \quad \implies \quad I_{dsp} + I_{dsN} = 0$$

---

## Analytical Expression of Vm as a function of (W/L)p and (W/L)n

- A mathematical derivation for $V_m$ was followed, starting from the current equality condition ($I_{dsp} + I_{dsN} = 0$) with both transistors in saturation.
- By equating the saturation current equations for the PMOS and NMOS transistors and solving for $V_m$, a simplified expression was derived:
  
  $$V_m = \frac{R \cdot V_{dd}}{1+R}$$
  
- The term $R$ represents the ratio of the current-driving capabilities of the PMOS and NMOS transistors, which is dependent on process parameters and their W/L ratios:
  $$R \approx \frac{k_p'(W/L)_p}{k_n'(W/L)_n}$$
- This formula demonstrates that $V_m$ is determined by the supply voltage and the relative strengths of the pull-up (PMOS) and pull-down (NMOS) transistors.

---

## Analytical Expression of (W/L)p and (W/L)n as a function of Vm

- The derivation was then reversed to determine the required transistor size ratio, $\frac{(W/L)_p}{(W/L)_n}$, needed to achieve a *desired* switching threshold, $V_m$.
- By rearranging the saturation current equality equation, the following expression was derived:
  $$\frac{(W/L)_p}{(W/L)_n} = \frac{k_n'}{k_p'} \cdot \frac{(V_m - V_{tn})^2}{((V_m - V_{dd}) - V_{tp})^2}$$
- This equation is crucial for circuit designers, as it allows them to calculate the precise sizing ratio needed to place $V_m$ at a specific point (e.g., $V_{dd}/2$ for optimal noise margins).

---

## Static and Dynamic Simulation of CMOS Inverter

- The analysis was extended from static (VTC) to **dynamic (timing)** behavior.
- For a case with equal transistor sizing ($\frac{W_p/L_p}{W_n/L_n} = 1$), the following results were observed:
    - **Static Result:** $V_m = 0.99V$.
    - **Dynamic Results:**
        - **Rise Delay:** 148 ps
        - **Fall Delay:** 71 ps
- It was concluded that with equal sizing, the **fall delay is significantly shorter than the rise delay**. This imbalance is due to the higher mobility of electrons (in NMOS) compared to holes (in PMOS), making the NMOS transistor "stronger."

---

## Static and Dynamic Simulation of CMOS Inverter with Increased PMOS Width

- To balance the delays, a series of simulations were run with a progressively wider PMOS transistor. The results were summarized in a table.
- **Key Trends Observed:**
    - As PMOS width increases, the **Rise Delay decreases** (stronger pull-up).
    - As PMOS width increases, the **Fall Delay increases slightly** (more capacitive load).
    - As PMOS width increases, the **Switching Threshold ($V_m$) increases**.
- The optimal point for **approximately equal rise and fall delays** was found when the PMOS W/L ratio was about **twice** the NMOS W/L ratio, yielding delays of 80ps (rise) and 76ps (fall). This is a common design target for clock buffers.

---

## Applications of CMOS Inverter in Clock Network and STA

- The CMOS inverter is a fundamental building block for **clock buffers** used in **clock distribution networks** like an H-Tree.
- Maintaining symmetric rise/fall delays in these buffers is critical to preserve the **duty cycle** of the clock signal as it propagates.
- The physical reason for this is that the ON-resistance of a PMOS is inherently higher than an NMOS of the same size ($R_{ON}(PMOS) > R_{ON}(NMOS)$ by ~2.5x in the example process). The PMOS must be made wider to compensate.
- The delays of these buffers are critical inputs for **Static Timing Analysis (STA)**.
- A basic STA setup analysis was examined:
    - **Data Arrival Time:** Time for data to travel from a launch flip-flop to a capture flip-flop.
    - **Data Required Time:** The deadline for the data to arrive at the capture flip-flop.
    - **SLACK = Data Required Time - Data Arrival Time**. Slack must be positive for the circuit to operate correctly at the specified frequency (e.g., 1 GHz).
- This final step connected the low-level transistor sizing decisions directly to the high-level performance and correctness of a digital integrated circuit.










































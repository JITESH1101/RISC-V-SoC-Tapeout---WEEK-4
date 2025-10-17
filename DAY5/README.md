# DAY5

# RISC-V SoC Tapeout Program: Learning Documentation

An analysis of CMOS inverter robustness, focusing on the impact of power supply and device variations. All simulations and concepts were studied as part of the course.

---

## 1. Smart SPICE simulation for power supply variations

* An evaluation of the static behavior and robustness of a CMOS inverter was conducted by analyzing the effects of power supply scaling. The primary tool used for this analysis was a SPICE simulation.
* To perform this analysis efficiently, a "smart" SPICE deck was constructed. Instead of writing separate simulations for each voltage, a control loop was implemented to automate the process.
* The circuit netlist was first defined. This included an NMOS transistor (`M2`) and a PMOS transistor (`M1`) configured as a standard inverter.
    * The PMOS transistor (`M1`) was defined with a width of `$W_p = 0.9375\mu m$` and a length of `$L = 0.25\mu m$`.
    * The NMOS transistor (`M2`) was defined with a width of `$W_n = 0.375\mu m$` and a length of `$L = 0.25\mu m$`.
    * A load capacitance of `$10fF$` was attached to the output node `out` to model a realistic load.
    * The simulation was based on the `$tsmc\_025um\_model.mod$` technology file.
* A `.control` block was used to program the simulation steps.
    * A variable `powerSupply` was initialized to `$2.5V$`.
    * A `dowhile` loop was created to run five times. In each iteration, a DC sweep analysis was performed by sweeping the input voltage `Vin` from `$0V$` to `$2.5V$` in `$0.01V$` increments.
    * After each DC sweep, the `powerSupply` variable was reduced by `$0.5V$`, and this new value was applied to the circuit's supply voltage `Vdd` using the `alter` command. This allowed for the characterization of the inverter at supply voltages of `$2.5V, 2.0V, 1.5V, 1.0V,` and `$0.5V$`.
* The results of all five DC analyses were plotted on a single graph. The `plot` command was used to display the output voltage (`dc1.out`, `dc2.out`, etc.) versus the input voltage. This generated a family of Voltage Transfer Characteristic (VTC) curves, with each curve corresponding to a different supply voltage.
* The resulting graph clearly showed that as the supply voltage `$V_{dd}$` was scaled down from `$2.5V$`, the VTC curve shifted and its transition region became steeper.

![VTC curves for different supply voltages](https://i.imgur.com/8QG9s0j.jpg)

---

## 2. Advantages and disadvantages using low supply voltage

* The results from the power supply scaling simulation were analyzed to understand the trade-offs of operating at a lower supply voltage. Two primary advantages and one major disadvantage were identified.
* **Advantage 1: Increase in Gain**
    * It was observed that as the supply voltage was lowered, the transition region of the VTC became significantly steeper.
    * A steeper transition region corresponds to a higher voltage gain for the inverter. An example calculation showed that the absolute gain `$|gain|$` reached `$11.53$`, which represented a `$56\%$` improvement. This increased gain contributes to better noise immunity and a more robust digital signal.
* **Advantage 2: Significant Reduction in Energy**
    * The most compelling advantage of lowering the supply voltage is the dramatic reduction in power consumption.
    * The dynamic energy consumed by a CMOS circuit is governed by the formula `$E = \frac{1}{2} C V^2$`, where `$C$` is the load capacitance and `$V$` is the supply voltage.
    * Because the energy is proportional to the *square* of the voltage, even a small decrease in `$V_{dd}$` yields a large energy saving. For instance, reducing the supply voltage to `$0.5V$` was shown to result in a `$96\%$` reduction in energy consumption, which is a critical improvement for mobile and power-constrained devices.
* **Disadvantage 1: Performance Impact**
    * The primary drawback of supply voltage scaling is the negative impact on circuit performance, specifically its speed.
    * Lowering the supply voltage reduces the drive current of the transistors, which means it takes longer to charge and discharge the load capacitance.
    * This leads to increased propagation delays. An example measurement showed a `rise delay = 66ps` and a `fall delay = 78ps` at the lower voltage, indicating that the inverter has become slower. This topic was noted to be a central part of dynamic circuit analysis.

![Summary of low supply voltage trade-offs](https://i.imgur.com/u7sU00z.jpg)

---

## 3. Sky130 Supply Variation Labs

* The theoretical principles understood from the generic SPICE simulation were intended to be applied in a practical lab environment using the open-source **Sky130 Process Design Kit (PDK)**.
* The objective of such a lab would be to characterize a standard cell inverter from the Sky130 library under different supply voltage conditions.
* This involves performing a similar set of DC sweep simulations, but using the specific model files and device parameters provided by the Sky130 PDK.
* By running these simulations, key performance metrics such as voltage gain (`$A_v$`), switching threshold (`$V_m$`), and noise margins (`$NM_H, NM_L$`) would be extracted for the Sky130 inverter at its nominal voltage (`$1.8V$`) and at lower, scaled voltages.
* The results would provide a practical understanding of how the real-world standard cells used in an SoC tapeout respond to power supply variations, confirming the trends observed in the initial, more generic simulation.

---

## 4. Sources of variation – Etching process

* An investigation into the physical sources of variation in CMOS manufacturing was conducted, focusing first on the **etching process**.
* A CMOS inverter is constructed from PMOS and NMOS transistors. The critical component defining the transistor's channel is the polysilicon gate. The layout of this gate is defined by a photolithographic mask.
* In an ideal world, the etching process would perfectly transfer the rectangular shape from the **Ideal Mask** to the physical polysilicon on the wafer.
* However, in reality, the chemical etching process is imperfect. This results in an **Actual Mask** that is irregularly shaped, with rough and uneven edges. This imperfection alters the effective physical dimensions of the gate, specifically its **width (`$W$`)** and **length (`$L$`)**.
* This physical variation has a direct impact on the transistor's electrical characteristics. The drain current in the linear region is described by the equation:
    $$I_d = \mu C_{ox} \left(\frac{W}{L}\right) \left[(V_{gs} - V_t)V_{ds} - \frac{V_{ds}^2}{2}\right]$$
* As seen in the formula, the drain current `$I_d$` is directly proportional to the `$W/L$` ratio. Because the etching process introduces random variations into the physical `$W$` and `$L$`, the resulting drain current will vary from transistor to transistor across a single chip and from wafer to wafer.
* This effect was also considered in the context of an **inverter chain**. It was noted that gates in the middle of a long, uniform chain are surrounded by identical structures, which can lead to a more predictable etching environment. Gates at the beginning or end of the chain, however, have different surroundings and may experience more pronounced and systematic variations.

![Ideal vs. actual etching process](https://i.imgur.com/oD4jCjU.jpg)

---

## 5. Sources of variation – oxide thickness

* Another critical source of manufacturing variation that was examined is the **thickness of the gate oxide (`$t_{ox}$`)**.
* The gate oxide is an extremely thin layer of insulating material (typically silicon dioxide) that separates the polysilicon gate from the silicon substrate. Its thickness is a critical parameter that controls the transistor's behavior.
* An **Ideal Oxidation Process** would grow a perfectly uniform layer of oxide with the same thickness everywhere.
* In practice, the **Actual Oxidation Process** is not perfect, leading to variations in the gate oxide thickness across the transistor. The surface can be rough and the thickness non-uniform.
* This physical variation in `$t_{ox}$` directly affects the gate oxide capacitance per unit area, `$C_{ox}$`, since `$C_{ox} = \frac{\epsilon_{ox}}{t_{ox}}$`, where `$\epsilon_{ox}$` is the permittivity of the oxide.
* The impact of this variation is understood by looking at the drain current equation, expressed to show the dependence on `$t_{ox}$`:
    $$I_d = \mu \frac{\epsilon_{ox}}{t_{ox}} \left(\frac{W}{L}\right) \left[(V_{gs} - V_t)V_{ds} - \frac{V_{ds}^2}{2}\right]$$
* As shown, the drain current `$I_d$` is inversely proportional to the oxide thickness `$t_{ox}$`. This means that even if the `$W/L$` ratio is perfect, random variations in `$t_{ox}$` will cause the drive strength of transistors to vary, leading to unpredictable circuit performance.

![Variation in gate oxide thickness](https://i.imgur.com/n6iG9uB.jpg)

---

## 6. Smart SPICE simulation for device variations

* To model the real-world impact of the physical variations described (like etching and oxide thickness), a SPICE simulation for **device variation** was performed.
* This simulation evaluates the inverter's robustness by considering extreme manufacturing corners, which represent the bounds of expected process variations. Two primary corners were simulated:
    * **Strong PMOS – Weak NMOS:** This scenario represents a process variation that results in a PMOS transistor that is stronger than nominal and an NMOS transistor that is weaker. This was simulated by significantly increasing the PMOS width (`$W_p = 1.875u$`) and decreasing the NMOS width (`$W_n = 0.375u$`).
    * **Weak PMOS – Strong NMOS:** This is the opposite corner, where the PMOS is weaker and the NMOS is stronger. This was simulated by swapping the widths (`$W_p = 0.375u$`, `$W_n = 1.875u$`).
* Similar to the power supply analysis, a series of DC sweeps were run to generate a family of five VTC curves. These curves represent the two extreme corners and several intermediate points, illustrating the full range of static behavior expected due to process variations.

![VTC plots for different device corners](https://i.imgur.com/X4uN0sL.jpg)

---

## 7. Conclusion

* The results of the device variation simulation led to several key conclusions about the impact of manufacturing variability on CMOS inverter robustness.
* **Shift in Switching Threshold (`$V_m$`):**
    * The most prominent effect observed in the VTC plot was a significant horizontal **shift in the switching threshold (`$V_m$`)**. The switching threshold is the input voltage at which the output voltage is equal to the input voltage.
    * In the case of a Strong PMOS / Weak NMOS, the PMOS overpowers the NMOS, causing the transition to happen at a higher input voltage (the curve shifts to the right).
    * Conversely, for a Weak PMOS / Strong NMOS, the NMOS dominates, and the transition occurs at a lower input voltage (the curve shifts to the left).
* **Variation in Noise Margins (`$NM_H$` and `$NM_L$`):**
    * This shift in the VTC directly impacts the circuit's **noise margins**, which are a critical measure of its robustness.
    * **Variation in Noise Margin Low (`$NM_L$`)**: The range of acceptable "low" input voltage changes. This variation, visible at the bottom of the VTC plot, indicates that the circuit's ability to tolerate noise when the input is low is inconsistent across different process corners.
    * **Variation in Noise Margin High (`$NM_H$`)**: Similarly, the range of acceptable "high" input voltage changes. This variation, visible at the top of the VTC plot, affects the circuit's tolerance to noise on high signals.
* In summary, it was concluded that physical manufacturing variations are not just a theoretical concern; they directly degrade circuit robustness by altering the switching point and reducing the noise margins, which can lead to functional failures in a digital system.

![VTC plot showing variations in Vm and Noise Margins](https://i.imgur.com/Y82hFfN.jpg)

---

## 8. Sky130 Device Variation Labs

* The final practical exercise was to apply the understanding of device variation to the **Sky130 PDK**.
* This lab would involve using the specific process corner model files provided with the Sky130 library. These libraries typically include models for various corners, such as:
    * **FF (Fast-Fast):** Both PMOS and NMOS are faster (stronger) than typical.
    * **SS (Slow-Slow):** Both PMOS and NMOS are slower (weaker) than typical.
    * **SF (Slow-Fast):** PMOS is slow, NMOS is fast.
    * **FS (Fast-Slow):** PMOS is fast, NMOS is slow.
* The objective would be to simulate a Sky130 standard cell inverter at each of these corners and plot its VTC.
* This analysis would allow for the extraction of the real-world range of `$V_m$`, noise margins, and propagation delays for the library cells that would be used in an actual chip design. This process, known as **static timing analysis (STA)** and characterization, is essential for guaranteeing that a designed chip will function correctly across all possible manufacturing variations.

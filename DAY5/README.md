# DAY5


***

##  Smart SPICE simulation for power supply variations

* An evaluation of the static behavior and robustness of a CMOS inverter was conducted by analyzing the effects of power supply scaling. The primary tool used for this analysis was a SPICE simulation.
* To perform this analysis efficiently, a "smart" SPICE deck was constructed. Instead of writing separate simulations for each voltage, a control loop was implemented to automate the process.
* The circuit netlist was first defined. This included an NMOS transistor (`M2`) and a PMOS transistor (`M1`) configured as a standard inverter.
    * The PMOS transistor (`M1`) was defined with a width of ` W_p = 0.9375\mu ` and a length of `L = 0.25\mu `.
    * The NMOS transistor (`M2`) was defined with a width of `W_n = 0.375\mu ` and a length of `L = 0.25\mu `.
    * A load capacitance of `10fF` was attached to the output node `out` to model a realistic load.
    * The simulation was based on the `$tsmc\_025um\_model.mod$` technology file.
* A `.control` block was used to program the simulation steps:
    ```spice
    .control
    let powerSupply = 2.5
    alter Vdd = powerSupply

    let voltageSupplyVariation = 0
    dowhile voltageSupplyVariation < 5
        dc Vin 0 2.5 0.01
        let powerSupply = powerSupply - 0.5
        alter Vdd = powerSupply
        let voltageSupplyVariation = voltageSupplyVariation + 1
    end
    .endc
    ```
* The results of all five DC analyses were plotted on a single graph. The `plot` command was used to display the output voltage (`dc1.out`, `dc2.out`, etc.) versus the input voltage. This generated a family of Voltage Transfer Characteristic (VTC) curves, with each curve corresponding to a different supply voltage.
* The resulting graph clearly showed that as the supply voltage `V_{dd}` was scaled down from `2.5V`, the VTC curve shifted and its transition region became steeper.


***

## Advantages and disadvantages using low supply voltage

* The results from the power supply scaling simulation were analyzed to understand the trade-offs of operating at a lower supply voltage. Two primary advantages and one major disadvantage were identified.
* **Advantage 1: Increase in Gain**
    * It was observed that as the supply voltage was lowered, the transition region of the VTC became significantly steeper.
    * A steeper transition region corresponds to a higher voltage gain. An example calculation showed that the absolute gain `|gain|` reached **11.53**, which represented a **56% improvement**. This increased gain contributes to better noise immunity.
* **Advantage 2: Significant Reduction in Energy**
    * The most compelling advantage of lowering the supply voltage is the dramatic reduction in power consumption
    * The dynamic energy consumed by a CMOS circuit is governed by the formula
      
                       `E = \frac{1}{2} C V^2` ,
      
      where `C` is the load capacitance and `V` is the supply voltage.
    * Because the energy is proportional to the *square* of the voltage, even a small decrease in `$V_{dd}$` yields a large energy saving. For instance, reducing the supply voltage to `$0.5V$` was shown to result in a **96% reduction in energy consumption**.
* **Disadvantage 1: Performance Impact**
    * The primary drawback of supply voltage scaling is the negative impact on circuit performance, specifically its speed.
    * Lowering the supply voltage reduces the drive current of the transistors, which means it takes longer to charge and discharge the load capacitance. This leads to increased propagation delays. An example measurement showed a **rise delay = 66ps** and a **fall delay = 78ps** at the lower voltage, indicating the inverter has become slower.



***

## Sky130 Supply Variation Labs

* The theoretical principles understood from the generic SPICE simulation were intended to be applied in a practical lab environment using the open-source **Sky130 Process Design Kit (PDK)**.
* The objective of such a lab would be to characterize a standard cell inverter from the Sky130 library under different supply voltage conditions.
* This involves performing a similar set of DC sweep simulations, but using the specific model files and device parameters provided by the Sky130 PDK to extract key performance metrics such as voltage gain (`A_v`), switching threshold (`V_m`), and noise margins (`NM_H, NM_L`).
* The results would provide a practical understanding of how the real-world standard cells used in an SoC tapeout respond to power supply variations.
* The following pictures show the supply variation plots

<img width="3384" height="2282" alt="supply_variation" src="https://github.com/user-attachments/assets/2d98189d-7b3b-4b72-8f37-06b8dca0077d" />

<img width="3969" height="2497" alt="supply_variation_plot" src="https://github.com/user-attachments/assets/6017c41d-cfdd-4a76-be46-eb29808537e2" />


***

## Sources of variation – Etching process

* An investigation into the physical sources of variation in CMOS manufacturing was conducted, focusing first on the **etching process**.
* In an ideal world, the etching process would perfectly transfer the rectangular shape from the **Ideal Mask** to the physical polysilicon on the wafer.
* However, in reality, the chemical etching process is imperfect. This results in an **Actual Mask** that is irregularly shaped, with rough and uneven edges. This alters the effective physical dimensions of the gate, specifically its **width (`W`)** and **length (`L`)**.
* This physical variation directly impacts the transistor's electrical characteristics. The drain current `I_d` is directly proportional to the `W/L` ratio, as shown in the equation for the linear region:

    $$
    I_d = \mu C_{ox} \left(\frac{W}{L}\right) \left[(V_{gs} - V_t)V_{ds} - \frac{V_{ds}^2}{2}\right]
    $$
  
* Because the etching process introduces random variations into the physical `W` and `L`, the resulting drain current will vary from transistor to transistor.
* This effect was also considered in the context of an **inverter chain**, where gates at the ends of the chain may experience more pronounced variations than those in the middle due to differences in their surrounding structures.



***

##  Sources of variation – oxide thickness

* Another critical source of manufacturing variation that was examined is the **thickness of the gate oxide (`t_{ox}`)**.
* An **Ideal Oxidation Process** would grow a perfectly uniform layer of oxide. In practice, the **Actual Oxidation Process** is not perfect, leading to variations in the gate oxide thickness across the transistor.
* This physical variation in `t_{ox}` directly affects the gate oxide capacitance per unit area, `C_{ox}`, since `C_{ox} = \frac{\epsilon_{ox}}{t_{ox}}`.
* The drain current `I_d` is inversely proportional to the oxide thickness `t_{ox}`, as seen in the rewritten drain current equation:
  
    $$
    I_d = \mu \frac{\epsilon_{ox}}{t_{ox}} \left(\frac{W}{L}\right) \left[(V_{gs} - V_t)V_{ds} - \frac{V_{ds}^2}{2}\right]
    $$
  
* This means that random variations in `t_{ox}` will cause the drive strength of transistors to vary, leading to unpredictable circuit performance.



***

## Smart SPICE simulation for device variations

* To model the real-world impact of physical variations, a SPICE simulation for **device variation** was performed, considering extreme manufacturing corners.
    * **Strong PMOS – Weak NMOS:** This scenario was simulated by significantly increasing the PMOS width (`W_p = 1.875u`) and decreasing the NMOS width (`W_n = 0.375u`).
    * **Weak PMOS – Strong NMOS:** This is the opposite corner, where the PMOS is weaker and the NMOS is stronger. This was simulated by swapping the widths (`W_p = 0.375u`, `W_n = 1.875u`).
* A series of DC sweeps were run to generate a family of VTC curves, illustrating the full range of static behavior expected due to process variations.


***

##  Conclusion

* The results of the device variation simulation led to several key conclusions about the impact of manufacturing variability on CMOS inverter robustness.
* **Shift in Switching Threshold (`V_m`):**
    * The most prominent effect observed was a significant horizontal **shift in the switching threshold (`V_m`)**.
    * A Strong PMOS / Weak NMOS combination shifts the VTC to the right, while a Weak PMOS / Strong NMOS combination shifts it to the left.
* **Variation in Noise Margins (`NM_H` and `NM_L`):**
    * This shift in the VTC directly impacts the circuit's **noise margins**, which are a critical measure of its robustness.
    * **Variation in Noise Margin Low (`NM_L`)** and **Variation in Noise Margin High (`NM_H`)** were observed, indicating that the circuit's ability to tolerate noise becomes inconsistent across different process corners.
* In summary, it was concluded that physical manufacturing variations directly degrade circuit robustness by altering the switching point and reducing noise margins, which can lead to functional failures.


***

## Sky130 Device Variation Labs

* The final practical exercise was to apply the understanding of device variation to the **Sky130 PDK**.
* This lab would involve using the specific process corner model files provided with the Sky130 library, such as **FF (Fast-Fast)**, **SS (Slow-Slow)**, **SF (Slow-Fast)**, and **FS (Fast-Slow)**.
* The objective would be to simulate a Sky130 standard cell inverter at each of these corners and plot its VTC.
* This analysis allows for the extraction of the real-world range of `V_m`, noise margins, and propagation delays for the library cells. This process is essential for guaranteeing that a designed chip will function correctly across all possible manufacturing variations.
* The following pictures shows the plot of device variations

<img width="3969" height="2505" alt="device_Variation" src="https://github.com/user-attachments/assets/49e1e56b-f543-4952-91e8-bea3663dd628" />

<img width="3969" height="2505" alt="device_Variation_plot" src="https://github.com/user-attachments/assets/a61a17e6-3585-4895-b165-18f0cce62f30" />

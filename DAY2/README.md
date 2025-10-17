# DAY2

# SPICE Simulation for Lower Nodes

- A SPICE simulation was performed to analyze the characteristics of a MOS device with a channel width (W) of 1.8μm and a channel length (L) of 1.2μm. This results in a W/L ratio of 1.5.

- The output characteristics of the device were plotted, showing the drain current (Id​) in microamperes (μA) versus the drain-source voltage (Vds​) in volts (V). Multiple curves were generated, each corresponding to a different constant gate-source voltage (Vgs​).

- The plot is clearly divided into two primary regions of operation for the transistor: the linear (or triode) region and the saturation region.

- A dashed blue line on the graph marks the boundary between these two regions. This boundary is defined by the condition where the drain-source voltage is equal to the overdrive voltage. This relationship is mathematically expressed as:
    Vds​=Vgs​−Vt​

where Vt​ is the threshold voltage of the device.

- For values of Vds​ less than the overdrive voltage (Vds​<Vgs​−Vt​), the device is said to be operating in the linear region. In this region, the drain current is described by the following equation:
  
    Id​=kn​⋅[(Vgs​−Vt​)Vds​−Vds^2/2​​]

 - When the drain-source voltage exceeds the overdrive voltage (Vds​≥Vgs​−Vt​), the device enters the saturation region. Here, the drain current ideally becomes independent of Vds​ and is primarily controlled by Vgs​. The equation governing the current in this region, which also accounts for the channel length modulation effect (λ), is given as:
    Id​=2kn′​​⋅LW​⋅(Vgs​−Vt​)^2[1+λVds​]

  - The slight upward slope of the curves in the saturation region is an illustration of this channel length modulation effect.

# Drain Current vs Gate Voltage for Long and Short Channel Devices

   - An observation was made by comparing the electrical characteristics of two different MOS devices: a long-channel device (W=1.8μm, L=1.2μm) and a short-channel device (W=0.375μm, L=0.25μm).

   - Initially, the standard Id-Vds curves were examined. A key difference noted was the magnitude of the drain current; the long-channel device showed a peak current of over 400 μA, while the short-channel device had a lower peak current of around 250 μA under similar voltage sweeps.

   - The analysis then shifted to the relationship between the drain current (Id​) and the gate-source voltage (Vgs​) for both devices.

   - For the long-channel device, the relationship between Id​ and Vgs​ in the saturation region was found to be a quadratic dependence. This is consistent with the classic saturation current equation, where Id​ is proportional to (Vgs​−Vt​)2. The Id-Vgs plot for this device exhibits a distinct parabolic curve.
    Id​=2kn′​​⋅LW​⋅(Vgs​−Vt​)2[1+λVds​]

   - In contrast, for the short-channel device, the relationship between Id​ and Vgs​ was observed to be a linear dependence. The Id-Vgs plot for this device is much closer to a straight line than a parabola.

   - This deviation from the classic quadratic behavior in short-channel devices is attributed to a second-order phenomenon known as the Velocity Saturation Effect.

 # Velocity Saturation at Lower and Higher Electric Fields

 - The Velocity Saturation Effect was explored as the reason for the linear Id-Vgs dependence in short-channel devices. This effect relates to the velocity of charge carriers (electrons, in this case) as they move through the channel.

 - Carrier velocity, vn​(x), is fundamentally related to the lateral electric field, ϵ, and the carrier mobility, μn​. The relationship is given by:
   
    vn​(x)=μn​⋅ϵ

   where the electric field ϵ is the gradient of the voltage along the channel, dxdV​.

  - A graph of carrier velocity (vn​) versus electric field (ϵ) was studied. It was shown that:

       - At lower electric fields (when ϵ≤ϵc​), the carrier velocity is directly proportional to the field. The relationship is linear.

       - At higher electric fields (when ϵ≥ϵc​), the velocity no longer increases with the field. It saturates and becomes constant at a maximum value, vsat​ (approximately 105 m/s). The reason for this saturation is identified as scattering effects, where carriers collide more frequently with the lattice atoms at high energies, limiting their average velocity.

   - This behavior is captured by a more complex velocity model. For continuity, a specific model was presented:
    vn​(m/s)={μn​⋅ϵ/(1+(ϵ/ϵc​))vsat​​for ϵ≤ϵc​for ϵ≥ϵc​​

   - The critical electric field, ϵc​, is the point where this transition begins and is defined as ϵc​=μn​2vsat​​.

   - An attempt was made to re-derive the drain current equation using this more accurate velocity model. The fundamental current equation, Id​=−vn​(x)⋅Qi​(x)⋅W, was used, where Qi​(x) is the inversion layer charge. This led to a very complex mathematical expression for Id​:
    Id​=1+ϵc​⋅LVds​​μn​⋅Cox​​⋅(W/L)[(Vgs​−Vt​)Vds​−Vds2​/2]

  - This formula was noted to be too complex for practical, manual circuit analysis, necessitating a simpler, more unified model.

# Velocity Saturation Drain Current Model

  - To simplify the analysis of short-channel devices, a more practical, unified drain current model was introduced. A variable Vgt​ was first defined as Vgt​=(Vgs​−Vt​).

  - A distinction was made between device operation modes for long-channel (>250nm) and short-channel (<250nm) technologies. While the Cutoff and Saturation modes are common to both, the linear/resistive mode in short-channel devices is replaced by a Velocity Saturation mode.

  - A single, unified current equation was proposed to cover all modes of operation (except cutoff, where Id​=0 for Vgt​<0):\
    
    Id​=kn​⋅[(Vgt​⋅Vmin​)−2Vmin2​​]⋅[1+λVds​]

   - The key to this model is the term Vmin​, which is defined as the minimum of three voltage values:
     
      Vmin​=min(Vgt​,Vds​,Vdsat​)

  - The term Vdsat​ is introduced as a critical technology parameter. It represents the drain-source voltage at which carrier velocity saturates and is independent of Vgs​ or Vds​.

  -  The behavior of this unified model was examined in different scenarios:

        -  Resistive/Linear Mode: When Vds​ is the minimum value, Vmin​=Vds​. The equation simplifies to the classic linear region formula:
          
        Id​=kn​⋅[(Vgt​⋅Vds​)−Vds^2/2​​]⋅[1+λVds​]

        - Saturation Mode (Long Channel): When Vgt​ is the minimum value, Vmin​=Vgt​. The equation simplifies to the classic saturation formula:
          
        Id​=kn​⋅[Vgt2​−2Vgt2​​]⋅[1+λVds​]=kn​⋅2Vgt2​​⋅[1+λVds​]

        - Velocity Saturation Mode (Short Channel): When Vdsat​ is the minimum value, Vmin​=Vdsat​. The drain current is now limited by this parameter. It was noted that at lower technology nodes, the current appears to increase because kn​ (which includes Cox​) increases as the oxide layer gets thinner.

  - Observation 2: A final comparison of the Id-Vds plots for the long- and short-channel devices confirmed a major consequence of velocity saturation. It causes the device to saturate early. The peak current for the long-channel device was measured at 410 μA, while the short-channel device saturated at a much lower peak current of 210 μA.

# Lab ON Sky130 Id-Vgs &  Vt

- From the github repository downloaded in day 1 , we use the following files with names starting with day 2to source to ngspice and get the Id vs Vds plot


 <img width="2528" height="1889" alt="id_vs_vds" src="https://github.com/user-attachments/assets/60f37851-915d-4e53-b15f-a9f3e43a4bff" />


  Now, enter the following command to get the following Id vs Vds plot
  

  ```
  plot -vdd#branch
  ```

<img width="3965" height="2477" alt="id_vs_vds_plot" src="https://github.com/user-attachments/assets/65906d2a-453a-4973-b562-671ce1449f8d" />

Now to get the Id vs Vgs plot follow the same process

<img width="3965" height="2477" alt="id_vs_vgs" src="https://github.com/user-attachments/assets/d1841f22-fe3c-41b6-a141-ba6c9c84f3bd" />

 Now, enter the following command to get the following Id vs Vds plot
  

  ```
  plot -vdd#branch
  ```

<img width="3965" height="2477" alt="id_vs_vgs_plot" src="https://github.com/user-attachments/assets/719db9e0-3644-4e99-9066-e7227766d250" />

Now to determine the threshold voltage (Vt) from the determined plot we see that the Id vs Vgs plot remains close to zero till a certain voltage , at certain voltage the graph starts to grow exponentially , basically inversion layer is formed in term of the mosfer structure

<img width="3965" height="2477" alt="Vt_plot" src="https://github.com/user-attachments/assets/5549c49b-f81a-4d0d-881f-490d2c3902df" />

We see that from this plot the threshold voltage is around 0.7 volts
  

# MOSFET as a Switch

- The fundamental characteristic of a MOS device to function as an electrical switch was studied. A transistor can be modeled as a switch in series with a resistor between its source and drain terminals.

- The state of this switch is controlled by the gate-source voltage, ∣Vgs​∣, relative to the threshold voltage, ∣Vt​∣.

     - The transistor is OFF when ∣Vgs​∣<∣Vt​∣. In this state, it behaves like an open switch with infinite OFF resistance.

     - The transistor is ON when ∣Vgs​∣>∣Vt​∣. In this state, it behaves like a closed switch with a finite ON resistance.

- This switching principle was then applied to a standard CMOS inverter, which consists of a PMOS and an NMOS transistor.

- Two input conditions were analyzed:

     - When the input voltage Vin​ is 'high' (equal to Vdd​), the NMOS transistor turns 'ON' (since its Vgs​=Vdd​>Vt​), and the PMOS transistor turns 'OFF' (since its Vgs​=0, which is not less than its negative Vt​). The 'ON' NMOS creates a direct path from the output (Vout​) to the ground (Vss​), pulling Vout​ low.

     - When the input voltage Vin​ is 'low' (equal to 0 V), the PMOS transistor turns 'ON' (since its Vgs​=−Vdd​, which is less than its Vt​), and the NMOS transistor turns 'OFF' (since its Vgs​=0<Vt​). The 'ON' PMOS creates a direct path from Vdd​ to the output (Vout​), pulling Vout​ high.


# Introduction to Standard MOS Voltage Current Parameters

 
 - The three main terminals of a MOSFET were identified as the Gate (G), Source (S), and Drain (D).

 - The key voltages and currents were defined as follows:

      - Vgs​ (Gate-Source Voltage): The potential difference between the gate and source terminals.

      - Vds​ (Drain-Source Voltage): The potential difference between the drain and source terminals.

      - IdsN​: The drain-source current flowing through the NMOS transistor.

      - IdsP​: The drain-source current flowing through the PMOS transistor.

  - The two static operating points of the inverter were re-stated based on these parameters:

       - When Vin​=Vdd​, a direct path exists between Vout​ and Vss​, resulting in Vout​=0.

       - When Vin​=0, a direct path exists between Vdd​ and Vout​, resulting in Vout​=Vdd​.

  - The circuit was also modeled using switches and resistors (Rn​ for NMOS, Rp​ for PMOS) to represent the ON-state resistance of the transistors, driving a capacitive load CL​.


# PMOS/NMOS Drain Current v/s Drain Voltage

  - The analysis of the CMOS inverter continued by establishing the relationships between the inverter's terminal voltages (Vin​, Vout​) and the individual transistor voltages.

  -  By observation of the inverter schematic, the following relations were derived:

        - For the NMOS: VgsN​=Vin​−Vss​. Assuming Vss​=0, this simplifies to VgsN​=Vin​. The drain-source voltage is VdsN​=Vout​−Vss​, which simplifies to VdsN​=Vout​.

        - For the PMOS: VgsP​=Vin​−Vdd​. The drain-source voltage is VdsP​=Vout​−Vdd​.

  - A fundamental principle based on Kirchhoff's Current Law for the inverter was stated: the current leaving the PMOS must equal the current entering the NMOS. This gives the relationship IdsP​=−IdsN​.

  - The characteristic I-V curves (Ids​ vs Vds​) for both transistors were shown.

       - The NMOS I-V curve plots IdsN​ vs VdsN​ for various positive values of VgsN​.

       - The PMOS I-V curve plots −IdsP​ vs −VdsP​ for various negative values of VgsP​. This convention is used to display the curves in the first quadrant, making them visually comparable to the NMOS curves.
         

# Step 1 – Convert PMOS Gate-Source-Voltage to Vin​

  - The first step in graphically deriving the inverter's Voltage Transfer Characteristic (VTC) was taken. This step involves re-labeling the PMOS I-V curves in terms of the inverter's input voltage, Vin​.

  - The relationship VgsP​=Vin​−Vdd​ was used to perform this conversion.

  - An example was provided assuming a supply voltage of Vdd​=2V. A table was constructed to show the mapping: | VgsP​ (V) | Vin​(=VgsP​+Vdd​) (V) | | :---: | :---: | | 0 | 2 | | -0.5 | 1.5 | | -1 | 1 | | -1.5 | 0.5 | | -2 | 0 |

  - This mapping was used to transform the family of PMOS I-V curves. The original curves, which were parameterized by different VgsP​ values (e.g., VgsP1​,VgsP2​), were re-labeled with their corresponding Vin​ values (e.g., Vin​=2V,Vin​=1.5V). This results in a set of PMOS characteristic curves parameterized directly by the inverter input voltage.


# Step 2 & Step 3 – Convert PMOS and NMOS Drain-Source-Voltage to Vout​

   -  The next steps focused on expressing the I-V characteristics of both transistors as a function of the inverter's output voltage, Vout​. This creates what are known as load curves.

   -  For the NMOS transistor: The conversion was straightforward. Since it was established that VdsN​=Vout​, the x-axis of the NMOS I-V plot was simply relabeled from VdsN​ to Vout​. The family of curves, already parameterized by Vin​ (since VgsN​=Vin​), now represents the Load Curve for the NMOS transistor.

   -  For the PMOS transistor: The conversion used the relationship VdsP​=Vout​−Vdd​. This means that the PMOS curves, which were originally plotted against −VdsP​, needed to be transformed to be plotted against Vout​. This effectively mirrors and shifts the curves along the voltage axis. The resulting graph, showing IdsP​ (or IdsN​) versus Vout​ for various Vin​ values, is the Load Curve for the PMOS transistor.

# Step 4 – Merge PMOS – NMOS Load Curves and Plot VTC

   -  The final step in the graphical derivation was to obtain the inverter's VTC. This was achieved by superimposing the load curve of the NMOS onto the load curve of the PMOS.

   -  The key principle is that for any given steady-state input voltage Vin​, the circuit must operate at a point where the current through the PMOS equals the current through the NMOS (IdsP​=IdsN​). On the merged plot, these operating points are the intersection points of the NMOS and PMOS curves that correspond to the same value of Vin​.

   - The process was demonstrated graphically:

   - For Vin​=0.5V, the intersection of the NMOS curve for Vin​=0.5V and the PMOS curve for Vin​=0.5V was found. The Vout​ value at this intersection is the output voltage for that input.

  -This process was repeated for all other values of Vin​ (0V, 1V, 1.5V, 2V).

  - By picking out the coordinates (Vin​,Vout​) from each intersection point, a series of points representing the inverter's static behavior was collected.

       When Vin​=0, Vout​=2.

       When Vin​=0.5, 1.5<Vout​<2.

       When Vin​=1, 0.5<Vout​<1.5.

       When Vin​=1.5, 0<Vout​<0.5.

       When Vin​=2, Vout​=0.

   - These points were then plotted on a new set of axes with Vout​ versus Vin​. Connecting these points produced the final Voltage Transfer Characteristic (VTC) curve for the CMOS inverter.

  The VTC plot was annotated to show the operating regions of the transistors at different points along the curve, illustrating how the NMOS and PMOS transition between cutoff, linear, and saturation modes as Vin​ sweeps from low to high.

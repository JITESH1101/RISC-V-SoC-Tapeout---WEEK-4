# DAY1

The day 1 of learning journey began with understanding the critical role of SPICE simulations in modern circuit design. The focus will be then shifted to the fundamental NMOS transistor, where its physical structure and theory of operation, including threshold voltage, inversion, and its resistive and saturation regions, were thoroughly examined. This theoretical understanding was then directly linked to practical application by detailing the derivation of drain current models and the syntax for setting up a basic SPICE simulation using the sky130 PDK.

# Why do we need SPICE simulations?

- It was understood that to analyze any circuit design, such as the fundamental CMOS inverter shown, a method to predict its electrical behavior is required. The inverter consists of a PMOS and an NMOS transistor, taking an input ``Vin`` and producing an output ``Vout``, while driving a capacitive load ``C_L``.

- The core challenge lies in understanding how the circuit behaves under various conditions. For instance, the drain current (IDSN​) of the NMOS transistor is not constant; it changes based on both the input voltage (Vin​) and the output voltage (Vout​). Similarly, the behavior of the PMOS transistor also changes.

- SPICE (Simulation Program with Integrated Circuit Emphasis) simulations were introduced as the tool to solve this problem. SPICE allows for the detailed analysis of transistor-level circuits. It can be used to generate critical plots, such as:

    - The relationship between drain current (IDSN​) and output voltage (Vout​) for various fixed input voltages (Vin​=0,0.5,1,1.5,2).

    - The Voltage Transfer Characteristic (VTC) curve, which plots Vout​ versus Vin​. This curve is fundamental as it reveals the different operating regions (off, linear, saturation) for both the PMOS and NMOS transistors throughout the inverter's switching process.

- The necessity for SPICE extends beyond simple circuits to complex, real-world problems like Power Aware Clock Tree Synthesis (CTS). In such systems, the delay of a buffer cell (e.g., CBUF '1' or CBUF '2') is a complex function of its Input Slew and Output Load.

- This relationship is captured in Delay Tables, where specific delay values (like x1, x9, y14, etc.) are pre-calculated. For example, if a buffer CBUF '2' has an input slew of 60ps and an output load of 50fF, its delay is characterized as 'y14'.

- A buffer tree structure was examined, showing two levels of buffering. It was observed that at every level, each buffer was driving the same load, and identical buffers were used at the same level. An example calculation was shown: if capacitors C1 = C2 = C3 = C4 = 25fF and the buffer's input capacitance Cbuf1 = Cbuf2 = 30fF, then the total capacitance at nodes 'A', 'B', and 'C' would be greater than 50fF. To determine the precise timing delay through this tree, a simple model is insufficient.

- This raised several critical questions that SPICE simulations are designed to answer:

    - Where does the delay of a cell actually come from?

    - Are the high-level delay models used in tools accurate enough?

    - How can the results of static timing analysis be verified for correctness?

- It was concluded that SPICE is essential for accurately characterizing the fundamental timing and power behavior of cells at the transistor level, which forms the foundation for all higher-level digital design and analysis.

# Introduction to basic element in circuit design - NMOS

- The N-channel Metal-Oxide-Semiconductor (NMOS) transistor was introduced as a foundational building block in modern circuit design.

- Its physical structure was examined. It is fundamentally a 4-terminal device.

- The components of the NMOS transistor were identified from a cross-sectional diagram:

    - P-Substrate: The base material, which is a p-type semiconductor.

    - n+ Diffusion Regions: Two heavily doped n-type regions diffused into the substrate, which form the source and drain.

    - Gate Oxide: A very thin layer of insulating material, typically Silicon Dioxide (SiO2​), placed between the gate and the substrate.

    - Poly-Si or Metal Gate: A conductive layer (the gate itself) placed on top of the gate oxide.

    - Isolation Region (SiO2​): Insulating regions that separate one transistor from another on the chip.

- The four terminals of the device were explicitly named and located on the diagram:

    - G: Gate

    - S: Source

    - D: Drain

    - B: Body (or Bulk/Substrate)

# Strong inversion and threshold voltage

- The concept of Threshold Voltage (Vt​) was explored by examining the behavior of the NMOS transistor under specific biasing conditions.

- The initial state was considered where the Gate-to-Source voltage (Vgs​) is zero. The Drain, Source, and Bulk terminals were all connected to ground (GND).

- In this state, two back-to-back p-n junctions are formed: one between the P-Substrate and the n+ Source (B-S) and another between the P-Substrate and the n+ Drain (B-D).

- With a zero-volt bias across them, both of these junctions are 'off' (non-conducting). Consequently, the resistance between the Source and the Drain (S-D) is extremely high, and no current can flow.

- When a positive voltage is applied to the gate (+ve Vgs​), the positive charge on the gate repels the positive charge carriers (holes) in the P-substrate beneath the gate oxide. This creates a depletion region.

- Simultaneously, the positive gate voltage attracts negative charge carriers (electrons) from the substrate to the region under the gate. This is seen as an accumulation of negative charge at the semiconductor surface.

- As the gate voltage (Vgs​) is increased further, a critical point is reached where enough electrons have accumulated at the surface to form a continuous layer connecting the n+ source and the n+ drain.

- This phenomenon, where the semiconductor surface effectively 'inverts' from p-type to n-type, is called strong inversion.

- The specific Gate-to-Source voltage (Vgs​) at which strong inversion occurs is defined as the threshold voltage, denoted as Vt​.

- Once Vgs​ surpasses Vt​, a continuous n-channel is formed. Electrons are drawn from the heavily doped n+ source region into this channel. This channel now provides a conductive path from the Source to the Drain, and its conductivity is modulated by the applied gate voltage, Vgs​. It was noted that a further increase in Vgs​ beyond Vt​ primarily increases the charge in the channel without changing the depletion layer width.


# Threshold voltage with positive substrate potential

- The effect of the substrate's potential on the threshold voltage was investigated. This is commonly known as the body effect.

- Two scenarios were compared. The first was the nominal case where the Source-to-Bulk voltage is zero (Vsb​=0). In this case, the semiconductor surface inverts to an n-type material at a voltage ``Vgs ​ = Vto​``.

- The second scenario involved applying a positive voltage to the bulk relative to the source, resulting in a positive Vsb​ value. This creates an additional reverse bias between the n+ source and the p-type substrate.

- This increased reverse bias causes the depletion region layer width to increase, particularly near the source.

- Because the depletion region is now wider, a higher gate voltage (Vgs​) is required to achieve strong inversion. The conclusion drawn was that in the presence of a substrate bias (Vsb​), an additional potential is required for strong inversion. The new threshold voltage is now Vt​=Vto​+V1​, where V1​ is the additional potential required.

- The complete mathematical model for this behavior was presented through the Threshold Voltage Equation:
  
  
     Vt​=Vto​+γ((∣−2Φf​+Vsb​∣)^1/2 ​−(∣−2Φf​∣)^1/2 ​)

- vThe terms in this equation were defined as follows:

    - Vto​: The threshold voltage when Vsb​=0. This is a function of the manufacturing process.

    - γ (gamma): The body effect coefficient, which expresses the impact of changes in body bias Vsb​. Its unit is V0.5.

    - Φf​ (phi_f): The Fermi Potential.

- The equations for γ and Φf​ were also provided, showing their dependence on physical parameters:
  
   γ=Cox​2qNA​ϵsi
  ​​​
   Φf​=−ϕT​ln(​NA/ni)

- The physical constants and process parameters within these formulas were defined:

    - ϵsi​: Relative permittivity of silicon (11.7).

    - NA​: Doping concentration of the substrate.

    - q: Charge of an electron.

    - Cox​: Gate oxide capacitance per unit area.

    - ϕT​: Thermal voltage.

    - ni​: Intrinsic doping parameter for the substrate.

# Resistive region of operation with small drain-source voltage

- The behavior of the NMOS transistor in its resistive (or linear) operation was analyzed. This mode occurs when the gate voltage is above the threshold voltage (Vgs​>Vt​).

- It was established that the induced charge in the channel, Qi​, is proportional to the overdrive voltage:
  
             Qi​∝(Vgs​−Vt​)

- A specific case was set up for analysis with a small Drain-to-Source voltage (Vds​):

    - Vgs​=1V

    - Vds​=0.05V

    - Vt​=0.45V

- A coordinate system was defined for the analysis, with the 'x' axis running along the channel from source (x=0) to drain (x=L), and the 'y' axis being perpendicular to the channel.

- Key voltage definitions were introduced:

    - V(x): The voltage at a point 'x' along the channel. This voltage varies from 0 at the source to Vds​ at the drain.

    - Vgs​−V(x): The gate-to-channel voltage at that specific point 'x'. This is the potential difference responsible for creating the inversion layer at that point.


# Drift current theory

- The mechanism of current flow in the channel was explained. From a device perspective, two kinds of current exist:

    1) Drift current: This is current caused by charge carriers moving under the influence of an electric field, i.e., due to a potential difference.

    2) Diffusion current: This is current caused by the movement of charge carriers from a region of high concentration to a region of low concentration.

- For the analysis of drain current in the channel, drift current was identified as the primary mechanism.

- A first-order analysis of the charge in the channel was performed. The induced charge per unit area at a point 'x', Qi​(x), is not uniform because the channel potential V(x) changes along the length. The equation was refined to:
  
  
   Qi​(x)∝−([Vgs​−V(x)]−Vt​)

This relationship was expressed precisely as:


                       Qi​(x)=−Cox​([Vgs​−V(x)]−Vt​)

- Here, Cox​ is the gate oxide capacitance per unit area, defined as:
  

                                Cox​=ϵox​​/tox

    - ϵox​: Oxide permittivity (given as relative permittivity 3.97×ϵ0​, or an absolute value of 3.5×10−11F/m).

    - tox​: The thickness of the gate oxide.

- The drain current, Id​, was then formulated based on drift principles. The current is the product of the charge carriers' velocity and the amount of charge available, considering the channel's width.
Id​=(velocity of charge carriers)×(available charge per unit length)

- This was expressed mathematically as:
  
  
                  Id​=−Vn​(x)⋅Qi​(x)⋅W
  

    - W: The width of the channel (as seen in the top view).

    - Vn​(x): The velocity of electrons at point x, which is the product of electron mobility (μn​) and the electric field (E(x)). The electric field is the gradient of the potential, so
      
      
                                      Vn​(x)=μn​⋅dV​/dx
      

- It was stated that by substituting these expressions and integrating over the entire channel length 'L', the complete voltage-current (V-I) relationship for the NMOS transistor could be derived.

# Drain current model for linear region of operation

- The derivation for the drain current (Id​) in the linear region was carried out.

- The process started by combining the previously established equations:
  
Id​⋅dx=μn​⋅Cox​⋅W⋅([Vgs​−V(x)]−Vt​)⋅dV

- To find the total current, this expression was integrated. The left side was integrated over the channel length (from 0 to L), and the right side was integrated over the channel voltage (from 0 to Vds​):

∫0L​Id​dx=∫0Vds​​μn​Cox​W([Vgs​−Vt​]−V(x))dV

Solving this integration yields the final drain current equation for the linear region of operation:

Id​=μn​Cox​LW​[(Vgs​−Vt​)Vds​−2Vds2​​]

- To simplify this expression, two parameters were introduced:

    - Process transconductance: kn′​=μn​Cox​

    - Gain factor: kn​=kn′​LW​

- Using these parameters, the equation was written more compactly as:

Id​=kn​[(Vgs​−Vt​)Vds​−2Vds2​​]

- Applying the specific voltage values from the earlier example (Vgs​=1V, Vds​=0.05V, Vt​=0.45V):

  Id​=kn​⋅[((1−0.45)⋅0.05)−(0.052/2)]
  
  Id​=kn​⋅[0.0275−0.00125]

- It was observed that for values of Vds​ that are small compared to the overdrive voltage (Vgs​−Vt​), the Vds2​/2 term is negligible and can be approximated as zero.

This simplifies the equation to Id​≈kn​⋅(Vgs​−Vt​)Vds​, which shows that Id​ is a linear function of Vds​. This is why this regime is called the linear or resistive region of operation.

# SPICE conclusion to resistive operation

The connection between the derived theoretical equations and practical simulation was made. The question was posed: "How do we calculate Id​ for different values of Vgs​, and at every value of Vgs​, sweep Vds​?"

   -  SPICE simulations were presented as the definitive tool to solve this. SPICE automates the calculation of these complex equations.

   -  A simulation plan was outlined:

        - The gate voltage Vgs​ would be swept through a series of values: 0.5V, 1V, 1.5V, 2V, 2.5V.

        - Given Vt​=0.45V, these Vgs​ values correspond to overdrive voltages (Vgs​−Vt​) of 0.05V, 0.55V, 1.05V, 1.55V, and 2.05V.

        - For each of these Vgs​ steps, the drain voltage Vds​ would be swept from 0 up to 2.05V.

   -  This process allows for the generation of the characteristic I-V curves for the transistor, which is a fundamental task in circuit analysis and design.

# Pinch-off region condition

- The transition of the NMOS transistor from the linear region to the saturation region was explained through the concept of pinch-off.

- The voltage in the channel is not constant; it increases from 0 at the source to Vds​ at the drain. The effective gate-to-channel voltage at any point 'x' is Vgs​−V(x).

- At the drain end of the channel (where x=L), the channel voltage is Vds​. Therefore, the gate-to-channel voltage here is Vgs​−Vds​.

- For the channel to exist, this local gate-to-channel voltage must be greater than the threshold voltage, Vt​.

- A table was used to illustrate what happens as Vds​ is increased while Vgs​ is held constant at 1V (and Vt​=0.45V).

    - When Vds​ is small (e.g., 0.05V), Vgs​−Vds​ is 0.95V, which is much greater than Vt​. A strong channel exists along the entire length.

    - As Vds​ increases, the value of Vgs​−Vds​ decreases.

    - A critical point is reached when Vds​=0.55V. At this point, Vgs​−Vds​=0.45V, which is exactly equal to Vt​. This is the onset of pinch-off; the channel is just about to disappear at the drain end.

- When Vds​ is increased beyond this point (e.g., to 0.65V), the condition Vgs​−Vds​<Vt​ is met. In this state, there is no longer an inverted channel near the drain region.

- The formal pinch-off condition was defined as:
  
Vgs​−Vds​≤Vt​

This is the condition for the transistor to be in the saturation region.

# Drain current model for saturation region of operation

- Once the transistor enters saturation (Vds​≥Vgs​−Vt​), the drain current behavior changes significantly.

- At the pinch-off point, the voltage across the inverted channel effectively becomes constant at Vds,sat​=Vgs​−Vt​, regardless of how much higher the external Vds​ becomes.

- To derive the drain current in this region, this saturation voltage (Vds,sat​) is substituted for Vds​ in the linear region current equation:
  
 Id​=kn′​LW​[(Vgs​−Vt​)Vds,sat​−2Vds,sat2​​]

Replacing Vds,sat​ with (Vgs​−Vt​):

Id​=kn′​LW​[(Vgs​−Vt​)(Vgs​−Vt​)−2(Vgs​−Vt​)2​]

This expression simplifies to the drain current equation for the saturation region:

Id​=21​kn′​LW​(Vgs​−Vt​)2

- It was noted that this current is no longer a linear function of Vds​; ideally, it is independent of Vds​ and depends only on the square of the overdrive voltage. This makes the transistor behave like a perfect current source.

- However, it was clarified that this model is not perfectly correct. In reality, as the applied Vds​ increases beyond the saturation point, the depletion region at the drain end widens. This widening encroaches on the channel, reducing the effective conductive channel length (L).

T- his phenomenon is known as channel length modulation.

A more accurate equation was introduced to account for this effect:

Id​=21​kn′​LW​(Vgs​−Vt​)2(1+λVds​)

The term λ is the channel length modulation parameter. This term introduces a slight dependence of Id​ on Vds​ in the saturation region, resulting in a non-zero slope on the I-V curves.
































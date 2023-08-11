# **Advanced-Physical-Design-Using-OpenLANE-Sky130**



## **Day 3: Design Library Cell using Magic Layout and ngspice characterization**

OpenLANE enables users to modify environment variables in real-time. For example, if there is a desire to alter pin placement from equidistant to a different style, the following steps can be taken within the openLANE workflow:

```
set ::env(FP_IO_MODE) 2
```

### **SPICE Deck Creation & Simulation for CMOS Inverter**

A SPICE deck typically contains a description of the circuit's components, their interconnections, and other relevant parameters. It is basically like a netlist.

SPICE deck comprises of the following information:
1. Component connectivity
2. Componenet values - Channel Width and Length values, Output Load Capacitance, Input and Supply Gate Voltage 
3. Identify nodes
4. Name nodes
5. Simulation commands
6. Model Libraries

To generate the output waveform from the SPICE deck, we will employ ngspice. The procedure for executing the simulation using ngspice is outlined below:

1. Begin by sourcing the .cir spice deck file.
2. Execute the spice file using the "run" command.
3. Utilize "setplot" to enable the visualization of plots generated from the simulations defined in the spice deck.
4. Specify the desired simulation by inputting its name in the terminal.
5. Employ the "display" command to observe the available nodes that can be plotted.
6. Employ the "plot 'node' vs 'node'" format to generate the desired output waveform.

### **Switching Threshold of CMOS inverter**
The switching threshold, Vm of a CMOS inverter is the juncture on the transfer characteristic where Vin = Vout. At this juncture, both the PMOS and NMOS transistors are in the saturation region, leading to the emergence of a leakage current.

### **16 Mask CMOS Process**

The 16-Mask CMOS Process involves the creation of integrated circuits through a sequence of steps:

1. **Substrate Selection:** The process begins by selecting a silicon wafer as the base material. The substrate's crystal orientation and quality are critical to the performance of the final integrated circuit.

2. **Active Region Formation:** To create the active regions where transistors will be formed, layers of silicon dioxide (SiO2) and silicon nitride (Si3N2) are deposited on the silicon wafer. Photoresist is applied and exposed through lithography to define pockets for the transistors.

3. **Nwell & Pwell Formation:** The Nwell (for NMOS transistors) and Pwell (for PMOS transistors) regions are created using doping techniques. Phosphorus and boron are introduced, respectively, to modify the silicon's electrical properties. The wafers are then subjected to high-temperature diffusion to distribute the dopants.

4. **Gate Terminal Creation:** A gate oxide layer is grown on the surface, followed by deposition of a polysilicon layer. The polysilicon is patterned and etched to form the gate terminals of the transistors. Controlling the gate oxide thickness and polysilicon properties ensures desired transistor behavior.

5. **Lightly Doped Drain (LDD) Formation:** LDD regions are introduced to minimize short-channel effects and hot electron effects. This involves controlled doping of the silicon near the transistor's drain and source regions.

6. **Source and Drain Establishment:** By introducing additional doping and annealing steps, the heavily doped source and drain regions are created. These regions are crucial for controlling the flow of current through the transistors.

7. **Contacts & Local Interconnects:** Openings are etched in the insulating layers to form contact holes, exposing the source, drain, and gate terminals. Metal (often aluminum or copper) is deposited into these holes to establish electrical connections. Titanium is commonly used to improve adhesion between metal and silicon.

8. **Higher-Level Metal Layers:** To enable complex interconnections between transistors and other components, additional metal layers are deposited and patterned. These layers allow for routing signals across the integrated circuit, completing the fabrication process.

The 16-Mask CMOS process optimizes the creation of modern semiconductor devices by carefully controlling the doping profiles, insulating layers, and metal connections. This systematic approach ensures the functionality, performance, and reliability of CMOS integrated circuits used in a wide range of electronic devices.


### **Magic Layout View of Inverter Standard Cell**

The Magic layout configuration of a CMOS inverter will be employed to incorporate the inverter into the PicoRV32a design. To achieve this, the inverter's Magic file is obtained from the vsdstdcelldesign repository by duplicating it within the openlane_working_dir/openlane directory.

````
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
````
This creates a folder named vsdstdcelldesign in the openlane directory. Following the directory structure:

/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign

Following command can be used to invoke magic to view sky130_inv.mag :
````
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/sky130_inv.mag &
````

![image1](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/708ab9fc-6e68-4133-88b4-824ab799abe0)

Within the Sky130 process, the initial layer is referred to as the "local interconnect layer" or Locali.

For validation of a layout resembling a CMOS inverter, it's possible to observe the verification of P-diffusion and N-diffusion areas with Polysilicon connections.

Additional verification steps encompass confirming the connections of source and drain. It's essential for both the PMOS and NMOS drains to link to the output port (in this case, labeled as "Y"), while their sources should be connected to the power supply VDD (referred to as "VPWR").

To navigate device inference, one can select the precise layer or device. This is achieved by hovering over the object and pressing the 's' key iteratively until the hierarchy is traversed to reach the intended object. Also run the "what" command in the tkcon window.

![image2](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/ea7771c4-66f5-4d36-b82d-0be4b762fb4e)

#### **DRC Errors**

DRC Errors will be shown in the DRC tab in the top panel. If the DRCs are clean then it will be in green colour and if it is violated then it will be in red. We make sure that the final database is DRC clean. 

#### **PEX Extraction**

The following command can be used in tkcon to do extraction on the design:

````
extract all
````
![image3](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/781efb1d-0f3e-4581-8968-4d5cf4cd7ec7)

![image4](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/50a554dd-55ef-4052-a409-85a01bb1fbea)

We need to create have .spice file using .ext file to use ngspice tool. Following command can be used to generate .spice :

````
ext2spice cthresh 0 rethresh 0
ext2spice
````


![image5](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/01907367-5960-42a5-9536-59f4268141a9)

sky130_inv.spice will be created in the vsdstdcelldesign directory.

Following is the content of the sky130_inv.spice :


![image6](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/27de07e5-31c6-40c2-8a5a-a7981cbe62cc)

The adjustments made to this SPICE deck involve the incorporation of the PMOS and NMOS libraries, namely pshort.lib and nshort.lib, respectively. Furthermore, the deck is enhanced by integrating the minimum grid size of the inverter, as determined from the magic layout, through the line: ".option scale=0.01u".

In the following manner voltage sources and simulation commands are defined in the .spice file:
````
VDD VPWR 0 3.3V
VSS VGND 0 0
Va A VGND PUSLE(0V 3.3V 0 0.1ns 0.1 ns 2ns 4ns)
.tran 1n 20n
.control
run 
.endc
.end
````

Also the model names in the MOSFET definitions are changed to pshort.model.0 and nshort.model.0 respectively for PMOS and NMOS.

![image7](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/0fe6a3cd-5f33-4e08-acd5-2e945005d9ce)

For simulation, ngspice is invoked in the terminal:
````
ngspice sky130_inv.spice
````
![image8](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/7e68bd70-aade-493c-b78b-e3f3d2e9679d)

To plot output vs Time, we can use the following command in the ngspice terminal:

````
plot y vs time a
````

Following is the graph obtained:

![image9](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/c0eeb152-03dd-414c-ab15-fd5addf6b5a9)



# **Advanced-Physical-Design-Using-OpenLANE-Sky130**
The project transpires within the "Advanced Physical Design using OpenLANE/Sky130" course by VLSI System Design Corporation. The project materializes a comprehensive RTL to GDSII workflow for the PicoRV32a System-on-Chip (SoC) using OpenLANE, firmly grounded in the Skywater 130nm PDK.OpenLANE stands as an automated RTL to GDSII flow, amalgamating tools such as OpenROAD, Yosys, Magic, Netgen, Fault, OpenPhySyn, SPEF-Extractor, and bespoke methodology scripts for design exploration and optimization. Notably, the process incorporates tailor-made standard cells designed with the Sky130 PDK, while concurrently implementing timing optimizations and eliminating slack violations.Aiming to enable a genuine open-source tape-out journey, OpenLANE operates under the APACHE version 2.0 license. Its primary objective involves generating pristine GDSII outcomes without necessitating human intervention. Optimized for the Skywater 130nm open-source Process Design Kit (PDK), OpenLANE empowers the creation of hard macros and chips.Through this endeavor, participants gain firsthand exposure to a holistic ASIC design journey, refining skills within a real-world context.


## **Getting Started**
The subsequent instance illustrates potential instructions for configuring your project within your local environment. Implement these straightforward steps to establish a local copy effectively.

## **Prerequisites**

Ensure possession of an Ubuntu OS-based System.
Allocate a minimum of 25GB of Disk Space.

## **Initiating Installation**
To initiate the installation process, kindly consult the following link: https://github.com/nickson-jose/openlane_build_script for comprehensive guidance on the installation procedures.






## **Stages of OpenLANE Design Flow:**

#### **1. Synthesis:**
- yosys: Undertakes RTL synthesis.
- abc: Executes technology mapping.
- OpenSTA: Conducts static timing analysis on the resulting netlist to produce timing reports.

#### **2. Floorplan and Power Distribution Network (PDN):**
- init_fp: Defines the core area for the macro, row placements, and routing tracks.
- ioplacer: Positions the macro's input and output ports.
- pdn: Generates the power distribution network.
- tapcell: Inserts welltap and decap cells in the floorplan.

#### **3. Placement:**
- RePLace: Performs global placement.
- Resizer: Offers optional design optimizations.
- OpenDP: Executes detailed placement for global component legalization.

#### **4. Clock Tree Synthesis (CTS):**
- TritonCTS: Synthesizes the clock distribution network (clock tree).

#### **5. Routing:**
- FastRoute: Conducts global routing to generate a guide file for detailed routing.
- CU-GR: Provides an alternative option for global routing.
- TritonRoute: Performs detailed routing.
- SPEF-Extractor: Extracts Signal Performance Estimation Format (SPEF).

#### **6. GDSII Generation:**
- Magic: Generates the final GDSII layout file from the routed DEF.
- Klayout: Acts as a backup by generating the GDSII layout file from the routed DEF.

#### **7. Checks:**
- Magic: Conducts Design Rule Checks (DRC) and Antenna Checks.
- Klayout: Performs DRC Checks.
- Netgen: Executes Layout vs. Schematic (LVS) Checks.
- CVC: Ensures Circuit Validity through Checks.

This comprehensive sequence outlines the various stages of the OpenLANE design process, encompassing synthesis, placement, routing, GDSII generation, and validation checks.

## **OpenLANE Directory Structure**
The organizational layout of the OpenLANE files takes the following shape:

- skywater-pdk: houses the PDK files furnished by the foundry.
- open_pdks: holds scripts responsible for configuring PDks for open-source tools.
- sky130A: encompasses the sky130 PDK files.

## **Launching OpenLANE and Preparing the Design**
To initiate OpenLANE, execute the docker command followed by entering an interactive session. The flow.tcl script delineates the particulars of the OpenLANE flow.

````
docker
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
````
![docker_command](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/d1b2a3d2-372e-4710-8fd7-429290b7a9a2)

Within the picorv32a directory, an "runs" folder emerges.
The merging process during picorv32a design setup results in the creation of the merged file. 

## **Synthesis**
Following is the command used:
````
run_synthesis
````

- Reports directory:
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-08_09-05/reports/synthesis

- Synthesized Netlist: /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-08_09-05/results/synthesis/picorv32a.synthesis.v

- Flop Ratio Calculation: 
`````
    Total no. of cells in the design = 14876
    Total no. of DFFs in the design = 1613
    Flop Ratio = Total no.of DFFs / Total no. of cells = 0.1084
`````
![cellcount](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/0d9e3ae0-7707-44c0-bf88-0721461aa4dd)


- Timing Reports:
/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-08_09-05/reports/synthesis/2-opensta.min_max.rpt

**Setup Violation Report:**

![max_rpt](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/541ddd8a-7450-4651-8308-3566652a3ea4)

**Hold Violation Report:**

![min_rpt](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/26fa72be-d01a-407f-a330-71e5b34cae89)


## **Floorplanning**

Floorplanning is a pivotal early phase in chip design, where strategic decisions influence subsequent stages. Key aspects include:

### Utilization Factor & Aspect Ratio:
- Utilization Factor = (Netlist Area) / (Total Core Area).
- Aspect Ratio = Height / Width.
- UF of 1 indicates full utilization; practically, it ranges from 0.5 to 0.6.
- An Aspect Ratio of 1 implies a square chip, other values depict rectangular shapes.
### Pre-Placed Cells:

- Define locations after determining Utilization Factor & Aspect Ratio.
- Pre-placed cells are IPs with fixed positions, e.g., large combinational logic.

### Decoupling Capacitors (Decaps):
- Surround high current drawing cells with decaps.
- Combat voltage drops due to long wire resistance and capacitance.
- Voltage drops can affect signals, pushing them into undefined territories.
- Decaps are substantial capacitors, charged to power supply voltage, placed near logic.
- Decoupling role: Supply current to circuits, preventing crosstalk and enabling local communication.
### Power Planning:
- Not every block can have its decap, unlike pre-placed macros.
- Effective power planning: Each block with individual VDD and VSS pads.
- Pads connected to horizontal and vertical power and GND lines, forming a power mesh.

### Pin Placement:
- Netlist defines logic gate connectivity.
- Pin placement occurs between the core and die.
- Connectivity info from VHDL/Verilog guides positioning of I/O pads.
- Logical placement block of pre-placed macros separates this area from the pin zone.

### **Floorplan Execution**
Floorplan executed using OpenLANE. Following command is being used:
````
run_floorplan
````

- Key files: floorplan.tcl, config.tcl, sky130A_sky130_fd_sc_hd_config.tcl.
The flow holds highest priority to the variables defined in sky130A_sky130_fd_sc_hd_config.tcl followed by config.tcl and floorplan.tcl. 

- Important environment variables used during floorplan: FP_CORE_UTIL, FP_ASPECT_RATIO, FP_CORE_MARGIN, FP_IO_MODE, FP_CORE_VMETAL, FP_CORE_HMETAL.
- Vertical & horizontal metal layer values typically exceed file specifications by 1.

Def file generated: /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-08_09-05/results/floorplan/picorv32a.floorplan.def

### **Viewing the Floorplan db**

To visualize our floorplan within Magic, three essential input files are required:

1. Sky130A.tech: The Magic technology file.
2. Floorplan DEF file: Contains floorplan details.
3. Merged LEF file: Integration of LEF and techlef files.

````
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech led read /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-08_09-05/tmp/merged.lef def read /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-08_09-05/results/floorplan/picorv32a.floorplan.def 
````
![floorplan](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/6fcd0cd4-697f-461e-933a-407077736d9a)

- Zooming into the Magic layout is achieved by marking an area using left and right mouse clicks and then pressing the "z" key. 
- Identifying different components becomes possible by utilizing the "what" command in the tkcon window following component selection.

![what_command](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/73d65d80-f42d-4363-b4d9-7653d895c24c)

- Pressing "v" key gives presents the full design. 

## **Placement**

In the broader context of the Digital ASIC design flow, placement emerges as the subsequent phase after floorplanning. The synthesized netlist, which is now intricately linked to standard cells, aligns harmoniously with the floorplan's designated standard cell rows, facilitating the progression into the placement phase. OpenLANE methodically undertakes the placement process through these two key stages:

### **1. Global Placement:** 
This initiatory stage strives for optimization, generating a placement that may not yet adhere to strict legality constraints. Optimization objectives encompass the minimization of wirelength by effectively addressing half-parameter wirelength reduction.

### **2. Detailed Placement:** 
Building on the foundation of global placement, this subsequent phase meticulously refines the positions of cells to ensure conformity with standard cell rows, thereby establishing a lawful placement configuration while retaining the optimization achievements from the previous stage.
### **Placement Execution**

Following command can be used to run placement:
`````
run_placement
`````
### **Viewing the Placement db**

````
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech led read /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-08_09-05/tmp/merged.lef def read /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-08_09-05/results/placement/picorv32a.floorplan.def &
````
![placement](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/f2b714c2-2196-483b-be80-2914dcec0d6c)

![zoomed_placement](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/67e8db79-2b42-45ca-ae75-219a936fe4de)

### **Standard Cell Design Flow:**
The standard cell design flow encompasses the subsequent phases:

- Input: Utilizing PDKs, DRC & LVS regulations, SPICE models, libraries, and user-specified criteria.
- Design Steps: Sequences include circuit design, layout design (employing layout techniques like Euler's path and stick diagram), parasitic extraction, and comprehensive characterization (embracing timing, noise, and power aspects).
- Outputs: Yielding CDL (circuit description language), LEF, GDSII formats, as well as an extracted SPICE netlist (.cir) along with timing, noise, and power .lib files.

### **Flow for Standard Cell Characterization:**
A standard cell characterization process follows these pivotal steps:

1. Model and tech files ingestion.
2. Loading of the extracted SPICE netlist.
3. Recognition of cell behavior.
4. Reading of subcircuits.
5. Integration of power sources.
6. Application of appropriate stimulus to the characterization configuration.
7. Providing essential output capacitance loads.
8. Issuing requisite simulation directives.

The open-source tool named GUNA proves valuable for characterization. Steps 1 through 8 are given as inputs into GUNA, which subsequently generates comprehensive timing, noise, and power models.

### **Definition of Timing Parameters:**
The timing parameters and their corresponding values are as follows:

- Slew_low_rise_thr: 20% of the value
- Slew_high_rise_thr: 80% of the value
- Slew_low_fall_thr: 20% of the value
- Slew_high_fall_thr: 80% of the value
- In_rise_thr: 50% of the value
- In_fall_thr: 50% of the value
- Out_rise_thr: 50% of the value
- Out_fall_thr: 50% of the value

Additionally, specific timing definitions encompass:

Rise delay: Calculated as time(out_fall_thr) minus time(in_rise_thr).
Fall transition time: Calculated as time(slew_high_fall_thr) minus time(slew_low_fall_thr).
Rise transition time: Calculated as time(slew_high_rise_thr) minus time(slew_low_rise_thr).
Ensuring precise threshold point selection is pivotal, as improper choices can lead to negative delay values. Accurate threshold point determination holds utmost significance.

## Design Library Cell using Magic Layout and ngspice characterization**

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

The surges observed in the output during switching moments stem from low-capacitance loads. To address this, adjustments can be made to the spice deck by raising the load capacitance parameter. In this instance, the output capacitance has been elevated to 2fF, and the resulting graph is presented below.

![waveform_updated spice](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/31fad9a3-1621-4172-9517-c9469dbec735)


### **Inverter Standard Cell characterization**

The inverter standard cell is characterized using four timing parameters:

- Rise Transition: This parameter measures the duration for the output to transition from 20% to 80% of its maximum value.

- Fall Transition: It represents the time taken for the output to transition from 80% to 20% of its maximum value.

- Cell Rise Delay: Calculated by subtracting the time at which the input falls to 50% from the time at which the output rises to 50%.

- Cell Fall Delay: Obtained by subtracting the time at which the input rises to 50% from the time at which the output falls to 50%.

The above mentioned timing parameters can be determined by recording different values from the ngspice waveform.

Rise transition = (2.24065 - 2.18023) = 60.42ps

Fall transition = (4.0936 - 4.02057) = 73.03ps

Cell rise delay = (2.20862 - 2.15) = 58.62ps

Cell fall delay = (4.07644 - 4.05115) = 25.29ps

### **Overview of Magic's DRC Engine**

Details on how Magic performs DRC and Magic's Syntax on DRC rules can be found here: http://opencircuitdesign.com/magic/


## **Day 4: Pre-layout Timing Analysis and CTS**

Place and routing (PnR) is executed using a conceptual representation of the GDS files produced by Magic. This conceptual data encompasses metal and pin details. The PnR tool leverages this conceptual view, known as LEF information, to execute interconnect routing alongside routing guides derived from the PnR process.

### **Introduction to LEF Files:**

Technology LEF - Encompasses layer specifications, via details, and specific DRC regulations.
Cell LEF - Represents the abstract specifics of standard cells.

Track information can be found from the below file: 
````
/home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd/tracks.info
````
![track_info](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/8625791b-7f1b-4ab3-b96a-3372b84c6d25)


Tracks play a pivotal role in the routing process, accommodating routes and metal traces in their course. The provided information focuses on the li1 layer and its horizontal track offset of 0.23 and a pitch of 0.46. This offset, half the pitch, ensures that tracks are symmetrically placed around the origin. It's crucial to adhere to on-track standard cell pin placement to ensure a Design Rule Check (DRC)-compliant Place and Route (PnR) process. Both li1 and met1 preferred routing directions must align with pin placements during standard cell creation. Visualizing a grid over the gds file aids in ensuring cell alignment with Magic's routing grids.

An essential requirement for ports, as detailed in tracks.info, is their positioning at the crossroads of horizontal and vertical tracks. For instance, CMOS Inverter ports A and Y reside on the li1 layer. To guarantee their placement at these intersection points, the tracks.info file is consulted for pitch and direction data. Adjusting the grid spacing in Magic (tkcon) to match li1 X and li1 Y values becomes essential to ensure proper port alignment.

Grid in Magic GUI can be enabled by pressing "g" key.

![grid_original](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/33557f16-12e6-4f0e-a7f8-35e9da3e61ac)


Grid definition can be converged to the track definition in the following manner: 
````
grid 0.46um 0.34um 0.23um 0.17um
````

![grid_command](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/be654760-6b0e-48f5-bacd-48de57fced25)

Width of the standard cell must be in the odd multiple of X direction pitch.

Height of the standard cell must be in the even multiple of Y direction pitch. 

We can see that the width of the standard cell is taking 3 units of the grid and height of the standard cell is taking 8 units of the grid. 

![grid_updated](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/8c05d08c-1420-4953-abc3-20de1f7caa86)


### **Create Port Definition**

Once the layout is prepared, the subsequent phase involves generating a LEF file for the specific cell. Yet, specific attributes and definitions must be configured for the cell's pins, serving to facilitate the tasks of the place and route tools. In the context of LEF files, a cell featuring ports is presented as a macro cell, with the declared pins representing the macro's PINs. Our goal is to derive a LEF file from a provided layout, such as that of a basic CMOS inverter, following standard conventions.

To achieve this, the primary step entails defining the ports and assigning accurate class and use attributes to each port. To facilitate the definition of ports, utilizing the Magic Layout window is the most straightforward approach. Here are the steps involved:

- Within the Magic Layout window, initiate by sourcing the design's .mag file (as in the case of an inverter). Subsequently, navigate to Edit >> Text, which triggers the opening of a dialog box.


![port_A](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/69775a9c-5432-4f5f-88a8-60d65ad6a53a)

 - For every layer intended to be transformed into a port, create a box on the corresponding layer. Assign a label name and affix a sticky label indicating the layer's name that the port should be linked to. Confirm that the "Port enable" checkbox is selected, while the default checkbox remains unchecked, as illustrated in the figure.

 !!add image!!

In the depicted images, ports A (input port) and Y (output port) are sourced from the local interconnect layer, referred to as "locali." Additionally, the numeral displayed in the textarea adjacent to the "enable" checkbox dictates the sequence in which the ports will be recorded within the LEF file (with 0 indicating the initial port).

- For power and ground layers, the definition can either align with or deviate from the signal layer's setup. In this scenario, the grounding and power connections are derived from metal1, as indicated by the presence of the sticky label.

![power_txt](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/4a89d3ad-3d43-4f98-a54b-9356cd928176)

### **Configure port class and port use attributes for a layout**

After defining the ports, the subsequent task involves assigning port class and port use attributes. While the "class" and "use" attributes of the port may not have inherent significance within Magic, they play a crucial role in the LEF and DEF format read and write procedures, aligning with the LEF/DEF CLASS and USE properties for macro cell pins. Permissible classes include: default, input, output, tristate, bidirectional, inout, feedthrough, and feedthru. Valid uses comprise: default, analog, signal, digital, power, ground, and clock. These attributes are configured within the tkcon window (once each port is selected on the layout window, which can be done by repeatedly pressing 's' until the desired port is highlighted), as demonstrated below:

![port_class_use](https://github.com/aleenavarghese95/Advanced-Physical-Design-Using-OpenLANE-Sky130/assets/141747430/74dfaa98-97ed-4fff-88a4-50eb370203aa)

### **Defining LEF Properties and Generating the LEF File**

Prior to crafting the LEF file, specific properties require configuration. As previously noted, these values are acquired by the placer and router to ascertain factors like the appropriate location for cell placement. While macro cell properties pertinent to the LEF/DEF definition lack direct translation within the Magic database, they are preserved through the employment of the cell property technique in Magic. Distinct property names are affiliated with the LEF format. Upon the configuration of these properties, the "lef write" command proceeds to generate the LEF file, adhering to the same nomenclature as the layout (.mag) file.

````
lef write <file_name>.lef
````

!!!add image!!

Generated lef file:

!!!add image!!

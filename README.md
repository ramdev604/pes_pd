# Advanced Physical Design using OpenLANE/Sky130 
This repository provides a comprehensive guide to navigate the entire physical design flow, starting from scratch.



# Lab Assignments

<details>
<summary>DAY 1 : Inception of opensource-EDA, Openlane and Skywater130</summary>
<br>

## Skywater-130 PDK


## Invoking OpenLANE


![d1_1](https://github.com/ramdev604/pes_pd/assets/43489027/cb079707-c611-486c-bab5-4bdde433faae)


flow.tcl is the file that contains the script to run the designs

## Importing package

Different software dependencies are needed to run OpenLANE. To import these into the OpenLANE tool we need to run: 
```package require openlane 0.9```


## Prepare the design for the flow 

```prep -design picorv32a```

![d1_2](https://github.com/ramdev604/pes_pd/assets/43489027/fa1bfd0c-9e08-4fe9-8117-73a577d94ee1)


## Synthesis

```run_synthesis```

![d1_3](https://github.com/ramdev604/pes_pd/assets/43489027/dd3a1f39-7e8a-4769-8e69-dffd743ff44c)


![d1_4](https://github.com/ramdev604/pes_pd/assets/43489027/eb0124bb-f865-4340-af6a-3c9b13e0da0b)

### Flop Ratio = (No. of D flip flops / Total number of cells) = 1613/14876 = 10.08%
                




</details>

<details>
<summary>DAY 2 : Good Floorplan vs Bad Floorplan and Introduction to library cells</summary>
<br>

## Floorplan

in OpenLANE, enter ```run_floorplan``` and the results will be updated in the runs folder

To view the layout of the floorplan, use the command ```magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &```


![d2_1](https://github.com/ramdev604/pes_pd/assets/43489027/b29d33fe-8860-4663-8a3e-72f0011602c3)


## Library Binding and Placement
### Placement

```run_placement```


![d2_2](https://github.com/ramdev604/pes_pd/assets/43489027/6e35d72e-789f-4026-85b6-bb02daa05741)

To view the layout of the placement, use the command ```magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &```


![d2_3](https://github.com/ramdev604/pes_pd/assets/43489027/a163cce3-dd19-4dae-9a59-271a3f96131b)


# Cell Design Flow

Cell Design is the process of creating electronic components, known as cells, for use in integrated circuits. It involves three main stages:

1) **Inputs**: In this phase, designers gather essential information and resources needed for the cell design. This includes Process Design Kits (PDKs), which contain manufacturing details, Design Rule Check (DRC) and Layout vs. Schematic (LVS) rules for ensuring correctness, SPICE models for simulating component behavior, and library specifications. These inputs provide the foundation for the design process.

2) **Design**: During this stage, designers use the gathered inputs to create the electronic cell. This process typically includes Circuit Design, where the logical and electrical schematic is defined, Layout Design, where the physical arrangement of components is determined while adhering to manufacturing rules, and Characterization, where the cell's performance is analyzed using tools like GUNA. Characterization can involve Timing characterization (evaluating signal timing), Power characterization (assessing power consumption), and Noise characterization (examining electrical noise).

3) **Outputs**: The design process yields various outputs that are essential for subsequent phases of chip development. These outputs include the Circuit Description Language (CDL), which describes the cell's behavior, the Graphic Data System II (GDSII) layout data used in manufacturing, the Library Exchange Format (LEF) for tool compatibility, an extracted Spice netlist (which considers parasitic elements for accurate simulation), timing data, noise data, power libraries, and a functional description to understand the cell's purpose.

# Standard Cell Characterization Flow

## Introduction

Standard Cell Libraries are a fundamental component in digital integrated circuit design, providing a collection of pre-designed cells with various functionalities. To effectively use these libraries, they must be characterized to generate Liberty files, which are essential for synthesis tools to determine the optimal arrangement of circuit components.The open-source software GUNA is used for characterization

## Characterization Flow

The characterization process involves the following steps:

1. **Link Model File of CMOS**: This step involves linking a model file that defines the properties and behavior of the CMOS (Complementary Metal-Oxide-Semiconductor) technology process being used.
2. **Specify Process Corner(s)**: Process corners represent different manufacturing variations that impact the performance of the integrated circuits. You must specify one or more process corners for the cell to be characterized. This information helps in understanding how the cell behaves under different conditions.
3. **Specify Cell Delay and Slew Thresholds Percentages**: Define the desired delay and slew thresholds as percentages. These thresholds are crucial for optimizing circuit performance and determining acceptable signal characteristics.
4. **Specify Timing and Power Tables**: Create tables that specify timing and power data for the cell under various conditions. These tables are essential for accurate modeling of cell behavior.
5. **Read the Parasitic Extracted Netlist**: Import the parasitic extracted netlist, which includes information about the interconnections and parasitic elements in the design. This step ensures a more realistic representation of the cell's behavior.
6. **Apply Input or Stimulus**: Provide input signals or stimuli to the cell. This can include various test vectors or patterns that are used to evaluate the cell's response under different conditions.
7. **Provide Necessary Simulation Commands**: Define the simulation commands required to run the characterization process. These commands may include simulation settings, simulation types (e.g., transient, static timing analysis), and other parameters to control the simulation environment.

# Timing threshold definitions

1) **slew_low_rise_thr**: This threshold is set at 20% above the lowest power supply voltage when the signal is transitioning from low to high (rising).
2) **slew_high_rise_thr**: This threshold is set at 20% below the highest power supply voltage when the signal is transitioning from low to high (rising).
3) **slew_low_fall_thr**: This threshold is set at 20% above the lowest power supply voltage when the signal is transitioning from high to low (falling).
4) **slew_high_fall_thr**: This threshold is set at 20% below the highest power supply voltage when the signal is transitioning from high to low (falling).
5) **in_rise_thr**: This threshold is placed at the point where the input signal is halfway through its transition from low to high during a rising edge.
6) **in_fall_thr**: This threshold is placed at the point where the input signal is halfway through its transition from high to low during a falling edge.
7) **out_rise_thr**: This threshold is positioned at the point where the output signal is halfway through its transition from low to high during a rising edge.
8) **out_fall_thr**: This threshold is positioned at the point where the output signal is halfway through its transition from high to low during a falling edge.
9) **Propagation Delay**: This is the time it takes for a signal to propagate from an input threshold (in_thr) to an output threshold (out_thr). It represents the time it takes for a change at the input to affect the output.
Propagation Delay = time(out_thr) - time(in_thr)
11) **Transition Time**: This is the time it takes for a signal to transition from a low to high state (rising edge) or from a high to low state (falling edge) within a specific voltage range. In this case, you are calculating the transition time from the high threshold (slew_high_rise_thr) to the low threshold (slew_low_rise_thr) during a rising edge.
Transition Time = time(slew_low_rise_thr) - time(slew_high_rise_thr)



</details>



<details>
<summary>DAY 3 :  Design library cell using Magic Layout and ngspice characterization  </summary>
<br>

# CMOS Inverter Fabrication Process

## Overview

A CMOS (Complementary Metal-Oxide-Semiconductor) inverter is a fundamental building block in digital integrated circuits. It consists of a p-type metal-oxide-semiconductor (PMOS) transistor and an n-type metal-oxide-semiconductor (NMOS) transistor connected in series. Here's a simplified overview of the fabrication process for a CMOS inverter:

## Fabrication Steps

1) **Substrate Preparation** : Start with a high-purity silicon substrate, typically n-doped.
2) **Oxidation** : Create an insulating silicon dioxide (SiO2) layer on the substrate through oxidation.
3) **Photolithography** : Apply a photoresist material, expose it to UV light through a mask, and develop it to define transistor areas.
4) **Ion Implantation (n-well and p-well)** : Create n-type (NMOS) and p-type (PMOS) regions through selective ion implantation.
5) **Gate Oxide Formation** : Grow or deposit a thin layer of gate oxide (SiO2) to insulate the gate electrode from the silicon channel.
6) **Polysilicon Deposition** : Deposit and pattern polysilicon to create gate electrodes that control current flow.
7) **Doping of Gate Electrodes** : Dope gate electrodes for desired conductivity (boron for PMOS, phosphorus/arsenic for NMOS).
8) **Source and Drain Formation** : Create heavily doped source and drain regions for both PMOS and NMOS transistors.
9) **Metal Layer Deposition** : Deposit and pattern a metal layer (e.g., aluminum or copper) for interconnections.
10) **Passivation Layer** : Deposit a passivation layer (usually silicon dioxide) to protect and insulate underlying layers.
11) **Contact Holes** : Etch contact holes through the passivation layer for metal contacts to transistors.
12) **Final Testing and Packaging** : Test the CMOS inverter to ensure it operates correctly, and then integrate multiple inverters and additional components onto a single chip for electronic device applications.


## Inverter Layout using Magic

```
cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
magic -T sky130A.tech sky130_inv.mag
```

## Exploring the Layout displayed by MAGIC

Select the specific layer/device by hovering over the object and pressing, s, iteratively, until you traverse the hierarchy to the specified object:
![d3_1](https://github.com/ramdev604/pes_pd/assets/43489027/7e77d661-2c1e-4f20-93a0-09eb411a247e)


- select a region from the layout, go to the console and type ```what``` to display the information of selected area
- To select a region, place ```cursor``` on that point and  press```s```. More the number of times you press ```s```, higher the abstraction selected.




## Modified Spice netlist

![modifiedspice](https://github.com/ramdev604/pes_pd/assets/43489027/78efc81d-8f8d-4aa0-8a9c-5dd210ddbfa8)


To run the spice netlist, run ```ngspice sky130_inv.spice``` and ```plot y vs time a```
![d3_2](https://github.com/ramdev604/pes_pd/assets/43489027/375b8998-04a9-4537-af16-35ae3ba9ebc1)

![d3_3](https://github.com/ramdev604/pes_pd/assets/43489027/735909c7-fd85-4a8e-bf3f-7405d4e839d7)


The results obtained from the graph are :
- Rise Transition : 0.0395ns
- Fall transition : 0.0282ns
- Cell Rise delay : 0.03598ns
- Cell fall delay : 0.0483ns

</details>


<details>
<summary>DAY 4 : Pre-Layout timing analysis and importance of good clock tree</summary>
<br>
    
## Extraction of LEF 


Track info can be found at :

``` ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/openlane/sky130fd_sc_hd/tracks.info```

![d4_1](https://github.com/ramdev604/pes_pd/assets/43489027/e1479fe6-55ca-4ed7-a226-2791275da645)


- 1st value indicates the offset and 2nd value indicates the pitch along provided direction

### Setting grid values using above file info

![d4_2](https://github.com/ramdev604/pes_pd/assets/43489027/1dfd759c-446f-419a-b276-aa4fa0a465bc)



- From the above pic, its confirmed that the pins A and Y are at the intersection of X and Y tracks. So the first condition is met.
- The PR boundary is taking 3 grids on width and 9 grids on height which says that the 2nd condition is also met

## LEF Generation

Since the layout is perfect, we can generate the lef file

#### 1. save the modified layout (with new grid)
   - In console, type ```save sky130_vsdinv.mag```
   - This saves the modified layout in current working directory

#### 2. Open the file and extract LEF
   - Open using ``` magic -T sky130A.tch sky130_vsdinv.mag```
   - in the console opened, type ```lef write``` and a lef file will be generated

![d4_3](https://github.com/ramdev604/pes_pd/assets/43489027/d6267e2d-62f1-4fd0-84b7-e9b3e5c8df6b)



#### 4. Make sure the lef file is added

- Include the below command to include the additional lef into the flow:
      
          set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
        
          add_lefs -src $lefs

![d4_4](https://github.com/ramdev604/pes_pd/assets/43489027/b545b775-280d-4d24-9601-845dcb073f02)


since there is slack, we have to reduce it

VLSI engineers will obtain system specifications in the architecture design phase. These specifications will determine a required frequency of operation. To analyze a circuit's timing performance designers will use static timing analysis tools (STA). When referring to pre clock tree synthesis STA analysis we are mainly concerned with setup timing in regards to a launch clock. STA will report problems such as worst negative slack (WNS) and total negative slack (TNS). These refer to the worst path delay and total path delay in regards to our setup timing restraint. Fixing slack violations can be debugged through performing STA analysis with OpenSTA, which is integrated in the OpenLANE tool. To describe these constraints to tools such as In order to ensure correct operation of these tools two steps must be taken:

- Design configuration files (.conf) - Tool configuration files for the specified design
- Design Synopsys design constraint (.sdc) files - Industry standard constraints file

For the design to be complete, the worst negative slack needs to be above or equal to 0. If the slack is outside of this range we can do one of multiple things:

1. Review our synthesis strategy in OpenLANE
    - Enalbed CELL_SIZING
    - Enabled SYNTH_STRATEGY with parameter as "DELAY 1"
    - The synthesis result is :
      

![d4_5](https://github.com/ramdev604/pes_pd/assets/43489027/f08a7a45-ce8e-4b9f-b385-c4556c3fb5a5)

    
![d4_6](https://github.com/ramdev604/pes_pd/assets/43489027/53d4f874-ccb3-4d10-b7a9-a38a798682c5)



The delay is high when the fanout is high. Therefore we can re-run synthesis by changing the value of ```SYNTH_MAX_FANOUT``` variable
    
2. Enable cell buffering 
3. Perform manual cell replacement on our WNS path with the OpenSTA tool

    - We can see which net is driving most outputs and replace the driver cell with larger form of its own kind

    ![d4_7](https://github.com/ramdev604/pes_pd/assets/43489027/3d290a73-ce45-4909-8e78-f1383db6436f)


4. Optimize the fanout value with OpenLANE tool

Since we have synthesised the core using our vsdinv cell too and as it got successfully synthesized, it should be visible in layout after ```run_placement``` stage which is followed after ```run_floorplan``` stage
![d4_8](https://github.com/ramdev604/pes_pd/assets/43489027/47852fe1-1979-44fd-9473-19d54a853f33)

## Clock Tree Synthesis

After addressing slack violations using the "pre_sta.conf" configuration, generate a netlist with the corrected design using "write_verilog." Subsequently, replace the original OpenLANE-generated "picorv32a.synthesis.v" file with this modified netlist to ensure the design incorporates the necessary fixes.

In the OpenLANE flow, proceed with the "run_floorplan," "run_placement," and "run_cts" stages to further refine the design and ensure that the corrections made to the netlist are incorporated into the physical layout.

#### Task 5 - Post CTS- STA Analysis

Step 1: Within OpenROAD, perform timing analysis by generating a `.db` database file.
1) Launch OpenRoad.
2) Load the LEF file from the "tmp" folder in your OpenLANE runs.
3) Load the DEF file from the CTS results.
4) Create and save the .db database file.
5) Load the generated .db file.
6) Read the CTS-generated Verilog file.
7) Import both the minimum and maximum liberty files.
8) Define clock domains.
9) Generate timing reports to analyze the design further.

![1](https://github.com/ramdev604/pes_pd/assets/43489027/1b3ed73d-88b7-44b4-969f-5dc6c7e9a274)


![2](https://github.com/ramdev604/pes_pd/assets/43489027/11bf6e13-d47f-440c-af19-fca9a36d7867)

The timing results are unlikely to meet our expectations due to the utilization of min and max library files, which OpenRoad does not currently support for multi-corner optimization. To address this, we will solely employ the typical corner library for optimization purposes.

![3](https://github.com/ramdev604/pes_pd/assets/43489027/b579270f-696c-4259-9491-2a31a61a4eb7)

![4](https://github.com/ramdev604/pes_pd/assets/43489027/55d8df9e-8633-4608-9f36-149d8dde70c3)


</details>

<details>
<summary>DAY 5 : Final steps for RTL2GDSII</summary>
<br>

# Power Distribution Network

After generating the clock tree network and verifying post-routing STA checks, we're ready to generate the power distribution network (PDN) in OpenLANE. The PDN feature within OpenLANE will create:

1) A power ring that spans the entire core.
2) A power halo, which is local to any preplaced cells.
3) Power straps, facilitating power distribution to the center of the chip.
4) Power rails dedicated to the standard cells.

![d5_1](https://github.com/ramdev604/pes_pd/assets/43489027/d49d45aa-5d23-4e8f-930c-948c394c4af7)


# Global and Detailed Routing

OpenLANE employs TritonRoute as the routing engine for physical design implementation, involving two key stages:

1) Global Routing: This phase generates routing guides for interconnects, defining the layers and locations on the chip for each net.
2) Detailed Routing: Metal traces are incrementally placed following the routing guides to physically implement the interconnects.

In case of persistent Design Rule Check (DRC) errors after routing, users have two options:

1) Re-run routing with higher Quality of Results (QoR) settings.
2) Manually address and correct specific DRC errors mentioned in the TritonRoute-generated "tritonRoute.drc" file.

# SPEF Extraction

After completing routing, interconnect parasitics need to be extracted for sign-off post-route Static Timing Analysis (STA). These parasitics are typically extracted into a Standard Parasitic Exchange Format (SPEF) file. It's important to note that, as of now, OpenLANE does not include an integrated SPEF extractor for this purpose.

#### Step 1: SPEF Extraction can be done by using these commands.
```
cd ~/Desktop/work/tools/SPEFEXTRACTOR
python3 main.py <path to merged.lef in tmp> <path to def in routing>
```
The SPEF file will be generated in the same location as the DEF file.



</details>

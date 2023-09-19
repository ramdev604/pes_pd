# Physical Design Flow
# Advanced Physical Design using OpenLANE/Sky130 

## OpenLANE Design Stages

OpenLANE flow consists of several stages. By default all flow steps are run in sequence. Each stage may consist of multiple sub-stages. OpenLANE can also be run interactively.

1. *Synthesis*
    1. `yosys` - Performs RTL synthesis
    2. `abc` - Performs technology mapping
    3. `OpenSTA` - Pefroms static timing analysis on the resulting netlist to generate timing reports
2. *Floorplan and PDN*
    1. `init_fp` - Defines the core area for the macro as well as the rows (used for placement) and the tracks (used for routing)
    2. `ioplacer` - Places the macro input and output ports
    3. `pdn` - Generates the power distribution network
    4. `tapcell` - Inserts welltap and decap cells in the floorplan
3. *Placement*
    1. `RePLace` - Performs global placement
    2. `Resizer` - Performs optional optimizations on the design
    3. `OpenPhySyn` - Performs timing optimizations on the design
    4. `OpenDP` - Perfroms detailed placement to legalize the globally placed components
4. *CTS*
    1. `TritonCTS` - Synthesizes the clock distribution network (the clock tree)
5. *Routing*
    1. `FastRoute` - Performs global routing to generate a guide file for the detailed router
    2. `CU-GR` - Another option for performing global routing.
    3. `TritonRoute` - Performs detailed routing
    4. `SPEF-Extractor` - Performs SPEF extraction
6. *GDSII Generation*
    1. `Magic` - Streams out the final GDSII layout file from the routed def
    2. `Klayout` - Streams out the final GDSII layout file from the routed def as a back-up
7. *Checks*
    1. `Magic` - Performs DRC Checks & Antenna Checks
    2. `Klayout` - Performs DRC Checks
    3. `Netgen` - Performs LVS Checks
    4. `CVC` - Performs Circuit Validity Checks



# Labs

<details>
<summary>DAY 1 : Inception of opensource-EDA, Openlane and Skywater130</summary>
<br>

## Skywater-130 PDK


## Invoking OpenLANE

*username changed to ramdev from Lab 2*

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


## Cell Design Flow

Cell design is done in 3 parts:

1. **Inputs** - PDKs (Process design kits), DRC & LVS rules, SPICE models, library & user-defined specs.
2. **Design Steps** - Design steps of cell design involves Circuit Design, Layout Design, Characterization. The software GUNA used for characterization. The characterization can be classified as Timing characterization, Power characterization and Noise characterization.
3. **Outputs** - Outputs of the Design are CDL (Circuit Description Language), GDSII, LEF, extracted Spice netlist (.cir), timing, noise, power.libs, function.

### Standard cell Charachterization Flow

Standard Cell Libraries consist of cells with different functionality/drive strengths. These cells need to be characterized by liberty files to be used by synthesis tools to determine optimal circuit arrangement. The open-source software GUNA is used for characterization.
Characterization is a well-defined flow consisting of the following steps:

- Link Model File of CMOS containing property definitions
- Specify process corner(s) for the cell to be characterized
- Specify cell delay and slew thresholds percentages
- Specify timing and power tables
- Read the parasitic extracted netlist
- Apply input or stimulus
- Provide necessary simulation commands

### General Timing characterization parameters

#### Timing threshold definitions

- ```slew_low_rise_thr``` - 20% from bottom power supply when the signal is rising
- ```slew_high_rise_thr``` - 20% from top power supply when the signal is rising
- ```slew_low_fall_thr``` - 20% from bottom power supply when the signal is falling
- ```slew_high_fall_thr``` - 20% from top power supply when the signal is falling
- ```in_rise_thr``` - 50% point on the rising edge of input
- ```in_fall_thr``` - 50% point on the falling edge of input
- ```out_rise_thr``` - 50% point on the rising edge of ouput
- ```out_fall_thr``` - 50% point on the falling edge of ouput

These are the main parameters that we use to calculate factors such as propogation delay and transition time

- ```propogation delay ``` - time(out_*_thr) - time(in_*_thr)
- ```Transition time``` - time(slew_high_rise_thr) - time(slew_low_rise_thr)


</details>



<details>
<summary>DAY 3 :  Design library cell using Magic Layout and ngspice characterization  </summary>
<br>


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

#### Clock Tree Synthesis

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

## Power Distribution Network

After generating our clock tree network and verifying post routing STA checks we are ready to generate the power distribution network ```gen_pdn``` in OpenLANE:

The PDN feature within OpenLANE will create:

- Power ring global to the entire core
- Power halo local to any preplaced cells
- Power straps to bring power into the center of the chip
- Power rails for the standard cells

![d5_1](https://github.com/ramdev604/pes_pd/assets/43489027/d49d45aa-5d23-4e8f-930c-948c394c4af7)


Note: The pitch of the metal 1 power rails defines the height of the standard cells

## Global and Detailed Routing

OpenLANE uses TritonRoute as the routing engine ```run_routing``` for physical implementations of designs. Routing consists of two stages:

- Global Routing - Routing guides are generated for interconnects on our netlist defining what layers, and where on the chip each of the nets will be reputed
- Detailed Routing - Metal traces are iteratively laid across the routing guides to physically implement the routing guides

If DRC errors persist after routing the user has two options:

- Re-run routing with higher QoR settings
- Manually fix DRC errors specific in tritonRoute.drc file

## SPEF Extraction

After routing has been completed interconnect parasitics can be extracted to perform sign-off post-route STA analysis. The parasitics are extracted into a SPEF file. The SPEF extractor is not included within OpenLANE as of now.

```
cd ~/Desktop/work/tools/SPEFEXTRACTOR
python3 main.py <path to merged.lef in tmp> <path to def in routing>
```

The SPEF File will be generated in the location where def file is present



</details>

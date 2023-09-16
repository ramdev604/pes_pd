# Advanced Physical Design using OpenLANE/Sky130 

## OpenLANE Design Stages

OpenLANE flow consists of several stages. By default all flow steps are run in sequence. Each stage may consist of multiple sub-stages. OpenLANE can also be run interactively..

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

OpenLane can be operated at 2 different modes ie., Automated flow and Interactive mode.

## To enter the automated flow, use these commands
```
cd OpenLane
make mount
./ flow.tcl -design openlane/<DESIGN_NAME>  -tag <TAG>
```

## To enter the Interactive mode, use these commands 
```
cd OpenLane
make mount
./flow.tcl -interactive 
prep -design <path_to_your_design_folder> -tag <tag> -overwrite //overwrite is optional
```

**Interactive mode** offers us to learn all the steps present in automated flow step by step.
The steps are as follows : 

```
run_synthesis
run_floorplan
run_placement
run_cts
run_routing
write_powered_verilog followed by set_netlist $::env(lvs_result_file_tag).powered.v
run_magic
run_magic_spice_export
run_magic_drc
run_lvs
run_antenna_check
```

# Lab

<details>
<summary>DAY 1 : Inception of opensource-EDA, Openlane and Skywater130</summary>
<br>

## Skywater-130 PDK


## Invoking OpenLane

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

# Advanced Physical Design using OpenLANE/Sky130 Workshop Notes
  * efabless-sponsored [VSD-IAT workshop (2022-08-03 to 2022-08-07)](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/)

  
## Table Of Contents
  
  * [Toolchain installation](#toolchain-installation)
  * [Workshop Day 1](#workshop-day-1)
    + [The OpenLane-driven Design Flow](#the-openlane-driven-design-flow)
    + [Synthesis in OpenLane](#synthesis-in-openlane)
  * [Workshop Day 2](#workshop-day-2)
    + [Floorplaning](#floorplaning)
      - [Pin placement and logical cell placement blockage](#pin-placement-and-logical-cell-placement-blockage)
    + [Floorplan in OpenLane](#floorplan-in-openlane)
    + [Placement in OpenLane](#placement-in-openlane)
    + [Cell Characterization](#cell-characterization)
  * [Workshop Day 3](#workshop-day-3)
    + [Inverter ngSPICE Simulations](#inverter-ngspice-simulations)
    + [CMOS Fabrication Process](#cmos-fabrication-process)
    + [Tech File Labs](#tech-file-labs)
  * [Workshop Day 4](#workshop-day-4)
    + [Standard Cell Layout to LEF](#standard-cell-layout-to-lef)
    + [Timing Settings for Synthesys](#timing-settings-for-synthesys)
    + [Post-Synthesys Timing Analysis - Custom Execution](#post-synthesys-timing-analysis---custom-execution)
    + [Clock Tree Synthesis](#clock-tree-synthesis)
    + [Timing analysis inside OpenLANE](#timing-analysis-inside-openlane)
  * [Workshop Day 5](#workshop-day-5)
    + [Power Distribution Network](#power-distribution-network)
    + [Global and Detail Routing](#global-and-detail-routing)
    + [Standard Parasitic Exchange Format (SPEF) File](#standard-parasitic-exchange-format-spef-file)
  * [References](#references)
  * [Credits](#credits)
  
## Toolchain installation

  * Prior the workshop, I already have an experience with qflow
  * I choose NOT to use the remote lab system and rather install tools on my private Debian machine to go through the complete process

  * Prerequisities:
```bash
$ apt install -y build-essential python3 python3-venv python3-pip
...
$
```
  * Docker installation [instructions](https://docs.docker.com/engine/install/debian/):
  * openLane:
```bash
$ cd ${PROJ_WORK}/tmp/
$ mkdir openLane
$ git clone https://github.com/efabless/OpenLane.git .
...
$ # Checkout the latest release: mpw-4
$ git checkout 4a1c799
$ make
...
$ make test
...
[SUCCESS]: Flow complete.
Basic test passed
$ 
```

## Workshop Day 1

### The OpenLane-driven Design Flow

![OpenLane](/img/openlane_flow.png)


### Synthesis in OpenLane

```bash
$ make mount
OpenLane Container (4a1c799):/openlane$ ./flow.tcl -interactive
```

```tcl
[INFO]: 
         ___   ____   ___  ____   _       ____  ____     ___
        /   \ |    \ /  _]|    \ | |     /    ||    \   /  _]
        |   | |  o  )  [_ |  _  || |    |  o  ||  _  | /  [_
        | O | |   _/    _]|  |  || |___ |     ||  |  ||    _]
        |   | |  | |   [_ |  |  ||     ||  _  ||  |  ||   [_
        \___/ |__| |_____||__|__||_____||__|__||__|__||_____|


[INFO]: Version: mpw-4
[INFO]: Running interactively
% # load package
% package require openlane 0.9
0.9
% # prepare design - strating withthe existing design AES128
% prep -design aes128
...
[INFO]: Preparation complete
% run_synthesis
...
[INFO]: Synthesis was successful
% # AES128 verilog was synthesised and mapped by Yosys/ABC
% #   - number of std cells is 46009
% #   - number of DFF is 5568
% #   - optimal area is 455210 um2
% #   - optimal area is 455210 um2
% # AES128 STA: (-> no violations)
% #   - worst setup slack is 3.39ns
% #   - worst hold slack is 0.1ns
% #   - clock skew is 3.39ns
% exit
OpenLane Container (4a1c799):/openlane$ exit
```


## Workshop Day 2

### Floorplaning
  * Wafer - contains many Dies
  * Package - contains a single die
  * Die - contains the logic and something like the "technological frame" for e.g. pads
  * Core - contains the implemented IC logic

#### Pin placement and logical cell placement blockage

![Logical cell blockage](/img/day2_log-cell-blockage.png)

### Floorplan in OpenLane

```bash
$ cd openLane/designs/aes128
$ # edit variables for the floorpanning step
$ editor sky130A_sky130_fd_sc_ms_config.tcl
...
$ cd ${PROJ_WORK}/tmp/openLane
$ make mount
OpenLane Container (4a1c799):/openlane$ ./flow.tcl -interactive -design aes128 -tag RUN_2022.08.04_07.33.37
```

```tcl
% run_floorplan
...
1
% exit
OpenLane Container (4a1c799):/openlane$ exit
```

  * Inspect the floorplan:
  
```bash
$ cd ${PROJ_WORK}/tmp/openLane/designs/aes128/runs/RUN_2022.08.04_07.33.37/results/floorplan
$ magic -T ${PROJ_WORK}/tmp/openLane/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read aes128.def
```

![Floorplan in Magic](/img/day2_floorplan-magic.png)

### Placement in OpenLane


```bash
$ make mount
OpenLane Container (4a1c799):/openlane$ ./flow.tcl -interactive -design aes128 -tag RUN_2022.08.04_07.33.37
```

```tcl
% run_placement
...
% exit
OpenLane Container (4a1c799):/openlane$ exit
```

  * Inspect the layout:
  
```bash
$ cd ${PROJ_WORK}/tmp/openLane/designs/aes128/runs/RUN_2022.08.04_07.33.37/results/placement
$ magic -T ${PROJ_WORK}/tmp/openLane/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read aes128.def
```
![Floorplan in Magic](/img/day2_placement-magic.png)

### Cell Characterization

![Floorplan in Magic](/img//day2_times.png)

## Workshop Day 3

  * as I've [experience](https://github.com/DDD-FIT-CTU/CMOS-PLS/tree/master_github/resistantGates) with this part of workshop, I mostly reviewed the theory provided by EDU videos.

### Inverter ngSPICE Simulations


### CMOS Fabrication Process

  * mostly covered by the equal [Udemy curse](VSD - Custom Layout) I already had
  * [Standard cell design and characterization using openlane flow by nickson-jose](https://github.com/nickson-jose/vsdstdcelldesign)
  
### Tech File Labs
  
  * Magic switch the set of DRC rules
  
```tcl
% drc style drc(fast)
% drc style drc(full)
% drc check
```

## Workshop Day 4

### Standard Cell Layout to LEF

  * the cell height must comply with power grid
  * the cell width should be an odd multiple of the minimum grid
  * the cell port must be at the intersection of the grid lines

  * grid parameteres from the pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd/tracks.info file:

```
li1 X 0.23 0.46
li1 Y 0.17 0.34
met1 X 0.17 0.34
met1 Y 0.17 0.34
met2 X 0.23 0.46
met2 Y 0.23 0.46
met3 X 0.34 0.68
met3 Y 0.34 0.68
met4 X 0.46 0.92
met4 Y 0.46 0.92
met5 X 1.70 3.40
met5 Y 1.70 3.40
```

  <layer-name> <X-or-Y> <track-offset> <track-pitch>
  
  * synthesis/mapping library example: pdks/sky130A/libs.ref/sky130_fd_sc_hd/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

### Timing Settings for Synthesys

  * The following env. variables could be tuned:
  
```
SYNTH_STRATEGY
SYNTH_BUFFERING
SYNTH_SIZING
```

### Post-Synthesys Timing Analysis - Custom Execution

  * /scripts/base.sdc file is the sdc template
  * pre_synth.conf config file example:
  
```
set cmd_units -time ns -capacitance pF -current mA -voltage V -resistance kOhm -distance um
read liberty -max PATH_TO/sky130_fd_sc_slow.lib
read liberty -min PATH_TO/sky130_fd_sc_fast.lib
read verilog ${PROJ_WORK}/tmp/openLane/designs/aes128/runs/RUN_2022.08.04_07.33.37/results/synthesis/aes128.v
link design aes128
read sdc path_to_my_updated_base.sdc
report checks -path_delay min_max -fields {slew trans net cap input_pin}
report_tns
report_wns
```

  * run OpenSTA:
  
```bash
$ sta pre_synth.conf
```
  * replace "bad" cell manually:

```tcl
% # This is OpenSTA command line
% replace cell <cell_num> <new_cell_name>
% report_checks -from <node> -to <node> -through <node>
% report_tns
% report_wns
% report_checks -fields {net cap slew input_pin}
% write_verilog <netlist>
```
  * now let's return to OpenLane and continue with P&R

### Clock Tree Synthesis

  * in OpenLane:
```tcl
% run_cts
```

  * CTS results were written to openlane/designs/aes128/runs/RUN_2022.08.04_08.32.49/results/cts/aes128.v

  
### Timing analysis inside OpenLANE

  * first create database for OpenRoad: 
  
```tcl
% openroad
...
openroad> read_lef <lef_file>
openroad> read_def <def_file>
openroad> write_db <db_file>
...
openroad> exit
...
openroad> read_db  <db_file>
openroad> read_verilog <synthetized_verilog>
openroad> read_liberty -max $::env(LIB_MAX)
openroad> read_liberty -min $::env(LIB_MIN)
openroad> read_sdc <sdc_file>
openroad> set_propagated_clock [all_clocks]
openroad> report_checks -path_delay min_max -format full_clock_expanded -digits 4
```

## Workshop Day 5

### Power Distribution Network

```tcl
% gen_pdn
...
[INFO]: PDN generation was successful.
[INFO]: Changing layout from /openlane/designs/aes128/runs/RUN_2022.08.04_08.32.49/results/cts/aes128.def to /openlane/designs/aes128/runs/RUN_2022.08.04_08.32.49/tmp/floorplan/31-pdn.def
% 
```

```bash
$ /usr/local/bin/magic -T ../../../../pdks/sky130A/libs.tech/magic/sky130A.tech lef read tmp/merged.lef def read tmp/floorplan/31-pdn.def
```

![Floorplan in Magic](/img/day5_pdn.png)

![Floorplan in Magic](/img/day5_pdn_detail.png)


### Global and Detail Routing

```tcl
% echo $::env(CURRENT_DEF)
/openlane/designs/aes128/runs/RUN_2022.08.04_08.32.49/tmp/floorplan/31-pdn.def
% set ::env(CURRENT_DEF) /openlane/designs/aes128/runs/RUN_2022.08.04_08.32.49/tmp/floorplan/31-pdn.def
/openlane/designs/aes128/runs/RUN_2022.08.04_08.32.49/tmp/floorplan/31-pdn.def
% set ::env(PL_RESIZER_DESIGN_OPTIMIZATIONS) 1
1
% set ::env(ROUTING_OPT_ITERS) 128
128
% set ::env(DETAILED_ROUTER) tritonroute
drcu
% run_routing
```

### Standard Parasitic Exchange Format (SPEF) File

  * SPEF file generation by using the [SPEF_EXTRACTOR](https://github.com/HanyMoussa/SPEF_EXTRACTOR)

  ```bash
$ cd <SPEF_EXTRACTOR_path>
$ python3 main.py <lef_file> <def_file>
```

  * in the new version of the flow, the extractor is already included and it is automatically invoked at the end of the routing stage


## References

  * [OpenLANE](https://github.com/efabless/openlane)

## Credits

  * [VLSI System Design](https://www.vlsisystemdesign.com/)
  * [efabless](https://efabless.com/)
  * [opencircuitdesign.com](http://opencircuitdesign.com/qflow/)
  * [OpenLane](https://github.com/efabless/OpenLane)

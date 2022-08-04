# Advanced Physical Design using OpenLANE/Sky130 Workshop Notes
  * efabless-sponsored [VSD-IAT workshop (2022-08-03 to 2022-08-07)](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/)

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

### Inverter ngSPICE Simulations

  * as I've [experience](https://github.com/DDD-FIT-CTU/CMOS-PLS/tree/master_github/resistantGates) with this part of workshop, I just reviewed the theory provided by EDU videos.


## References

  * [OpenLANE](https://github.com/efabless/openlane)

## Credits

  * [VLSI System Design](https://www.vlsisystemdesign.com/)
  * [efabless](https://efabless.com/)
  * [opencircuitdesign.com](http://opencircuitdesign.com/qflow/)

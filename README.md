# iiitb_wm

ASIC design of automatic washing machine

## INTRODUCTION

Washing machines are everywhere these days. In this projects we will try to implement ASIC design of Aoutomatic washing machine.

![Washing machine](https://user-images.githubusercontent.com/110079753/182373089-20ff5a7f-3fa0-4641-be5f-c933e174825f.jpg)
Above given image is the state diagram of Aoutomatic washing machine

In a specific automatic washing machine there are usually six states as given above such as, Check door, Fill water, Add detergent, Cycle, Drain water and Spin.


### About iverilog

Icarus Verilog or iverilog is an implementation of the Verilog hardware description language.

### About GTKWave

GTKWave is a fully featured GTK+ v1. 2 based wave viewer for Unix and Win32 which reads Ver Structural Verilog Compiler generated AET files as well as standard Verilog VCD/EVCD files and allows their viewing.

### Downloading iberilog and gtkwave

#### For Ubuntu

Open your terminal and write following command.

```
 sudo apt update
 sudo apt install iverilog
 sudo apt install gtkwave
```

### Functional Simulation
To clone the Repository and download the Netlist files for Simulation, enter the following commands in your terminal.
```
   sudo apt install -y git
   git clone $ https://github.com/archandesai/iiitb_wm
   cd iiitb_wm
   iverilog iiitb_wm.v iiitb_wm_tb.v
   ./a.out
   gtkwave iiitb_wm_tb.vcd
```
## Synthesis of verilog code

#### About Yosys
Yosys is a framework for Verilog RTL synthesis. It currently has extensive Verilog-2005 support and provides a basic set of synthesis algorithms for various application domains
```
   sudo apt-get update
   sudo apt-get -y install yosys
```
to synthesize
```
   yosys -s yosys_run.sh
```
## GLS - Gate Level Simulation
GLS is generating the simulation output by running test bench with netlist file generated from synthesis as design under test. Netlist is logically same as RTL code, therefore, same test bench can be used for it.

Below picture gives an insight of the procedure. Here while using iverilog, you also include gate level verilog models to generate GLS simulation.

![image](https://user-images.githubusercontent.com/72696170/183296780-4bad9547-69e9-4cee-b791-acb5a38951bf.png)


## SIMULATION
Pre - synthesis simulation waveform:
![Screenshot from 2022-08-16 23-17-02](https://user-images.githubusercontent.com/110079753/184945366-80562068-8839-4a1b-b7a5-772d8141741f.png)

Post - synthesis simulation waveform:
![Screenshot from 2022-08-16 14-29-24](https://user-images.githubusercontent.com/110079753/184944682-8e1a9e65-c063-497a-8d3c-90cfb19ebea6.png)


### Creating a Custom Inverter Cell
To create custom inverter cell we have to follow following steps
```
 git clone https://github.com/nickson-jose/vsdstdcelldesign.git
 cd vsdstdcelldesign
 cp ./libs/sky130A.tech sky130A.tech
 magic -T sky130A.tech sky130_inv.mag &
```
This is the layout of the inverter.
![vsdinv](https://user-images.githubusercontent.com/110079753/187478897-012d32bf-b122-489b-9c47-f0c9eca16976.png)


## PHYSICAL DESIGN
For getting physical design we have to do following steps:

1) RTL design
2) Synthesis
3) Floorplanning
4) Placement
5) Clock tree synthesis
6) Routing
7) Signoff
8) Tapeout


###OpenLane

OpenLane is an automated RTL to GDSII flow based on several components including OpenROAD, Yosys, Magic, Netgen, CVC, SPEF-Extractor, CU-GR, Klayout and a number of custom scripts for design exploration and optimization. The flow performs full ASIC implementation steps from RTL all the way down to GDSII.

First you have to install prerequsites.
```
 apt install -y build-essential python3 python3-venv python3-pip
 
```
The docker installation is given here: https://docs.docker.com/engine/install/ubuntu/

```
  git clone https://github.com/The-OpenROAD-Project/OpenLane.git
  cd OpenLane/
  sudo make
  sudo make test
```
### MAGIC

Magic is a venerable VLSI layout tool, written in the 1980's at Berkeley by John Ousterhout, now famous primarily for writing the scripting interpreter language Tcl. Due largely in part to its liberal Berkeley open-source license, magic has remained popular with universities and small companies. The open-source license has allowed VLSI engineers with a bent toward programming to implement clever ideas and help magic stay abreast of fabrication technology. However, it is the well thought-out core algorithms which lend to magic the greatest part of its popularity. Magic is widely cited as being the easiest tool to use for circuit layout, even for people who ultimately rely on commercial tools for their product design flow. More about magic at http://opencircuitdesign.com/magic/index.html

Run following commands one by one to fulfill the system requirement.

```
 sudo apt-get install m4
 sudo apt-get install tcsh
 sudo apt-get install csh
 sudo apt-get install libx11-dev
 sudo apt-get install tcl-dev tk-dev
 sudo apt-get install libcairo2-dev
 sudo apt-get install mesa-common-dev libglu1-mesa-dev
 sudo apt-get install libncurses-dev
```
Then we have to do following steps to instal magic.

```
 git clone https://github.com/RTimothyEdwards/magic
 cd magic/
 sudo make
 sudo make install
```


### LAYOUT
In this section we will see layout of the projects using openlane and magic for different steps.

#### PREPERATION

To get layout of the project first we have to set few things.
```
 cd OpenLane
 cd design
 mkdir iiitb_wm
 cd iiitb_wm
 mkdir src
```
Here, We have to add ``` config.json ``` file in the iiitb_wm directory. And we have to add following file in src directory : ```iiitb_wm.v```, ```sky130_fd_sc_hd__fast.lib```,```sky130_fd_sc_hd__typical.lib```,```sky130_fd_sc_hd__slow.lib``` and ```sky130_vsdinv.lef```.

Now we have to do following steps:

```
 cd OpenLane
 sudo make mount
 ./flow.tcl -interactive
 package require openlane 0.9
 prep -design iiitb_wm
 set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
 add_lefs -src $lefs
```

#### Synthesis
In this part we will do sythesis of the project using following code:
```
 run_synthesis
```
This is synthesized output:

![Screenshot from 2022-08-30 15-43-32](https://user-images.githubusercontent.com/110079753/187411613-0130c3ac-e316-477f-9e8f-87ef52b712b5.png)


### FLOORPLANNING
In floorplanning we will do following steps:
```
 run_floorplan
```
Then we will go to ``` results/floorplan``` and type following command on terminal
```
 magic -T /home/archan/OpenLane/pdks/sky130A/libs.tech/magic/sky130A.tech lef read /home/archan/OpenLane/designs/iiitb_wm/runs/RUN_2022.08.30_11.56.04 ../../tmp/merged.nom.lef def read iiitb_wm.def &
 ```
 We will get following floorplan
 ![floorplan](https://user-images.githubusercontent.com/110079753/187462644-c6700dce-a8f1-4724-961d-6ce1414702f6.png)

#### PLACEMENT
In placement we will do following steps:
```
 run_placement
```
Then we will go to ``` results/placement``` and type following command on terminal
```
 magic -T /home/archan/OpenLane/pdks/sky130A/libs.tech/magic/sky130A.tech lef read /home/archan/OpenLane/designs/iiitb_wm/runs/RUN_2022.08.30_11.56.04 ../../tmp/merged.nom.lef def read iiitb_wm.def &
```
We will get following layout

![Screenshot from 2022-08-30 15-38-20](https://user-images.githubusercontent.com/110079753/187410773-7a725e44-60e4-4051-86c0-a89c21daef4a.png)
![Screenshot from 2022-08-30 15-37-43](https://user-images.githubusercontent.com/110079753/187410707-caa29762-7b8d-44f9-ba0a-531ed186e761.png)


#### CLOCK TREE SYNTHESIS
In this section we wil do following step:
```
 run_cts
 ```
We will get following reports after clock tree synthesis
![cts1](https://user-images.githubusercontent.com/110079753/187482546-a60cca50-3da1-4c6e-aa12-e2faf5973149.png)
![cts2](https://user-images.githubusercontent.com/110079753/187482593-d4c49b16-1994-402b-a213-64949414d9db.png)

 
 #### ROUTING
 In this step we will do routing of our projects using:
 ```
  run_routing
 ```
 
Then we will go to ``` results/routing``` and type following command on terminal
```
 magic -T /home/archan/OpenLane/pdks/sky130A/libs.tech/magic/sky130A.tech lef read /home/archan/OpenLane/designs/iiitb_wm/runs/RUN_2022.08.30_11.56.04 ../../tmp/merged.nom.lef def read iiitb_wm.def &
```
 
 We will get following layout after routing.
 ![routing](https://user-images.githubusercontent.com/110079753/187477803-40f8c3d5-0c85-40e4-8960-bfdb3a9dbff7.png)
 
 ## Results
 
 ### Area
 To get area of the design after rounting we will use ``` box ``` command in magic termina
 
 This is an area of our design.
 
 ![exam_box1](https://user-images.githubusercontent.com/110079753/192448294-26915eab-60c8-407f-92e0-916f98ed4248.png)
 
 ### Performance
 In this section we will find performance of the design for that we have to follow foloowing step:
 ```
 cd OpenLane
 sudo make mount
 sta
 read_liberty -max /home/archan/OpenLane/pdks/sky130A/libs.ref/sky130_fd_sc_hd/lib/sky130_fd_sc_hd__ff_n40C_1v56.lib
 read_liberty -min /home/archan/OpenLane/pdks/sky130A/libs.ref/sky130_fd_sc_hd/lib/sky130_fd_sc_hd__ff_n40C_1v56.lib
 read_verilog /home/archan/OpenLane/pdks/sky130A/libs.ref/sky130_fd_sc_hd/lib/iiitb_wm.v
 link_design iiitb_wm
 read_sdc /home/archan/OpenLane/pdks/sky130A/libs.ref/sky130_fd_sc_hd/lib/iiitb_wm.sdc
 read_spef /home/archan/OpenLane/pdks/sky130A/libs.ref/sky130_fd_sc_hd/lib/iiitb_wm.spef
 set_propagated_clock [all_clocks]
 report_checks

 report_checks -form _75_ -to _75_
 ```
 ![exam_slack](https://user-images.githubusercontent.com/110079753/192700327-c4363bca-5c7e-42e3-8dba-cd42c6424a97.png)
 
``` 

Performance = 1/(data required time - slack)
Performance = 1/(64.83 - 62.05)
Performance = 0.3597 GHz
```
### Power
This is a power analysis of our design.
![exam_power](https://user-images.githubusercontent.com/110079753/192704337-cd0a223c-eacd-4af1-b511-8b44586cced7.png)

### Total Cells
![exam_stat3](https://user-images.githubusercontent.com/110079753/192704463-841f25fc-ef90-44d9-8daa-b5a9019befe2.png)
 
```
Flip-flop to standard cell ratio = Flip-flop / Total cell
Flip-flop to standard cell ratio = 3/43
Flip-flop to standard cell ratio = 6.98%
```
 

## Contributors 


- **Archan Desai** 
- **Kunal Ghosh** 
- **Rakshit Bhatia**

- **Ishan Desai**
- **Arsh Kedia** 


## Contact Information

- Archan Desai, Postgraduate Student, International Institute of Information Technology, Bangalore  archan.desai00@gmail.com
- Kunal Ghosh, Director, VSD Corp. Pvt. Ltd. kunalghosh@gmail.com




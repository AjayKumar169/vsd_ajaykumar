# vsd_ajaykumar
# 1 Day1 (Summary): Inception of opensource EDA, OpenLane & Sky130 PDK
## 1.1 How to talk to computers:
Understanding of the basics is summarized in the below screenshots (where my inception/understanding of the concepts is also added as comments)
![image](https://user-images.githubusercontent.com/30960084/224394031-bf79d6e8-81cf-488c-a4fa-391695b5d970.png)
![image](https://user-images.githubusercontent.com/30960084/224394260-d8129a6a-9233-4cb3-a2a1-225e0c9f98be.png)
![image](https://user-images.githubusercontent.com/30960084/224394299-a68fa0c6-94cc-4d25-a067-110c18c6abc7.png)
![image](https://user-images.githubusercontent.com/30960084/224394594-0050b007-7328-4958-9c1b-81f1905da57b.png)

## 1.2 SOC Design and Openlane (Summary)
Here we learnt on how the complete flow is made opensource:
  a. We need RTL designs, EDA tools and PDK data (as opensource for enabling the OpenSource RTL->GDSII flow)
  b. OpenLane is nothing but a flow which combines above mentioned aspects under a single umbrella.
 ![image](https://user-images.githubusercontent.com/30960084/224560463-bf395bf0-8e61-4057-b544-ead5d69e8721.png)

## 1.3 Get familiar to open-source EDA tools:

Firstly, here is how the complete directory structure goes (had created local notes share the text itself here):

---------------------------------------------
OPENLANE RUN AREA (Basic Directory Structure)
---------------------------------------------
	Desktop->work->tools->openlane_working_dir->openlane

	Desktop->work->tools->openlane_working_dir->openlane->designs   (has close to 30-40 designs)
	Desktop->work->tools->openlane_working_dir->openlane->designs->picorv32a
																	src   (contains verilog and sdc files i.e. RTL input is here)
																	sky130A_sky130_fd_sc_hd_config.tcl    (eda specific config file, no present in starting, has high priority then config.tcl)
																	config.tcl
																	runs	(This dir is created post design prep stage) 

	Desktop->work->tools->openlane_working_dir->openlane->designs->picorv32a->src
																		picorv32a.v
																		picorv32a.sdc
																		
	Desktop->work->tools->openlane_working_dir->openlane->designs->picorv32a->runs    (This dir is created post design prep stage)
																		folder_with_current_date created
															
	Desktop->work->tools->openlane_working_dir->openlane->designs->picorv32a->runs->current_date    (everything except tmp would be empty)
																			cmds.log		(record of commands fired by us)
																			tmp
																			results      (synthesis, placement, floorplan, routing, cts, magic, lvs)
																			reports      (synthesis, placement, floorplan, routing, cts, magic, lvs)
																			logs
																			config.tcl		(shows which all default parameters were taken by the run)
	
  
-----------------------------------------------
PROCESS DESIGN KIT (PATHS) and their details
-----------------------------------------------
	Desktop->work->tools->openlane_working_dir->pdks
									skywater-pdk          (has all the PDK related files, lef, timing etc all files
									open_pdks			  (foundry files are made to work with commericial EDA tools not for opensource EDA files, 
														   open_pdks are a set of scripts or files that convert foundry files to be compatible 
														   with opensource EDA tools)
									sky130A				   ( this is the output of open_pdks scripts which made the foundry files compatible to opensource EDA's)

NOTE: PDK that we are going to use is (skywater130nm) can also use (osu130nm) in openlane
			
	Desktop->work->tools->openlane_working_dir->pdks->sky130A
													libs.ref		(all the process technology files)
													libs.tech		(files more specific to the tool)
			
	Desktop->work->tools->openlane_working_dir->pdks->sky130A->libs.ref
																sky130_fd_sc_hd    fd->foundry   sc->standard cell   hd-> high density
	
	Desktop->work->tools->openlane_working_dir->pdks->sky130A->libs.tech
																klayout, ngspice, netgen etc
																
	Desktop->work->tools->openlane_working_dir->pdks->sky130A->libs.ref->sky130_fd_sc_hd
																verilog, spice, techlef, maglef, mag, gds, doc, cdl, lib, lef
																
	Desktop->work->tools->openlane_working_dir->pdks->sky130A->libs.ref->sky130_fd_sc_hd->lib
																		sky130_fd_sc_hd__ff_100C_1v80.lib		
																		
																		1v80-> voltage
																		100C-> temp
																		tt-> typical, ss-> slow slow, ff-> fast fast
  
  
Secondly, here is how the labs were ran: (This portion includes the launching of openlane and then run_synthesis step screenshots):
  1. Launching openlane:
  ![image](https://user-images.githubusercontent.com/30960084/224561096-57202708-51d8-4000-be6b-932cfd6666a5.png)
  2.Synthesis:
  ![image](https://user-images.githubusercontent.com/30960084/224561190-c854e880-f4d7-40cc-b53a-5257fb9eb8de.png)
  
# 2 Day2 (Summary): Good Floorplan V/S Bad Floorplan and introduction to library cells:
## 2.1 Floorplanning includes the following stages:
### Define Width and Height of Core and Die:
1. Here we got familiar to certain terminologies like (Die&Core area, core utilization, aspect ratio etc.
2. For deciding the Width and Height we basically take Utilisation factor into account.
3. Utilisation factor of 1 means there is no scope of adding more cells. Typical value of 0.5-0.7 core utilisation is seen in several designs.
4. Aspect ratio of 1 means that the cell is a square shaped cell in lib else a rectangular cell.

### Define locations of preplaced cells
1. We don't want these cells to be touched during the placement stage.
2. Location of these cells is pre-decided based on their connectivity with other cells & boundary.

### Surround the preplaced cells with decaps
1. Preplaced cells might demand a huge current while switching.
2. And power grid should be able to cater the demand current need:
     i. As a solution we add decaps arround them which serve the role of catering the demand current in addition to the power grid.
     ii. When block is not switching or demand current is low, decaps would charge them.
3.Here is the detailed study of working of decaps:
![image](https://user-images.githubusercontent.com/30960084/224562205-c49aa603-19a1-4937-b4a1-34b5aec5300b.png)

### Power Planning
1. We create power/ground mesh to provide power efficiently to the total demand.
2. There might be 1000s of cells switching at a particular time, our power grid should be capable of efficiently providing the demand current.

### Pin Placement
1. Here we identify the input/output pins in the design and place the pins in the die area.
2. Pin placement requires little bit of hand shaking between the frontend team and the backend team.
   i. Front END:	Front end team decides the netlist and connectivity of the design.
   ii. Back END:  Back end team decides the pin placement 
3. CLOCK PINS ARE WIDE:
   i. CLOCK IS THE PORT WHICH IS DRIVING ALL THE CELLS CONTINUOUSLY.
   ii. WE NEED LEAST RESISTANCE PATH FOR THE CLOCKS.

### Logical Cell Placement Blockage

This is to ensure that no logical cell is placed in the pin area.

### LABS FLOORPLAN (OPENLANE)
![image](https://user-images.githubusercontent.com/30960084/224562859-d51a91c9-f3c1-4102-90d1-0e63de3ff534.png)

Output of Floorplan in results dir:
![image](https://user-images.githubusercontent.com/30960084/224563048-d01cf3ee-f04b-42f2-aa72-1171b9da5ccc.png)

Design in magic:
![image](https://user-images.githubusercontent.com/30960084/224563410-2c6386cc-6307-49ab-a76e-5ca477d4ab84.png)

![image](https://user-images.githubusercontent.com/30960084/224563487-c4d4a3b8-2cf5-4a80-9b45-617928a6e667.png)
NOTE: (TAP cells are inserted to avoid latchup)

## 2.2 Placement & Routing:
### 2.2.1 Bind netlist with physical cells
For binding the netlist with physical cells we will need libraries for the cells.

Library file has:
	shapes and sizes
	delay and timing information
	required conditions (when condition) under which condition the output is shown for a input
	For the same gate there would be different sizes also present (bigger size cells would be faster since it has least resistance path)
![image](https://user-images.githubusercontent.com/30960084/224563757-72deadff-cd03-41de-8e05-438b07e5aed1.png)


### 2.2.2 Placement:
Now we will place the mapped cells from the library over the die.
Here are two types of placement (IN openlane flow) we are firstly focusing on the global placement.
Global Placement - Optimized but not legal placement. Optimization works to reduce wirelength by reducing half parameter wirelength
Detailed Placement - Legalizes placement of cells into standard cell rows while adhering to global placement

Here is a summary of labs (sharing the screenshot):
![image](https://user-images.githubusercontent.com/30960084/224563951-bfe55328-722d-4638-abae-325eb8adfda9.png)


### 2.2.3 Optimize Placement

Here is my understanding of: WHY optimisation is needed:
![image](https://user-images.githubusercontent.com/30960084/224564034-cfc6e3cb-e8cc-402d-8a93-b754c3cd4a55.png)

### LABS (PLACEMENT OPENLANE):
![image](https://user-images.githubusercontent.com/30960084/224564111-20864dac-9df0-4b2e-ab0a-7226396f8d04.png)

OUTPUT DIR:
![image](https://user-images.githubusercontent.com/30960084/224564351-c707b15d-a45b-4ddc-818f-97916a7422bc.png)


DESIGN IN MAGIC:
![image](https://user-images.githubusercontent.com/30960084/224564431-e16f8979-7e68-4fc4-9b77-691ef0f407ad.png)
![image](https://user-images.githubusercontent.com/30960084/224564452-2f6462e4-a260-4915-ac56-b6b19108caa2.png)

# 2.3 Standard Cell Design Flow:
![image](https://user-images.githubusercontent.com/30960084/224564628-38d7f4e8-3ec8-40a1-b2d3-dfd111b1c411.png)

We design cell in 3 parts:

Inputs - PDKs (Process design kits), DRC & LVS rules, SPICE models, library & user-defined specs.

Design Steps - Design steps of cell design involves Circuit Design, Layout Design, Characterization. The software GUNA used for characterization. The characterization can be classified as Timing characterization, Power characterization and Noise characterization.

Outputs - Outputs of the Design are CDL (Circuit Description Language), GDSII, LEF, extracted Spice netlist (.cir), timing, noise, power.libs, function.

# 2.3.1: INPUTS
PDKS. DRC & LVS rules:
	For every process we have some Design Rules for which we do the DRC checks and then post that we compare layout with schematic using LVS.
SPICE MODELS
	Here the device parameters like gamma, Vth etc are contained for the transistor that we include in our SPICE netlist.
	
Library&UserDefined Specs
![image](https://user-images.githubusercontent.com/30960084/224564993-392afff3-5443-49bc-959a-8f0f7cc1a8b5.png)

# 2.3.2: Design Steps:
Circuit Design
---------------
1. Implement the function itself.
2. Model the PMOS and NMOS transistor to meet the library requirements
3. Mostly based on SPICE SIMULATIONS

OUTPUT:
CDL: Circuit Description Language

Layout Design
---------------
![image](https://user-images.githubusercontent.com/30960084/224565278-fba5b910-ea68-472e-9077-fd1925980a19.png)



Characterization
----------------
1. Extract the parasitics and characterize it in terms of timing.
2. Output: 
	Timing, noise, power .libs, function

![image](https://user-images.githubusercontent.com/30960084/224565332-a1bcea23-7b77-47de-a806-ba788eef0d3a.png)

TIMING CHARATERISATION:
------------------------
SLEW:
slew_high_rise_thr: 20% from the logic 1 level
slew_low_rise_thr : 20% from the logic 0 level
slew_low_fall_thr : 20% from logic 0 level of a fall
slew_high_fall_thr : 20% value from logic 1 of a fall
![image](https://user-images.githubusercontent.com/30960084/224565894-5ea78095-52a2-4f8c-87eb-b5e89a4afed5.png)

TRANSITION TIME:
For rising waveform:
Transition time = time(slew_high_rise_thr) - time(slew_low_rise_thr)
![image](https://user-images.githubusercontent.com/30960084/224566028-5472719b-696e-4c6e-ac95-7a84dd1ae6dd.png)

PROPAGATION DELAY:
![image](https://user-images.githubusercontent.com/30960084/224566076-2674ee43-8976-4b29-aaf8-1675252205f9.png)

*** NEGATIVE DELAYS (UNDERSTANDING):
![image](https://user-images.githubusercontent.com/30960084/224566124-d76f2c63-cd8f-4cbf-a8f9-d5e24b233910.png)






# 3 Day3 (Summary): Design library cell using Magic Layout and ngspice characterization:

SPICE DECK CREATION FOR CMOS INVERTER:

SPICE FORMAT:
![image](https://user-images.githubusercontent.com/30960084/224566528-262006f9-2045-4af8-9f84-ea5a6e468d3c.png)


# 4 Day 4: Pre Layout timing analysis and importance of good clock tree

## Clock tree routing and buffering using H-Tree alogrithm

### Clock Tree Syntheis:
Skew: Is the difference in the time taken by clock to reach different flipflops or the difference between the latency of two clock inputs. Skew should be closer to zero for good timing analysis.
![image](https://user-images.githubusercontent.com/30960084/225530516-e958b6d9-97ee-469f-b6a2-419339f28843.png)

For achieving close to zero skew we have a concept named H-Tree:
In HTree we follow the midpoint strategy so that the time taken by the clock to reach every clock input is almost same and the skew is very close to zero.
Here is the example of HTree:
![image](https://user-images.githubusercontent.com/30960084/225530834-c110ae6b-c168-4e92-87ce-a1cf467676c3.png)

Clock Tree Buffering:
Problem: When a signal is being sent to a clock period, it has to travel the complete length of the wire and charge the capacitances over the path and that might lead to signal transistion time being very bad.
![image](https://user-images.githubusercontent.com/30960084/225531244-303f946d-0b8e-420d-bd37-d4497893854e.png)

Solution: Insert repeaters over the clock tree. Repeaters are nothing but buffers, only difference between the repeaters that we use for clock and the repeaters that we use for the data path is that, the clock repeaters have same rise & fall times.

### Clock Net Shielding
Clock nets are one of the critical nets in our design.
Cross talk phenomenan can happen on our clock net.
If there is huge coupling capacitance between a clock net and some neighboring switching net, we might see two problems:
	1. Glitch : Due to an aggressor our victim net (clock net) might show different result (logic 0 in place of 1 or vice verca), and that's due to switching happening on the aggressor.	![image](https://user-images.githubusercontent.com/30960084/225533846-38f6b428-d498-49f2-9b3a-24cff540ef90.png)
	2. Delta Delay: When both aggressor and victim switch we might see a delta delay during charging/discharging.


	

Solution:
For shielding we take a particular clock net and encapsulate it to protect it from the outside world. We shield the clock net with some VDD or VSS net between the aggressor and victim. And since the VDD,VSS net won't swtich, glitch and delta delay would not happen. 

NOTE: If a data net is also critical, we should shield them as well.

### Timing Analysis (With Real Clocks)
When it comes to Timing analysis with real clocks, we start considering the delay of the clock buffers also into the setup time equation.
![image](https://user-images.githubusercontent.com/30960084/225538632-063a846b-d9b2-425d-8265-9bbf5cddc317.png)


Data Required Time: Is the time by which the data is expected to be present.
Data Arrival Time: Is the time at which the data arrives.
Slack=Data Required Time - Data Arrival Time
NOTE: Negative Slack is a violation and we should try to make it zero or positive.

### Hold Timing Analysis
Hold Time Refers to the second MUX delay in the capture Flop
![image](https://user-images.githubusercontent.com/30960084/225540311-70f9e1ad-2a9f-4c11-9ccc-299722c972ae.png)

Second MUX in the capture flop basically wants the data to be holded by first MUX till it sends the data in mid to the output. That's the hold time definition. 
![image](https://user-images.githubusercontent.com/30960084/225540961-956ff408-5052-4703-9448-e6ade10f0e83.png)

NOTE: For Hold Time Analysis Uncertaininty doesn't matter much because the clock edge going to the launch and the capture flop is same. 

For Hold Timing Analysis:
Slack= Data Arrival Time -Data Required Time

# 5 Day 5: Final Steps for TRL2GDS using tritonRoute and openSTA

## ROUTE
We need to form route between two points for data path. 

### LEE's Algorithm
One Algorithm for getting the routing is Maze Routing - Lee's Algorithm [Lee 1961]

Algorithm:
Basically we divide our chip into a grid.
The grid would be ignored for pre-placed cell region and the PIN region.
Then we will mark the two points between which we want to have the route as SOURCE & TARGET.
From Source we will start numbering as  1 for the horizontal/vertical adjacent boxes only. (Not the diagnols)
We will continue the count till we reach the target.
Then we form a path in a continous manner from 1 to n where n is number touching the target.
Finally, out of all the possiblities we will pick up the one having least bends.

![image](https://user-images.githubusercontent.com/30960084/225555506-019ba598-befc-4345-9303-99a9dede348c.png)


### DRC Clean (Design Rule Check)

Rule says that : Whenever we try to build two wires, there should be a minimum distance/spacing between the two wires.
Some examples of rules are:
1. Wire Width: Minimum width of wire should be some x number. Basically there are some techniques to build the wires, like photolithography, these use light to form the wires, light has got certain minimum wavelength, and using that wavelength we can draw certain minimum width of the wire.
2. There should be some minimum pitch between two wires.
3. Minimum wire spacing is also one of the rules.
4. There are several other rules which are considered as DRC rules.

When we go to multiple layers we increase the DRC rules:
1. VIA Width
2. VIA Spacing

### PARASITIC EXTRACTION

Resistances and Capacitances on the drawn geometries are done and used for further process.

# Labs:

# OpenLane directory strucutre in detail (Day 1: Session 3: Lecture 1)

openlane_working_dir:
![image](https://user-images.githubusercontent.com/30960084/225564853-74c35728-8f03-409c-9d6f-19f1e89f2e55.png)

pdks:
![image](https://user-images.githubusercontent.com/30960084/225565037-3af5d122-33ee-4d97-8a60-6fc15e5e8042.png)

sky130A:
![image](https://user-images.githubusercontent.com/30960084/225565843-fff433c7-56fa-4c13-aa0e-73a24f52a461.png)

libs.ref:
![image](https://user-images.githubusercontent.com/30960084/225566422-86b3093d-bcab-45db-9a52-564b054340fd.png)

libs.tech:
![image](https://user-images.githubusercontent.com/30960084/225566693-c8d5adbe-91fd-4de3-9f18-1e9faeb59fa8.png)

libs.ref/sky130_fd_sc_hd:
![image](https://user-images.githubusercontent.com/30960084/225567336-5c2af2af-60cb-4db0-b318-bad4322dd404.png)

libs.ref/sky130_fd_sc_hd/lib:
![image](https://user-images.githubusercontent.com/30960084/225567972-1d220505-ce83-4875-8192-c9537d65cb0e.png)

lef and tech lef in libs.ref/sky130_fd_sc_hd/:
![image](https://user-images.githubusercontent.com/30960084/225568459-b99a61fd-e5fe-4caf-a017-4e6be5f7fea1.png)

openlane:
![image](https://user-images.githubusercontent.com/30960084/225571076-342713da-1a3d-46ac-b5cd-45cba79109bf.png)

openlane/designs: 
contains close to 40-44 designs
![image](https://user-images.githubusercontent.com/30960084/225571719-9ecfc55b-bea0-474a-878c-66b61c8893cd.png)

NOTE: EVERY RUN SHOULD BE DONE IN openlane directory

# Design Preparation Step (Day 1: Session 3: Lecture 2)

picorv32a folder:
![image](https://user-images.githubusercontent.com/30960084/225572348-643e0b5a-ea28-4e70-b488-c60e359de3a6.png)

picorv32a/src:
![image](https://user-images.githubusercontent.com/30960084/225572967-5853ce9b-9fad-4477-b0e8-3d6de9df733a.png)

config.tcl:
![image](https://user-images.githubusercontent.com/30960084/225573270-f5750751-160c-4da4-a7af-ce064fd2d600.png)

Precedence of .tcl files:
default < config.tcl < sky_130A_sky130_fd_sc_hd_config.tcl

INVOKING OPENLANE:
![image](https://user-images.githubusercontent.com/30960084/225575034-ab33626a-3c1b-4624-b37c-e6a6aa1116cf.png)

PREPARE DESIGN IN OPENLANE:
This step merges the cell level LEF and the Layer tech lef into one....
![image](https://user-images.githubusercontent.com/30960084/225575270-7f8284d6-5efe-48f5-be78-8c219a536037.png)

# Review Files after design Prep and run synthesis:

runs directory is created post design prep stage:
![image](https://user-images.githubusercontent.com/30960084/225575987-7c3898d3-c8a4-4d43-befd-86753ec79c20.png)

Inside runs directory we will see a current date folder created which contains our run results:
![image](https://user-images.githubusercontent.com/30960084/225576264-2ee1381c-1ea1-46a9-a94e-d331b01cf88b.png)

Contents of Current Date folder:
![image](https://user-images.githubusercontent.com/30960084/225576456-f60efbb2-10e9-4c2d-a3c6-ccee2245a8e4.png)

NOTE: At this stage everything except the tmp folder would be empty:

tmp folder:
![image](https://user-images.githubusercontent.com/30960084/225576844-272ca910-5d4b-4cc7-b9e3-929bab5f104e.png)

tmp/merged.lef:
would contain both:
1. Layer Information:![image](https://user-images.githubusercontent.com/30960084/225577287-be33e998-9aa0-4020-9ce0-ce9cf4ec0adc.png)
2. Cell Information: ![image](https://user-images.githubusercontent.com/30960084/225577476-f6a80adc-4338-43db-9b28-5f1afca5208c.png)

results and reports directory are also created post prepare design step: (As of now nothing would be there in stage wise directories inside these)
![image](https://user-images.githubusercontent.com/30960084/225578187-ef3cc36f-8345-4867-941a-50de5c95ad5c.png)

config.tcl file in current date folder would contain final parameters used:
![image](https://user-images.githubusercontent.com/30960084/225578863-78aad5cb-b494-48cf-8b92-a40f02d755a9.png)

### RUN SYNTHESIS:
This will invoke both YOSIS syntesis and abc.
![image](https://user-images.githubusercontent.com/30960084/225579314-b93a0125-585f-4391-9e09-826e4998d071.png)
![image](https://user-images.githubusercontent.com/30960084/225579612-a4d39d1f-09a2-48bd-8df9-b3262e572c0c.png)
![image](https://user-images.githubusercontent.com/30960084/225579796-6d2df2c6-5219-49f8-8343-89b94074bcf9.png)

Openlane documentation: https://github.com/efabless/openlane

# Steps to charaterize synthesis results (Day 1: Session 3: Lecture 5)
Objective 1: Calculate the flop ratio: Number of dff/ total cells

Sol: ![image](https://user-images.githubusercontent.com/30960084/225581294-ba2dab69-cd27-4a25-a630-ab420a9904bd.png)

1613/14876=0.108429 
10.8429%

results/synthesis folder post run_synthesis step:
![image](https://user-images.githubusercontent.com/30960084/225581826-4eeb54dd-1042-47bf-b19c-8d419f20b2f5.png)

Syntesised Netlist is as shown below:
![image](https://user-images.githubusercontent.com/30960084/225582060-a60be366-7e7e-43da-bf16-ad233dce160e.png)

reports/synthesis folder post run_synthesis step:
![image](https://user-images.githubusercontent.com/30960084/225582817-3e96e2a9-69e6-48d6-86a7-051c8fc6f5e5.png)

yosis_4.stat.rpt for synthesis results:
![image](https://user-images.githubusercontent.com/30960084/225583079-1a5b0eb9-16b5-4667-9477-53d5dd50a89b.png)

2-opensta.rpt (For STA report which is pre layout):
![image](https://user-images.githubusercontent.com/30960084/225583604-e2f9e96e-26d6-4279-b881-faf519e05e56.png)

2-opensta.timing.rpt:
![image](https://user-images.githubusercontent.com/30960084/225583795-44be214b-76ac-44f4-8387-43dc9bf56c26.png)
![image](https://user-images.githubusercontent.com/30960084/225583845-e3e0bc7b-f0fb-4991-8ea2-4e67623d357a.png)


Day 4: Session 3: Lecture 3
Lab steps to run CTS using TritonCTS

Day 4: Session 3: Lecture 4
Lab steps to verify CTS runs

Day 4: Session 4: Lecture 3
Lab steps to analyze timing with real clocks using OpenSTA

Day 4: Session 4: Lecture 4
Lab steps to execute OpenSTA with right timing libraries and CTS assignment

Day 4: Session 4: Lecture 5
Lab steps to observe impact of bigger CTS buffers on setup and hold timing

Day 5: Session 2: Lecture 1
Lab steps to build power distribution network

Day 5: Session 2: Lecture 2
Lab steps from power straps to std cell power

Day 5: Session 2: Lecture 3
Basics of global and detail routing and configure TritonRoute

Day 5 : Session 3 all three lectures

# vsd_ajaykumar
# NOTE: I've first covered the theory parts and later summarised all the labs
# THEORY
# =========
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


### 2.2.3 Optimize Placement

Here is my understanding of: WHY optimisation is needed:
![image](https://user-images.githubusercontent.com/30960084/224564034-cfc6e3cb-e8cc-402d-8a93-b754c3cd4a55.png)


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

## 16 Mask CMOS Process Steps
Substrate Selection : Selection of base layer on which other regions will be formed.
Create an active region for transistors : SiO2 and Si3N2 deposited. Pockets created using photoresist and lithography.
Nwell & Pwell formation : Pwell uses boron and nwell uses phosphorus. Drive in diffusion by placing it in a high temperature furnace.
Creating Gate terminal : For desired threshold value NA (doping Concentration) and Cox to be set.
Lightly Doped Drain (LDD) formation : LDD done to avoid hot electron effect and short channel effect.
Source and Drain formation : Forming the source and drain.
Contacts & local interconnect Creation : SiO2 removed using HF etch. Titanium deposited using sputtering.
Higher Level metal layer formation : Upper layers of metals deposited.


# 4 Day 4: Pre Layout timing analysis and importance of good clock tree

![image](https://user-images.githubusercontent.com/30960084/225734257-5606b23e-4bb6-4ce1-8a0f-574a80d01dfe.png)
![image](https://user-images.githubusercontent.com/30960084/225734505-7248add1-88ff-4ff7-b389-0995b7723f99.png)

# STA with ideal clocks
![image](https://user-images.githubusercontent.com/30960084/225734718-88ae3985-fd6d-46e3-978f-8bf7474b358d.png)


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


# =========
# Labs:
# =========

# OpenLane directory strucutre in detail (Day 1: Session 3: Lecture 1)

openlane_working_dir:
![image](https://user-images.githubusercontent.com/30960084/225564853-74c35728-8f03-409c-9d6f-19f1e89f2e55.png)

pdks:
![image](https://user-images.githubusercontent.com/30960084/225565037-3af5d122-33ee-4d97-8a60-6fc15e5e8042.png)

sky130A:
![image](https://user-images.githubusercontent.com/30960084/225565843-fff433c7-56fa-4c13-aa0e-73a24f52a461.png)

libs.ref:
![image](https://user-images.githubusercontent.com/30960084/225584278-9e0437da-049e-45a4-a019-884a5072c016.png)


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
Layer Information:
![image](https://user-images.githubusercontent.com/30960084/225577287-be33e998-9aa0-4020-9ce0-ce9cf4ec0adc.png)

Cell Information:
![image](https://user-images.githubusercontent.com/30960084/225577476-f6a80adc-4338-43db-9b28-5f1afca5208c.png)

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

# Steps to run floorplan using openlane (Day 2: Session 1: Lecture 6)

We have several switches in openlane which we can use to control the floorplan results:
We can see the switches in configuration directory in openlane:
![image](https://user-images.githubusercontent.com/30960084/225585788-47b97c4a-e73b-47c1-b705-7fa6256d051f.png)

configuration folder :
Here for each stage we contain the default parameters.
![image](https://user-images.githubusercontent.com/30960084/225586160-b6f8ea60-0c97-40e9-810f-6783ad1f12ef.png)

README file in configuration folder contains the details of each switch per stage based:
![image](https://user-images.githubusercontent.com/30960084/225586298-d4004713-1fcd-401f-963c-6656b92b1040.png)

### run_floorplan
![image](https://user-images.githubusercontent.com/30960084/225589033-f090e668-0007-4440-a3e7-0a1dfdad4ce8.png)
![image](https://user-images.githubusercontent.com/30960084/225589198-0e8cddd5-f5d3-436b-b564-643a79e3b3cf.png)

# Review floorplan files and steps to view floorplan (Day 2: Session 1: Lecture 7)

We saw that in the runs folder .config file as picked up the value coming from .config file defined in the designs folder
![image](https://user-images.githubusercontent.com/30960084/225613427-c43f13f0-f97f-46c3-bb27-3e00b5f378e7.png)

results directory for floorplan : After floorplan stage:
We see a DEF file saved here.
![image](https://user-images.githubusercontent.com/30960084/225613801-7e2476d7-2fe1-4475-a649-b5dffb21adca.png)

Data in DEF File:
![image](https://user-images.githubusercontent.com/30960084/225613928-3bc05c90-7168-49c1-8776-fea06027e035.png)

To see the actual layout post floorplan we use MAGIC:
Here is the command used to open MAGIC:
![image](https://user-images.githubusercontent.com/30960084/225614667-8602f086-9bf9-47ed-adfa-c4bb9a59afd4.png)
![image](https://user-images.githubusercontent.com/30960084/225614750-c7c46625-a7f2-4ca8-88ac-2114e82433b9.png)

# Review floorplan layout in Magic (Day 2: Session 1: Lecture 8)

Magic useful commands:
To select press S.
Pressing V, would fit to screen.
Pressing Z, would zoom in the selected bboxz

Seeing layer of IO pin:
Press S to select and then in tkcon window type what:
For Horizontal IO:
![image](https://user-images.githubusercontent.com/30960084/225615491-7d57ea59-8a80-401f-8b37-3ac8eeaa5755.png)
![image](https://user-images.githubusercontent.com/30960084/225615687-5b3ef00c-0c93-438c-9f83-233c9fcf8264.png)

For Veritcal IO:
![image](https://user-images.githubusercontent.com/30960084/225616110-cf404b4d-a95a-4f9c-b8f4-4550189cd1c4.png)

NOTE: From above results it seem, openlane takes layer as one value less then what was given in config.tcl

TAP CELLS:
TAP cells are basically meant to avoid the latch up condition in CMOS devices. They connect NWELL to VDD and subtrate to the ground.
![image](https://user-images.githubusercontent.com/30960084/225616667-9bff3f8b-435a-4533-9869-1d57356eb440.png)

Also these TAP cells are diagnolly equidistant:
![image](https://user-images.githubusercontent.com/30960084/225617317-860a4c50-5c0a-4924-b677-834e8ec6aca6.png)

# Congestion aware placement using Replace (Day 2: Session 2: Lecture 5)

![image](https://user-images.githubusercontent.com/30960084/225618403-b7ad7fca-a9e6-4fe1-a0c6-b12fb42f93d8.png)

## Run Floorplan:
![image](https://user-images.githubusercontent.com/30960084/225618631-086260cd-e62c-4284-84d7-0766ed0c06b1.png)

OVFL: Overflow,
Our objective is to converge this ovfl, as this decreases that means the design is converging.
![image](https://user-images.githubusercontent.com/30960084/225618987-194f1b4c-0ef4-447f-8099-74aa8833ad91.png)
Placement complete:
![image](https://user-images.githubusercontent.com/30960084/225619148-32820d91-4867-4590-afa1-8f5bebdf64fa.png)

Results after placement:
This DEF would contain the std cells also, which were missing post floorplan.
![image](https://user-images.githubusercontent.com/30960084/225619478-b7588975-2ecb-4360-ad03-395f5c612526.png)

MAGIC COMMAND and output:
![image](https://user-images.githubusercontent.com/30960084/225619980-97215487-5aa0-4a90-9c70-1c82343daa3b.png)
![image](https://user-images.githubusercontent.com/30960084/225619882-c0b2c59b-364e-4ee5-a2fb-e6777636b42f.png)

# IO Placer revision (Day 3: Session 1: Lecture 1)

In openlane we can make changes on the fly:
Right now we have the pins to be equidistant as was seen earlier.
We will change the FP_IO_MODE to 2 and see what happens:
![image](https://user-images.githubusercontent.com/30960084/225621969-29f46653-6eb2-4020-b5e4-23e9f3a109c7.png)
OUTPUT IN MAGIC:
![image](https://user-images.githubusercontent.com/30960084/225622364-8eafdca4-36c0-4c7e-95d3-79ddbb3c050f.png)

# STEPS to git clone vsdstdcelldesign (Day 3: Session 2: Lecture 8)
![image](https://user-images.githubusercontent.com/30960084/225623312-29fe5f88-28e1-4b0d-954b-850ed2ac204a.png)
![image](https://user-images.githubusercontent.com/30960084/225623493-e05c35ec-c13a-45ce-9e48-64e125157327.png)

Opening the INVERTER mag file in MAGIC:
![image](https://user-images.githubusercontent.com/30960084/225624380-58f6a1a3-3750-4749-8fbe-70ca90cf73b7.png)
OUTPUT:
![image](https://user-images.githubusercontent.com/30960084/225624827-a6277f8b-d939-4db3-8465-969ffd658049.png)

Looking at the layer in the right side panel:
![image](https://user-images.githubusercontent.com/30960084/225625329-e7710760-adc5-4a07-a87a-7e8d14099241.png)

Checking NMOS in MAGIC:
![image](https://user-images.githubusercontent.com/30960084/225626100-334c8218-bdf3-4dad-8068-5ecdea4baa14.png)
Checking PMOS in MAGIC:
![image](https://user-images.githubusercontent.com/30960084/225626279-c098133e-6ece-43da-9b58-48eb2735f2d2.png)

Checking connectivity of the gates:
![image](https://user-images.githubusercontent.com/30960084/225626478-52639a39-a3e6-4215-8b4a-a50e3feb2f5d.png)


# LAB STEPS to create STD cell layout and extract spice netlist (Day 3: Session 2: Lecture 9)

DRC check in MAGIC:
![image](https://user-images.githubusercontent.com/30960084/225628090-67bd06ec-6fed-4fb6-8119-74d579d8db9b.png)
To know the ERROR:
![image](https://user-images.githubusercontent.com/30960084/225628177-38c4ae2c-8b88-445a-b2c5-3f6cb82cfbd2.png)
Tool will then zoom in to the area with error:
![image](https://user-images.githubusercontent.com/30960084/225628304-fc0738ff-cfcd-4705-8bd5-92b40e1cf222.png)
Details of error in tkcon:
![image](https://user-images.githubusercontent.com/30960084/225628403-feae6ed9-3600-44d4-a4f6-2776ad39a571.png)

## Extraction from Magic:
![image](https://user-images.githubusercontent.com/30960084/225628982-e45be4d2-9bf2-4509-a150-2a9bf8436f4f.png)
OUTPUT:
![image](https://user-images.githubusercontent.com/30960084/225629100-b2ed88e5-4657-4fb9-a40e-ba60b6d9b806.png)

## Extract SPICE from MAGIC:
![image](https://user-images.githubusercontent.com/30960084/225629613-9ae4cfac-6d9a-4133-9900-b072b348deae.png)
OUTPUT:
![image](https://user-images.githubusercontent.com/30960084/225629890-bbc25cab-0195-4fb1-8303-fe8c4b486f92.png)

SPICE FILE:
![image](https://user-images.githubusercontent.com/30960084/225630027-2c500b79-6460-4279-96b9-b1f402963059.png)


# LAB STEPS to create final SPICE deck using SKY130 tech (Day 3: Session 3: Lecture 1 and 2)

![image](https://user-images.githubusercontent.com/30960084/225632178-f7a493ac-67d5-4972-9f51-37d679a4692e.png)
SPICE netlist defines only the below circuit as of now:
NOTE: I've not shown the capcitances C0-C4 just to simplify the drawing....
![image](https://user-images.githubusercontent.com/30960084/225632225-9b6bed42-c0f4-47ea-b848-a3b29309f5ac.png)

Modify the units based on grid box dimensions:
![image](https://user-images.githubusercontent.com/30960084/225633553-b8999c18-bb6b-49b7-88d2-333fd837af53.png)

Modified SPICE file:
![image](https://user-images.githubusercontent.com/30960084/225637935-ee5fdb74-543a-4f8c-bbaf-611d9cd794e1.png)
The RED part is the added circuit:
![image](https://user-images.githubusercontent.com/30960084/225638594-99e96515-5087-4c5f-bf30-ae0fbf3a64c1.png)

OUTPUT OF SPICE SIMULATIONS:
![image](https://user-images.githubusercontent.com/30960084/225639383-a95cddab-ad50-41cb-9071-2d2a4090cd76.png)

![image](https://user-images.githubusercontent.com/30960084/225639235-6c1da782-ef3b-475f-b183-73ca4b233fbe.png)

#  LAB STEPS to convert grid info to track info (Day 4: Session 1: Lecture 1)
![image](https://user-images.githubusercontent.com/30960084/225642718-ff6ae339-3675-4b5f-9557-7d58c1d948cd.png)

Track File:
![image](https://user-images.githubusercontent.com/30960084/225644077-fedc4f40-ad51-4a55-8aba-4ee0fd35c206.png)
![image](https://user-images.githubusercontent.com/30960084/225644142-d600e050-6b61-4567-b535-4602f5188e2e.png)

Ports overlap with horizontal and vertical tracks:
![image](https://user-images.githubusercontent.com/30960084/225644713-82cd866a-9300-411d-8710-9e2f60983207.png)

Height and width is odd multiple of pitch:
![image](https://user-images.githubusercontent.com/30960084/225646147-e2207462-6dbf-4a1e-a338-87a6b07c28db.png)

#  LAB STEPS to convert magic layout to std cell LEF (Day 4: Session 1: Lecture 2)
![image](https://user-images.githubusercontent.com/30960084/225690274-3efd6fe8-a622-4567-b055-00043a9d8b45.png)

Converting MAGIC FILE TO LEF:
![image](https://user-images.githubusercontent.com/30960084/225646476-789a3c5c-75c2-49c0-940c-522778c44621.png)
![image](https://user-images.githubusercontent.com/30960084/225646821-a3e79ff3-5f8a-4960-86da-8b621f4f382b.png)

Saving by custom name:
![image](https://user-images.githubusercontent.com/30960084/225647289-e802e2da-59aa-4b17-900e-16b33d4669d4.png)

Dumped out custom mag file:
![image](https://user-images.githubusercontent.com/30960084/225658446-29f216d9-1ec4-4ff3-863d-2206f15c5d86.png)

DUMP THE LEF FILE:
![image](https://user-images.githubusercontent.com/30960084/225659348-f405e7c1-0a0d-4084-89fb-70c3c0563e49.png)

LEF FILE: 
![image](https://user-images.githubusercontent.com/30960084/225659532-5efb45a1-de68-4c1a-98fc-e48e5072bb7e.png)
![image](https://user-images.githubusercontent.com/30960084/225659897-c06afba0-c7be-4dbe-8209-4d6697e060dc.png)


#  Introduction to timing libs and steps to include new cell in synthesis (Day 4: Session 1: Lecture 3)

![image](https://user-images.githubusercontent.com/30960084/225735047-3d9bfb8f-e204-47ca-8ab3-cbe4bd7f9789.png)

![image](https://user-images.githubusercontent.com/30960084/225690442-486c3ef1-96e5-4a2d-ac46-684f08f2a161.png)
![image](https://user-images.githubusercontent.com/30960084/225690503-3558a6c4-eef7-4171-a324-4c185133ad13.png)


Copying all the lib files and lef file to src folder:
I did modify the cell name in lib files as well to make the cell name as sk130_ajay
![image](https://user-images.githubusercontent.com/30960084/225686083-63ec1a52-d2b6-4fa6-9672-39cc9f7703af.png)

Config.tcl:
![image](https://user-images.githubusercontent.com/30960084/225693559-19d4bf29-a4fe-4c62-83b1-cb3c5c8c0ad3.png)

Adding our custom lef to the openlane flow:
![image](https://user-images.githubusercontent.com/30960084/225693715-ab23c041-132f-4b9c-8588-4586b4a5cfb2.png)

Here we can see that the custom cell is taken during run_synthesis step:
![image](https://user-images.githubusercontent.com/30960084/225694242-9f9eb720-e17c-4050-8785-230500232b0c.png)

Synthesis complete:
![image](https://user-images.githubusercontent.com/30960084/225694401-3ef4758f-8d71-431a-9b28-8b88b78ac00a.png)

Post placement layout:

![image](https://user-images.githubusercontent.com/30960084/225732231-ff817784-d88f-4bee-bb90-006d3b3f5210.png)

Routing:

![image](https://user-images.githubusercontent.com/30960084/225733090-acb00f00-b3d0-4350-a735-427079f684c8.png)

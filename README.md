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
OPENLANE RUN AREA (Basic Directory Structure
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

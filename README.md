# vsd_ajaykumar
#1 Day1 (Summary): Inception of opensource EDA, OpenLane & Sky130 PDK
##1.1 How to talk to computers:
Understanding of the basics is summarized in the below screenshots (where my inception/understanding of the concepts is also added as comments)
![image](https://user-images.githubusercontent.com/30960084/224394031-bf79d6e8-81cf-488c-a4fa-391695b5d970.png)
![image](https://user-images.githubusercontent.com/30960084/224394260-d8129a6a-9233-4cb3-a2a1-225e0c9f98be.png)
![image](https://user-images.githubusercontent.com/30960084/224394299-a68fa0c6-94cc-4d25-a067-110c18c6abc7.png)
![image](https://user-images.githubusercontent.com/30960084/224394594-0050b007-7328-4958-9c1b-81f1905da57b.png)

##1.2 SOC Design and Openlane (Summary)
Here we learnt on how the complete flow is made opensource:
  a. We need RTL designs, EDA tools and PDK data (as opensource for enabling the OpenSource RTL->GDSII flow)
  b. OpenLane is nothing but a flow which combines above mentioned aspects under a single umbrella.
 ![image](https://user-images.githubusercontent.com/30960084/224560463-bf395bf0-8e61-4057-b544-ead5d69e8721.png)

##1.3 Get familiar to open-source EDA tools:

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
  
#2 Day2 (Summary): Good Floorplan V/S Bad Floorplan and introduction to library cells:
##Floorplanning includes the following stages:
###Define Width and Height of Core and Die:
1. Here we got familiar to certain terminologies like (Die&Core area, core utilization, aspect ratio etc.
2. For deciding the Width and Height we basically take Utilisation factor into account.
3. Utilisation factor of 1 means there is no scope of adding more cells. Typical value of 0.5-0.7 core utilisation is seen in several designs.
4. Aspect ratio of 1 means that the cell is a square shaped cell in lib else a rectangular cell.

###Define locations of preplaced cells
1. We don't want these cells to be touched during the placement stage.
2. Location of these cells is pre-decided based on their connectivity with other cells & boundary.

###Surround the preplaced cells with decaps
1. Preplaced cells might demand a huge current while switching.
2. And power grid should be able to cater the demand current need:
     i. As a solution we add decaps arround them which serve the role of catering the demand current in addition to the power grid.
     ii. When block is not switching or demand current is low, decaps would charge them.
3.Here is the detailed study of working of decaps:
![image](https://user-images.githubusercontent.com/30960084/224562205-c49aa603-19a1-4937-b4a1-34b5aec5300b.png)

###Power Planning
1. We create power/ground mesh to provide power efficiently to the total demand.
2. There might be 1000s of cells switching at a particular time, our power grid should be capable of efficiently providing the demand current.

###Pin Placement
1. Here we identify the input/output pins in the design and place the pins in the die area.
2. Pin placement requires little bit of hand shaking between the frontend team and the backend team.
   i. Front END:	Front end team decides the netlist and connectivity of the design.
   ii. Back END:  Back end team decides the pin placement 
3. CLOCK PINS ARE WIDE:
   i. CLOCK IS THE PORT WHICH IS DRIVING ALL THE CELLS CONTINUOUSLY.
   ii. WE NEED LEAST RESISTANCE PATH FOR THE CLOCKS.

###Logical Cell Placement Blockage

This is to ensure that no logical cell is placed in the pin area.

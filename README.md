# VSD-IAT-Sign-off-Timing-Analysis---Basics-to-Advanced

This repository provides the entire summary of Lokeshwar Peddigari's hands-on work done during the VSD workshop SIGN-OFF-TIMING-ANALYSIS. Using OpenSTA and the SKY130nm PDK, the complete STA flow was built.

![152016610-be3ef4c8-601c-40e7-af85-91dc3ae9b2a4](https://user-images.githubusercontent.com/105622389/220615294-4978d666-e066-4eee-bfa6-06c44774ad64.png)
## Static timing Analysis

By examining all potential paths for timing violations, static timing analysis (STA) is a technique for evaluating the timing performance of a design. STA divides a design into timing paths, determines the delay in signal propagation along each path, and looks for timing constraints that have been violated both inside the design and at the input/output interface.

It starts with basics of Static Timing Analysis, timing paths, startpoint, endpoint and combinational logic definitions. It explains setup and hold checks, how STA tools calculate setup and hold violations. Then it slowly builds up to cover all aspects of STA like multiple types of timing paths, design rule checks, checks on async pins and clock gates. After that we go into slightly advanced topics like Time borrowing on latches, timing arcs, cell delays and models, impact of clock network on STA. Since STA and timing constraints go hand in hand the workshop covers basics of all the timing constraints that an engineer should know for STA like clock definitions, clock groups, clock characteristics, port delays and timing exceptions.
 * [Day-1 Labs](#day-1-labs)
   - [OpenSTA](#ot_Intro)
   - [Inputs to OpenSTA](#ot_inputs)
   - [RunScript](#ot_RunScript)
   - [Run OpenSTA](#Run-OpenSTA)  
* [Day-2 Labs](#day-2-labs)
  - [Liberty Files](#Liberty-Files)
  - [Exercise 1](#Exercise-1)
  - [SPEF Files](#SPEF-Files)
  - [Exercise 2](#Exercise-2)
* [Day-3 Labs](#day-3-labs)
  - [Slack computation](#slack-computation)
* [Day-4 Labs](#day-4-labs)
  - [Clock Gating Checks](#Clock-Gating-Checks)
  - [Async Pin Checks](#Async-Pin-Checks)
* [Day-5 Labs](#day-5-labs)
  - [CPPR - comman path pessimism removal](#CPPR)
  - [ECO - Engineering Change Order](#ECO)
_________________________________________________________________________________________________________________________________________________________________

## Day-1 Labs

The working files for this workshop were downloaded using this Command.
```
git clone https://github.com/vikkisachdeva/openSTA_sta_workshop    
```    
Tool used to perform the Static Timing Analysis in this workshop is OpenSTA

### OpenSTA
OpenSTA is a gate level static timing verifier. As a stand-alone
executable it can be used to verify the timing of a design using
standard file formats.

* Verilog netlist
* Liberty library
* SDC timing constraints
* SDF delay annotation
* SPEF parasitics

OpenSTA uses a TCL command interpreter to read the design, specify
timing constraints and print timing reports.

##### Clocks
* Generated
* Latency
* Source latency (insertion delay)
* Uncertainty
* Propagated/Ideal
* Gated clock checks
* Multiple frequency clocks

##### Exception paths
* False path
* Multicycle path
* Min/Max path delay
* Exception points
*  -from clock/pin/instance -through pin/net -to clock/pin/instance
*  Edge specific exception points
*   -rise_from/-fall_from, -rise_through/-fall_through, -rise_to/-fall_to

##### Delay calculation
* Integrated Dartu/Menezes/Pileggi RC effective capacitance algorithm
* External delay calculator API

##### Analysis
* Report timing checks -from, -through, -to, multiple paths to endpoint
* Report delay calculation
* Check timing setup

##### Timing Engine
OpenSTA is architected to be easily bolted on to other tools as a
timing engine.  By using a network adapter, OpenSTA can access the host
netlist data structures without duplicating them.

* Query based incremental update of delays, arrival and required times
* Simulator to propagate constants from constraints and netlist tie high/low

### Inputs to OpenSTA
1. Netlist Verilog File (provided in TCL file `read_verilog` command)
    
#### Synthesized verilog file with liberty cells linked.
<img src="https://user-images.githubusercontent.com/105622389/220642363-561f68b4-e18e-4a65-9c37-f00076f702c8.jpg " width="600" height="400" />


2. Standard Cell Library (provided in TCL file `read_liberty` command)

    Usually a cell in the liberty file has 
    - Input ports definitions
    - Output port definitions
    - Functionality of the cells
    - Input to Output relationship
    
    Here we are using `sky130_fd_sc_hd_tt_025C_1v80.lib`
    
    ##### Exercise 
    * Pins of sky130_fd_sc_hd_nand2_1
        + Input Pins : A,B.
        + Output Pins : Y (!A | !B).
        
    ![nan2-1](https://user-images.githubusercontent.com/105622389/220662682-677a8549-ea86-41cb-bac5-2ed192fe59e9.jpg)
    ![nandA](https://user-images.githubusercontent.com/105622389/220662692-44a0c749-8a6e-4b56-b22c-24422ec23a7d.jpg)
    ![nandB](https://user-images.githubusercontent.com/105622389/220662700-c04e4e65-46d6-420c-a9c2-ade020dc0a1f.jpg)
    ![nandY](https://user-images.githubusercontent.com/105622389/220662706-d68b69c8-fcc8-4bc1-af5a-eeb5de1e8692.jpg)

        
        
3. Timing Contraints file(Synopsys design constraints `.sdc` file) - Usually written and as `.sdc` file and sourced in tcl file using read_sdc command.

    <img src="https://user-images.githubusercontent.com/105622389/220652726-b5ae368d-daf3-44f4-8ad6-40896e579f0a.jpg " width="483" height="244.5" />


### RunScript
- In runscript you can define all the commands you want to run in the openSTA tool
- Tool will execute each command sequentially in order(There are some commands which are executed in parallel in some cases)
- Runscript is in `.tcl` format.

    <img src="https://user-images.githubusercontent.com/105622389/220700418-a53f14c1-8966-447a-8bde-4532c172b54e.jpg " width="400" height="200" />

### Run OpenSTA
• Run openSTA using command 
```
    sta run.tcl -exit | tee run.log
```
![lab1 report](https://user-images.githubusercontent.com/105622389/220704206-28dca38c-04e3-4f87-8ae1-0e52cb57cac7.jpg)

## Day-2 Labs
### Liberty Files
The .lib file is an ASCII representation of the timing and power parameters associated with any cell in a particular semiconductor technology.

The .lib file contains timing models and data to calcumax
- I/O delay paths
- Timing check values
- Interconnect delays

    <img src="https://user-images.githubusercontent.com/105622389/220705392-31cae93b-35a7-4f49-a038-5713acb7c29c.png " width="400" height="200" />
    <img src="https://user-images.githubusercontent.com/105622389/220705929-fe638a80-ec0c-451d-8ce4-042380f72843.png " width="400" height="200" />
    <img src="https://user-images.githubusercontent.com/105622389/220706471-76fd6035-cb05-4df8-87ab-df73520cdfb8.png " width="400" height="200" />


### Exercise 1

#### Find all the cells in simple_max.lib
Total 211 Cells      
Using the below command
`grep -c "Begin cell" simple_max.lib`

![lab2 no of cells](https://user-images.githubusercontent.com/105622389/220709071-3ec062ab-8726-4e43-bd55-7020478a5f40.jpg)

#### Find all the pins of the cell NAND2_X1 in simple_max.lib
NAND2_X1 is a 2 input NAND Gate with pin 'o' , 'a' , 'b'

![image](https://user-images.githubusercontent.com/105622389/220709890-6515255e-e646-48e1-8cb3-102d7f378072.png)
![image](https://user-images.githubusercontent.com/105622389/220709996-5ab7f72c-b7c1-44f5-a9b3-1f2e984d31a7.png)

#### What difference you see between NAND2_X1 and NAND3_X1

- NAND2_X1 is a 2 input NAND Gate with pin 'o' , 'a' , 'b'
- NAND3_X1 is a 3 input NAND Gate with pin 'o' , 'a' , 'b' , 'c'
- Apart from that Max Capacitance of output pin "o" is different. 

![image](https://user-images.githubusercontent.com/105622389/220712706-cc8c82a5-9a0c-47b9-b11f-bf672fd0d3f6.png)
![image](https://user-images.githubusercontent.com/105622389/220713057-ba2264ea-dd5d-4727-8bf8-835956b8ad58.png)
![image](https://user-images.githubusercontent.com/105622389/220712894-0560d272-90ce-445d-9b78-f4912115bc52.png)
![image](https://user-images.githubusercontent.com/105622389/220709996-5ab7f72c-b7c1-44f5-a9b3-1f2e984d31a7.png)
 
#### What is the difference between ‘simple_max.lib’ and ‘simple_min.lib’
Comparing simple max.lib with simple min.lib, it can be seen that both files have different values for parameters like delay, fall transition, cell rise, rise transition, cell fall, etc. The maximum value for each parameter is defined in the simple max.lib file, while the minimum value is defined in the simple min.lib file.

<img align="left" width="485" height="650" src="https://user-images.githubusercontent.com/105622389/220714484-d3c1f107-f73e-4562-8844-31e6c75a8375.jpg">
<img align="right" width="485" height="650" src="https://user-images.githubusercontent.com/105622389/220714514-e2db6bc9-85cf-47ec-a4e2-1aa274fb56ac.jpg">


- left picture is simple_max.lib

- right picture is simple_min.lib


### SPEF Files
It describes parasitic information of the design and is automatically generated by the tool.It is mainly used to pass parasitic information from one tool to
another.
 
A Typical SPEF File has 4 main sections
• Header
• Name Map
• Top Level Ports
• Parasitic description

 Command for spef parsing `read_spef`
 
 ![image](https://user-images.githubusercontent.com/105622389/220717273-9bf5e893-9878-4866-9c0b-216a6bd10b4f.png)
 
### TIMING REPORT EXERCISE
Add Command in run.tcl file: `report_checks –group_count 5`
Rerun  `sta run.tcl –exit | tee run.log`		

![lab2 path1](https://user-images.githubusercontent.com/105622389/220718240-b58566df-8a7e-47cf-9c16-ffba6af2bf37.jpg)
![lab2 path2 ](https://user-images.githubusercontent.com/105622389/220718291-f5f7a76b-94d8-4166-a82c-20de9958fad1.jpg)


## Day-3 Labs

Design schematic for slack computation
![lab3 schematic](https://user-images.githubusercontent.com/105622389/220720081-327a6a8a-63ce-4b24-9de9-26d9c468a233.jpg)
![image](https://user-images.githubusercontent.com/105622389/220721946-853c220c-83fd-4af1-b1bb-88ef13f2f7ba.png)

 - run script should include the following command to check the slack of different timing paths from F1/CLK to F2
  `report_checks –from F1/CK -endpoint_count 100`
  
 ![image](https://user-images.githubusercontent.com/105622389/220720642-8fa66a74-f83b-4420-bc98-b7e883862b71.png)
 
 - different paths reported
 ##### Path1
![lab3 exp1](https://user-images.githubusercontent.com/105622389/220722359-1cef09c4-9a45-48b9-8d06-1612cfcf05b1.jpg)
![image](https://user-images.githubusercontent.com/105622389/220722375-8290beb9-fa01-44cf-af4c-eb281f1c011c.png)
##### Path2
![lab3 exp2](https://user-images.githubusercontent.com/105622389/220722992-f473a103-b729-4e36-95cb-550df4d058c9.jpg)
![image](https://user-images.githubusercontent.com/105622389/220723423-83b2c58a-8621-439a-95c6-566a97b5889f.png)

##### Path3
![lab3 exp3](https://user-images.githubusercontent.com/105622389/220723021-8bcfe435-ccc1-4017-bf88-c17138282fcf.jpg)
![image](https://user-images.githubusercontent.com/105622389/220723553-6b1c8a9c-ede7-4549-bd37-34115cfebf5e.png)

##### Path4
![lab3 exp4](https://user-images.githubusercontent.com/105622389/220723064-ebd392e0-b326-40ff-a87a-90b5e12abf69.jpg)
![image](https://user-images.githubusercontent.com/105622389/220723645-0b5b12e5-7d36-4b8b-8fbe-12a80f2369cc.png)


##### Path5
![lab3 exp5](https://user-images.githubusercontent.com/105622389/220723115-e19d9c92-0e70-4d49-903f-c8539d17f44a.jpg)
![image](https://user-images.githubusercontent.com/105622389/220723697-2e848c38-b01f-44dd-954a-adae1286920f.png)


##### Path6
![lab3 exp6](https://user-images.githubusercontent.com/105622389/220723211-29fb36c3-3ed8-4a3c-8352-b6d62e3861c6.jpg)
![image](https://user-images.githubusercontent.com/105622389/220723779-cd46c402-fa55-4e39-9902-4a1ceda50157.png)


##### Path7
![lab3 exp7](https://user-images.githubusercontent.com/105622389/220723240-b914f781-525e-4563-bcc1-01571d960e2c.jpg)
![image](https://user-images.githubusercontent.com/105622389/220723896-732750c3-aa51-472f-8dda-debe7ca36732.png)


##### Path8
![lab3 exp8](https://user-images.githubusercontent.com/105622389/220723267-0caa59e7-674d-4172-a243-a4efebae6809.jpg)
![image](https://user-images.githubusercontent.com/105622389/220723966-a68340da-285c-4ce2-be94-cfcfa38d5404.png)

## Day-4 Labs
1.Clock Gating Checks
 * Gating techniques used is ‘Active Low Clock Gating’
 * run.tcl file 
 
![lab4 clock gating run tcl](https://user-images.githubusercontent.com/105622389/220728323-1be5c723-8f6a-4fbb-9afe-b12fc4bd1408.jpg)

Slack Violated on the clogated report

![lab4 exp1](https://user-images.githubusercontent.com/105622389/220728439-ad0ab66c-707d-4db8-9018-89a4eb75c9f1.jpg)

2.Async Pin Checks
 * Recovery and Removal checks
 * run.tcl file
 
 ![image](https://user-images.githubusercontent.com/105622389/220729447-282857ed-1a0e-4dc8-97f3-2578261ab17a.png)
 
 Slack met the timing contraints
 
 ![image](https://user-images.githubusercontent.com/105622389/220729767-80134281-d4fe-4a3c-bd99-7aa3806f7abd.png)

## Day-5 Labs
1.CPPR 
Removing common clock buffer delay between launch path and capture path is CPPR. (comman path pessimism removal).
![image](https://user-images.githubusercontent.com/105622389/220731470-f1bfe6a1-b204-4929-9169-bdc9f78250e4.png)
* run.tcl file before enabling CRPR 

![image](https://user-images.githubusercontent.com/105622389/220731702-58e6c6e5-ad06-4de9-8f3a-efa86682bfb8.png)
* report before enabling
 
![lab5 before crpr](https://user-images.githubusercontent.com/105622389/220732023-69d9f9d8-3ce4-4963-a87e-fb63732fcb29.jpg)

* Enabling CRPR using command `set sta_crpr_enabled 1`
* Reported a reduced slack compared to the before enabling.

![image](https://user-images.githubusercontent.com/105622389/220732785-ea36dd8a-a720-4021-9eb5-447a38869a6b.png)
![image](https://user-images.githubusercontent.com/105622389/220732937-987a4608-7d3b-423c-aa12-1e3eb483195c.png)

2.ECO - Engineering Change Order
• In the ECO cycle, we perform various analysis one by one for every 
check which we need to close but not closed till PnR stage. 
• There are specialized signoff tools that help us to analyze the issue 
and also suggest the changes we need to do in order to close the 
issue. 
• The suggested change is captured in an eco file.
• In this lab we will focus on ECO for timing purposes, this is done to fix 
setup and hold violations

- run.tcl file with ECO insertions

![lab5 run tcl](https://user-images.githubusercontent.com/105622389/220733746-5bb65735-4a91-4702-b6fa-f1060ba5848d.jpg)

### Exercise
* Open Verilog file s27_eco.v, what differences you find compared to s27.v

![lab5 s27 v](https://user-images.githubusercontent.com/105622389/220734824-6b27e0c5-27fd-4280-aa35-1b3ad9a42271.jpg)
![lab5 s27_eco v](https://user-images.githubusercontent.com/105622389/220734846-9debfb7e-f1dd-4255-92d4-040a53f41b9e.jpg)

** Comparing both the verilog files we can notice there is new clkbuffer created i.e., clkbuf16 and few corresponding wire changes in clkbuf15 etc as ,mentioned in the above lab snapshots.

- Reports generated after the run.tcl file execution. 
- Following are the before and after ECO changes reports. 

* Setup check of before and after ECO changes

![lab5 max before eco](https://user-images.githubusercontent.com/105622389/220735740-edb59d61-57e7-4a03-8a8d-46a92f6bac8d.jpg)
![lab5 max after eco](https://user-images.githubusercontent.com/105622389/220735778-f1737e5c-17f3-4e5c-9f19-21671f309e10.jpg)

* Hold Check of before and after ECO changes

![lab5 min before eco](https://user-images.githubusercontent.com/105622389/220736050-64c9b16f-75f6-4742-aedd-8c5b6b690816.jpg)
![lab5 min after eco](https://user-images.githubusercontent.com/105622389/220736023-f9606a10-0b6e-4683-9bd4-391c70d1a152.jpg)

## Acknowledgements

- [Kunal Ghosh](https://github.com/kunalg123), Co-founder of VLSI System Design (VSD) Corp. Pvt. Ltd.
- [Vikas Sachdeva](https://vlsideepdive.com/), Advisor, Tech and VLSI Coach, Trainer and Innovator at vlsideepdive.

## Author

[Lokeshwar Peddigari](https://www.linkedin.com/in/lokeshwar-peddigari), B.Tech ECE (2019-23), IFHE(ICFAI foundation for Higher Education), Hyderabad, Telangana, India
- Contact: lokeshwarpeddigari@gmail.com  <br>
- System configuration during the undertaking:  <br>\
-- Processor: Intel(R) Core(TM) i5-9300H CPU @ 2.40GHz   <br>\
-- Installed RAM: 8.00 GB   <br>\
-- System type:	Windows 11, 64-bit operating system, x64-based processor, Ubuntu 22.04 64-bit OS(virtual Box) <br>

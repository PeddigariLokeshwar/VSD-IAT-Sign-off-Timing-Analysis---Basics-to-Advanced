# VSD-IAT-Sign-off-Timing-Analysis---Basics-to-Advanced

This repository provides the entire summary of Lokeshwar Peddigari's hands-on work done during the VSD workshop SIGN-OFF-TIMING-ANALYSIS. Using OpenSTA and the SKY130nm PDK, the complete STA flow was built.

![152016610-be3ef4c8-601c-40e7-af85-91dc3ae9b2a4](https://user-images.githubusercontent.com/105622389/220615294-4978d666-e066-4eee-bfa6-06c44774ad64.png)
## Static timing Analysis

By examining all potential paths for timing violations, static timing analysis (STA) is a technique for evaluating the timing performance of a design. STA divides a design into timing paths, determines the delay in signal propagation along each path, and looks for timing constraints that have been violated both inside the design and at the input/output interface.

It starts with basics of Static Timing Analysis, timing paths, startpoint, endpoint and combinational logic definitions. It explains setup and hold checks, how STA tools calculate setup and hold violations. Then it slowly builds up to cover all aspects of STA like multiple types of timing paths, design rule checks, checks on async pins and clock gates. After that we go into slightly advanced topics like Time borrowing on latches, timing arcs, cell delays and models, impact of clock network on STA. Since STA and timing constraints go hand in hand the workshop covers basics of all the timing constraints that an engineer should know for STA like clock definitions, clock groups, clock characteristics, port delays and timing exceptions.
* [Day 1](#day-1)
    + [STA Defination](#STA-Defination)
    + [Timing Paths](#Timing-Paths)
    + [Setup & Hold Checks](#Setup-&-Hold-Checks)
    + [Slack Calculation](#Slack-Calculation)
    + [SDC File](#SDC-File)
    + [Clock](#Clock)
    + [Port Delays and Boundary Constrains](#Port-Delays-and-Boundary-Constrains)
    + [Lab 1](#Lab-1)
        - [OpenSTA](#OpenSTA)
        - [Constraints Creation](#Constraints-Creation)
        - [Run OpenSTA](#Run-OpenSTA)
* [Day 2](#day-2)
    + [Other Timing Checks](#Other-Timing-Checks)
    + [Design Rule Checks](#Design-Rule-Checks)
    + [Latch Timing](#Latch-Timing)
    + [STA Text Report](#STA-Text-Report)
    + [Lab 2](#Lab-2)
        - [Liberty Files](#Liberty-Files)
        - [Exercise 1](#Exercise-1)
        - [SPEF Files](#SPEF-Files)
        - [Exercise 2](#Exercise-2)
* [Day 3](#day-3)

## Day-1 Summary
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


### runScript
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

## Day-2 Summary
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

<img align="left" width="400" height="650" src="https://user-images.githubusercontent.com/105622389/220714484-d3c1f107-f73e-4562-8844-31e6c75a8375.jpg">
<img align="right" width="400" height="650" src="https://user-images.githubusercontent.com/105622389/220714514-e2db6bc9-85cf-47ec-a4e2-1aa274fb56ac.jpg">

- left picture is simple_max.lib

- right picture is simple_min.lib


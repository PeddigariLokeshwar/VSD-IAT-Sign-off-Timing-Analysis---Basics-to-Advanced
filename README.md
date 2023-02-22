# VSD-IAT-Sign-off-Timing-Analysis---Basics-to-Advanced

This repository provides the entire summary of Lokeshwar Peddigari's hands-on work done during the VSD workshop SIGN-OFF-TIMING-ANALYSIS. Using OpenSTA and the SKY130nm PDK, the complete STA flow was built.

![152016610-be3ef4c8-601c-40e7-af85-91dc3ae9b2a4](https://user-images.githubusercontent.com/105622389/220615294-4978d666-e066-4eee-bfa6-06c44774ad64.png)
## Static timing Analysis

By examining all potential paths for timing violations, static timing analysis (STA) is a technique for evaluating the timing performance of a design. STA divides a design into timing paths, determines the delay in signal propagation along each path, and looks for timing constraints that have been violated both inside the design and at the input/output interface.

It starts with basics of Static Timing Analysis, timing paths, startpoint, endpoint and combinational logic definitions. It explains setup and hold checks, how STA tools calculate setup and hold violations. Then it slowly builds up to cover all aspects of STA like multiple types of timing paths, design rule checks, checks on async pins and clock gates. After that we go into slightly advanced topics like Time borrowing on latches, timing arcs, cell delays and models, impact of clock network on STA. Since STA and timing constraints go hand in hand the workshop covers basics of all the timing constraints that an engineer should know for STA like clock definitions, clock groups, clock characteristics, port delays and timing exceptions.

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






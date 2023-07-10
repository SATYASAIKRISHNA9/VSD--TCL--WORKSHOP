# TCL Workshop: From Introduction to Advanced Scripting Techniques in Design and Synthesis
---------------------------------------------------------------------------------------------------

## Introduction
Tool Command Language (Tcl) is a scripting language commonly used in across domains because of its flexibility and ease of integration with other programming languages and tools.

## DAY-1

Introduction to TCL and VSDSYNTH Tool box
letting the system know that its a UNIX script

```
#!/bin/tcsh -f  
```

verifying three general scenarios for a user POV and giving info w.r.t case by the script below
  - user doesnt enter the csv file
  - user enters the wrong csv file/ file doesnt exist
  - user enters __-help__
![image](https://github.com/Visruat/VSD-TCL/assets/79971687/23682215-d304-46b6-9cf6-a187ae589173)

source the Unix shell to the Tcl script by passing the required csv file as an argument

```
tclsh vsdsynth.tcl $argv[1]
```
Note : Make sure the file is executable by using the command ``` chmod -R 777 ``` 

## DAY-2
__Converting inputs to format[1] and feeding it to yosys for synthesis__

  ###Create Variables

  __- Checking if the directories exist or not ( done to prevent the script from breaking )__

![Screenshot from 2023-06-15 15-29-57](https://github.com/Visruat/VSD-TCL/assets/125136551/17c7398b-593d-40f9-a267-35bc6e7afc9a)

Displays an error when the required file is not in the needed directory

![Screenshot from 2023-06-15 16-50-05](https://github.com/Visruat/VSD-TCL/assets/125136551/a82bf99c-b7bf-4288-b77a-5766d2ac8112)


## DAY-3

__Read Constraints.csv file and convert to sdc format__

  - getting clock details from csv file and writing it the sdc file in the required format
  - getting input details from csv file and writing it in the required format
  - getting output details from csv file and writing it in the required format
 
Note: need to identify bussed and non bussed inputs and outputs before entering in the required format.

The script writing the sdc constraints.

![Screenshot from 2023-06-18 14-19-37](https://github.com/Visruat/VSD-TCL/assets/125136551/8d0cb933-cbc5-4aae-93dd-bc5819c33a3a)

A snip of the sdc file 

![Screenshot from 2023-06-18 14-16-25](https://github.com/Visruat/VSD-TCL/assets/125136551/d80f44f6-45ce-418a-b067-bebf900f1217)

## DAY-4

### YOSYS (Yosys Open SYnthesis Suite)

YOSYS is an open-source RTL synthesis and formal verification framework for digital circuits. It takes RTL descriptions (e.g., Verilog) as input and performs synthesis to generate a gate-level netlist. YOSYS supports technology mapping, optimization, and formal verification. It has a scripting interface, integrates with other EDA tools, and is widely used in academia and industry for digital design tasks.

__Creating scripts for synthesis and running it on yosys__

  - creating script for Hierarchy check and running it on different cases:

  Case --> When all the modules are present and called by verilog file

  ![8gj8hq1n](https://github.com/SATYASAIKRISHNA9/VSD-TCL/assets/79971687/30a26473-44ec-418f-80b8-2d4f85887eb3)


  Case --> moduled referenced and not called or module doesn't exist and calling by verilog

![Screenshot from 2023-06-18 16-53-40](https://github.com/Visruat/VSD-TCL/assets/125136551/2ea42db4-f89a-4bc6-b4fb-345ab93d1637)

  - after the hierarchy check is passed . The main synthesis script is written.
  - running synthesis if there is no error in hierarchy check.

  Case -->  All hierarchies present in design
  
![8ydifnlk](https://github.com/SATYASAIKRISHNA9/VSD-TCL/assets/79971687/ab7bc142-8746-4553-9f41-b9dab874a43c)


  Case --> error encountered in hierarchy

![Screenshot from 2023-06-18 17-12-06](https://github.com/Visruat/VSD-TCL/assets/125136551/24095315-680a-4905-960c-6da157f034d3)



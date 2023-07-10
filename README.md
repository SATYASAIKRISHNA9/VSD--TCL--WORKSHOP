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

  ![image](https://github.com/SATYASAIKRISHNA9/VSD--TCL--WORKSHOP/assets/79971687/b253fbef-b889-4e8c-a223-82bfce2966da)

  Case --> moduled referenced and not called or module doesn't exist and calling by verilog

![Screenshot from 2023-06-18 16-53-40](https://github.com/Visruat/VSD-TCL/assets/125136551/2ea42db4-f89a-4bc6-b4fb-345ab93d1637)

  - after the hierarchy check is passed . The main synthesis script is written.
  - running synthesis if there is no error in hierarchy check.

  Case -->  All hierarchies present in design
  
  ![image](https://github.com/SATYASAIKRISHNA9/VSD--TCL--WORKSHOP/assets/79971687/0576168c-8c8a-47fb-8ce1-da67043ebe86)



  Case --> error encountered in hierarchy

![atsl2lmf](https://github.com/SATYASAIKRISHNA9/VSD--TCL--WORKSHOP/assets/79971687/9efc27d1-b8c2-4f94-aece-f97c38ba3830)


## DAY-5

__create script for OpenTimer and run STA analysis followed by generation of Quality of Results (QoR)__

_An introduction to procs_
  - To generate a script for OpenTimer I will be making use of procs. Procs are an external tcl file that perform an operation that is specified in it when sourced to the main tcl file. It works similar to how a function works in Python Programming. An example of a proc would be read_liberty <args> where options _like -lib, -late, -early and /or <filename>_ can be passed as an arguememt to the proc. Once the proc is sourced in the main tcl script the read_liberty command will be executed by referring to the proc and mapping the arguements to the external tcl script(proc script). At the end of the proc command, the main tcl script will be left with the output of the proc.
  
Refer to the image for pictorial undertanding

<img src = "https://github.com/Visruat/VSD-TCL/assets/125136551/a4d8166c-5026-4e26-b7de-499626926123" width ="300" height ="300">  <img src = "https://github.com/Visruat/VSD-TCL/assets/125136551/c33f56bb-99fe-42f5-ba1f-d21e86795f96" width ="600" height ="300">

__- Entering the world of procs__
1) reopenStdout.proc

```
proc reopenStdout {file} {
  #closes the main terminal window where all the puts statements were being displayed as info to user
  close stdout
  #opens $file in write mode
  open $file w       
}
```
The reopenStdout proc is a simple proc which is used to close the main terminal stdout and open a file in write mode

2) set_num_threads.proc
   
```      
proc set_multi_cpu_usage {args} {
    array set options {-localCpu <num_of_threads> -help "" }
    foreach {switch value} [array get options] {
    puts "Option $switch is $value"
    }
    while {[llength $args]} {
    puts "llength is [llength $args]"
    puts "lindex 0 of \"$args\" is [lindex $args 0]"
        switch -glob -- [lindex $args 0] {
          -localCpu {
              puts "old args is $args"
              set args [lassign $args - options(-localCpu)]
              puts "new args is \"$args\""
              puts "set_num_threads $options(-localCpu)"
              }
          -help {
              puts "old args is $args"
              set args [lassign $args - options(-help) ]
              puts "new args is \"$args\""
              puts "Usage: set_multi_cpu_usage -localCpu <num_of_threads>"
              }
        }
    }
}
set_multi_cpu_usage -localCpu 8 -help 
```
- "array set options { -localCpu <num_of_threads> -help "" }" --> set an array named options. options is a list of key-value pairs, where each key is a string representing the element's name, and each value is the corresponding value to assign to that element. eg, "-localCpu is linked to <num_of_threads>" and "-help" is linked to "".
- "switch -glob -- [lindex $args 0]" --> globbing is used to get the term inside [] so that switch can map to the corresponding case. Takes only the ket of the key-value pair 
- "set args [lassign $args - options(-localCpu)]" --> assigning new value to args after removing the array element which was used to enter the loop

![image](https://github.com/SATYASAIKRISHNA9/VSD-TCL/assets/79971687/8e6445ec-62f0-4a4f-866a-ac721d7eff0f)


3) read_lib.proc

```
proc read_lib args {
	array set options {-late <late_lib_path> -early <early_lib_path> -help ""}
	while {[llength $args]} {
		switch -glob -- [lindex $args 0] {
		-late {
			set args [lassign $args - options(-late) ]
			puts "set_late_celllib_fpath $options(-late)"
		      }
		-early {
			set args [lassign $args - options(-early) ]
			puts "set_early_celllib_fpath $options(-early)"
		       }
		-help {
			set args [lassign $args - options(-help) ]
			puts "Usage: read_lib -late <late_lib_path> -early <early_lib_path>"
			puts "-late <provide late library path>"
			puts "-early <provide early library path>"
		      }	
		default break
		}
	}
}
read_lib  -late  /home/vsduser/vsdsynth/osu018_stdcells.lib  -early /home/vsduser/vsdsynth/osu018_stdcells.lib -help
```
- Similar to the set_num_threads proc , the read_lib proc will have 3 options i.e _late early and help_
- the proc ensures to read the late and early lib file for STA and write it in a file

![image](https://github.com/SATYASAIKRISHNA9/VSD--TCL--WORKSHOP/assets/79971687/0ad84154-11dd-40c5-ade7-a17cb4a98d2a)

4) read_verilog.proc

```
proc read_verilog arg1 {
  puts "set_verilog_fpath $arg1"
}
read_verilog /home/vsduser/vsdsynth/outdir_openMSP430/openMSP430.synth.v
```
- This proc enters the puts statement followed by the netlist file

![image](https://github.com/SATYASAIKRISHNA9/VSD--TCL--WORKSHOP/assets/79971687/f815be16-c501-4502-850b-b2e4a6462ee6)

5) read_sdc.proc

The read_sdc proc is a large proc file which will be covered in parts.
This is done to convert sdc file into OpenTimer format
```
proc read_sdc {arg1} {
set sdc_dirname [file dirname $arg1]
#"file tail" is used to get the last file of the arguement given
set sdc_filename [lindex [split [file tail $arg1] .] 0 ]
#set sdc_filename [lindex [split [lindex [split $arg1 /] [expr {[llength [split $arg1 /]] -1}]] .] 0]
set sdc [open $arg1 r]
set tmp_file [open ./temp/4 "w"] 
puts -nonewline $tmp_file [string map {"\[" "" "\]" " "} [read $sdc]]     
close $tmp_file
}
```

- setting directory and filename for sdc , also replacing the " [] " with "" in a temp file
- special mapping done so that it can diffrentiate between "abc" and "abc_en". Refer the block of code.

  ![8qgyh8vd](https://github.com/SATYASAIKRISHNA9/VSD--TCL--WORKSHOP/assets/79971687/55e59d87-80e0-4a80-b3f2-682ad0d5fe3a)

- converting create_clock constraints
```
set tmp_file [open /tmp/4 r]
set timing_file [open /tmp/3 w]
set lines [split [read $tmp_file] "\n"]
set find_clocks [lsearch -all -inline $lines "create_clock*"]
foreach elem $find_clocks {
	set clock_port_name [lindex $elem [expr {[lsearch $elem "get_ports"]+1}]]
	set clock_period [lindex $elem [expr {[lsearch $elem "-period"]+1}]]
	set duty_cycle [expr { 100 - [expr {[lindex [lindex $elem [expr {[lsearch $elem "-waveform"]+1}]] 1]*100/$clock_period}]}]
	puts $timing_file "clock $clock_port_name $clock_period $duty_cycle"
	}
close $tmp_file
```

- set the tmp_file obtained the start of the proc to read mode so that you can read data from it.
- create another tmp_file to write the create_clock constraints
- the lines are split based off "\n" being the delimiter --> $lines
- the ports which contain "create_clock" are sorted out using lsearch -inline  --> find_clocks
- in a foreach loop elem --> clock_port_name is set by taking lindex +1 of "get_ports"
- --> clock_period is identified by doing the same for "-period"
- --> duty_cycle is found by implemeting this logic into tcl script = [ Ton/Tperiod * 100 ] where Ton is taken {x y} after "-waveform"
- dump the puts statement if $timing_file

- creating clock_latency constraints
```
set find_keyword [lsearch -all -inline $lines "set_clock_latency*"]
#puts $find_keyword
set tmp2_file [open ./temp/5 "w"]
set new_port_name ""
foreach elem $find_keyword {
        set port_name [lindex $elem [expr {[lsearch $elem "-clock"]+1}]]
        #puts "b = $port_name"
        #puts "c = $new_port_name"
	    if {![string match $new_port_name $port_name]} {
        	set new_port_name $port_name 
        	set delays_list [lsearch -all -inline $find_keyword [join [list "*" " " $port_name " " "*"] ""]]
            #puts "d = $delays_list"
        	set delay_value ""
        	foreach new_elem $delays_list {
        		set port_index [lsearch $new_elem "-clock"]
        		lappend delay_value [lindex $new_elem [expr {$port_index-1}]]
			#puts "e= $delay_value"
        	}
		puts -nonewline $tmp2_file "\nat $port_name $delay_value"
	}
}

close $tmp2_file
set tmp2_file [open ./temp/5 ]
puts -nonewline $timing_file [read $tmp2_file]
close $tmp2_file
```
- similar to finding create_clock constraints , clock_latency constraints are found by grepping for "set_clock_latency"
- then we continue to separate each clock based of the name of the clock. delays_list will contain all the lines with same clock name due to the wildcards search that was performed using ``` [lsearch -all -inline $find_keyword [join [list "*" " " $port_name " " "*"] ""]] ```
- now for each of these lines latency values are separated and appended to a object called delay_value.
- arrival time (at) $port_name $delay_value is then written into the timing file.
Note: for grepping all wildcards --> we compared it with * $port_name * since its given that way in the file content

![create_clock_constraints](https://github.com/SATYASAIKRISHNA9/VSD--TCL--WORKSHOP/assets/79971687/48e61006-a7fd-47a9-b03d-26f68845c20f)


script written based off the above image 

![image](https://github.com/SATYASAIKRISHNA9/VSD--TCL--WORKSHOP/assets/79971687/7937ac0b-62ab-4af7-83a4-34c61c80b3fc)


output of the script.

- similarly the constraints for "set_clock_transition", "set_input_delay", "set_input_transition", "set_output_delay" and "set_load" are written in same fashion
- after which the timing file is closed.
- now we need to make sure that all bussed inputs and outputs receive the same constraints on each pin. hence we make use of the following code
```
set ot_timing_file [open $sdc_dirname/$sdc_filename.timing w]
set timing_file [open /tmp/3 r]
while {[gets $timing_file line] != -1} {
        if {[regexp -all -- {\*} $line]} {
                set bussed [lindex [lindex [split $line "*"] 0] 1]
                set final_synth_netlist [open $sdc_dirname/$sdc_filename.final.synth.v r]
                while {[gets $final_synth_netlist line2] != -1 } {
                        if {[regexp -all -- $bussed $line2] && [regexp -all -- {input} $line2] && ![string match "" $line]} {
                        puts -nonewline $ot_timing_file "\n[lindex [lindex [split $line "*"] 0 ] 0 ] [lindex [lindex [split $line2 ";"] 0 ] 1 ] [lindex [split $line "*"] 1 ]"
                        } elseif {[regexp -all -- $bussed $line2] && [regexp -all -- {output} $line2] && ![string match "" $line]} {
                        puts -nonewline $ot_timing_file "\n[lindex [lindex [split $line "*"] 0 ] 0 ] [lindex [lindex [split $line2 ";"] 0 ] 1 ] [lindex [split $line "*"] 1 ]"
                        }
                }
        } else {
        puts -nonewline $ot_timing_file  "\n$line"
        }
}

close $timing_file
puts "set_timing_fpath $sdc_dirname/$sdc_filename.timing"
}
```
- we open final timing file in write mode
- we now open the temp timing file in read mode , to grep the bussed ports
- we setup a file which is linked to the synthesis netlist. from this file grep all the nets which are bussed and enter them in the required format such that all ports of the bus has been constrained.
- incase we find a non bussed lines while going through the netlist we write them as it is in the netlist to the final timing file.

![sm0pghxe](https://github.com/SATYASAIKRISHNA9/VSD--TCL--WORKSHOP/assets/79971687/500bd52d-adf7-4566-b71d-a59033cf33fe)


now we have reached the end of the read_sdc. proc
Now all the procs need to be sourcd so that they can be called in the main file.

```
puts "\nInfo: Timing Analysis Started ... "
puts "\nInfo: initializing number of threads, libraries, sdc, verilog netlist path..."
source /home/vsduser/vsdsynth/procs/reopenStdout.proc
source /home/vsduser/vsdsynth/procs/set_num_threads.proc
reopenStdout $OutputDirectory/$DesignName.conf
set_multi_cpu_usage -localCpu 8

source /home/vsduser/vsdsynth/procs/read_lib.proc
read_lib -early /home/vsduser/vsdsynth/osu018_stdcells.lib

read_lib -late /home/vsduser/vsdsynth/osu018_stdcells.lib

source /home/vsduser/vsdsynth/procs/read_verilog.proc
read_verilog $OutputDirectory/$DesignName.final.synth.v

source /home/vsduser/vsdsynth/procs/read_sdc.proc
read_sdc $OutputDirectory/$DesignName.sdc
reopenStdout /dev/tty
```

__Creating the spef file and config file__

```
if {$enable_prelayout_timing == 1} {
	puts "\nInfo: enable prelayout_timing is $enable_prelayout_timing. Enabling zero-wire load parasitics"
	set spef_file [open $OutputDirectory/$DesignName.spef w]
	puts $spef_file "*SPEF \"IEEE 1481-1998\""
	puts $spef_file "*DESIGN \"$DesignName\""
	puts $spef_file "*DATE \"Sun Jun 11 11:59:00 2023\""
	puts $spef_file "*VENDOR \"VLSI System Design\""
	puts $spef_file "*PROGRAM \"TCL Workshop\""
	puts $spef_file "*DATE \"0.0\""
	puts $spef_file "*DESIGN FLOW \"NETLIST_TYPE_VERILOG\""
	puts $spef_file "*DIVIDER /"
	puts $spef_file "*DELIMITER : "
	puts $spef_file "*BUS_DELIMITER [ ]"
	puts $spef_file "*T_UNIT 1 PS"
	puts $spef_file "*C_UNIT 1 FF"
	puts $spef_file "*R_UNIT 1 KOHM"
	puts $spef_file "*L_UNIT 1 UH"
}
close $spef_file

set conf_file [open $OutputDirectory/$DesignName.conf a]
puts $conf_file "set_spef_fpath $OutputDirectory/$DesignName.spef"
puts $conf_file "init_timer"
puts $conf_file "report_timer"
puts $conf_file "report_wns"
puts $conf_file "report_tns"
puts $conf_file "report_worst_paths -numPaths 10000 " 
close $conf_file
```
### Quaity of Results (QoR)

we have reached the final phase of the Tcl box which involves output generation as a datasheet

running sta analysis

```
set tcl_precision 3
set time_elapsed_in_us [time {exec /home/vsduser/OpenTimer-1.0.5/bin/OpenTimer < $OutputDirectory/$DesignName.conf >& $OutputDirectory/$DesignName.results} 1]
puts "time_elapsed_in_us is $time_elapsed_in_us"
set time_elapsed_in_sec "[expr {[lindex $time_elapsed_in_us 0]/100000}]sec"
#puts "time_elapsed_in_sec is $time_elapsed_in_sec"
puts "\nInfo: STA finished in $time_elapsed_in_sec seconds"
```
- logs and results are saved in the file $DesignName.results
- encountered an error where a string variable was being assigned either a null value or was bveing initialised incorrectly. It was rectified when the sdc file, __final.synth.v file__ ( string issue was from this file) and conf file was checked for errors.

Now we take the outputs of STA analysis from the .results file 
```
#-------------------------find worst output violation--------------------------------#
set worst_RAT_slack "-"
set report_file [open $OutputDirectory/$DesignName.results r]
set pattern {RAT}
while {[gets $report_file line] != -1} {
	if {[regexp $pattern $line]} {
		set worst_RAT_slack "[expr {[lindex $line 3]/1000}]ns"
		break
	} else {
		continue
	}
}
close $report_file

#-------------------------find number of output violations--------------------------------#	
set report_file [open $OutputDirectory/$DesignName.results r]
set count 0
while {[gets $report_file line] != -1} {
	incr count [regexp -all -- $pattern $line]
}
set Number_output_violations $count
close $report_file

#-------------------------find worst setup violation--------------------------------#
set worst_negative_setup_slack "-"
set report_file [open $OutputDirectory/$DesignName.results r] 
set pattern {Setup}
while {[gets $report_file line] != -1} {
	if {[regexp $pattern $line]} {
		set worst_negative_setup_slack "[expr {[lindex $line 3]/1000}]ns"
		break
	} else {
		continue
	}
}
close $report_file

#-------------------------find number of setup violations--------------------------------#
set report_file [open $OutputDirectory/$DesignName.results r]
set count 0
while {[gets $report_file line] != -1} {
	incr count [regexp -all -- $pattern $line]
}
set Number_of_setup_violations $count
close $report_file

#-------------------------find worst hold violation--------------------------------#
set worst_negative_hold_slack "-"
set report_file [open $OutputDirectory/$DesignName.results r] 
set pattern {Hold}
while {[gets $report_file line] != -1} {
	if {[regexp $pattern $line]} {
		set worst_negative_hold_slack "[expr {[lindex $line 3]/1000}]ns"
		break
	} else {
		continue
	}
}
close $report_file

#-------------------------find number of hold violations--------------------------------#
set report_file [open $OutputDirectory/$DesignName.results r]
set count 0
while {[gets $report_file line] != -1} {
	incr count [regexp -all -- $pattern $line]
}
set Number_of_hold_violations $count
close $report_file

#-------------------------find number of instances--------------------------------#

set pattern {Num of gates}
set report_file [open $OutputDirectory/$DesignName.results r] 
while {[gets $report_file line] != -1} {
	if {[regexp $pattern $line]} {
		set Instance_count "[lindex [join $line " "] 4 ]"
		break
	} else {
		continue
	}
}
close $report_file
```

once all the output data is taken from .results file, we shall format it into a datasheet and display in the termianl window 

![image](https://github.com/SATYASAIKRISHNA9/VSD--TCL--WORKSHOP/assets/79971687/fcdcc68f-a979-4c15-8a50-0a585cd78086)


```
puts "\n"
puts "						****PRELAYOUT TIMING RESULTS**** 					"
set formatStr "%15s %15s %15s %15s %15s %15s %15s %15s %15s"

puts [format $formatStr "----------" "-------" "--------------" "---------" "---------" "--------" "--------" "-------" "-------"]
puts [format $formatStr "DesignName" "Runtime" "Instance Count" "WNS Setup" "FEP Setup" "WNS Hold" "FEP Hold" "WNS RAT" "FEP RAT"]
puts [format $formatStr "----------" "-------" "--------------" "---------" "---------" "--------" "--------" "-------" "-------"]
foreach design_name $DesignName runtime $time_elapsed_in_sec instance_count $Instance_count wns_setup $worst_negative_setup_slack fep_setup $Number_of_setup_violations wns_hold $worst_negative_hold_slack fep_hold $Number_of_hold_violations wns_rat $worst_RAT_slack fep_rat $Number_output_violations {
	puts [format $formatStr $design_name $runtime $instance_count $wns_setup $fep_setup $wns_hold $fep_hold $wns_rat $fep_rat]
}

puts [format $formatStr "----------" "-------" "--------------" "---------" "---------" "--------" "--------" "-------" "-------"]
puts "\n"
```
- formatStr --> %15s represents a string identifier. When a string is called in $formatStr it will be automatically respaced because of the identifier in the object
- using foreach loop to enter the outputs in formatStr and generate the datasheet

### Final Output of Tcl Box

![image](https://github.com/SATYASAIKRISHNA9/VSD--TCL--WORKSHOP/assets/79971687/7ab140df-3c87-43e9-8557-24a5f1a7880f)

## Conclusion

- Completed all task for Tcl workshop.
- all encountered errors during simulation were rectified.

__Thank_You_see_you_again__

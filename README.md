<h1 align="center">RTL to GDSII using SKY130nm Technology node</h1>

<h1 align="center">LIFO Buffer Design</h1>

## TABLE OF CONTENT

I. [**Introduction to LIFO Buffer**](#i-introduction) 

II. [**RTL Design and Synthesis**](#ii-rtl-design-and-synthesis)  
  1. [Icarus Verilog (iverilog) & Yosys Installation on Ubuntu](#1-icarus-verilog-iverilog--yosys-installation-on-ubuntu)
  2. [RTL Pre-Simulation](#rtl-pre-simulation)  
  3. [Synthesis](#icg---synthesis)  
  4. [GLS Post-simulation](#gls-post-simulation)

## **I. Introduction**   

## **II. RTL Design and Synthesis**

### **1. Icarus Verilog (iverilog) & Yosys Installation on Ubuntu**

  //_Icarus Verilog is an open-source EDA tool for implementing verilog hardware description language_//
  
 In the context menu, right click on an empty space, you’ll see the option of ‘Open in Terminal’
 
  * Type the following command to install `iverilog & gtkwave`
 ```
$ sudo apt-get update

$ sudo apt-get install iverilog gtkwave
 ```
 
  * Type the following command to install `yosys`
 ```
 $ git clone https://github.com/YosysHQ/yosys.git
 
 $ sudo apt install make
 
 $ sudo apt-get install build-essential clang bison flex \
	libreadline-dev gawk tcl-dev libffi-dev git \
	graphviz xdot pkg-config python3 libboost-system-dev \
	libboost-python-dev libboost-filesystem-dev zlib1g-dev
  
 $ sudo make install

 ```

## RTL Pre-Simulation

Verification of the design is done at two points. First, it is applied to the initial verilog design. This process is called the Register Transfer Level (RTL) simulation. This verifies only the logic without delays. The input to this verification process is a test bench written in verilog.
To simulate and run the verilog, enter the following command in your terminal.
```
$ iverilog iiitb_lifo.v iiitb_lifo_tb.v
$ ./a.out
```
For pre-synthesis waveform:
```
$ gtkwave iiitb_lifo_out.vcd
```
![Screenshot from 2023-10-14 22-23-41](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/59b1ba9e-51d6-4ba3-8d90-e7d53d19e990)

## ICG - Synthesis

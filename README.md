<h1 align="center">RTL to GDSII using SKY130nm Technology node</h1>

<h1 align="center">LIFO Buffer Design</h1>

## TABLE OF CONTENT

I. [**Introduction to LIFO Buffer**] 

II. [**RTL Design and Synthesis**] 
  1. [Icarus Verilog (iverilog) & Yosys Installation on Ubuntu](###Icarus-Verilog-(iverilog)-&-Yosys-Installation-on-Ubuntu)
  2. [RTL Pre-Simulation]
  3. [Synthesis]
  4. [GLS Post-simulation]
     
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

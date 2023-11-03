<h1 align="center">RTL to GDSII using SKY130nm Technology node</h1>

<h1 align="center">LIFO Buffer Design</h1>

## TABLE OF CONTENT

I. [**Introduction to LIFO Buffer**](#i-introduction) 

II. [**RTL Design and Synthesis**](#ii-rtl-design-and-synthesis)  
  1. [Icarus Verilog (iverilog) & Yosys Installation on Ubuntu](#1-icarus-verilog-iverilog--yosys-installation-on-ubuntu)
  2. [RTL Pre-Simulation](#rtl-pre-simulation)  
  3. [Synthesis](#synthesis)  
  4. [Gate Level simulation](#gate-level-simulation)
     
III. [**PHYSICAL DESIGN FROM NETLIST TO GDSII**](#iii-physical-design-from-netlist-to-gdsii)
  1. [Invoke OpenLane](#invoke-openlane)
  2. [Synthesis](#synthesis)
  3. Floorplan
  4. Placement
  5. CTS

## **I. Introduction**   

LIFO is an abbreviation for last in, first out. It is a method for handling data structures where the first element is processed last and the last element is processed first.The LIFO data structure can be referred to as a stack, but not be confused with the processor stack. The LIFO differs from the processor stack because it adds and removes data of the same type, whereas the stack on the processor adds or removes different types of data depending on the type of calling convention or function. In terms of memory, this buffer usually is implemented in an array-like format but it is not an array. LIFO buffers are a contiguous piece of memory that require special methods to add and remove data. 

### **Reference circuit diagram**

![Verilog Module](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/c7fae8e9-5736-4fe0-a052-0cebfc68e676)


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

## Synthesis

1. Invoke yosys
   
![Screenshot from 2023-10-14 22-31-19](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/70b0e2ec-0ee2-4af9-a0a5-80ddec38f6db)


```
// reads the library file from sky130//

yosys> read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

```
// reads the verilog files//

yosys> read_verilog lifo.v
```

```
//synthesize the top module of verilog file//  

yosys> synth -top lifo
```

```
//map the FF library file//

yosys> dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

```

```
//Generates netlist//

yosys> abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
yosys> stat
```

```
//Simplified netlist//

yosys> flatten
```
![Screenshot from 2023-10-15 12-28-11](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/93ee2b1c-ffaf-47f9-91bb-6e7072e6d02d)


```
//Displays the Netlist circuit//

yosys> show
```
```
//Writing Netlist//

yosys>write_verilog -noattr iiitb_lifo_net.v
```

**Synthesized Circuit**

![Screenshot from 2023-10-15 12-26-56](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/44771816-bf90-415f-807a-99a443ccd7b5)

## Gate Level Simulation

```
$ iverilog -DFUNCTIONAL -DUNIT_DELAY=#1 primitives.v sky130_fd_sc_hd.v lifo_net.v lifo_tb.v
$ ./a.out
```
For post-synthesis waveform:
```
$ gtkwave lifo_out.vcd
```

![Screenshot from 2023-10-15 12-26-60](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/54fbe46b-db5d-42fb-b746-7c8ab6ca5393)

## **III. Physical Design from Netlist to GDSII**

### **Invoke Openlane**

![Screenshot from 2023-11-03 12-57-30](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/85312f90-3a8d-4572-a937-c42de2acb338)

### **Synthesis**

![Screenshot from 2023-11-03 12-59-04](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/ade10fd3-09bb-4b9e-bfbf-aba48c41d906)

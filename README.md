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
  1. [Installation of ngspice magic and OpenLane](#1-installation-of-ngspice-magic-and-openlane) 
  2. [Invoke OpenLane](#2-invoke-openlane)
  3. [Synthesis](#3-synthesis)
  4. [Floorplan](#4-floorplan)
  5. [Placement](#5-placement)
  6. [CTS](#6-CTS)
  7. [Routing](#7-routing)

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

### **1. Installation of ngspice magic and OpenLane**

**ngspice**
- Download the tarball from https://sourceforge.net/projects/ngspice/files/ to a local directory
```
cd $HOME
sudo apt-get install libxaw7-dev
tar -zxvf ngspice-41.tar.gz
cd ngspice-41
mkdir release
cd release
../configure  --with-x --with-readline=yes --disable-debug
sudo make
sudo make install
```

**magic**
```
sudo apt-get install m4
sudo apt-get install tcsh
sudo apt-get install csh
sudo apt-get install libx11-dev
sudo apt-get install tcl-dev tk-dev
sudo apt-get install libcairo2-dev
sudo apt-get install mesa-common-dev libglu1-mesa-dev
sudo apt-get install libncurses-dev
git clone https://github.com/RTimothyEdwards/magic
cd magic
./configure
sudo make
sudo make install
```

**OpenLANE**
```
sudo apt-get update
sudo apt-get upgrade
sudo apt install -y build-essential python3 python3-venv python3-pip make git

sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io
sudo docker run hello-world
sudo groupadd docker
sudo usermod -aG docker $USER
sudo reboot 
# After reboot
docker run hello-world (should show you the output under 'Example Output' in https://hub.docker.com/_/hello-world)

- To install the PDKs and Tools
cd $HOME
git clone https://github.com/The-OpenROAD-Project/OpenLane
cd OpenLane
make
make test
```

### **2. Invoke Openlane**

![Screenshot from 2023-11-03 12-57-30](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/85312f90-3a8d-4572-a937-c42de2acb338)

### **3. Synthesis**

![Screenshot from 2023-11-03 12-59-04](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/ade10fd3-09bb-4b9e-bfbf-aba48c41d906)

Area Report

![Screenshot from 2023-11-03 14-39-23](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/e644eb2f-2a56-40b2-afc6-d8701c291951)


### **4. Floorplan**

![Screenshot from 2023-11-03 12-59-24](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/17c94031-0316-479a-9633-0a312b04a978)

```
$ magic -T /home/poojar/OpenLane/vsdstdcelldesign/libs/sky130A.tech lef read /home/poojar/OpenLane/LIFO/runs/RUN_2023.11.03_07.14.55/tmp/merged.max.lef def lifo.def &
```
![Screenshot from 2023-11-03 13-04-15](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/6eb03eea-0ec8-491c-a39c-1d8ad2e2b88a)

![Screenshot from 2023-11-03 13-04-37](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/4e37b749-c731-413e-a825-4dbafebc4282)

### **5. Placement**

![Screenshot from 2023-11-03 13-05-49](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/df657263-8147-4134-b355-ca2ad6e1b5d8)

```
$ magic -T /home/poojar/OpenLane/vsdstdcelldesign/libs/sky130A.tech lef read /home/poojar/OpenLane/LIFO/runs/RUN_2023.11.03_07.14.55/tmp/merged.max.lef def lifo.def &
```
![Screenshot from 2023-11-03 13-10-00](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/032faf38-3aa3-40ef-aa1a-dc6bf7cb225e)

![Screenshot from 2023-11-03 13-10-13](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/c1ae552e-d02b-4f1b-9a2b-fdd0c2a24b3a)

### **6. CTS**

![Screenshot from 2023-11-03 13-15-20](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/eaa896f6-3d02-467c-aa60-2bf83f94f149)

#### **Reports Generated**

![Screenshot from 2023-11-03 13-22-23](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/95b739e1-1c20-4f38-8ac9-4eae18d54ed3)

![Screenshot from 2023-11-03 13-27-46](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/1bc3b90c-f5a2-49f9-a797-79f66b67561b)

![Screenshot from 2023-11-03 13-28-10](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/4dbfa7cd-fd9f-4b4e-8d00-88c1576bb355)

![Screenshot from 2023-11-03 13-29-18](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/4ad7363b-0337-40ed-b540-45a623443e1c)

![Screenshot from 2023-11-03 13-29-38](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/f4ab2026-7521-4116-b3ca-17d72a2dff0b)

![Screenshot from 2023-11-03 13-32-09](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/14c00ea5-ed40-44ae-b9d2-9eb6d3df27ae)

#### **Power Report**

![Screenshot from 2023-11-03 18-40-02](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/402dd33a-66ca-4168-8e13-84d2095cd0cf)

#### **Skew Report**

![Screenshot from 2023-11-03 18-40-21](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/0afe9cee-eedb-460d-8f3a-344ff2671bd6)

![Screenshot from 2023-11-03 18-40-47](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/87025a9c-df60-4692-81dd-864631788c71)

#### **Area Report**

![Screenshot from 2023-11-03 18-41-11](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/2a22fb80-e8c2-4eab-94dd-1b67fae247a9)


### **7. Routing**

![Screenshot from 2023-11-03 13-37-54](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/2d6b010b-e8cc-4f09-9b7b-61fc6721f666)

```
$ magic -T /home/poojar/OpenLane/vsdstdcelldesign/libs/sky130A.tech lef read /home/poojar/OpenLane/LIFO/runs/RUN_2023.11.03_07.14.55/tmp/merged.max.lef def lifo.def &
```

![Screenshot from 2023-11-03 13-45-36](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/45c002bb-d141-46bc-bc89-20c9d6edf705)

![Screenshot from 2023-11-03 13-45-55](https://github.com/PoojaR07/pes_lifo_buffer/assets/135737910/870df1bc-086f-429c-9f3b-dc759b56c8ce)

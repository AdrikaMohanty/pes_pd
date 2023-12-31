# pes_pd

<details>
  <summary>
    DAY1: Inception of Opensource EDA , Openlane and Sky130 PDK 
  </summary>
  <br>
  To develop an ASIC you need three major components 
  + ``` RTL IP ```

  + ```PDK``` : Process Design Kit ; Collection of files used to model a fabrication .

  + ```EDA Tools```

    ASIC Design flow goes from RTL to GDSII
    The flow is as follows :
    + Synthesis:Converts RTL into a circuit which has elements in the standard libraray 
    + Floor Planning: Partition of CHIP die between different system building blocks and place teh IO pads or define dimension , pin locations
    + Placement: place the cells on the floorplanned rows
    + Clock Tree Synthesis:Create clock distribution networks with minimum skew 
    + Routing: Implementation of interconnects between the different layers.
    + Sign Off: DRC , LVS and STA
   
    For this course we use OpenLane which comes with various other packages such as Yosys and abc .
    Openlane is used to harden the macros and chips , Yosys is used for RTL synthesis , ABC is used for RTL optimizations , abc maps the
    netlist converted by Yosys to a technological library .Further OpenRoad performs placement and routing .

    Given is the OPenLane flow:
    ![Screenshot from 2023-09-16 21-03-59](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/1e7d74d6-8c43-4d4e-b240-7f5f88be8107)

    

    ## Importing Package :
    Since different software dependencies are needed to run openlane you need to import package , so every time you run the interactive terminal you need to import the package
    by using the command ```package require openlane 0.9```

    ```
        cd OpenLane
        sudo make mount
        ./flow.tcl -interactive
    ```
  Here we are checking for pre defined module picorv32a

  ```
    package require openlane 0.9
    prep -design <filename>
    run_synthesis
```

  ![Screenshot from 2023-09-16 22-21-41](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/dee1cc9a-3af5-4eab-a18d-22310e026aa7)

  
  
  The task given is to find the ratio of total number of cells to d flip flop 

  ![Screenshot from 2023-09-16 22-28-34](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/28650b7a-9e9b-4a1f-a387-f0655c5ea1cf)





 From here we can see total number of cells is 9541 and dff (sky130_fd_sc_hd__dfxtp_2)is 1596, thus the ratio is 0.1672
 
</details>




<details>
  <summary>
    DAY2:Good Floorplan vs Bad Floorplan and introduction to Library cells
  </summary>
  <br>
   ## Chip floorplanning : 

  + Defining the width and height of core and die : In defining the width and height *Utilization Factor* plays an important role , UTILISATION FACTOR = Area Occupied by the Netlist / Area of the core, Aspect ratio=Height / width
  + Defining location of pre placed cells: Some IPs such as memories , clock gating cells, comparator , mux needs to be instantiated multiple times , such IPs are placed on chip before automated placement and routing .
  + De-coupling of Capacitors :Combinational blocks need to be connected to Vss and Vdd for operation . But if the circuit is large with many resistors, there might be a problem with charging and discharging of capacitors , this can lead to noise margin in the circuits ,for this we use de-coupling capacitors that is placed close to the combinational block , when switching activity takes place it detaches from circuit and the capacitance can be charged fully .
  + Power planning :  When a transition occurs on a net, charge associated with coupling capacitors may be dumped to ground. If there are not enough ground taps charge will accumulate at the tap and the ground line will act like a large resistor, raising the ground voltage and lowering our noise margin. To bypass this problem a robust PDN with many power strap taps are needed to lower the resistance associated with the PDN.
  + Pin Placement: All input pins should be placed on the left and all output pins to he right
  + Logical cell placement Blockage : Block the area occupied by the pins to prevent the PNR tool from placing logical blocks where pins are present

    ### This lab focuses on floorplanning of picorv32a , which we have previously synthesized

    ```
    cd OpenLane
    sudo make mount
    ./flow.tcl -interactive
    package require openlane 0.9
    run_synthesis
    run_floorplan
    ```
  ![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/52f3f496-fbc2-43d9-895f-78394d333a49)


  ![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/aa8949fa-8632-4ae6-9cb2-c0afe846f480)



  ![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/4da51cc5-f87e-4cc2-905e-1d9154fd741a)

  ![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/4e8d3b99-6746-4e08-8036-76d7b5f241c8)

  



  
</details>


<details>
  <summary>DAY3 : CMOS Inverter ngspice simulations</summary>

  git clone the following to get all the necessary files
  ```
cd OpenLane
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
cd vsdstdcelldesign
```

make sure your sky130A.tech file is in ```vsdstdcelldesign``` folder , if not you can copy it .

To view the layout use the following command :
```
magic -T sky130A.tech sky130_inv.mag &
```

![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/d64397ed-3638-4bcb-a767-0ca177d82910)

we perform sll the dimulstions on the spice file , to extract the spice file use the following command in tckcon window:

```
pwd
extract all
ext2spice cthresh 0 rthresh 0
ext2spice

```
![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/48ecf6b4-a777-445d-8127-7c0b59b606d7)

you need to make some changes in the spice file to get the simulation results :

![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/839c3b13-32cc-45d9-b409-44eda5318644)


to see the ngspice simulation:
```ngspice sky130_inv.spice```


![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/faf3ee6a-f41d-4036-bf30-3125ab74a7e1)



To get the transient analysis of inverter you can do 
```plot y vs time a```


![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/234693ef-0fcd-41a0-a4f2-0050dd06c6b8)


Use the drc_tests files , I have uploaded can be downloaded from there 

```
cd drc_tests
```

use the following to invoke magic :

```magic -d XR```

![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/6ea64307-9f92-4152-a3e1-2d8970affe7c)


now open met3.mag using the command
```
magic -T sky130A.tech met3.mag &
```
Typing drc why in the tckcon window gives the drc error associated 

![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/f5fa8da4-a504-4828-a978-ff5984a18083)

Load poly by typing ```load poly ``` in tkcon window 

after this you can see there is incorrect poly 

![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/e9c41b3d-8442-4a4a-aa16-9841f31833db)

To fix this:

Make the following changes in sky130A.tech file :
 Add the following lines after line 5178

 ```
spacing xhrpoly,uhrpoly,xpc allpolynonres 480 touching_illegal \
	"xhrpoly/uhrpoly resistor spacing to diffusion < %d (poly.9)"  
```

Add the following lines after line 4815
```
spacing npres allpolynonres 480 touching_illegal \
	"poly.resistor spacing to N-tap < %d (poly.9)"
```


![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/a00bb55f-e75f-4fe4-8eed-d38098377ac9)


To fix poly and diff and tap drc, make the following changes to the sky130A.tech file. Substitute the following lines in 4814 and 4815
```
spacing npres alldiff 480 touching_illegal \
	"poly.resistor spacing to N-tap < %d (poly.9)"
```

To fix nwell errors write the foolowings lines after line 4728 in sky130A.tech
```
variants (full)
cifmaxwidth nwell_untapped 0 bend_illegal \
	"Nwell missing tap (nwell.4)"
variants *
```

Add the following afetr line 1239
```
templayer nwell_tapped
bloat -all nsc nwell
 
templayer nwell_untapped nwell
and-not nwell_tapped

```

</details>


<details>
	<summary>DAY 4: Pre layout timing analysis and importance of good clock tree</summary>

 to see the tracks file :
![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/47fc7060-1772-4e60-b33d-4cd4d9040797)
The ports should be in the intersection of vertical and horizontal tracks .
To ensure that :

In magic press ```g``` this activates the grid ,

Before invoking the grid :




![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/88aea553-80fd-478f-ba24-76d3ef06a9f9)




After the grid invokement 




![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/fd03075f-51d8-47b3-8aea-ebf267f5e2f5)

save the layout with grid by typing ```save sky130_vsdinv.mag```

Open using  ```magic -T sky130A.tch sky130_vsdinv.mag```

in the console type ``` lef write sky130_vsdinv.lef```
This will create the lef file 

![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/3709e3e7-69d4-4cfc-9914-732157623882)


## Including custom cell into openlane :

Do the following :



![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/f266ab02-a770-44a5-bf3c-b796b38dda01)


![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/c31f7db5-1498-4174-8f0d-59866d109eb8)


![image](https://github.com/AdrikaMohanty/pes_pd/assets/84654826/895857c8-6bd2-4b7a-8f92-387936c9818e)




</details>

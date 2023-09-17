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
  
</details>

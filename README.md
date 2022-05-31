# Mixed-Signal-FPGA

# Overview

This workshop is a continuation of the RVMYTH RISC-V core. Once we have made the RISC-V core, we will now implement it on an FPGA and test its functionality. This workshop aims to teach the following:
- Converting .tlv file to Verilog file
- Using iverilog for simulation.
- Using Vivado
- Using ILA (Integrated Logic Analyzer)
- Adding timing constraints
- Performing timing analysis
- False paths and true paths
- FPGA programming

# Table of Contents

- [PLL (Phase-locked loop)](#pll-phase-locked-loop)



# Introduction

- A mixed-signal SOC refers to a chip with both analog and digital IP. Here the digital IP is the RISC-V core we designed previously, and the analog IP is a PLL designed by VSD. Note that this PLL is only a functional model and cannot be synthesized since it uses constructs like 'initial block'.
- For implementation on FPGA we will use an analog IP provided by Xilinx on Vivado.

## PLL (Phase-locked loop)

- A Phase-locked loop is a circuit which takes an input signal and outputs a multiple of that signal keeping the phase of both input and output signals the same.

## Transaction-Level Verilog

- Transaction-Level Verilog (TL-Verilog) is an emerging extension to SystemVerilog that supports a new transaction-level design methodology. It is being developed by Redwood EDA.
- Some notable features are:
  - No confusion between blocking vs non-blocking assignments.
  - No need to worry about the sensitivity list.
  - No confusion between data types like reg, wire, logic and bit.
  - Pipelines, transactions, and states are fundamental constructs.
  - The same IP can be utilized under a wide variety of design constraints.


## FPGAs

- FPGA stands for Field Programmable Gate Array. We can dump our designs and check their functionality on an actual chip using FPGAs. In addition, they are reconfigurable, meaning we can reprogram the device multiple times to test different designs.
- Compared to ASICs (Application-specific Integrated Circuits), the time taken to design a SOC on FPGA is significantly less.
- ASICs cannot be reconfigured and are very expensive to manufacture. It might require a respin if there is a fault in the design.
- This price is compensated by their speed since FPGAs are generally slower.

## Converting TL-Verilog to Verilog

- To convert the file type, we will use Sandpiper SaaS.
- It is developed by Redwood EDA. SandPiper is a code generator that helps you write Verilog or SystemVerilog code from TL-Verilog. Use the following command to convert the file:
- `sandpiper-saas -i rvmyth.tlv -o rvmyth.v --iArgs`
- Here is how the two files (.v and .tlv) look side by side.

![v vs tlv](https://user-images.githubusercontent.com/92947276/171103574-b54d7228-0c23-45f9-b7bf-dff8622a6d82.PNG)

- It is a comparision between the same part of the code.

## Icarus Verilog Simulation

- To check the funtionalty of our design we will be using Icarus Verilog for simulation. We will simulate the PLL by using the following commands:
  - `iverilog rvmyth_pll_tb.v rvmyth_pll.v clk_gate.v`
  - `./a.out`
  - `gtkwave rvmyth_pll.vcd`

![gtk1](https://user-images.githubusercontent.com/92947276/171110958-5e9347a0-d925-4d75-a832-8ac7e8847a3d.PNG)

![gtk2](https://user-images.githubusercontent.com/92947276/171110995-2c620093-e179-4688-a6e2-65292576150a.PNG)

- The above waveform can be obtained by changing the output type to analog.

## Vivado

- Create a new project in Vivado.
- Select the Board which you will be using.
- Once the project is created, you can add constraints, design sources and simulation sources.
- For the design sources add the Verilog files.

## IP Generation

- To add the Xilinx PLL, we will add an existing IP from the IP catalog.
- Different parameters like clock period can be changed in the menu.
- When adding the ILA (as a waveform viewer), mention the number of probes as specified in the design and keep the sample data depth maximum in order to view a clear waveform.
- Specify the width of the probes. Here is a side by side comparison of the code and IP menu:

![probe width](https://user-images.githubusercontent.com/92947276/171195181-e26d3189-e263-4de3-a7c7-bec499d92904.PNG)

## RTL Simulation

- For simulation we need to add a testbench.
- To add testbench select 'add simulation sources' and select the testbench file. This is our testbench file:

![top soc tb](https://user-images.githubusercontent.com/92947276/171196918-f1b0f45b-90fd-40ef-9de3-a53d40523203.PNG)

- Click on 'Run Simulation', this will open the waveform viewer.
- Add the core clock to compare the input clock to the PLL and the recieved clock to the RISC-V core.

![ila result](https://user-images.githubusercontent.com/92947276/171198165-fa213cef-52be-4387-bce7-cf219a197d6e.PNG)

- From the above waveform we calculate the clock frequencies. 
- To view the analog waveform change the waveform type to analog.

![ila result analog](https://user-images.githubusercontent.com/92947276/171198732-0c60dc12-f4d1-441d-b891-a0a13d543da5.PNG)

- We can compare this result to the waveform obtained by gtkwave.

## Design Constraints

- Our design might seem to be working completely fine in simulation, but in reality, we have to follow multiple timing constraints for the design to work in hardware.
- The vendors usually provide these constraints. Designs that do not follow these constraints cause setup/hold violations. These violations result in metastability (uncertainty) in the output. Here is what the constraint file looks like:

![constraint file clock](https://user-images.githubusercontent.com/92947276/171201786-28a8a1a1-b655-4b88-8958-74f5bf3fe30e.PNG)

- Add the constraints file to the project by selecting the 'add constraints' option. Then, you can choose the constraint file you want to insert from there.
- Synthesise the design, then move to implementation.
- After implementation, we see the following message:

![implementation](https://user-images.githubusercontent.com/92947276/171203846-039eb5fc-c147-4db5-b3c5-54ea49f827ef.PNG)

- We can see that the design does not meet the constraints provided.
- After opening the implemented design, we can see the timing report:

![timing report](https://user-images.githubusercontent.com/92947276/171206476-470e8c37-a066-4a32-8baa-4011a25695e8.PNG)

- For any design to meet the timing constraints, it needs to have a positive slack. We can see that the hold slack is negative. This means that the design has hold time violations.
- By clicking on the negative slack, we can view the failing paths (paths causing this violation).- We can see that the design does not meet the constraints provided.
- After opening the implemented design, we can see the timing report:

![failed path details](https://user-images.githubusercontent.com/92947276/171208079-18d8916b-670e-4000-a4e6-501cc19599fa.PNG)

- We can see that source and destination of these failing paths. These are known as false paths because we dont need them in our design:

![true paths falls path](https://user-images.githubusercontent.com/92947276/171208588-9b74ee8f-c868-41d5-af0a-89c720b459c1.PNG)

- To remove them, we will add false path constraints to our constraint file:
- `set_false_path -hold -from [get_pins uut1/inst/plle2_adv_inst/CLKOUT0] to [get_pins uut3/isnt/ila_core_inst/*/D]` 
- `set_false_path -hold -from [get_pins uut1/inst/plle2_adv_inst/CLKOUT0] to [get_pins uut3/isnt/ila_core_inst/u_trig/U_TM/N_DDR_MODE.g_NMU[2].U_M/allx_typeA_match_detection.ltlib_v1_0_0_allx_typeA_inst/*/D]`

- After saving the constraints file rerun synthesis and implementation.

![implementation success](https://user-images.githubusercontent.com/92947276/171211578-47992e78-1a34-49c7-8f8e-528c62830119.PNG)

- We can see that there are no timing erros now.

![no violation](https://user-images.githubusercontent.com/92947276/171211746-e0304424-df18-4de4-be98-1a07e62a1b32.PNG)

## Bit Stream Generation

- A bitstream includes the description of the hardware logic, routing, and initial values for both registers and on-chip memory (e.g., LUT).
- We generate the bitstream by clicking on the 'Generate Bitstream' option under 'Program and Debug'.
- We will now upload this onto our FPGA.

## Acknowledgements
- [Kunal Ghosh](https://github.com/kunalg123), Co-founder, VSD Corp. Pvt. Ltd. 
- [Steve Hoover](https://github.com/stevehoover), Founder, Redwood EDA                                   
- [Shivani Shah](https://github.com/shivanishah269), TA                                
- And all the people who helped organise this workshop. 

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

- [Introduction](#introduction)



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

- FPGA stands for Field Programmable Gate Array. We can dump our designs and check their functionality on a real chip using FPGAs. They are reconfigarable, meaning we can reprogram the device multiple times to test different designs.
- Compared to ASICs (Application specific Integrated Circuits), time taken to design a SOC on FPGA is significantly less.
- ASICs cannot be reconfigured and are very expensive to manufacture. It might require a respin if there is a fault in the design.
- This price is compensated by their speed, since FPGAs are generally slower.

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

A mixed-signal SOC refers to a chip with both analog and digital IP. Here the digital IP is the RISC-V core we designed previously, and the analog IP is a PLL designed by VSD. Note that this PLL is only a functional model and cannot be synthesized since it uses constructs like 'initial block'.
For implementation on FPGA we will use an analog IP provided by Xilinx on Vivado.

# PLL (Phase-locked loop)

A Phase-locked loop is a circuit which takes an input signal and outputs a multiple of that signal keeping the phase of both input and output signals the same.

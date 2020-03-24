sha1
====
## Implementation status ##

The core has been completed and been used in several designs. It is
considered mature and ready to use.. Minor changes are non-functional
cleanups of code.


## Introduction ##
Verilog implementation of the SHA-1 cryptgraphic hash function. The
functionality follows the specification in NIST FIPS 180-4.

The implementation is iterative with one cycle/round. The initialization
takes one cycle. The W memory is based around a sliding window of 16
32-bit registers that are updated in sync with the round processing. The
total latency/message block is 82 cycles.

There are top level wrappers that provides interface for easy
integration into a System on Chip (SoC). This interface contains mesage
block and digest registers to allow a host to load the next block while
the current block is being processed.

The implementation also includes a functional model written in Python.


## Implementation details ##

The sha1 design is divided into the following sections.
- src/rtl - RTL source files
- src/tb  - Testbenches for the RTL files
- src/model/python - Functional model written in python
- doc - documentation (currently not done.)
- toolruns - Where tools are supposed to be run. Includes a Makefile for
building and simulating the design using [Icarus Verilog](http://iverilog.icarus.com/)

The actual core consists of the following files:
- sha1_core.v - The core itself with wide interfaces.
- sha1_w_mem.v - W message block memort and expansion logic.

The top level entity is called sha1_core. This entity has wide
interfaces (512 bit block input, 160 bit digest). In order to make it
usable you probably want to wrap the core with a bus interface.

Unless you want to provide your own interface you therefore also need to
use a top level wrapper. There is one wrapper provided:
- sha1.v - A wrapper with a 32-bit memory like interface.

The core (sha1_core) will sample all data inputs when given the init
or next signal. the wrappers provided contains additional data
registers. This allows you to load a new block while the core is
processing the previous block.


The implementation implements three pipeline cycles to reach
200+ MHz clock frequency.


## FPGA-results ##

### Xilinx FPGAs ###
Implementation results using Vivado 19.2

**Xilinx Artix-7**
- Device: xc7a200tsbg484-2
- 1494 LUTs
- 1709 regs
- 204 MHz

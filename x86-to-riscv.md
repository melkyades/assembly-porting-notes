This guide just tries to provide a quick look of RISC-V ISA to Intel-x86/AMD64 programmers. It was written
and tested using Ubuntu 20.04. With other OSes and distributions YMMV.


# Basics

While x86 has a Complex Instruction Set Computer (CISC) design, RISC-V (pronounded "risk five") is a Reduced Instruction Set Computer design.
That means, among other things, that you have an optimized amount instructions, as few and as regular as possible.
RISC-V has 32, 64 and 128 bit variants, known as RV32I, RV64I and RV128I.
The I comes from integer, as the basic variant defines just a set of integer registers and basic instructions to work on them.
Over time, several extensions have been defined, which added support for multiplication and division (M), atomic instructions (A),
32-bit floating point(F), 64-bit floating point (D), etc. These four M,A,F, D, plus I, are considered the basic ones needed for general-purpose
computing and therefore are known as G.

## Registers

RISC-V base ISA has 32 integer registers (`x0` to `x31`). They are 32-bit wide on RV32, 64-bit wide on RV64 and
128-bit wide on RV128. `x0` is fixed to zero, `x1-x31` hold integer values.

# Working with RISC-V code and tools

Because RISC-V is an open platform, there exist many open source tools to compile, assemble, emulate and
simulate execution, in case you don't have RISC-V hardware at hand.
As the ecosystem is pretty new, it's useful to work on a not-very-old distribution. Ubuntu 20.04 seems
enough to work with pre-compiled tools without much effort. Some people may prefer/need to work with latest
tools by compiling everything from sources, we won't explain how to do that here, please refer to other
sources in that case.

## RARS - the RISC-V Assembler, Simulator, and Runtime

If you have java installed, this is the simplest way to get a quick look of the RISCV ISA. It is a very
small (1.3mb) and simple all-in-one IDE solution for testing and learning.
Ideal for checking out how instructions work in RV32/64.
It includes a text editor with autocompletion, assembler and graphic debugger to step through instructions
and see how registers and memory change.
Just download it from [here](https://github.com/TheThirdOne/rars/releases/latest) and run in java vm
with something like this:
```
$ java -jar rars_46ab74d.jar
```

The GUI is pretty simple and intuitive, you can create an assembly file there and assemble and step. 
On Ubuntu you can install jave with something like `sudo apt install openjdk-11-jre` (worked on 20.04).

## RISC-V GCC + Simulators/Emulators

If you want to compile and run C code, you should be able to cross-compile and link it with GNU/GCC or
CLANG. However, the ecosystem seems not mature enough to provide prebuilt packages. Unfortunately it
looks like [there are not enough resources for that](https://github.com/riscv/riscv-tools/issues/309).

If you want to [download and build all the riscv-tools from sources](https://github.com/riscv/riscv-tools),
(spoiler: it needs to download many GBs of things) then two main simulated execution
environments should be available: GCC with Spike/Proxy Kernel and GCC with QEMU and a linux kernel.
The former uses `gcc-riscv64-unknown-elf` compiler, the latter uses `gcc-riscv64-linux-gnu`.
Below we add some (incomplete) instructions to explain how to set up and run the two environments.
They shall be completed when binary packages become available.

## GCC/Spike

This is thought for embedded systems so it should be the shortest way to compile and run C code.

Install the corresponding GCC flavor: 
```
sudo apt install gcc-riscv64-unknown-elf
```

This version of GCC doesn't include a standard C library and will generate binaries that can be run inside
Spike simulator. 

## GCC/QEMU
This approach is useful to simulate a whole RISC-V linux system. The downside is that having a whole system
for just testing can be a too large overhead.

To get `qemu-system-riscv64` and `qemu-system-riscv32` installed you'll need this:

```
sudo apt install qemu-system-misc
```

Then you will also need to download an image of a RISC-V OS.

## GEM5
TBD.

# RISCV Assembly and Instructions

Instructions on RISC-V are 32-bit fixed size (there's a compressed 16-bit format C extension though).
It is a load-store architecture, which means you don't have indirect operands such as in `add rax, [rdx]`.
Instead, to use data in memory you first have to load it into a register, and the same to write it. This
is usually not a problem because of the large number of registers available compared to x86.

Of course, there is no Intel syntax (the one available in assemblers like NASM) in RISC-V, so you'll
typically have to stick to AT&T syntax (yuck!).

## Arithmetic and Logical instructions

Instead of the two-operand instructions typical to `x86`, RISC-V instructions are 3-operand: one destination
and two sources. To give an example, let's compare register addition. To do c <- a + b, in `x86` you would do:

```
mov rcx, rax
add rcx, rbx
```
assuming a, b and c are in `rax`, `rbx` and `rcx` respectively. 
In RV64 you would instead do:

```
add x3, x1, x2
```

assuming x1, x2 and x3 correspond to a, b and c respectively.

Unlike `x86`, in RISC-V there's no `mov dstReg, srcReg` instruction, instead you do `add dstReg, x0, srcReg` and
voila!, RISC architecture in all of its glory.

## Load and store instructions

RISC-V is little endian, the same than x86, which means that lower bytes of integers are stored in lower
addresses in memory.

# Useful links

- [The RISC Instruction Set Manual](https://riscv.org/wp-content/uploads/2017/05/riscv-spec-v2.2.pdf)
- [RISC-V Assembly Programmers Manual](https://github.com/riscv/riscv-asm-manual/blob/master/riscv-asm.md)



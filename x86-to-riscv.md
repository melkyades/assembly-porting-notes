This guide just tries to provide a quick look of RISC-V ISA to Intel-x86/AMD64 programmers. It was written
and tested using Ubuntu 20.04. With other OSes and distributions YMMV.

# Working with RISC-V code and tools

When learning about RISC-V it can be very useful to have a working system even if you don't have RISC-V
hardware at hand.
Because RISC-V is an open platform, there exist many open source tools to compile, assemble, emulate and
simulate execution.
The ecosystem is pretty new, so it's useful to work on a not-very-old distribution. Ubuntu 20.04 seems
enough to work with pre-compiled tools without much effort. Some people may prefer/need to work with latest
tools by compiling everything from sources, we won't explain how to do that here, please refer to other
sources in that case. In the next section we show hot to setup RARS IDE. 
There many other much more powerful tools than RARS, but they require more work to setup. 
In case you are interested in those tools, [this document](riscv-tools-setup.md) should explain what the
options are and how to set them up.


## RARS - the RISC-V Assembler, Simulator, and Runtime

RARS is the recommended way to go if you are just starting and want to get a grasp of the most basic
things in RISC-V: registers, instructions and assembly syntax. 
If you have java installed, RARS is the simplest way to get a quick look of the RISCV ISA. It is a very
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
On Ubuntu you can install java with something like `sudo apt install openjdk-11-jre` (worked on 20.04).


# RISC-V Basics

While x86 has a Complex Instruction Set Computer (CISC) design, RISC-V (pronounded "risk five") is a Reduced Instruction Set Computer design.
That means, among other things, that you have an optimized amount instructions, as few and as regular as possible.
RISC-V has 32, 64 and 128 bit variants, known as RV32I, RV64I and RV128I.
The I comes from integer, as the basic variant defines just a set of integer registers and basic instructions to work on them.
Over time, several extensions have been defined, which added support for multiplication and division (M), atomic instructions (A),
32-bit floating point(F), 64-bit floating point (D), etc. These four M,A,F, D, plus I, are considered the basic ones needed for general-purpose
computing and therefore are known as G.

## Registers / C ABI

RISC-V base ISA has 32 integer registers (`x0` to `x31`). They are 32-bit wide on RV32, 64-bit wide on RV64 and
128-bit wide on RV128. `x0` is fixed to zero, `x1-x31` hold integer values. There's one extra register, `pc`,
equivalent to `rip` in `x86`.
The integer registers can also be referenced by their _ABI name_, which correspond to their typical use when
dealing with RISC-V C ABI. For example, `x2` is used to store the stack pointer, and its ABI name is `sp`
(equivalent to `rip` in AMD64). The complete list is:


```
Reg  ABI Name      Usage                    saved by    more info, comparison to x86
 x0     zero     hard-wired zero              -         does not exist in x86
 x1       ra     return address             caller      used for calls (`jal` and `jalr` in RISC-V terminology),
                                                        instead of directly pushing to stack. See more later.  
 x2       sp     stack pointer              calle       equivalent to `rsp`
 x3       gp     global pointer             fixed       for global data, constants and static variables
 x4       tp     thread pointer             fixed       for thread local data, similar to `fs` and `gs` registers in `x86`.
 x5       t0     temporary 0                caller
 x6       t1     temporary 1                caller
 x7       t2     temporary 2                caller
 x8    fp/s0     frame pointer              callee      equivalent to `rbp`
 x9       s1     saved reg1                 callee
x10       a0     1st argument/1st retval    caller      in `x86` 1st ret value goes in `rax`, first arg in `rdi` (linux)/`rcx`(win) 
x11       a1     2nd argument/2nd retval    caller      in `x86` 2nd ret value goes in `rdx`, first arg in `rsi` (linux)/`rdx`(win) 
...
x17       a7     7th argument               caller
x18       s2     saved reg0                 callee
...
x27      s11     saved reg11                callee
x28       t3     temporary 3                caller
...
x31       t6     temporary 6                caller
```

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



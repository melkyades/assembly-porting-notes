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



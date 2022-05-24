#rev
# Reversing index
---
## Rev tips and tricks
- Binary Ninja tools and tricks I've found [[Binary Ninja]]
- Cool things I've come across with pwndbg and pwntools: [[Pwndbg and Pwntools]]
- Working with weird architectures [[Notes on reversing random architectures]]
- Opcode mappings: [Link](https://pnx.tf/files/x86_opcode_structure_and_instruction_overview.pdf)
- 

## Redressing a stripped libc

- Often times when we do pwnables, we are given the pwnable along with a stripped version of the libc that the pwnable is using on the remote server. If we want an easier time debugging with the provided libc preloaded, here are some steps we can take to add symbols back to the stripped libc. (dependencies: [eu-unstrip](https://helpmanual.io/help/eu-unstrip/))
    1.  run `strings <stripped-libc> | grep glibc` to determine the libc version
    2.  download the associated debug symbol file (eg.[https://launchpad.net/ubuntu/xenial/amd64/libc6-dbg/2.23-0ubuntu5](https://launchpad.net/ubuntu/xenial/amd64/libc6-dbg/2.23-0ubuntu5))
    3.  merge stripped libc file with debug symbol file using `eu-unstrip` like so: `eu-unstrip <stripped-libc> <symbol-file>`
    4.  now `<symbol-file>` will be your newly redressed libc w/symbols!
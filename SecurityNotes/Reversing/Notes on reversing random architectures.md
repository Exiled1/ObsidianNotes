
## Working with foreign architectures
[Link](https://ropemporium.com/guide.html) to where I'm getting the info

#arm
## ARMv5
Running programs compiled for foreign architectures is much easier than it used to be, this example uses **qemu-user** to achieve this. The setup is simple, and only involves installing 2 packages and creating a symlink:
```
$ sudo apt install qemu-user  
$ sudo apt install libc6-armel-cross  
$ sudo mkdir /etc/qemu-binfmt  
$ sudo ln -s /usr/arm-linux-gnueabi /etc/qemu-binfmt/arm
```

This is enough to let you run the ARMv5 challenges from the command line as if they were native binaries. Be aware that at the time of writing, **qemu does not support ASLR in this configuration**. You'll just have to suspend your disbelief and imagine that the stack, heap & libraries are all subject to ASLR when building your ROP chains.

### Workarounds
Pwntools has a way to run these kinds of executables. Here's a [link](https://docs.pwntools.com/en/stable/qemu.html)

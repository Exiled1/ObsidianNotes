#heap 

# Heap Index
---
So this index is to help me keep track of everything I've learned. As well as link to challenges, writeups, and pretty much anything I've learned while doing heap exploitation.

## Note links
- Learning about the heap and its internals: [[Heap learning]]
- Note's on exploiting the heap: [[Heap Exploitation]]
- Heap exploitation and demonstrations of different attacks: [Link](https://heap-exploitation.dhavalkapil.com/)
- more chals [link](https://exploit.education/protostar/) 

## Stuff I've learned
- A single tcache bin contains at **most 7** chunks by default. (It's just a feature in GLIBC)
- There exists a thing called the **wilderness** which is a massive block of free memory.
- **Coalescing** is the act of merging two free chunks next to each other.
	- You can prevent this by having an allocated block of memory between two freed blocks of memory.
	- Example `( val )` represents something freed: `(a) -> b -> (c) -> d -> [wilderness]`
		-  As you can see, we have two freed nodes with an allocated node between them. This prevents them from coalescing into one large free chunk. the ` d ` node exists to prevent node `(c)` from coalescing with the `[wilderness]` block.
- `__free_hook` is a malloc debugging utility function **(removed after glibc > 2.33)** 
	- `__free_hook` is a pointer to something that gets executed whenever you free a chunk of memory. Nothing's supposed to go in it. It's **initialized to 0x0/NULL.**
	- Can be hijacked and- if written into- can be used to execute malicious code. 
- Unsorted chunks form a doubly linked list* (maybe, Andrew said not to quote him on that.)
	- Example: `Unsorted chunks := [10] -> [8] -> [libc]` 

### Libc specific stuff
- Libc quirks and table of contents for stuff I've learned about specific ones: [[libc and their quirks]]
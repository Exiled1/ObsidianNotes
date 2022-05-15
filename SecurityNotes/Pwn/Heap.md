#heap 

# Heap Index
---
So this index is to help me keep track of everything I've learned. As well as link to challenges, writeups, and pretty much anything I've learned while doing heap exploitation.

## Stuff I've learned
- A single tcache bin contains at **most 7**  chunks by default. (It's just a feature in GLIBC)
- There exists a thing called the **wilderness** which is a massive block of free memory.
- **Coalescing** is the act of merging two free chunks next to each other.
	- You can prevent this by having an allocated block of memory between two freed blocks of memory.
	- Example `( val )` represents something freed: `(a) -> b -> (c) -> d -> [wilderness]`
		-  As you can see, we have two freed nodes with an allocated node between them. This prevents them from coalescing into one large free chunk. the ` d ` node exists to prevent node `(c)` from coalescing with the `[wilderness]` block.
- Unsorted chunks form a doubly linked list* (maybe, Andrew said not to quote him on that.)
	- Example: `Unsorted chunks := [10] -> [8] -> [libc]` 
	- 
- Glibc 2.33 and things I've learned through challenges: [[libc 2.33]]
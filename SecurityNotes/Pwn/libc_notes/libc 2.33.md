#libc-2-33

## Safety features
**Safelinking**
- Safelinking is a defense mechanism made to prevent tcache poisoning.
- Tcache poisoning is overriding the tcache forward pointer.
- With safelinking we need an **additional heap pointer leak** to correctly mitigate it.
- **How to beat it:**
	- Essentially, what safelinking does is it adds a random mask to pointers (that takes advantage of ASLR) which **signs** the pointers in a singly linked list. 
	- The actual protection itself follows really stupid logic, that being:
		- `Mask := (L >> PAGE_SHIFT)` 
		- Where `L` is the single-linked list pointer.
	- We get around it by obfuscating our pointer with an offset from heap base (I believe it's heap base) so that when it goes into the translation/reveal step we have the following calculation:
		- `original_ptr = target_ptr ^ ((secondary_leak + offset) >> 12)` or whatever the page shift value is here. Essentially this is done so that we XOR the mask away to recover the target/original pointer.

### Safe linking background
Attackers that modify the `FD` and `BK` pointers of double-linked lists (such as the Small-Bins, for example) can use the `unlink()` operation to trigger an Arbitrary-Write, and thus achieve code execution over the target program.

From glibc 2.3.6 and above, **safe-unlinking** was introduced. Which "verifies" (might be up for debate) the integrity of a **doubly-linked** node before unlinking it. Doing this **prevents** arbitrary-writes using the unsafe unlinking exploit technique.

However, **safelinking** is actually the proposed solution for singly linked lists (also it's worth noting that **Fast-Bins** are singly linked lists).

### Fast-Bin sample exploitation
For this example I got it off of a [blog](https://research.checkpoint.com/2020/safe-linking-eliminating-a-20-year-old-malloc-exploit-primitive/) that talked about an exploitation against a micro Libc which used this meta-data for the heap implementation.

```c
struct malloc_chunk{
	size_t prev_size; /* Size of previous chunk (if free).*/
	size_t size; /* Size in bytes, including overhead */
	struct malloc_chunk* fd; /* double links --used only if free */
	struct malloc_chunk* bk;
}
```

#### Stolen Notes
- When a buffer is allocated and used, the first two fields are stored **before** the user’s data buffer.
- When the buffer is freed and placed in a Fast-Bin, the third field is also used and points to the next node in the Fast-Bin’s linked list. This field is located at the first 4 bytes of the user’s buffer.
- When the buffer is freed and isn’t placed in a Fast-Bin, both the third and fourth fields are used as part of the doubly-linked list. These fields are located at the first 8 bytes of the user’s buffer.

**Fast-Bins** are an array of various sized "bins," each of which hold a 

### Protection explanation
This layer of protection basically stops an attacker when they have no knowledge about the ASLR offset while modifying the pointer to something they control.

All allocated chunks in the heap are aligned to a known fixed offset which is usually 8 bytes on 32 bit machines, and 16 on 64 bit machines. By checking that each `reveal()`ed pointer is aligned accordingly, we add two important layers:
-   Attackers must correctly guess the alignment bits.
-   Attackers can’t point chunks to unaligned memory addresses.

**However,** even on its own, this alignment check prevents known exploit primitives, such as the one described in this [article](https://quentinmeffre.fr/exploit/heap/2018/11/02/fastbin_attack.html) that describes how to point the Fast-Bin at the `malloc()` hook to immediately gain code execution.
- **Side Note:** On intel CPUs, glibc still uses an alignment of 0x10 bytes on both 32 bits **and** 64 bits architectures, unlike the common case we just described above. This means that for glibc, we provide enhanced protection on 32 bits, and can statistically block 15 out of 16 attacks attempts.


### Safelinking Issues
Just like Safe-Unlinking (for double-linked lists), this protection relies on the fact that the attacker **doesn’t** know what **legitimate heap pointers** look like. In the double-linked list scenario, an attacker that can forge a memory struct, and knows what a valid heap pointer looks like, can successfully forge a valid `FD`/`BK` pair of pointers that won’t trigger an Arbitrary-Write primitive, but allows a chunk at an attacker-controlled address.
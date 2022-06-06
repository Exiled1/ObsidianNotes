#Heap 

# Understanding **glibc** malloc.
---
[Link](https://sploitfun.wordpress.com/2015/02/10/understanding-glibc-malloc/) to summarized article.


Turns out that there are multiple memory allocators that exist:

>- dlmalloc – General purpose allocator
>- ptmalloc2 – glibc
>- jemalloc – FreeBSD and Firefox
>- tcmalloc – Google
>- libumem – Solaris
>- …etc.

---
(This is incredibly useful as a refresher, use obsidians table of contents to quickly traverse it.)

## Useful terms
**Arena**
- A structure that is shared among one or more threads which contains references to one or more heaps, as well as linked lists of chunks within those heaps which are "free". Threads assigned to each arena will allocate memory from that arena's free lists.

**Heap**
- A contiguous region of memory that is subdivided into chunks to be allocated. Each heap belongs to exactly one arena.

**Chunk**
- A small range of memory that can be allocated (owned by the application), freed (owned by glibc), or combined with adjacent chunks into larger ranges. Note that a chunk is a wrapper around the block of memory that is given to the application. Each chunk exists in one heap and belongs to one arena.
**Coalesce**
- When two chunks of memory (except fast bin chunks) that are free are adjacent to each other, they're combined into a single free chunk.

**Tcache**
- These are called Thread Localized Caches. Otherwise known as **tcaches**
- You can learn more about the internals of the tcache at the [[TCache Explanation and attacks]] section.

**Bin**
- Within every arena, chunks are marked as either **in use**, or they're marked as **free**.
- Freed chunks are stored in various **lists** based on their size, and history in order to best find chunks that can service an allocation request. These "lists" are called **bins!**. 
- Below you'll find an overview for each type of bin!

[material citation](https://sourceware.org/glibc/wiki/MallocInternals)

**Incredibly useful link to bins and chunks**: [link](https://heap-exploitation.dhavalkapil.com/diving_into_glibc_heap/bins_chunks#fast-bins)

## Bins and Chunks:

### Fast bins
- Smaller chunks are stored in **size-specific** bins. Chunks that are added to a fast bin are not combined with adjacent chunks (see **coalesce**).
- Chunks stored in fastbins could be moved to another bin if the allocator deems it necessary.
- Fastbin chunks are stored in a **singly linked list**. Addition and deletion happen in a LIFO manner from the front of the list.
- There are **10** fast bins.
- Refer to [[Fast Bin]] for more specific information.

### Unsorted bin
- There is only **1** unsorted bin. Small and large chunks, when freed, end up in this bin.
- The primary purpose of this bin is to act as a cache layer (kind of) to speed up allocation and deallocation requests.
- Uses a **circular double linked list** (see **binlist**) to keep track of freed chunks.
- There's no size restriction with the unsorted bin.

### Small bins
- There are **62** small bins. Small bins are faster than large bins but slower than fast bins. Each bin maintains a **doubly-linked** list. Insertions happen at the '**HEAD**' while removals happen at the '**TAIL**' (in a FIFO manner).
- Small bins are composed of chunks that are **8 bytes apart**.
- Like fast bins, each bin has chunks of the same size. The 62 bins have sizes: 16, 24, … , 504 bytes.
- While freeing, small chunks may be coalesced together before ending up in unsorted bins.

### Large bins
- There are **63** large bins. Each bin maintains a doubly-linked list. A particular large bin has chunks of different sizes, sorted in decreasing order (i.e. largest chunk at the 'HEAD' and smallest chunk at the 'TAIL'). Insertions and removals happen at any position within the list.

- The first **32** bins contain chunks which are **64** bytes apart:
	- 1st bin: 512 - 568 bytes 
	- 2nd bin: 576 - 632 bytes
	- etc.

```

No. of Bins       Spacing between bins

64 bins of size       8  [ Small bins]
32 bins of size      64  [ Large bins]
16 bins of size     512  [ Large bins]
8 bins of size     4096  [ ..        ]
4 bins of size    32768
2 bins of size   262144
1 bin  of size what's left
```

- Like small chunks, while freeing, large chunks may be coalesced together before ending up in unsorted bins.

---
## Special chunk types

There are **two special types** of chunks which are **not** part of any bin.

### Top chunk

It is the chunk which borders the top of an arena. While servicing 'malloc' requests, it is used as the last resort. If still more size is required, it can grow using the `sbrk` system call. The `PREV_INUSE` flag is always set for the top chunk.

### Last remainder chunk

It is the chunk obtained from the last split. Sometimes, when exact size chunks are not available, bigger chunks are split into two. One part is returned to the user whereas the other becomes the last remainder chunk.

## Recycling memory with bins

There are 5 type of bins: 62 small bins, 63 large bins, 1 unsorted bin, 10 fast bins and 64 tcache bins per thread.

The small, large, and unsorted bins are the oldest type of bin and are used to implement what I’ll refer to here as the basic recycling strategy of the heap. The fast bins and tcache bins are optimizations that layer on top of these.

Confusingly, the small, large, and unsorted bins all live together in the same array in the heap manager’s source code. Index 0 is unused, 1 is the unsorted bin, bins 2-64 are small bins and bins 65-127 are large bins.

```
bin[0] = Not Used
bin[1] = Unsorted bin
bin[2] through bin[63] = small bin
bin[64] through bin[126] = large bin
```

---



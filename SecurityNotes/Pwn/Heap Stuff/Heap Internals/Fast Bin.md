# Fast Bins
---

Chunks that are between **16 to 80 bytes** are called fast chunks. Bins that contain fast chunks are called fast bins. Fast bins have special properties that other bins don't have, which makes fast bins much faster in allocating and deallocating memory.

## Keywords
- **Binlist:** A list of free chunks.
---
Here's a little overview of a fast bin:

## Number of bins - 10
- Each fastbin contains a **singly linked list** (see **binlist**) of freed chunks.
- A **singly linked list** is used due to the fact that fast bin chunks are only accessed in a LIFO manner.
- Due to this nature, deletion and addition into a fastbin occur from the front, aka LIFO.

## Chunk size - 8 bytes apart
- Fast bins all contain a **binlist** which are **8 bytes** apart in terms of **size**.
- Ex: You have the first fast bin (at index 0) which contains a binlist of **16 byte chunks**. The second fast bin (at index 1) will contain a binlist of **24 byte chunks**, and the cycle goes on until the **80 byte chunks** (at index 9).
- Due to the above chunk constraint, fastbin chunks are all the same size within their own sized bins.

## Coalescing behavior
- **No Coalescing –** Two chunks which are free can be adjacent to each other, it doesn't get combined into single free chunk.


## Malloc Initialization behavior
- During [malloc initialization](https://github.com/sploitfun/lsploits/blob/master/glibc/malloc/malloc.c#L1778), maximum fast bin size is [set](https://github.com/sploitfun/lsploits/blob/master/glibc/malloc/malloc.c#L1795) to [64](https://github.com/sploitfun/lsploits/blob/master/glibc/malloc/malloc.c#L794) (!80) bytes. Hence by default chunks of size 16 to 64 is categorized as fast chunks.


## Malloc behavior post-initialization
- Initially [fast bin max size](https://github.com/sploitfun/lsploits/blob/master/glibc/malloc/malloc.c#L1765) and [fast bin indices](https://github.com/sploitfun/lsploits/blob/master/glibc/malloc/malloc.c#L1680) would be empty and hence even though user requested a fast chunk, instead of [fast bin code](https://github.com/sploitfun/lsploits/blob/master/glibc/malloc/malloc.c#L3330), [small bin code](https://github.com/sploitfun/lsploits/blob/master/glibc/malloc/malloc.c#L3367) tries to service it. ([citation](https://sploitfun.wordpress.com/2015/02/10/understanding-glibc-malloc/))
- Later when its not empty, fast bin index is [calculated](https://github.com/sploitfun/lsploits/blob/master/glibc/malloc/malloc.c#L3332) to retrieve its corresponding binlist.([citation](https://sploitfun.wordpress.com/2015/02/10/understanding-glibc-malloc/))
- First chunk from the above retrieved binlist is [removed](https://github.com/sploitfun/lsploits/blob/master/glibc/malloc/malloc.c#L3341) and [returned](https://github.com/sploitfun/lsploits/blob/master/glibc/malloc/malloc.c#L3355) to the user. ([citation](https://sploitfun.wordpress.com/2015/02/10/understanding-glibc-malloc/))

## Free behavior
- Fast bin index is [calculated](https://github.com/sploitfun/lsploits/blob/master/glibc/malloc/malloc.c#L3887) to retrieve its corresponding binlist.
- This free chunk gets [added at the front position](https://github.com/sploitfun/lsploits/blob/master/glibc/malloc/malloc.c#L3908) of the above retrieved binlist.

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

**System calls:** Malloc internally calls the `brk` or `mmap` syscalls.

**Threading:** This act of maintaining separate heap and free-list data structures for each thread is called **per thread arena**.




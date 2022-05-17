#heaptechnique 

# First Fit technique

This one's pretty is pretty simple, glibc uses a **first-fit** algorithm to choose a free chunk.
Essentially, if a chunk is free and large enough, malloc will select this chunk. 

This type of malloc internal can be abused in a use-after-free scenario.

---

## Description

Whenever any chunk (**not a fast chunk**) is freed, it ends up in an **unsorted** bin. Insertion happens at the **HEAD** of the list. Whenever a new chunk (**Still not fast chunks**), initially unsorted bins will be looked up as small bins, and will be empty.

The lookup starts from the **TAIL** of the list. If a single chunk is present in the unsorted bin, an exact check is not made and if the chunk's size exceeds the one requested, it's split into two and returned. This gives us first in first out behavior (**read FIFO**).

Example:

```c
char *a = malloc(300);    // 0x***010
char *b = malloc(250);    // 0x***150

free(a);

a = malloc(250);          // 0x***010
```

The state goes like:

1. 'a' freed.
   - `head -> a -> tail`

1. 'malloc' request.
   - `head -> a2 -> tail [ 'a1' is returned ]`



## Struct Definition:
```c
#[repr(C)]
struct cart {
	cart+0: char* | str,
	cart+4: char, // gives unprintable chars, ex: 199 is C with chin.
	cart+8: int,
	cart+12: cart*, n
}
```
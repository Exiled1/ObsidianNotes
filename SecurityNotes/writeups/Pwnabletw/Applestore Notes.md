
## Struct Definition:
```c
#[repr(C)]
struct cart {
	cart+0: char* | str,
	cart+4: char, // gives unprintable chars, ex: 199 is C with chin.
	cart+8: int,
	cart+12: cart*, // Next 
}
```

## Regular functions
Create, seems to make a product that goes into cart.
- Takes a string and char as a parameter, 199 - 399

## Interesting functions:

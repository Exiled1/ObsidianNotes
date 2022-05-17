#binja


## Binja tricks for easier rev
- Okay, so I figured it out, you have to press `o` on the random pointers to turn them into pointers, THEN you can press `s` to turn them into a struct.
	- This lets you 
	- This is if the stuff looks like ```
```
*((current_note << 2) + 0x804a050) = malloc(bytes: 8)
```

---
## Custom demangling (probably useful for Rust rev)
```python

import subprocess
for func in bv.functions:
    if func.name.startswith("__T") or func.name.startswith("_T"):
        func.name = subprocess.check_output(["/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/swift-demangle", "-compact", func.name])
```


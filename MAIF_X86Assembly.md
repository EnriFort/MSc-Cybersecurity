# Malware Analysis and Incident Forensics Notes 🦠🔍
## X86 Assembly 
##### The goal of these notes is to understand how to read and analyze assembly code.

### Analyzing binary code
Reverse engineering of binary code, i.e., take a piece of code and understanding it, is difficult. Thankfully, only some portions of a malware sample really require it (there are parts that you can "skip"). 

**Malicious code** doesn't look always **compiler-generated code** because:
- some parts are hand-written;
- certain instructions can rewrite themselves;
- they use subtle semantics;
- they employ obfuscation;
- they contain other decoys. 

Hovewer, there are some tools that helps, like **decompilers** (e.g. `Ghidra` for free, `Hex-Rays`, only some version for free, `rev.ng`), which try to reconstruct a high-level C-like representation of a binary code fragment. 

Even though this process is far from perfect due to: 
- information loss during compilation, for example types (in assemply integer or unsigned integer are the same thing);
- decompiling obfuscated code return an obfuscated source;

neverthleless they are quite usefull for reverse-engineering professionals, provided you are confident enough in their use. 

### CPU and Memory Basics




# The Firmware Handbook by Jack Ganssle

## Chapter 18: Understanding Your C Compiler (How to Minimize Code Size)

Sturcture of a Compiler:
- Parser: Convertion to intermidiate language.
- High-Level Optimization: Optimizations on intermidiate language.
- Code Generation: Generation of target machine code.
- Low-Level Optimization: Optimizations on machine code.
- Assembly: Generation of an object file that can be linked from the target machine code.
- Linking: Linking of all the code for a program into an executable file.

 -- Optimization is a bit misnomer (Transformation is more suitable ). Not always the optimizations improves the code.
 -- The goal of the code generator is to keep as many values as possible in registers, since register-based operations are typically faster and smaller than memory-based operations.
  

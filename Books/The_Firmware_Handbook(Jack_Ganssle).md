# The Firmware Handbook by Jack Ganssle

## Chapter 18: Understanding Your C Compiler (How to Minimize Code Size)

Sturcture of a Compiler:
- Parser: Convertion to intermidiate language.
- High-Level Optimization: Optimizations on intermidiate language.
- Code Generation: Generation of target machine code.
- Low-Level Optimization: Optimizations on machine code.
- Assembly: Generation of an object file that can be linked from the target machine code.
- Linking: Linking of all the code for a program into an executable file.
 
Key Notes:
- Optimization is a bit misnomer (Transformation is more suitable ). Not always the optimizations improves the code.
- The goal of the code generator is to keep as many values as possible in registers, since register-based operations are typically faster and smaller than memory-based operations.
- All code that is not considered useful is removed (according to the compiler), but could be something useful for you. E.G: Empty loops to use it as delay.
- While optimizing most of the time you had 2 ways:  **Speed** or **Size**. Usually one affects the other. In some compilers you can select which file you optimize by size and which by speed.
  
#### Register Allocation
The compiler will prefer assign the variables function to registers (Core Registers). The processors give better performance and require smaller code when calculations are performed using registers.

The compiler will put in register the variables used in a small part of the code.
When the variable is "optimized away" by the debugger is because the compiler put that variable in a register.

If you don't want to have a variable in a register use the keyword **Volatile**

#### Function Inlining
The compiler will try to not do a function call if it is small and it is called in various places, the compiler will copy his code where it's calling instead of calling it. This could cause gain in speed but also gain in code size.

### Tips on Programming: Write compiler-friendly code.

#### Use the Right Data Size

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
- **Use the Right Data Size**: Choose the correct data type depending on the architecture of the cpu. It's more efficient a uint8_t operation in a 8bit cpu than a uint32_t operation.
- **Use the Best Pointer Types**
- **Structures and Padding**: The compiler will insert padding between structure members to align each member. One advice is to define the member in decreasing size order to avoid the padding. For example in a 32 bit uC:
```
struct s { 
uint8_t a;  /* 1 byte */
            /* 3 bytes of padding */
uint32_t b; /* 4 bytes */
uint16_t c; /* 2 bytes */
int8_t d;   /* 1 byte */
            /* 1 byte padding */
int32_t e;  /* 4 bytes */
};
```
- **Use Function Prototypes**
- **Use Parameters instead of Global Var**: avoid global variables a use parameters in functions calls.
- **Do Not Take Addresses of a Local Var**: Only take addresses of a local variable if you really must. Because, if you take his address is not likely that the variable is allocated to a register.
- **Do Not Use Inline Assembly Language**: Do not mix C and Assembly code in the same function. The compiler cannot optimize it and your code is going to loss portability.
- **Do Not Write Clever Code**: Keep in mind that, Clever code that is hard to read is also hard to compile. So make sure that your code is not doing more operations only to save some lines of code. Usually is better so use a Straightfordwar solution.
- **Use Switch for Jump Tables**: Use Switch statements for the Functions Jumps intead of and if/else. It will generate smaller code and make the flow explicit.
- **Investigate Bit Fields Before Using Them**: Test your compiler bitfield layout. In the case you assing a value of 1 byte into a 4 byte variable, the compiler could ompimize it. The advice is to test a few bitfield var and check that the bit layout is as expected.
- **Watch Out for Library Functions**
- **Use Extra Hints**: Help the compiler with useful information. For example if two pointers or array arguments are unaliased.

## Chapter 19: Optimizing C and C++ Code
  

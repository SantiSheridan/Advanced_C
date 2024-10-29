# The Firmware Handbook by Jack Ganssle

## Chapter 4: Tools and Methods for Improving Code Quality


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
- **Use the Best Pointer Types**: Use the smallest pointers you can, and avoid any form of generic pointer unless necessary.
- **Structures and Padding**: The compiler will insert padding between structure members to align each member. One advice is to define the member in decreasing size order to avoid the padding.

For example in a 32 bit uC:

```c
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
- **Use Function Prototypes**: Always prototype your functions to help the compiler to know which functions you're going to declare and his parameters type.
- **Use Parameters instead of Global Var**: avoid global variables and instead use parameters in functions calls.
- **Do Not Take Addresses of a Local Var**: Only take addresses of a local variable if you really must. Because, if you take his address is not likely that the variable is allocated to a register.
- **Do Not Use Inline Assembly Language**: Do not mix C and Assembly code in the same function. The compiler cannot optimize it and your code is going to loss portability.
- **Do Not Write Clever Code**: Keep in mind that, Clever code that is hard to read is also hard to compile. So make sure that your code is not doing more operations only to save some lines of code. Usually is better so use a Straightfordwar solution.

 For example, consider the two ways to set the lowest bit of variable b if the lower 21 bits of another (32-bit) variable are non-zero. The straightfordward solution is performing 1 less operations with the cost of 1 line more.

```c
unsigned long int a;
unsigned char     b;

/*Clever Solution*/
b |= !!(a << 11);

/*Straightforward Solution*/
if( (a & 0x1FFFF) != 0)
    b |= 0x01;
```
- **Use Switch for Jump Tables**: Use Switch statements for the Functions Jumps intead of and if/else. It will generate smaller code and make the flow explicit.
- **Investigate Bit Fields Before Using Them**: Test your compiler bitfield layout. In the case you assing a value of 1 byte into a 4 byte variable, the compiler could ompimize it. The advice is to test a few bitfield var and check that the bit layout is as expected.
- **Watch Out for Library Functions**: If a small change to a program cause a big change in program size, look at the library functions. Sometimes in the library you can bring in floating points, even if no floating point variables are being use because of a expression like the following:
```c
#define ImportantRatio 1.95
int tmp;
tmp = CONSTANT * ImportantRatio;
```
- **Use Extra Hints**: Help the compiler with useful information. For example if two pointers or array arguments are unaliased.

## Chapter 19: Optimizing C and C++ Code

### Adjust Structure Sizes to Power of Two
Keeping structure sizes aligned to a power of 2 will improve performance in array indexing. Because the compiler will perform a shift operation instead of a multiplication in the indexing.

### Place Case Labels in Narrow Range and Frequent Case First
In the case the values are in a narrow range (1,2,3,4,5) the compiler does not generate if-else-if cascade for switch statement. Instead, it generates a jump table of case labels. This jump table is faster and is independent of the number of case entries in the switch statement.
By placing the frequent case labels first, you can reduce the number of comparisons that will be performed for frequently occurring scenarios.

### Minimize Local Variables
If the number of local variables in a function is small, the compiler will be able to fit them into registers.

### Declare Local Variables in the Innermost Scope
In the case you have a object that you need only in the error case, you should invoked only inside the error check.
```c
int foo(char *pName)
{
    if (pName == NULL){
        int a;
        ...
        return ERROR;
    }
    ...
    return SUCCESS;
}
```
### Reduce the Number of Parameters
The function calls may push parameters on stack on each call.
**Avoid passing complete structures as parameters. Use pointers and references in such cases.**

### Use references for Parameters passing and return value for Types bigger than 4 bytes
It's efficient to pass references as parameters. This way you save on the overhead of a temporary object creation, copying and destruction.
The same idea should be apply to return values.

### Don't Define a Return Value if Not Used
The function will always pass the return value, no matter if it's being use.

### Consider Locality of Reference for Code and Data
The processor keeps data or code that is referenced in cache so that on its next reference it gets it from cache.
Hence it's recommended that code and data that are being used together should actually be placed together physically.

### Prefer int over char and short
The main reason is that the compiler will first convert the valus into integer, perform the operations and then convert back the result to char.

### Prefer Initialization over Assignment
It's prefered to initialize the value directly.
```c
void foo()
{
    Complex c;
    c = (Complex)5;
}
void foo_optimized()
    Complex c = 5;
{
}
```
### Use Inline 1 to 3 line Functions
Converting small functions into inline will give you big improvements in throughput.
When using inline, the compiler will copy the code of the function, instead of calling it.
```
inline int add(int a, int b) {
    return a + b;
}
void main() {
    int result = add(3, 4);
    printf("Result: %d\n", result);
}
```





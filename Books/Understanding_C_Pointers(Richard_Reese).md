# Understanding and Using C Pointers by Richard Reese

## Chapter 1: Introduction
A pointer variable contains the address in memory of another variable, object or function.
When a C program is compiled, it works with three types of memory:
- Static/Global: They are allocated when the program starts and remain in existence until the program terminates.
- Automatic: They are declared within a function and are created when a function is called.
- Dynamic: Is allocated from the Heap and can be reeased as necessary. It exists until it is released

.......

## Chapter 3: Pointers and Functions

### Program Stack and Heap
The Stack it's an area of memory that supports the execution of functions, it's shared with the heap. Usually the stack it's on the lower part of this region.
The Stack holds **stack frames** (activation records o frames), these are in charge of hold several parameters:
- Return Address
- Storage for local data 
- Storage for parameters
- Stack and base pointers
```c
float average(int *arr, int size) {
  int sum;
  printf("arr: %p\n",&arr);
  printf("size: %p\n",&size);
  printf("sum: %p\n",&sum);
  for(int i=0; i<size; i++) {
    sum += arr[i];
  }
  return (sum * 1.0f) / size;
}
/* When executed:
arr: 0x500
size: 0x504
sum: 0x480
*/
```
Coceptually, the stack in this example grows "up". However, the actual direction is implementation-specific.
C threats block statements as "mini" functions and will push and pop them as appropiated. So the i used in the for is not included as part of this stack frame.

As stack frames are pushed onto the program stack, the system may run out of memory (Stack Overflow). Keep in mind that each thread is typically allocated it's own program stack.

The Heap manages Dynamic Memory which tend to grow "downward". The heap will fragment as memory is allocated and then deallocated.

### Passing and Returning by Pointer

#### Passing Data Using a Pointer:
One of the primary reasons for passing data using a pointer is to allow the function to modify the data (E.G: Swap Variables).
```c
void SwapVariables(int* pnum1, int* pnum2) //We can modify the actual value of the integers.
```
When a pointer is passed to a function, it's always a good practice to verify it's not null before using it.

#### Passing Data by Value:
In this case, is stored only the value of the numbers and we cannot swap the variables. This will be useful only to share the value.
```c
void SumV(int A, intB) //We cannot modify the actual value, we can only use it.
```

#### Passing a Pointer to a Constant:
It's a common technique to avoid copying large amount of memory by passing the address of the data and prevent them from being modified. 
```c
void passingAddrConst(const int* num1, int* num2) //We cannot modify the actual value, we can only use it.
```

#### Returning a Pointer:
If we need to return an object from a function, the following 2 techniques are used:
- Allocate memory (malloc) and return its address
- Pass an object to the function where it's modified.

Several potential problems can occur when returning a pointer from a function, including:
- Returning an unitialized pointer
- Returning a pointer to an invalid address
- Returning a pointer but failing to free it. (The caller it's responsible for deallocating it)
- Returning a pointer to a local variable 

The last one it's an easy mistake. The address of the returned object is no longer valid once the functions returns because the function's stack frame is popped of the stack. An alternative approach is to declare the object as static, this will restrict the variable scope to the function but allocate it outside of the stack frame, eliminating the possibility of another function overwriting the variable's value.

#### Passing a Pointer to a Pointer:
When a pointer is passed to a function, it's passed by value. If we want to modify the original pointer and not the copy of the pointer, we need to pass it as a pointer to a pointer.
In the cases where we define a pointer and we want to modify where the pointer is pointing to (change it's value) we had to use a pointer to a pointer.


### Functions Pointers
It's a pointer that hold the address of a function. This provides a way of executing in an order that may not be known at compile time and without using conditional statemens.

The sintax looks like a function prototype, the difference is the first set of parentheses. We need to be careful when a function pointer is used, C does not check if the correct parameter are passed.
```c
void (*fptr0)();   //Passed void and returns void
int (*fptr1)(double); // Passed a double and returns an int
void (*fptr2)(char*); // Passed a pointer to char and returns void
double* (*fptr3)(int, int); // Passed two integers and returns a pointer to a double
```

While using the function pointers, we can:
- Pass a function pointer to a function.
- Return a function pointer from a function.
- Use an array of Function Pointer.
- Compare Function Pointers
- Cast Function Pointer (Careful with the parameters type)

## Chapter 4: Pointers and Arrays
  

# Understanding and Using C Pointers by Richard Reese
# Table of contents
1. [Chapter 1: Introduction](#Chapter-1)
2. [Chapter 3: Pointers and Functions](#Chapter-3)
3. [Chapter 4: Pointers and Arrays](#Chapter-4)

   
## Chapter 1: Introduction <a name="Chapter-1"></a>
A pointer variable contains the address in memory of another variable, object or function.
When a C program is compiled, it works with three types of memory:
- Static/Global: They are allocated when the program starts and remain in existence until the program terminates.
- Automatic: They are declared within a function and are created when a function is called.
- Dynamic: Is allocated from the Heap and can be reeased as necessary. It exists until it is released

.......

## Chapter 3: Pointers and Functions <a name="Chapter-3"></a>

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

int (*fptr4)(int);
int squate(int num){
  return num*num;
}

int main(){
int n = 5;
fptr5 = square;
printf("%d squared is %d\n",n, fptr1(n));
/* fptr1 = &square; == fptr1 = square; */
}
```

While using the function pointers, we can:
- Pass a function pointer to a function.
- Return a function pointer from a function.
- Use an array of Function Pointer.
- Compare Function Pointers
- Cast Function Pointer (Careful with the parameters type)


## Chapter 4: Pointers and Arrays <a name="Chapter-4"></a>
Is a contiguous collection of homogeneous elements that can be accessed using an index.
The elements are adjacent to one another in memory with no gaps and they are all of the same type.

The array has no information about the number of elements it contains. The array name simply references a block of memory.
A two-dimensional arrays is treated as an array of arrays, when we access the arrayusing onle one subscript, we get the pointer to the correspondng row. In memory, the array's first row is places in memory, followed by the second row (Matrix[n][0] == Matrix[n]) . 

```c
/* One-Dimensional Arrays */
int vector[5] = {1, 2, 3};
/* In Memory
Vector[0] 0x100
Vector[1] 0x104
Vector[2] 0x108
*/

/* Two-Dimensional Arrays */
int matrix[2][3] = {{1,2,3},{4,5,6}};
/* In Memory
Matrix[0][0] 0x100
Matrix[0][1] 0x104
Matrix[0][2] 0x108
Matrix[1][0] 0x112
Matrix[1][1] 0x116
Matrix[1][2] 0x120

Matrix[0] -> 0x100
Matrix[1] -> 0x112
*/
```

### Pointer Notation and Arrays
When an array name is used by itself, the array's address is returned.
We can use either the array name by itself or use the address-of operator with the aray's first element. These are equivalent.

```c
/* Assumed vector -> 0x100 & pv -> 0x96 */
int vector[5] = {1, 2, 3, 4, 5};
int *pv = vector;
 /*
0x92  -> &vector[-2] | vector-2 | &pv[-2] | pv-2
0x100 -> &vector[0]  | vector+0 | &pv[0]  | pv
0x104 -> &vector[1]  | vector+1 | &pv[1]  | pv+1
*/

The following demonstrates the use of pointers in implementation of the scalar adition operation. This operations takes a value and multiplies it against each element of the vector:
```c
pv = vector;
int value = 3;
for (int i=0; i<5; i++){
    *pv++ *= value;
}
```

### Differences Between Arrays and Pointers
The main difference of interest is that the pointer pv is an lvalue, which it's capable of being modified. An array name such as vector is not an lvalue and cannot be modified.
```c
pv = pv + 1;
vector = vector + 1; //Syntax Error

sizeof(pv) //len of pointer type -> 4 bytes
sizeof(vector) //bytes allocated to the array -> 20 bytes
```

### Passing a One-Dimensional Array
When a one-dimensional array is passed to a function, the array's address is passed by value. This means the array's size must be passed and make more efficient since we're not passing the entire array.

### Using Array Notation





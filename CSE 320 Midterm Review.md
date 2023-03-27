## **C Programming**
### Variables
```c
int *(*f[3])();
// an array of pointers to functions that return pointers to int

if (n = 0) { ... } // assignment returns value assigned 

```
- Global (static) variables initialized to **0**
- Local variables => **could be anything**

```c
int var_9 = 1; // allowed
int 9_var = 2; // not allowed 
int _ = 3; // allowed
```
- letters, digits and underscore allowed but var name has to begin with letter/underscore, NOT **NUMBER**

```c
sizeof(int);
sizeof(int*);
sizeof(int**);
```
- assuming size of int is 4 bytes and size of pointer is also 4 bytes, all of them are 4 bytes 
- **size of all pointer types is the same**
- pointer => char / pointer => int / pointer => pointer => int
	- size remains the same for all of them 
	- pointer to user defined data type (a struct) would also have the same size as any other pointer.

```c
   int *pInt;
   int **ppInt1;
   int **ppInt2;

   pInt = (int*)malloc(sizeof(int));
   ppInt1 = (int**)malloc(10*sizeof(int*));
   ppInt2 = (int**)malloc(10*sizeof(int*));
   // assume we free
```
- runs smoothly
- data allocations match up based on data types

```c
void *pVoid;
pVoid = (void*)0;
```
- `(void *)0` => NULL pointer 


```c
int main()
{
	void demo(); // load the function so that we can call it
	void (*fun)(); // define 'fun' pointer of type void (*)() which points to functions w/ no args and a void return type
	fun = demo; // assign address of demo() function to fun 
	(*fun)(); // call it by dereferencing
	fun(); // call it using function ptr
	return 0;
}
void demo()
{
	printf("GeeksQuiz ");
}
```

### Booleans
- **False** if **0**
- **True** if anything else even negative


#### C Storage Class Examples
**Types of Storage Classes:**
- Auto(matic)
	- var that is declared inside a function without the static attribute -- created and destroyed when block is exited
- Static
	- global variables (vars defined outside any function)
	- global lifetime
	- may not have **global scope** (if not explictly declared static)

```c
int count;

increment(); // somewhere else initialized

int main() { 
	increment(); 
	increment(); 
	increment(); 
	return 0; 
}
```
>[!faq]- What is count's storage class?
>static

>[!faq]- Lifetime start and end?
> when program is loaded and ends when program terminates.

```c

increment(); // somewhere else initialized

int main() { 
	int count = 0;
	
	return count; 
}
```

>[!faq]- What is count's storage class?
>auto

>[!faq]- Lifetime start and end?
> when main is called and ends when main returns

```c
void increment() { 
	static int count = 0;
}
```

>[!faq]- What is count's storage class?
>static

>[!faq]- Lifetime start and end?
> when main is called and ends when program ends

>[!faq]- Scope?
>only local scope






##### Static
- Variables of storage class static have a definite initial value & persist throughout lifetime 
```c
static int x = 3;

(stored in the data segment)

static int y;

(stored in bss segment)
```
- Otherwise, automatically initialized to 0

##### Non-static
- Other variables (non-static) have indeterminate initial values (can be anything) -- usually stored in heap/stack




### GCC & Build Automation
- compilation cycle:
	- `.c` => assembly `.s` => object file `.o` => executable `.exe`
	- ![[Pasted image 20230326102235.png]]
		- cpp (C preprocessor) => preprocessed files (.i) => compiler (cc1) => assembly (translated by assembler)
- compiler creates object file for each source file (`.s` assembly file) and then links them together for the executable




> [!faq]- Where does linking occur?
> After creation of object files for each source file


## **Memory Allocation**
- Two types of allocators
	- Explicit allocator: app allocates & frees space
		- malloc/free in C
	- Implicit allocator: app allocates but does not free space
		- garbage collection in Java, ML, Lisp

* Constraints of Allocators:
    * Applications have few constraints
        * Can cause arbitrary sequence of `malloc` and `free` requests
        * Free request must be to a `malloc` block
    * Allocators have many constraints:
        1. Can't assume allocation patterns
        2. Must respond immediately to `malloc` (can't defer allocation)
        3. Can't relocate allocated memory
        4. Can manipulate and modify only free memory
        5. Can't move the allocated blocks once they are malloc'd
            1. compaction is not allowed
        6. Must allign blocks so they satisfy all alignment requirements

- Throughput
	- 5000 malloc and 5000 free in 10 seconds
	- throughput is 1000 operations/sec



```c
void *malloc(size_t size)
```
- Success:
	- ptr to memory block at least **size** bytes aligned to 8 byte (x86) or 16 byte (x86-64) boundary
	- if size == 0, return NULL
- Unsuccessful:
	- returns NULL w/ sets errno



### Heap
* grows towards higher addresses
* stack is at the top
* Dynamic memory allocation is useful when the size of the data to be stored is not known at compile time
* Storing data on the stack requires knowing the size of that data at compile time.
	* This is why we use heap over stack

![[Pasted image 20230320134307.png|350]]
> internal fragmentation: fragmentation within memory blocks -- occurs when memory allotted is greater than the requested memory

![[Pasted image 20230320134316.png|350]]
> external fragmentation: enough memory for allocation request but the memory is not contiguous.


## **Linking**
![[elf.png]]
>[!faq]- In what section of an ELF binary are initialized variables located? (a) .symtab (b) .data (c) .bss (d) .text
>(b) **.data**

![[Pasted image 20230326112033.png]]
> [!faq]- Where is x stored?
> .data
- limited in scope to the function it is defined in

![[Pasted image 20230326112151.png]]
>[!faq]- How compiler allocate space/definition for these two x's?
>Creates space in .data for each defin. of x. 
>Creates local symbols in symbol table w/ unique names (e.g., x.1 and x.2)

Linker Rules:
1. Multiple strong symbols => Linker error
2. Strong and multiple weak symbols => choose strong symbol
	1. references to weak symbols => resolve to strong symbol
3. Multiple weak symbols => pick randomly
	1. override w/ ``gcc -fno-common`` (multiple weak symbols => linker erro)


Notes:
- local variables do not have any strength (strong/weak)
- the object file (main.o) is a symbol as well and is strong and global
- Global symbols can be referenced by other modules (files), but local symbols can only be referenced by the module that defines them.


```c
/* main.c */
int i = 0;
int main() {

foo();

return 0; }

/* foo.c */
int i = 1;
void foo() {

    printf("%d", i);
}
```
- fails to link because i takes up the same symbol space - symbol resolution fails because multiple strong symbols are not allowed
	- question: where does the symbol table get stored?
	- question: what is the syntax for linking?


```c
/* main.c */

#include <stdio.h>

int a = 1;
static int b = 2;
int c = 3;

int main() {}

int c = 4;

    foo();
    printf("a=%d b=%d c=%d\n", a, b, c);
    return 0;
}  

/* foo.c */
int a, b, c;

void foo() {
	a = 5;
	b = 6;
	c = 7;
}
```
- The `a` gets redeclared to 5 through the call to `foo()` and `a` is not static so it gets changed
- `b` does not change as the static limits the scope of the `b` to this file
- The declared `c = 4` in main shadows the global variable so we print out 4 for `c`.


## Multiprocessing
```c
fork() // one call, returns twice
execve() // no return if success, -1 if error
exit(int status) // no return, returns void
longjmp(jmp_buf envir, int val) // no return but changes setjmp: if 0, setjmp = 1 else nonzero, setjmp will return setjmp
waitpid(pid_t pid, int *status_ptr, int options) // waits for specific process to terminate 
wait(int *child_status) // suspends current process until one of its children terminates -- return value is the pid of terminated child process
```
> Reaping / waiting for proccesses:
- Call `wait` or `waitpid`
> Process completion:
- Call `exit`
- One call, no return


Threads
* Each thread has its own stack

## Signal
Fall Final 2012 - 9, 10
## Concurrency
fall 2012 - 3

```c
char c;
   int fd1 = open("foo.txt", O_RDONLY);
   int fd2 = open("foo.txt", O_RDONLY);
   if (!fork()) {

       read(fd1, &c, 1);
   }
```
- parent opens a file twice, then the child reads a character:
	- fd1 is now at the second character and fd2 is still on the first character


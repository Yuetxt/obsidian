## **C Programming**

### Variables

#### C Storage Class Examples
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
- compiler creates object file for each source file (`.s` assembly file) and then links them together for the executable

> [!faq]- Where does linking occur?
> After creation of object files for each source file




## **Memory Allocation**
![[elf.png]]
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


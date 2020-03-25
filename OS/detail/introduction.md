# 1. OS(operating system)

* There is a body of software, that is responsible for making it easy to run programs (even allowing you to seemingly run many at the same time), allowing programs to share memory, enabling programs to interact with devices, and other fun stuff like that.
* That body of software is called the operating system

# 2. Virtualization

* `OS` is in charge of making sure the system operates correctly and efficiently in an easy-to-use manner. And does this through `virtualization`
* `system call`
  * allow user to tell the `OS` what to do
* with help from the hardware achieve `virtualization`
* handle problem with `policy(intelligence, for which question)` and `mechanisms(mechinery, for how question)`

## Virtualizing The CPU

* `virtualization` make illusion that the system has a vary large number of virtual CPUs.

## Virtualizing Memory

*  Every process accesses its own private virtual address space (sometimes just called its address space), which the `OS` somehow maps onto the physical memory of the machine.

* The running program has physical memory all to itself which does not affect the address space of other processes (or the OS itself).
* The code below shows that each process has their own memory, but in sight of virtual memory(address space) , same memory space(address) can be accessed by multiple processes.
* code(`mem.c`)

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]){
	int *p = malloc(sizeof(int));
	printf("(%d) address pointed to by p: %p\n", getpid(), p);
	*p = 0; 
	while (1) {
        Spin(1);
        *p = *p + 1;
        printf("(%d) p: %d\n", getpid(), *p);
    }
    return 0;
}
```

* input & output
  * two processes are running and they have their own physical memory space but in sight of user program they use virtual space, so they could access have address space

```
prompt> ./mem &; ./mem &
[1] 24113
[2] 24114
(24113) address pointed to by p: 0x200000
(24114) address pointed to by p: 0x200000
(24113) p: 1
(24114) p: 1
(24114) p: 2
(24113) p: 2
(24113) p: 3
(24114) p: 3
(24113) p: 4
(24114) p: 4
...
```

# 3. Concurrency

*  The problems of concurrency arose first within the operating system itself; the OS is juggling many things at once.
* Modern multi-threaded programs exhibit the same problems.
  * The code below shows the problems of concurrency
    * Instruction is executed one at a time, but where the shared `counter` is incremented takes three instructions.
    *  Because these three instructions do not execute atomically (all at once), strange things can happen.

* code(`thread.c`)

```c
#include <stdio.h>
#include <stdlib.h>
#include "common.h"

volatile int counter = 0;
int loops;

void *worker(void *arg) {
    int i;
    for (i = 0; i < loops; i++) {
        counter++;
    }
    return NULL;
}

int main(int argc, char *argv[]) {
    if (argc != 2) {
		fprintf(stderr, "usage: threads <value>\n");
		exit(1);
	}
    loops = atoi(argv[1]);
	pthread_t p1, p2;
	printf("Initial value : %d\n", counter);
    
	Pthread_create(&p1, NULL, worker, NULL);
	Pthread_create(&p2, NULL, worker, NULL);
	Pthread_join(p1, NULL);
	Pthread_join(p2, NULL);
	printf("Final value : %d\n", counter);
	return 0;
}
```

* input & output

```
prompt> ./thread 100000
Initial value : 0
Final value : 143012
prompt> ./thread 100000
Initial value : 0
Final value : 137298
```

# 4. Persistence

* As devices such as DRAM store values in a `volatile` manner, we need hardware and software to be able to store data persistently

* `file system`
  * The software in the operating system that usually manages the disk is called the `file system`.
  * `device driver`
    * A `device driver` is some code in the operating system that knows how to deal with a specific device.
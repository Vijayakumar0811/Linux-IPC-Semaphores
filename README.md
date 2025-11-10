
# Linux-IPC-Semaphores
Ex05-Linux IPC-Semaphores
### NAME: VIJAYAKUMAR S
### REG NO: 212224040359
# AIM:
To Write a C program that implements a producer-consumer system with two processes using Semaphores.

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux Process API - Sempahores

### Step 3:

Execute the C Program for the desired output. 

# PROGRAM:

## Write a C program that implements a producer-consumer system with two processes using Semaphores.

```
/*
 * sem.c - Producer-Consumer using Semaphores
 */
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/sem.h>
#include <sys/wait.h>
#include <time.h>

#define NUM_LOOPS 10  // Number of producer-consumer cycles

// Define union semun if not predefined
union semun {
    int val;
    struct semid_ds *buf;
    unsigned short int *array;
    struct seminfo *__buf;
};

// Wait (P) operation
void wait_semaphore(int sem_set_id) {
    struct sembuf sem_op = {0, -1, 0};
    semop(sem_set_id, &sem_op, 1);
}

// Signal (V) operation
void signal_semaphore(int sem_set_id) {
    struct sembuf sem_op = {0, 1, 0};
    semop(sem_set_id, &sem_op, 1);
}

int main() {
    int sem_set_id, child_pid;
    union semun sem_val;

    sem_set_id = semget(IPC_PRIVATE, 1, 0600);
    if (sem_set_id == -1) {
        perror("semget");
        exit(1);
    }

    printf("Semaphore set created, ID: %d\n", sem_set_id);

    sem_val.val = 0; // Consumer waits for producer
    if (semctl(sem_set_id, 0, SETVAL, sem_val) == -1) {
        perror("semctl");
        exit(1);
    }

    child_pid = fork();

    if (child_pid < 0) {
        perror("fork");
        exit(1);
    }

    if (child_pid == 0) {  // Consumer
        for (int i = 0; i < NUM_LOOPS; i++) {
            wait_semaphore(sem_set_id);
            printf("Consumer: %d\n", i);
            fflush(stdout);
        }
        exit(0);
    } else {  // Producer
        for (int i = 0; i < NUM_LOOPS; i++) {
            printf("Producer: %d\n", i);
            fflush(stdout);
            signal_semaphore(sem_set_id);
            usleep(500000);
        }

        wait(NULL);
        semctl(sem_set_id, 0, IPC_RMID, sem_val);
        printf("Semaphore removed.\n");
    }

    return 0;
}
```


## OUTPUT
$ ./sem.o 

<img width="784" height="734" alt="image" src="https://github.com/user-attachments/assets/9cb3b6ef-5566-42c6-8c03-49b281b0717c" />


# RESULT:
The program is executed successfully.

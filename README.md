Download Link : https://programming.engineering/product/study-the-implementation-of-spinlock-and-sleeplock-in-xv6/

# CS330A
 🔍 Study the implementation of spinlock and sleeplock in xv6 Study the implementation of spinlock and sleeplock in xv6

Study the implementation of spinlock and sleeplock in xv6. The relevant codes are in spinlock.{h,c} and sleeplock.{h,c}. Pay attention to the acquire, release, acquiresleep, and releasesleep functions. While the spinlock implements a busy-wait loop in acquire, the sleeplock puts the waiting processes to sleep in acquiresleep function. Note how a process is put to sleep using the sleep function and woken up later using the wakeup function. You will be using the same mechanism in the condition variable and semaphore implementation. In all your implementations, you should use sleeplock only.

Condition Variable Implementation

Define the condition variable of type cond_t in a new file named condvar.h. Think about what this type should be. In another new file condvar.c implement the following three functions.

void cond_wait (cond_t *cv, struct sleeplock *lock)

void cond_signal (cond_t *cv)

void cond_broadcast (cond_t *cv)

Since the sleep(…) function takes one void* argument and a spinlock* argument, you will have to implement a new sleep function (you may name it condsleep) which takes a cond_t* and a sleeplock* argument. This function will be needed to implement cond_wait. The condsleep function should be implemented in proc.c.

Note that the wakeup function wakes up all processes that are waiting on a channel (a channel is essentially a pointer). To wake up just one process (needed to implement cond_signal), you will have to implement a new function wakeupone(…) in proc.c. This function will be almost identical to the wakeup function except that it will return as soon as it encounters one process waiting on the channel. Include condvar.o in the OBJS list of Makefile. Also, include all new function prototypes in defs.h for smooth compilation. These include cond_wait, cond_signal, cond_broadcast, condsleep, and wakeupone.

Testing Condition Variable Implementation

Three user programs have been included in user that make use of condition variables. These are barriertest, barriergrouptest, condprodconstest. The first one tests several rounds of barrier. The second one divides all processes into two groups (works for even number of processes only) and each group invokes several rounds of barrier. The third program implements multiple producers and multiple consumers on a bounded buffer. Go through these programs and understand what they do and how to run them. To get these programs to compile, you will need to implement a few new systems calls, which are discussed below. Your implementation must not require any change in the user programs.

You will implement the barrier as a group of three system calls described below. You will implement an array of barriers inside xv6. Fix the size of the array to ten. Declare this array at an appropriate place inside the kernel directory. You may put this declaration in a new file as well.

A. barrier_alloc: The barrier_alloc system call will find a free barrier from the barrier array and return its id to the user program.

B. barrier: This system call implements the barrier using condition variables. It takes three arguments: barrier instance number, barrier array id, and number of processes. The implementation of this system call should be such that when a process enters the barrier it prints out a line like the following.

pid: Entered barrier#k for barrier array id n

Replace pid, k, n with actual values. Also, after exiting the barrier, a process prints out a line like the following.

pid: Finished barrier#k for barrier array id n

You should acquire an appropriate sleeplock for printing these without jumbling up the output.

C. barrier_free: This system call frees the barrier corresponding to the passed barrier array id.

Here is a sample output of barriertest.

$ barriertest 4 2

7: got barrier array id 0

8: Entered barrier#0 for barrier array id 0

7: Entered barrier#0 for barrier array id 0

9: Entered barrier#0 for barrier array id 0

10: Entered barrier#0 for barrier array id 0

10: Finished barrier#0 for barrier array id 0

10: Entered barrier#1 for barrier array id 0

7: Finished barrier#0 for barrier array id 0

7: Entered barrier#1 for barrier array id 0

8: Finished barrier#0 for barrier array id 0

9: Finished barrier#0 for barrier array id 0

8: Entered barrier#1 for barrier array id 0

9: Entered barrier#1 for barrier array id 0

9: Finished barrier#1 for barrier array id 0

7: Finished barrier#1 for barrier array id 0

8: Finished barrier#1 for barrier array id 0

10: Finished barrier#1 for barrier array id 0

$

Here is a sample output of barriergrouptest.

$ barriergrouptest 4 2

11: got barrier array ids 0, 1

12: Entered barrier#0 for barrier array id 0

11: Entered barrier#0 for barrier array id 1

13: Entered barrier#0 for barrier array id 1

14: Entered barrier#0 for barrier array id 0

13: Finished barrier#0 for barrier array id 1

13: Entered barrier#1 for barrier array id 1

14: Finished barrier#0 for barrier array id 0

11: Finished barrier#0 for barrier array id 1

14: Entered barrier#1 for barrier array id 0

11: Entered barrier#1 for barrier array id 1

11: Finished barrier#1 for barrier array id 1

12: Finished barrier#0 for barrier array id 0

13: Finished barrier#1 for barrier array id 1

12: Entered barrier#1 for barrier array id 0

12: Finished barrier#1 for barrier array id 0

14: Finished barrier#1 for barrier array id 0

$

You will implement the multiple producers and multiple consumers on a bounded buffer using three system calls described below. You will implement the bounded buffer and its code as discussed in the class where each element of the buffer has a condition variable and other necessary fields (please refer to multi_prod_multi_cons.c in course homepage). Fix the size of the buffer to twenty. Declare the buffer at an appropriate place in the kernel directory. You may put this declaration in a new file as well.

A. buffer_cond_init: This system call initializes all sleeplocks and any other variable involved in the bounded buffer implementation.

B. cond_produce: This system call implements the producer function. It takes the produced value as argument.

C. cond_consume: This system call implements the consumer function. This system call should be implemented in such a way that the consumed item is printed out. Make sure to acquire a sleeplock before printing. The consumed item is also returned to the user program although it is not used.

Here is a sample output of condprodconstest.

$ condprodconstest 20 3 2

Start time: 1345

0 1 2 3 4 5 6 7 8 10 9 11 12 20 40 21 41 22 42 23 43 24 44 25 45 26 46 27 47 28 14 13 15 16 29 30 31 32 33 34 35 36 37 38 39 48 49 50 51 52 53 54 17 55 18 56 19 57 58 59

End time: 1348

$

Semaphore Implementation

You will implement the semaphore using condition variables and sleeplocks. This implementation is available in the lecture slides. Define the semaphore structure in a new file named semaphore.h. In another new file semaphore.c implement the following three functions.

void sem_init (struct semaphore *s, int x)

void sem_wait (struct semaphore *s)

void sem_post (struct semaphore *s)

Testing Semaphore Implementation

A user program named semprodconstest has been included in user that implements the multiple producer multiple consumer bounded buffer using semaphores. To get this program to compile, you will need to implement a few new systems calls, which are discussed below. Your implementation should not require any change in the user program. You will implement the bounded buffer and its code as discussed in lecture slide#63. You will need to use a buffer that is separate from the buffer used in the condition variable-based implementation of bounded buffer. Fix the size of the buffer to twenty. Declare the buffer at an appropriate place in the kernel directory. You may put this declaration in a new file as well.

buffer_sem_init: This system call initializes all semaphores and any other variable involved in the bounded buffer implementation.

sem_produce: This system call implements the producer function. It takes the produced value as argument.

sem_consume: This system call implements the consumer function. This system call should be implemented in such a way that the consumed item is printed out. Make sure to acquire a sleeplock before printing. The consumed item is also returned to the user program although it is not used.

Here is a sample output of semprodconstest.

$ semprodconstest 20 3 2

Start time: 8105

0 20 40 21 41 22 42 23 43 24 44 25 45 46 47 48 50 49 51 52 53 54 55 56 57 58 59 1 2 26 3 4 5 6 7 8 9 10 11 12 13 27 28 14 29 15 30 31 32 33 34 35 36 16 17 18 19 37 38 39

End time: 8109

$

# Shared memory and consistency models

## Mutual exclusion
### Peterson algorithm
Originally syncs two threads, has been expanded to an arbitrary number of processes. However the size of the array is exponential to the number of processes.  
Informally, consider the case where T1 enters the critical section. Then the loop condition must have been false, which means either flag2 was false or victim was set to 2. If flag2 was false, then T2 was not even trying to enter the critical section. If victim was set to 2, then T2 was trying to enter the critical section, but T1 won the race.
#### Code 
```c
T1                           T2
flag1 = true                 flag2 = true
victim = 1                   victim = 2
while (flag2 &&              while (flag1 && 
    victim == 1) {}              victim == 2) {} 
// critical section          // critical section
flag1 = false                flag2 = false
```
Each thread begins by setting its flag to `true`. It then sets the `victim` variable to its thread id, and waits until either the other thread's flag is `false` or `victim` is set by the other thread.
#### ELI5
Be a gentleman, let people go through the door before going yourself.

### Lamport Bakery Algorithm
N-processes. Uses two shared variables, _flag[2]_ and _turn_. Starvation-free.
- _flag_ at 1 indicates that the process wants to enter the CS
- _turn_ holds the id of the process whose turn it is 
#### Code
```c 
// Init
choosing[1..n] = 0; 
timestamp[1..n] = 0;

// Shared variables 
bool boosing[n];
int timestamp[n];

// Entry CS Code
choosing[i] = 1; 
timestamp[i] = 1 + max(timestamp);
choosing[i] = 0;
for (j = 1 to n) {
    await(choosing[j]=0);
    await(timestamp[j] == 0) or (timestamp[i] < timestamp[j]);

// Exit CS Code 
timestamp[i]=0
}
```
## Registers
### Safe register 

### Regular register

### Atomic register


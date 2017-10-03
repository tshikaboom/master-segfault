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

## Linearizibility
For any concurrent execution, there is a total order of the operations such that each read to a location (_variable_) returns the value written by the last write to this location (_variable_) that precedes in the ordered. Total order.

Other more relaxed models exist 
- sequential 
- causal
- PRAM 
- weak, etc...

### Specs
- The total order must be consistent with the temporal order of operations
- Each operation _op(Read or Write)_ has an _invocation_ and _response_ events  
An execution in global time is viewed as sequence _seq_ of such invocations and responses

## Registers
Abstraction of distributed shared memory by Lamport

### Safe register 
- _Read_ does not overlap with a _write_  
_Read_ returns the most recently written value
- _Read_ overlaps with a _write_  
_Read_ returns any value that the register could possibly have


### Regular register
It is a _safe_ register and: 
- if _Read_ overlaps with a _write_ then _read_ returns either the most recent value of a concurrently written value

### Atomic register
is _regular_ and 
- _read_ and _write_ that overlap are _linearizable_. There exists an equivalent totally ordered sequentilal execution of them

# TODO
List
- [ ] Add images for safe, regular and atomic registers from cs
- [ ] Compare linearazability and sequentialy
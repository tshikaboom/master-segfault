# Locks

- [Locks](#locks)
    - [Algo Mellor-Crumley & Scott (MCS Lock)](#algo-mellor-crumley-scott-mcs-lock)
        - [Why](#why)
        - [MCS Code](#mcs-code)
    - [Raisonnement Rely-Guarantee](#raisonnement-rely-guarantee)
    - [TAS, getAndSet](#tas-getandset)
    - [Exclussion mutuelle (Lock)](#exclussion-mutuelle-lock)
    - [TTAS Lock](#ttas-lock)
    - [Coarse grain locking](#coarse-grain-locking)
    - [Fine-grained locking](#fine-grained-locking)
    - [Hand-over-hand lock coupling](#hand-over-hand-lock-coupling)
        - [Conclusions](#conclusions)
    - [Optimistic locking](#optimistic-locking)
    - [Lazy Locking](#lazy-locking)
        - [Idea: Use CAS to change next pointer](#idea-use-cas-to-change-next-pointer)
        - [Idea: Add “mark” to a node](#idea-add-%E2%80%9Cmark%E2%80%9D-to-a-node)
    - [Liste sans verrous](#liste-sans-verrous)
    - [Ressources](#ressources)

## Algo Mellor-Crumley & Scott (MCS Lock)

### Why

Regular, TAS based locking does not guarantee FIFO order, so a process can find itself waiting long for a lock, despite being the first to ask for it. TAS needs to be executed atomically, this makes it a very heavy-weight instruction.

Lamport's bakery solves the FIFO problem, but still spins on the same variable, the `current_ticket_number`. Why is this a problem? Well, think of it from a cache coherency perspective. Each processor is spinning, constantly reading the `now_serving` variable. Finally, the processor holding the lock releases and increments `now_serving`. This invalidates the cache line for all other processors, causing each of them to go across the interconnect and retrieve the new value. If each request for the new cache line is serviced in serial by the directory holding the cache line, then the **time to retrieve the new value is linear in the number of waiting processors.**

The linear acquisition time can be improved, and this is where MCS comes in. Each process spins on local variable, not one shared with other processes.

- FIFO order
- Spin on local memory only
- Small, constant-size overhead
- Explicit queue

### MCS Code

```c
typedef struct mcs_node{
    mcs_node next;
    int is_locked;
} mcs_node;

mcs_lock{
    mcs_node queue;
}

function Lock(mcs_lock lock, mcs_node my_node){
  my_node.next = NULL;
  mcs_node predecessor = fetch_and_store(lock.queue, my_node);
  //if its null, we now own the lock and can leave, else....
  if (predecessor != NULL){
    my_node.is_locked = true;
    //when the predecessor unlocks, it will give us the lock
    predecessor.next = my_node;
    while(my_node.is_locked){
      // Waiting for lock
    }
}
function UnLock(mcs_lock lock, mcs_node my_node){
  //is there anyone to give the lock to?
  if (my_node.next == NULL) {
      //need to make sure there wasn't a race here
      if (compare_and_swap(lock.queue, my_node, NULL)){
            return;
      }
      else{
          //someone has executed fetch_and_store but not set our next field
          while(my_node.next==NULL) {
            // Let the other process catch up, set itself as next
          }
      }
  }
  //if we made it here, there is someone waiting, so lets wake them up
  my_node.next.is_locked=false;
}
```

Each process is represented by `mcs_node` in the queue. When attempting to acquire lock, if it's busy, the node is added at the end of the queue, and spins on `my_node.is_locked` (locally). `fetch_and_store(lock.queue, my_node)` atomically places our node at the end of the queue. `lock.queue` becomes the precedessor of `my_node`. The process then busy-waits for the predecessor to finish and wake it up.

To unlock, we need to ensure that no other process had performed `fetch_and_store(lock.queue, another_node)`, but didn't yet set `mynode.next=another_node`.

## Raisonnement Rely-Guarantee

- Logique pour raisonner sur les programmes concurrents
- Logique de Hoare + processus + non-interference
- Rely -> interference que mon processus accepte de la part des autres
- Guarantee = ce que mon proc promet aux autres

Pour chaque proc: `[Pi, Ri] Ci [Gi, Qi]`

Hoare + parallelisme : non interference

- Hypotheses (proc i)

  - Precondition `Pi`
  - Rely: a chaque pas d'execution `Ri`

La garantie est a faire _uniquement_ en parallel. On ne raisonne pas en iterative

Stuffs:

- _P_: Pre-conditions
- _R_: Relay : Ce que les autres fond pendant mon execution
- _G_: Guarante that I offer
- _Q_: Post contitions

## TAS, getAndSet

```c
TAS(L v) {
  atomic {
    int tmp = L;
    L = v;
    return tmp;
  }
}

```

P: True
R: true

G: MOD(L)
Q: OLD(L) ^ L = v

## Exclussion mutuelle (Lock)

```c
Lock:
P: L.owner = null
R: true
lock(L)
G: MOD(L)
Q: L.owner = thistd

Unlock:
P: L.owner = thistd
R: true
unlock(L)
G: MOD(L) // Only L Modified
Q: L.owner = null
```

Variable partagee _X_ progetee par verrou _L_. Plusieurs processus identiques paralleles :

```bash
P: true
R: L.owner = thisthr -> ID(X)
---
...; lock(L); ...; X= thisthr; ...; unlock(L);
                                        ^ point de linearisation
---
G: L.owner != thisthd -> ID(X)
Q: X=thisthr
```

## TTAS Lock

```c
TTAS-lock(L) {
    while (true) {
        while (L.state = busy {}
        if (TAS(L.state, busy)==free}
            break;)
    }
    z = z+1
}

TTAS-unlock(L) {
    L.state = free
}
```

## Coarse grain locking

`synchronized()` all the things!

## Fine-grained locking

Lock smaller bits of data, not the whole list

## Hand-over-hand lock coupling

Though we again have a basic concurrent linked list, once again we are in a situation where it does not scale particularly well. One technique that researchers have explored to enable more concurrency within a list is something called hand-over-hand locking (a.k.a. lock coupling).

The idea is pretty simple. Instead of having a single lock for the entire list, you instead add a lock per node of the list. When traversing the list, the code first grabs the next node’s lock and then releases the currentnode’s lock (which inspires the name hand-over-hand).

### Conclusions

- No deadlock
- No starvation
- Linearisation points:
  - `add` success : at `current` lock
  - `add` fail : at `pred` lock
  - `remove` success : at `pred` lock
  - `remove` fail : at `current` lock

## Optimistic locking

 Search down list without locking

- Find and lock appropriate nodes
- Verify that nodes are still adjacent and in list (validation)
  - we can do this by traversing list again (provided that nodes are not removed from list while they are locked)
- Better than hand-over-hand if
  - traversing twice without locking is cheaper than once with locking
    - traversal is wait-free! (we'll come back to this)
  - validation typically succeeds

## Lazy Locking

### Idea: Use CAS to change next pointer

- make sure next pointer hasn't changed since you read it
  - assumes nodes aren't reused
- possible because operations only change one pointer
- but still nontrivial

### Idea: Add “mark” to a node

to indicate whether its key been removed from the set.

- set mark before removing node from list
  - thus, if mark is not set, node is in the list
- setting the mark removes key from the set
  - it is the serialization point of a successful remove operation
- don't change next pointer of a marked node
  - mark and next pointer must be in the same word
- “steal” a low-order bit from pointers
- Java provides special class: AtomicMarkableReference

## Liste sans verrous

Eliminer les verrous completement 

- Affectation de pointeurs par CAS 
- Si conflit, echec, recommencer a zero

Probleme: Concurrent `cas` and `remove`

**Solution:**

- CAS booleen de marquage `current=15`
- CAS pointeur `pred=15`
- si modifie, echec - recommencer


## Ressources

- [[pdf] Threads locks usage](http://pages.cs.wisc.edu/~remzi/OSTEP/threads-locks-usage.pdf)
- [[pdf] Techniques for highly concurrent objects, part1](http://courses.csail.mit.edu/6.852/08/lectures/Lecture21.pdf)
- [[pdf] Techniques for highly concurrent objects, part2](http://courses.csail.mit.edu/6.852/08/lectures/Lecture22.pdf)
- [[writeup] ffwd: delegation is (much) faster than you think](https://blog.acolyer.org/2017/12/04/ffwd-delegation-is-much-faster-than-you-think/)

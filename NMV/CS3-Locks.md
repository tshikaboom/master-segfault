# Locks

## Algo Mellor-Crumley & Scott (MCS Lock)

- FIFO order
- Spin on local memory only
- Small, constant-size overhead
- Explicit queue

_**Unifnished**_

## Raisonnement Rely-Guarantee

- Logique pour raisonner sur les programmes concurrents
- Logique de Hoare + processus + non-interference
- Rely -> interference que mon processus accepte de la part des autres
- Guarantee = ce que mon proc promet aux autres

Pour chaque proc: `[Pi, Ri] Ci [Gi, Qi]
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

Variable partagee _X_ progetee par verrou _L_. Plusieurs processus identiques || :

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

## Ressources

- [Threads locks usage [pdf]](http://pages.cs.wisc.edu/~remzi/OSTEP/threads-locks-usage.pdf)
- [Techniques for highly concurrent objects [pdf]](http://courses.csail.mit.edu/6.852/08/lectures/Lecture21.pdf)
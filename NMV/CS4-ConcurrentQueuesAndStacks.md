- [Queues and Stacks](#queues-and-stacks)
    - [Bounded Queue](#bounded-queue)
- [Ressources](#ressources)


# TODO
- [x] Algo de Treiber - wut dat? 

# This lecture

- Bounded, Blocking, Lock-based Queue
- Unbounded, Non-Blocking, Lock-free Queue
- ABA problem
- Unbounded Non-Blocking Lock-free Stack
- Elimination-Backoff Stack

# Concurrent Queue


- FIFO
- Add one end, remove the other
- Relatively easy to implement concurrency

# Concurrent Stack

If #`pop()` == #`push()` alors elimination stack. Only return to the actual stack if singled oud `pop()` or `push()`.

## Treiber Stack

The Treiber Stack Algorithm is a scalable lock-free stack utlizing the fine-grained concurrency primitive Compare-and-swap.

### Basic principle

Only add something new to the stack once you know the item you are trying to add is the only thing that has been added since you began the operation. This is done by using compare-and-swap. With stacks when adding a new item, you take the top of the stack and put it after your new item. You then compare the second item of this newly constructed head element (old head) to the current one. If the two match, then you can swap old head to the new one, if not then it means another thread has added another item to the stack in which case you must try again.

When popping an item from the stack, before returning the item you must check another thread has not added another item since the operation began.

### Correctness

In some languages -- particularly, those without garbage collection -- the Treiber stack can be at risk for the ABA problem. When a process is about to remove an element from the stack (just before the compare and set in the pop routine below) another process can change the stack such that the head is the same, but the second element is different. The compare and swap will set the head of the stack to the old second element in the stack mixing up the complete data structure.

### Java implementation

```java
import java.util.concurrent.atomic.*;

import net.jcip.annotations.*;

/**
 * ConcurrentStack
 *
 * Nonblocking stack using Treiber's algorithm
 *
 * @author Brian Goetz and Tim Peierls
 */
@ThreadSafe
public class ConcurrentStack <E> {
    AtomicReference<Node<E>> top = new AtomicReference<Node<E>>();

    public void push(E item) {
        Node<E> newHead = new Node<E>(item);
        Node<E> oldHead;
        do {
            oldHead = top.get();
            newHead.next = oldHead;
        } while (!top.compareAndSet(oldHead, newHead));
    }

    public E pop() {
        Node<E> oldHead;
        Node<E> newHead;
        do {
            oldHead = top.get();
            if (oldHead == null)
                return null;
            newHead = oldHead.next;
        } while (!top.compareAndSet(oldHead, newHead));
        return oldHead.item;
    }

    private static class Node <E> {
        public final E item;
        public Node<E> next;

        public Node(E item) {
            this.item = item;
        }
    }
}
```

# Ressources

- [Art of Multiprocessor Programming Herlihy-Shavit 2007, `pdf, 539p`](https://www.e-reading.club/bookreader.php/134637/Herlihy,_Shavit_-_The_art_of_multiprocessor_programming.pdf)

- [Slides, Chapter10, `ppt, w/ comments`](https://www.google.fr/url?sa=t&rct=j&q=&esrc=s&source=web&cd=5&ved=0ahUKEwiD38mJ5PnWAhWJLlAKHVhcACMQFgg5MAQ&url=https%3A%2F%2Fproglang.informatik.uni-freiburg.de%2Fteaching%2Fconcurrency%2F2010ws%2Fslides%2F10-chapter-10.ppt&usg=AOvVaw1h92quDzmPGdxgMSO2mv6U)

- [Companion Slides](https://booksite.elsevier.com/9780123705914/?ISBN=9780123705914)


# AllSummary

- Parallelism is hard
- Invariants, pre-post conditions, relays, garanties
- Algos
    - Pile, ABA problem with GC (or manual managing in general)
    - Masse d'algos, on sait pas a priori quel algo est bon pour quoi exactemment
- Big problems:
    - Bottleneck sequentiel
    - Meme une petite partie du code non-parallelle nique les perf en 2-2

- On a vu la memoire transactionnelle viteuf
    - Multiple atomics in a single tx

- Tout ce qu'on a vu marche si pas trop de contention (meme avec MCS et conneries)
    - JP Lozi a fait des mails mystqiues en client/serveur pour resoudre la contention sur certains locks du noyau linux
    - Software Combining accumule de facon dynamique un nombre de requetes

On ne sait jamais a l'avance quel va etre la performance de qqch. Faut vraiment faire des mesures. Il faut pas optimiser, il ne faut jamais optimiser au debut. Prenez toujours l'algo le plus simple, peut etre ca vaut le coup de test qqch d'autre. Mais simplicite c'est un atout. Plus simple c'est moins y a des bugs.
- [Queues and Stacks](#queues-and-stacks)
    - [Bounded Queue](#bounded-queue)
- [Ressources](#ressources)


# TODO
- Algo de Treiber - wut dat? 

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
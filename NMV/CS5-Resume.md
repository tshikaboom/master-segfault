# Programme 2eme partie semestre

- 2 seances ordonnancement multicoeur
- 3 seances sur la memoire
    - Comment fonctionne la gestion de la memoire
    - Comment fonctionne la table de pages
    - Qu'est ce que c'est de faire un hyperviseur
    - Coherence de la memoire
    - Associativite de lignes de caches etc
- Containers

Pour l'evaluation:

- Pas de code complique
- Pas de documents
- Ensemble de plein de questions de cours
- "Est-ce que vous avez au moins retenu ce qui peut servir a tout le monde"

# Techniques de synchronisation

Concurrence  1 coeur, comment faire:
Deux problemes pour monocoeur:
- Context switch
- Interruptions
- Deux personnes qui partagent mutuellement le CPU
- Dans le multicoeur on a les memes problemes mais on utilise pas les memes solutions

## Solutions monocoeur

Problemes monocoeur:

1. Stop les Interruptions

```c
local_irq_disable/enable()
```

2. Desactiver la preemption

```c
preempt_disable()
preempt_enable()
```

Linux preemptif a partir de 2.6

## Solutions de synchro multicoeur

### Spinlock (je bloque tout)

#### Inconvénients
- Locks à attente active.
- On teste une même case mémoire tout le temps => contention memoire
- Sur les caches avec protocole MOESI - lourd aussi pour les reads que les writes
car toute lecture diffuse des requêtes sur le bus (état S == Shared de MOESI)
- Dans le noyau ils ne sont pas recursifs
- Comme les spinlocks utilisent le CPU à fond, on peut avoir des problèmes au niveau de la consommation énergétique
#### L'avantage
- Les spinlocks sont surtout utilisés dans les handlers d'interruptions (meilleures perf avec peu de concurrence/peu de contention)
- On ne peut faire de mutex/barriere/etc dans un handler d'irq car pas moyen de s'endormir sur une ressource

#### API

>Note that these methods are implemented at least in part as macros, so the flags parameter (which must be defined as an `unsigned long`) is seemingly passed by value. This parameter contains architecture-specific data containing the state of the interrupt systems. Because at least one supported architecture incorporates stack information into the value (ahem, SPARC), flags cannot be passed to another function (specifically, it must remain on the same stack frame). For this reason, the call to save and the call to restore interrupts must occur in the same function.
>
> _Robert Love, Linux Kernel Development_

```c
DEFINE_SPINLOCK(my_slock)
unsigned long flags;
spin_lock_irqsave(&my_spin, flags);
spin_unlock_irqsave(&my_spin, flags);
```
__Attention!__

`spin_lock_irqsave` is basically used to save the interrupt state before taking the spin lock, this is because spin lock disables the interrupt, when the lock is taken in interrupt context, and re-enables it when while unlocking. The interrupt state is saved so that it should reinstate the interrupts again.

Example:

- Lets say interrupt `x` was disabled before spin lock was acquired
- `spin_lock_irq` will disable the interrupt `x` and take the the lock
- `spin_unlock_irq` will enable the interrupt `x`, leading to incoherent state

So in the 3rd step above after releasing the lock we will have interrupt `x` enabled which was earlier disabled before the lock was acquired.

So only when you are sure that interrupts are _not disabled_ only then you should `spin_lock_irq` otherwise you should _always_ use `spin_lock_irqsave`.



### Recopier les variables par coeur (je me ramene en monocoeur)

fig1

Le principe est de faire une copie d'une variable localement sur chaque CPU pour éviter des accès en partage.
Un point de linéarisation/cohérence possible dans le noyau est la lecture de la valeur dans sysfs. À ce moment-là, la valeur normale est "ré-assemblée" depuis les variables \_per_cpu. Genre une somme par exemple.


Dans le noyau y a plein de variables marques `PER_CPU` pour ces raisons de perf et de simplification d'accès mémoire/cache.

#### API

```c
/**
S'arrange pour que les viariables soient sur des lignes de caches differentes. Sinon niveau perf ca peut ne rien apporter, si on fait int i1, i2, i3, i4; ca peut etre alloue sur la meme ligne -> nothing changed.
**/
DEFINE_PER_CPU(type, name)
DEFINE_PER_CPU(int, i)

// To recover
per_cpu(nomVar, cpu);
// Dans multicoeur on peut avoir preemption et commutation <- ultra mega cher
__get_cpu_var;
// Donc y a la fonction disable la preemption
get_cpu_var(var)
```

Le probleme possible:
On fait `__get_cpu_var`, en tant que cpu1, on se fait preempte au millieu d'appel systeme et migre sur cpu2, ou on fait `__put_cpu_var` et on ecrit (on ecrit pas chez nous)

### Operations Atomiques (on se repose sur les instructions matérielles)

Type: `atomic_t`
Operations: `atomic{_inc, _add, _dec_and_Test, tons more}`

### Mutex
Equivalent à sémaphore initialisé à 1
#### Avantage
Classique. On connait!

#### Inconvénient
On dort donc y a plein d'endroits dans le noyau ou on peut pas l'utiliser

#### API:

```c
mutex_init(&my_mut);
mutex_lock(&my_mut);
mutex_unlock(&my_mut);
```


### Sémaphore

Deux questions à se poser sur les sémaphores:
- Est-il interruptible?
- Dijkstra ou generaliste?

Un sémaphore revient à un mutex généralise avec un compteur, deux types
- Semaphore de Dijkstra : Compteur est positif ou nul
- Semaphore generalise : Compteur peut être negatif

API:

```c
sem_init(&my_sem);
if (sem_down(&my_sem)) {
    stuff;
}
sem_up(&my_sem);
```

### Completion Variables
Variables indiquant la terminaison/completion d'un job. Sorte de barrière mémoire.
[Ressources gnu.org](https://www.gnu.org/software/emacs/manual/html_node/elisp/Completion-Variables.html)

> ++
- 1 `V()` pour multiple `P()`
- `P()` si personne -> `/dev/null`

API:

```c
init_completion(&my_comp);
wait_for_completion(&my_comp);
complete(&my_comp);
```

### Lecteur/Ecrivain

Interruptible!

API:

```c
down_read(&my_rw);
up_read(&my_rw);

down_write(&my_rw);
up_write(&my_rw);
```

### Read-Copy-Update (RCU)

Soit on lit une copie de la donnée, soit la donnée mise à jour. On n'a pas forcément besoin de la dernière valeur == on chope une valeur ayant été cohérente/valide à un moment donné dans le passé (proche)

Quand on veut faire un read, en vrai on démarre un lecteur RCU
rcu_read_unlock()

Quand on écrit, on balance un synchronize_rcu()
Ceci fait la barrière de cohérence

Les RCU assurent qu'avant une écriture sur une variable "partagée", on est cohérent, et après aussi. Les lectures PENDANT l'écritures vont finir par se compléter, et on s'assure toujours que l'écriture finit APRÈS toute lecture déjà qui s'éxecute

Impl. Chaque CPU a un état quiescent/pas quiescent

The name comes from the way that RCU is used to update a linked structure in place. A thread wishing to do this uses the following steps:

- create a new structure,
- copy the data from the old structure into the new one, and save a pointer to the old structure,
- modify the new, copied, structure update the global pointer to refer to the new structure, and then
 - sleep until the operating system kernel determines that there are no readers left using the old structure, for example, in the Linux kernel, by using synchronize_rcu().

When the thread which made the copy is awakened by the kernel, it can safely deallocate the old structure.

So the structure is read concurrently with a thread copying in order to do an update, hence the name "read-copy update". The abbreviation "RCU" was one of many contributions by the Linux community. Other names for similar techniques include passive serialization and MP defer by VM/XA programmers and generations by K42 and Tornado programmers.

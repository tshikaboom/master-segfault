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
local_ing_disable/enable()
```

2. Desactiver la preemption

```c
preempt_disable()
preempt_enable()
```

Linux preemptif a partir de 2.6

## Solutions de synchro multicoeur

### Spinlock (je bloque tout)

> --
- Cause contention memoire
- Protocole de cache avec protocole moisy (?) - lourd aussi pour les reads que les writes

> ++
- Dans le noyau ils ne sont pas recursifs
- On peut pas utiliser les spinlock dans
    - L'avantage des spinlock c'est que ca peut etre utiliser dans les handlers d'interruptions (meilleures perf avec peu de concurrence/peu de contention)
    - ^ pour ca qu'on peut pas faire mutex/barriere/etc, on peut pas s'endormir dans un handler d'interruption

> API Spinlocks dans le noyau

```c
DEFINE_SPINLOCK(my_slock)
unsigned long flags;
spin_lock_irqsave(&my_spin, flags);
spin_unlock_irqsave(&my_spin, flags);
```

### Recopier les variables par coeur (je me ramene en monocoeur)

fig1

Dans le noyau y a plain de variables marques `PER_CPU`

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

### Operations Atomiques (materiel)

Type: `atomic_t`
Operations: `atomic{_inc, _add, _dec_and_Test, tons more}`

### Mutex

Equivalent a semaphore init a 1

API:

```c
mutex_init(&my_mut);
mutex_lock(&my_mut);
mutex_unlock(&my_mut);
```

Probleme:
On dort donc y a plein d'endroits dans le noyau ou on peut pas l'utiliser

### Semaphore

Deux questions a se poser sur les semaphores:

- Est-il interruptible?
- Dijkstra ou generaliste?

Mutex generalise avec un compteur, deux types

- Semaphore de Dijkstra : Compteur est positive ou null
- Semaphore generalise : Compteur peut etre negatif

API:

```c
sem_init(&my_sem);
if (sem_down(&my_sem)) {
    stuff;
}
sem_up(&my_sem);
```

### Completion Variables

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

Offert par materiel, y en a qu'un.

The name comes from the way that RCU is used to update a linked structure in place. A thread wishing to do this uses the following steps:

- create a new structure,
- copy the data from the old structure into the new one, and save a pointer to the old structure,
- modify the new, copied, structure update the global pointer to refer to the new structure, and then
 - sleep until the operating system kernel determines that there are no readers left using the old structure, for example, in the Linux kernel, by using synchronize_rcu().

When the thread which made the copy is awakened by the kernel, it can safely deallocate the old structure.

So the structure is read concurrently with a thread copying in order to do an update, hence the name "read-copy update". The abbreviation "RCU" was one of many contributions by the Linux community. Other names for similar techniques include passive serialization and MP defer by VM/XA programmers and generations by K42 and Tornado programmers.
# I dunno man stuff

## Reachability

- Un objet est atteignable s'il est reference par au moins une variable directement, ou par un autre objet attaignable
- Un objet est atteignable si:
    - Accessible depuis le call stack
        - Variables locales
        - Arguments de fonction
    - Variables globales

### Exemple

```java
Obj a = new Machin();
Obj b - new Bidule();
a = new Truc(); // ecrase Obj a
if a.testUnTruc()
    a.faitUnTruc(b);
System.exit(a);
```

La notion de reachability n'est pas optimale dans la mesure ou la derniere utilisation d'un objet peut etre longue, avant que cet objet ne sorte de la portee de l'environment. On peut distinguer

- `miettes syntaxique`, les objets que le programme ne peut atteindre, et;
- `miettes semantique`, ces objects que le programme n'utilisera plus jamais.

## Strong vs Weak reference

- `Weak`: L'objet existera tant qu'il est reference par une reference forte
- `Strong`: L'objet existera tant qu'il est reference

## Algos

### Mark-and-sweep

![mns](./images/mark_and_sweep.gif)

Go through objects, mark those that are referenced. 
Go over again, delete non marked

### Tri-color

![3color](./images/tri-color_garbage_collection.gif)

Tout ce qui est en blanc est desalloue.
Des que j'ai pas gris j'ai fini.
Les blacks restent.

# TD

1. Trouver la source de la segfault

- `gc_malloc:gcmalloc:54`
- `alloc.h` :

```c
struct object_header {
    uint64_t size; 
    struct object_header * next;
    struct object_header * prev;
    int isRacine;
    int color;
}
```

2. Vous etes sur le bon fichier a lire
3. Comprendre le code
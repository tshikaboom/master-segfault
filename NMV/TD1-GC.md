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

- [+] Ne casse pas les cycles de references
- [+] Aucun overhead, toujours le meme traitement
- [-] Pas de paralellism
- [-] Apres plusieurs executions de M&S la memoire est fragmentee

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

# Primitives GCC

## Utilite de `static`, `extern` et `inline`

- `static`
    - The static keyword **inside a function**  
    In C, inside a function, we use the static modifier to declare variables with static storage duration. Such variables retain their value throughout multiple calls of the function. These variables are initialized only once at compile time. Their life time matches the life time of our program.
    
        Look at the example below. The initialization statement callCount = 0 will not be executed a second time. The variable callCount will be initialized to 0 and it will be incremented with each call to the function count().

        ```c
        int main()
        {
            count();
            count();
            count();
        } 

        void count()
        {
            static int callCount = 0;
            ++callCount;
        printf("The function \"count\" was called %d times.\n", callCount);
        }
        ```


    - Using the C static keyword **outside of a function**  
    The static keyword can be used with global variables and functions. In this context it limits their visibility to the current file (translation unit is more precise here). This means that we cannot access a static function or variable from another source file.

        Example with a global variable
        The following definitions will give an error during compilation:
        
        ```c
        // source1.c:
            int count = 0; 

        // source2.c:
            int count = 0;
        /**
            This will not compile and the error looks something like this:

            (.bss+0x0): multiple definition of `count'

        // …or like this:

            source2.obj : error LNK2005: _count already defined in source1.obj

            To define different variables with the same name on global level, you need to use the static keyword for at least one of them: 

        source1.c: **/
            static int count = 0;

        // source2.c:
            static int count = 0;

        // Example with static functions in C
            static void count()
            {
                static int callCount = 0;
                ++callCount;
            printf("The function \"count\" was called %d times.\n", callCount);
            }
        ```
    - Specifying the minimum size of an array parameter  
    This is another use of the static keyword in C, although it is rarer. Since C99, we can tell the compiler a minimum number of elements that the array contains. This is only valid when we pass an array pointer to a function. Here is an example of usage:
    
        ```c
        void printArray(int myArray[static 10], int size)
        {
            int i;
            printf("[");
            for(i = 0; i < size; ++i)
            {
                printf("%d ", myArray[i]);
            }
            printf("]\n");
        } 

        // Sometimes this technique is used to guarantee that the argument will be not null.

        void myFunction(int myArray[static 1], int size)
        {
            ...
        }
        ```



```c
struct thread_descriptor {
    struct thread_descriptor *prev;
    struct thread_descriptor *next;
    char * top_of_stack;
    struct obj_header liste_allouees;
    struct obj_header liste_racines;
    int total_size;
}
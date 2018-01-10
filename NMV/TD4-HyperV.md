# On fait un hyperviseur

# Ex1

## Question 1

> Quel mapping l’hyperviseur doit-il mettre en place pour donner l’impression au systeme invite qu’il accede directement à la m´ emoire physique?

Fonction identite, 1:1, flatmapping

## Question 2

> Le fichier `monitor/shadow.c` contient le squelette de la fonction `void set_flat_mapping(size_t ram)` qui donne acces au systeme invite à la memoire physique de ram octets via un modele plat. Implementez la fonction `set_flat_mapping`. Vous disposez pour cela de la fonction `void map page(vaddr t vaddr, paddr t paddr, size t len)` qui mappe l’adresse virtuelle `vaddr` sur l’adresse physique `paddr` du backend memoire pour une taille de `len` octets.
>
> Note : la fonction `void display mapping(void)`, disponible via `include/monitor/memory.h`, af
fiche sur la sortie standard le mapping du processus courant. Le format d’affichage est decrit par man 5 proc à la section `/proc/[pid]/maps`. Vous pouvez vous en servir pour verifier le bon fonctionnement devos fonctions.

```c
#define G_LOW_START 0x100000
#define G_LOW_END 0x80000000
#define G_HIGH_START 0x100000000
#define G_HIGH_END 0x700000000000

void set_flat_mapping(size_t ram)
{
  size_t left = ram;
  size_t low_size = G_LOW_END - G_LOW_START;
  size_t high_size = G_HIGH_END - G_HIGH_START;
  
  // void map_page(vaddr_t vaddr, paddr_t paddr, size_t len)

  fprintf(stdout, "flat mapping of %p\n", ram);
  map_page(G_LOW_START, G_LOW_START, low_size);
  map_page(G_HIGH_START, G_HIGH_START, high_size);
}

```

# Ex2

## Question 1

> Avec un modele memoire plat, à quelle adresse virtuelle le systeme invite doit-il ecrire pour atteindre l’adresse physique `0xb8000`?

En flatmap a priori a `0xb8000`.

## Question 2

> Etant donne le modele memoire de Janus, que se passe-t-il quand le systeme invite essaye d’afficher un caractere sur l’ecran CGA?

```
shadow.h
/*
 * Memory access emulation and shadow paging.
 * Memory model for Janus VMM
 *
 * +----------------------+ 0xfffffffffffffff
 * | Monitor              |
 * | (forbidden accesses) |
 * +----------------------+ 0x700000000000
 * | Guest                |
 * +----------------------+ 0x100000000
 * | Monitor              |
 * | (forbidden accesses) |
 * +----------------------+ 0x80000000
 * | Guest                |
 * +----------------------+ 0x100000
 * | Monitor              |
 * | (trapped accesses)   |
 * +----------------------+ 0x0
 *
 * Trapped are safe to perform from the guest.
 * Forbidden accesses may succeed but would result in process corruption.
 *
 * Memory trap return code:
 *   0  - the guest should retry (typically, the memory has been mapped)
 *   !0 - the access has been emulated
 */
```
Donc en ecrivant a l'@ `0xb8000`, on ecrit dans trapped.


## Question 3

> Completez le corps la fonction `trap_write` pour emuler l’affichage de caracteres sur un ecran CGA. Vous pouvez pour cela utiliser la fonction `void write_vga(uint16 t off, uint16 t val)` qui marque le caractere `val` dans l’ecran virtuel CGA à la position `off` (la position 0 indique le caractere en haut à gauche de l’ecran). 
>
> Note : La fonction `write_vga` ne fait pas d’affichage sur votre terminal. Pour afficher l’ecran virtuel CGA sur votre terminal, vous pouvez invoquer la fonction `void display_vga(void)`.
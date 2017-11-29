# On compile `Rackdoll`

# Ex1

## Question 1

> Dans le cadre de ce TP on supposera toujours qu’un niveau intermédiaire de la table des pages est mappé par une identité (il est accessible par une adresse virtuelle égale à son adresse physique). Pourquoi cette supposition est-elle importante ?
___

PML - Page Mapping Level

Il faut que les PML soit mappe physiquement, donc un minimum de 1-to-1 pour y arriver.

Sans ca on pourrait pas acceder au PML. Pour acceder a mon PML il faudrait faire traduir les addresses par PML -> never ending story.

## Question 2

> Combien y a-t-il d’entrée par niveau de table des pages pour une architecture x86 64 bits ? Comment la MMU détermine-t-elle si une entrée d’un niveau de table des pages est valide ? Si elle est terminale ?
___

- On a donc 2^9 entree par niveau de table, soit 512 (cours)
- Validite: Bit de validite (cours/annexe tp)
- Terminale:
    - Tu regardes dans PML1
    - Si c'est huge page
    - Bit de validite

## Question 3

> Programmez et testez la fonction `print_pgt`. Vous testerez votre nouvelle fonction en l’appellant depuis la fonction `main_multiboot2` située dans le fichier `kernel/main.c` juste avant le chargement des tâches utilisateur. Pour cela vous pouvez utiliser la fonction `uint64_t store_cr3(void)` qui retourne le contenu du registre `CR3`.

Si votre fonction d’affichage fonctionne correctement, vous devriez observer une table des pages avec la structure indiquée dans l’annexe de ce sujet.
___

CR3 is:
> Used when virtual addressing is enabled, hence when the PG _(If 1, enable paging and use the CR3 register, else disable paging)_ bit is set in CR0. CR3 enables the processor to translate linear addresses into physical addresses by locating the page directory and page tables for the current task. Typically, the upper 20 bits of CR3 become the page directory base register (PDBR), which stores the physical address of the first page directory entry.
>
> _[Wiki](https://en.wikipedia.org/wiki/Control_register#CR3) `(https://en.wikipedia.org/wiki/Control_register#CR3)`_


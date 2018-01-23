# Auto-stabilisation

## 2.4 Quelques preuves de lemmes.

### Lemme 1. Y'a forcément un jeton
Preuve par l'absurde.

On veut construire un anneau sans jeton.
Pour tout i appartenant à [0..*n-1*], on construit un anneau où c*i* = c*i-1*;
on a donc des noeuds de même couleur partout. Tout c*i* n'a donc rien à faire.

Or, comme tous les noeuds sont de même couleur, c*0* (top) va prendre la main.
Il y a un jeton => contradiction. Il y a donc forcément un jeton.

### Lemme 2. Tout processeur possède le jeton infiniment souvent

Par contradiction, supposons qu'il existe une exécution *epsilon* issue de gamma*0*
(l'ensemble des configurations du système, genre snapshots quoi), telle qu'il
existe 1 processus p*i* tel que p*i* finit par ne plus être activé (toujours).
Supposons que gamma*0* corresponde à la configuration où p*i* n'a plus jamais
le jeton.

### Lemme 1 prouve que toutes les exécutions sont infinies car il existe
toujours un processus activable (qui a le jeton).
=> Il existe p*j* qui est infiniment souvent activé.
=> Parr construction de l'algorithme, tous les processus entre p*j* et p*i*
sont infiniment souvent activés. En particulier, p*i-1* est infiniment souvent
activé.
=> c*i-1* est incrémenté infiniment souvent
=> c*i-1* = c*i* infiniment souvent
=> p*i* est infiniment souvent activé. Contradiction.

### lemme2

Par contradiction, supposns qu'il existe 1 éxéctiion $\epsilon$ issue de $\gamma_0$ telle que il existe 1 processeur $p_i$ tel que $p_i$ finit par ne pas être activé (toujours).
supposons que $\gamma_0$ correspond à la configuration ou $p_i$ n'a plus jamais le jeton.
Lemme 1 prouve que toutes les éxécitions sont infinies car il existe toujours un processeur activable (donc qui à le jeton) => $\exists$ $p_i$ qui oest infiniment souvent activé.
- par contradiction de l'algorithme, tous les processeur compris entre $p_i$ et $p_j$ sont inifiment souvent activés
- $c_{i-1}$ est incrémenté infiniment souvent
- $c_{i-1} != c_i$ infiniment souvent
- $p_i$ infiniment activé, CONTRADICTION.

### lemee3

pour $\gamma_0$ on à deux possibilité:
1. si $P_i$ avec i = 0 alors on exécute la règle R1
2. si $P_i$ avec i != 0 alor son exécute la règle R2

$P_0$: $\forall_i \in$[0..n-1], $C_i$ = $\alpha$
$C_0$ = ($\alpha$+1)$mod_k$
En $\alpha1$ seul P1 peut exécuter R2 => p0 ne peut pas créer de jeton en exécutant R1.

### lemme4
Dans le lemme2, on a montré que P0 introduisait en un temps fini une valeur qui n'était pas présente dans le système. (R > n) et les n processeur ne peuvent stocker que n valeurs dans une config. Comme L2 => p exécute (R1) infiniment souvent, en partant d'une fonfiguration qqc, P0 introduit $\alpha$ en un temps fini.

Puisque P0 ne peut changer sa valeur $\alpha$ lorsque $p_{n-1}$ a pris la valeur $\alpha$ => le système atteint une configuration dans laquelle $\forall_i \in$ [0..n-1], $c_i$ = $\alpha$ en un temps fini. Dans cette configuration, il n'y a qu'un seul jeton détenu par p0.

Th: Par le lemme2, la vivacité de la circulation du jeton est garantie par les lemmes 1, 3 et 4, l'algorithme de Dijkstra garantie que la surété est garantie en un temps fini. L'EM autostabilisante est ovtenue ainsi.

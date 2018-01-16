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
**Lemme 1** prouve que toutes les exécutions sont infinies car il existe
toujours un processus activable (qui a le jeton).
=> Il existe p*j* qui est infiniment souvent activé.
=> Parr construction de l'algorithme, tous les processus entre p*j* et p*i*
sont infiniment souvent activés. En particulier, p*i-1* est infiniment souvent
activé.
=> c*i-1* est incrémenté infiniment souvent
=> c*i-1* = c*i* infiniment souvent
=> p*i* est infiniment souvent activé. Contradiction.

Rappels selon Sopena
====================

Type de faute
-------------

- **Crash** la machine a gagné le droit de ne rien faire
- **Temporelle** la machine ne respecte pas ses échéances. La spécification est
suivie, bien que lentement.

Un crash est un type de ralentissement, la machine peut être considérée comme
infiniment lente. Si l'on sait traiter une faute temporelle, à priori on sait traiter un crash aussi.

- **Byzantine** la spécification n'est pas suivie
- **Malicieuse** la machine fait exprès tout ce qu'il ne faut pas

**"Relation d'inclusion"**: Crash `<` faute Temporelle `<` faute byzantine `<` faute malicieuse

Temporalité
-----------
- **temporaire**: pas fait exprès
- **périodique**: parfois
- **permanent**: tout le temps

Cardinalité
-----------
- _1_: au plus 1 crash, après l'algo ne fonctionne plus
- _N/2_: tolérance de N/2 fautes
- etc.

Checkpointing
=============

Dans le reste du cours, on va considérer que les machines reviennent en cas de panne.
Les algorithmes en eux-mêmes ne sont donc pas tolérants aux fautes.

Là, si la machine redémarre en cas de crash, on considère qu'on revient à un point connu d'éxécution.

```
                     t                   t+ƛ
S1 ------------------X (crash)            |----------------
S2 --------------------------------------------------------
S3 --------------------------------------------------------
```

Pour reprendre, il faut que S1 ait 100% de son état sur "support stable" (backup
et compagnie).

On peut effectuer des sauvegardes incrémentales (checkpoint!) pour réduire le coût
de la copie du contexte. On peut aussi retarder la sauvegarde jusqu'au dernier moment,
à l'écriture (Copy on Write).

Sauvegarder bien avant le crash est aussi une solution dès lors que toute
l'éxécution est déterministe, ça ne fait pas de différence de sauvegarder
"au moment" ou "un peu avant". 
Si y'a un input d'utilisateur ou quoi, ce n'est pas déterministe. Du coup on
oublie ça dans ces cas-là. **Dès que y'a interaction non-déterministe, on
sauvegarde sur support stable.**

**Coupure cohérente.** Tous les événements du passé sont indépendents des événements
après la coupure. Toutes les machines peuvent reprendre tranquillement

**Coupure incohérente.** Pas vraiment possible de reprendre.

**Points de reprise coordonnées** (implicites ou explicites). Les algorithmes construisent des checkpoints
entre machines de manière coopérative/coordonnée.
- **Explicites**: mise en accord entre serveurs.
- **Implicites**: l'algo va markup certains messages envoyés afin de créer un point de reprise de manière naturelle.
Les applications vont donc elles-mêmes effectuer leurs points de sauvegarde.
Après cela, il faudra trouver une ligne de recouvrement cohérente entre tous les points.

**Algorithmes à checkpoints spontanés** Je fais ce que je veux et je vous emmerdeuh!
D'ailleurs si y'a problème qui survient, bah on verra sur le moment.
Dans les checkpoints spontanés, on ne sait pas forcément d'où est-ce que l'on redémarre.

```
---------------A-----B---------------------------------- | dépendance locale de A sur B

-----------------A-------------------------------------- |
                  \                                      | dépendance causale de A sur B
-------------------B------------------------------------ |

```

On note A → B la dépendance de A sur B. A et B sont des événements plutôt quelconques,
on peut dire que ça équivaut a "un message envoyé de A vers B"

Hist(A) = { e, e → A }
C'est l'ensemble des événements sur lesquels dépend A.

**Ligne de recouvrement** Ligne définissant un instant t pour chacune des machines où
l'on sait que celles-ci ont un état cohérent et qu'elles puissent reprendre sans souci.

**Recherche de la ligne de recouvrement par marquage [Wang93]**
1. Ligne de recouvrement = toutes les machines à l'état t.
2. La machine fautive est remplacé par l'événement précédant causalement le crash.
3. On itère jusqu'à ce qu'on ait plus de machine fautive

**Algorithme du rollback**

------

Problème avec une grande quantité de checkpoints: la scalabilité. Beaucoup de complexité

Le pire des cas == pas d'état courant (tout le monde a crash).

En l'occurrence si on arrive à choper un état cohérent en cas de crash, on peut
libérer des ressources en supprimant les checkpoints avant celui-ci. ?? does dat mek sense

Caractérisation de points inutiles; les Z-chemin

Z-chemin/Z-path: 

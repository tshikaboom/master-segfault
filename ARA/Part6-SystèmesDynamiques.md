# Systèmes dynamiques

## 1. Introduction

**Approche à la Lamport**: graphe statique et complet. On considère que le système ne change pas.

**Approche réseau**: le graphe est toujours statique **et non complet**. Il manque des arêtes, tout le monde n'est plus à distance "1" de tout le monde. On ne peut plus récupérer des informations rapidement de tout le monde, il faut donc calculer un chemin de saut en saut dans certains cas (il y a du routage).

L'approche réseau est non suffisant pour les réseaux dynamiques (connexions/déconnexions impromptus d'une ou plusieurs machines par exemple).

**Réseau dynamique**.
- L'ensemble de noeuds peut évoluer.
- L'ensemble d'arêtes peut évoluer. Ce n'est pas parce qu'un noeud reste dans le réseau qu'il va garder le même voisinage.

### Exemples de réseaux dynamiques

| Mobilité/dynamique "imprévisible" | Mobilité/dynamique "prévisible"   | *blah*
|-----------------------------------|-----------------------------------|----------------------
| P2P                               | Réseaux satellites                | Réseaux de robots/agents
| Réseaux de voitures (comm. proche)| Réseaux de transport (RATP genre) |
| Réseaux opportunistes (Bluetooth) |                                   |
| Réseaux de capteurs embarqués     |                                   |
| **Dynamique subie**               |  **Dynamique subie**              | **Dynamique controlée**

Dans la suite, on ne s'intéressera qu'aux réseaux dont la dynamique est **subie**.

**Remarque**. On se consacre à des approches déterministes aux problématiques posées.  
**Remarque2. On ne peut pas considérer la dynamique comme une faute**.

## 2. Modèles

Comment modéliser un graphe dynamique? Un graphe dynamique n'est pas défini de la même manière partout, on va s'attarder sur les définitions et modèles proposés.

### 2.1. Graphes évolutifs (Evolving graphs)

**Principe**: un graphe dynamique est une suite de graphes statiques qui représente l'évolution e la connectivité à chaque instant.

G = (*G_0*, *G_1*, *G_2*, ...)  
*G0* à *t* = 0, *G1* à *t* = 1, ...

exemple. beaucoup de graphes pas connexes.

**Limite du modèle**: On discretise le temps, on ne peut donc pas modéliser le graphe dynamique en temps continu. On se limite aussi aux systèmes synchrones (tout le monde, au même moment, a sa vision du graphe tout en sachant qu'elles sont cohérentes avec la vision globale).

### 2.2. Graphe évoluant dans le temps (Time-Varying Graphs)

**Principe**: graphe statique auquel on associe une fonction de présence des arêtes au cours du temps.  
G = (V, E, *ρ*) avec *ρ*: E × R -> { 0, 1 }

Ça revient donc à avoir un graphe avec le nombre maximal de noeuds et d'arêtes, qui seront présentes (ou pas) au cours du temps grâce à la fonction *ρ*.

exemple. un truc graphe maximal avec des intervalles.

**Remarque**. Possibilité d'enrichir le modèle avec des fonctions:
- de présence de noeude
- de latence du système
- bande passante du système, ..
- d'étiquetage des arêtes évoluant au cours du temps (type de lien, ..)

Ce modèle peut s'appliquer aux systèmes asynchrones.

## 3. Redéfinition des concepts classiques

Nécessaire car il n'y a pas la notion du temps dans les graphes statiques. Chemin, diamètre, connexité etc évoluent désormais au cours du temps.

**Exemple: Voisinage**.  
Défini en fonction du temps?  
Défini en tant que l'union de tous les voisins au cours du temps?  
Les deux termes ont du sens.

### 3.1. Graphe sous-jacent/Empreinte du graphe
Graphe statique regroupant l'ensemble des noeuds **et** des arêtes du graphe dynamique qui apparaissent au moins une fois au cours du temps.

**Attention**! Empreinte connexe **ne signifie pas** que le graphe dynamique soit connexe à tout instant.  
Par contre, une empreinte pas connexe signifie qu'à tout instant, le graphe dynamique n'est pas connexe.

### 3.2. Trajet
**Remarque**. Un chemin entre deux noeuds *u* et *v* dans l'empreinte **n'implique pas** que *u* et *v* sont capables de communiquer.

**Trajet** = suite d'arêtes formant un chemin dans l'empreinte dont les dates de présence sont croissantes. (on va se limiter à ça, la def formelle est dégueue apparemment).

**Remarque2**.  Un trajet **n'est pas symétrique**, à l'inverse d'un chemin.
Graphes dynamiques
---

# Intro

- Approche "a la Lamport : graphe **statique** et **complet**"
- Approche "reseaux" : graphe **statique et incomplet**
    - Non suffisant pour les reseaux dynamiques
        - Dynamique: Ensemble de noeuds peut evoluer
        - Ensemble d'arretes peut evoluer

| Mobilite imprevisible | Previsible | Controlee |
|-|-|-|
| P2P | Reseaux satellites | Reseaux agents/robots |
| Reseaux voitures | Reseaux transport commun |
| Reseaux opportunistes |  |
| Reseaux capteurs |  |

Les deux premiere colonnes representes des reseaux ou la mobilite est subie.
Troisieme colonne c'est des reseaux ou la mobilite est controlee avec l'algo.

Dans le cadre de ce cours on s'interesse aux deux premieres colonnes.

## Remarques

- Approches deterministes aux systemes non-deterministes **existent**
- La dynamique n'est pas une faute! (de meme maniquere que l'on peut pas considerer l'asynchronie comme une "faute")

# Modeles

## Graphes evolutifs (*Evolving graph*)

Principe: 1 graphe dynamique est une suite de graphes statiques qui represente l'evolution de la connectivite a chaque instant.

`G = (G0, G1, G2,...)`

> fig1 td5

Limite:
- Temps discret
- Systeme synchrone

## Graphe evoluant dans le temps (*Time-Varying Graph*)

Principe: Graphe statique auquel on associe une fonction de presence des aretes au cours du temps

`G = (V, E, f)` -> `'ro' : ExR -> {0,1}` (on associe a une arrete 1 si elle est presente, 0 sinon)

> fig2 td5

- Possible d'enrichir le modele avec fonctions
    - de presence de noeuds
    - de latence
    - bande passante
    - etiquetage des noeuds
- Systeme asynchrone

# Redefinition des concepts classiques

Besoin de redefinir toutes les notions de base des graphes statiques (chemin, diametre, connexite,...) puisque ces grandeurs evoluent au cours du temps.

Example:
Voisinage
    - En fonction du temps
    - Union de tous les voisins du temps

## Graphe sous-jacent / Empreinte

> Graphe statique regroupant l'ensemble des noeuds **et** des aretes du graphe dynamique qui apparaissent au moins 1 fois.

La superposition de tout les graphs a tout les instants, ecrase.

Empreinte connexe =/=> graphe dynamique connexe a tout instant

## Trajet

> fig3 td5

> Trajet : Suite d'aretes formant un chemin dans l'empreinte dont les dates de presence sont croissantes

Remarques:

- Trajet non symmetrique
- Trajet depend date depart

> fig4 td5

## Connexite

Notion intuitive: Tout le monde joignable tout le temps

Graphe dynamique `connexe` : Pour toute paire de sommets, pour tout sommet `t` il existe un trajet entre ces noeuds apres `t`

## Distance

Distance defini par deux trucs:

- `Shortest` Nb de sauts minimal de tous les trajets entre 2 sommets. **Attention, pas forcemment le plus rapide!**
- `Fastest` Trajet minimisant `arrive-depart`, sur l'ensemble des trajets apres `t`
- `Foremost` Trajet arrivant le plus tot possible minimise la date d'arrivee apres `t`

## Exercice 1. Foremost broadcast
### 1) cf Théorème 3.
Pour prouver une impossibilité, on passe par le raisonnement par l'absurde/contradiction.

Par l'absurde, supposons exister un algorithme satisfaisant TDB[foremost] sur R sans connaissance de n
Soit un TVG G ∈ ℛ à *n* sommets => si on demande un broadcast à t=0, A est capable
de le diffuser en un temps fini t*f*. On construit un graphe G' où une arête apparaît
après l'instant t*f*, sinon il est identique à G en [0, t*f*].

=> A broadcast un message envoyé à t=0 sur G' avant t*f*. Or, le message n'a pas
pu atteindre la nouvelle arête *a*. L'algorithme, à t*f*, considère qu'il a fini
la diffusion à t*f* pourtant.
=> contradiction avec la spécification.

### Question 2)
cf algorithme 1.
Principe de l'algorithme.

1. Chaque processus informé informe chacun de ses voisins dès que possible (apparition/présence arêtes)
2. Remontée des ACK à la racine à l'aide de la primitive `send_retry`
3. La racine compte les ACK et détecte la terminaison grâce à la valeur de *n*

## Exercice 2. Shortest broadcast

### 1)
Il est impossible de résoudre le shortest broadcast dans la classe B en
connaissant *n*.

cf Théorème 16

On construit un graphe G tranquille, avec deux arêtes u et v ayant comme chemin le plus court l'obligation de passer par une troisième arete, entre [0 et t*f*]. En supposant que celle-ci disparaît après t*f* et qu'un chemin plus court est créé entre *u* et *v*, on a contradiction comme le shortest ne marche plus du coup.

algorithme 3. Idée.

Construire un BFST (breadth-first-spanning-tree, arbre en largeur) de
l'empreinte du TVG, par "couche" autour de l'initiateur. Chaque couche de
profondeur *i* est construite dans la ronde (t*0* + iΔ; t*0* + (i+1)Δ) + gestion
des ACK pour détection de terminaison

## Exercice 3. Fastest broadcast
"je suis pas pressé, prêt à attendre, mais une fois qu'on diffuse y'a pas l'time"

### le 1 on saute genre

### Qustion 2
"il existe un algo capable de calculer la distance temporelle entre deux sommets
au bout d'une période"

1. On laisse l'algo de distance apprendre toutes les distances à l'initiateur pendant 2*p*.
2. On peut calculer l'excentricité temporelle (max distance entre deux noeuds) sur une période
3. On déclence un broadcast en foremost à l'instant où l'excentricité est minimale


## Moralité de l'histoire
Confirmation de l'intuition: plus la classe est petite/plus les contraintes sont fortes, plus on arrive à résoudre des problèmes difficiles. Puis à priori c'est le mieux que l'on pourrait faire

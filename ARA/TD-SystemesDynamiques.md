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


# Exo1

cf th3
--

Par l'absurde, supposons exister un algo A satisfaisant TDB (foremost) sur R, sans connaissance de `n`.

Prenons un TVG `G` appartenant a `R` a `n` summets -> si on demande 1 broadcast a `t=0`, `A` est capable de le diffuser en 1 temps fini `tp`.
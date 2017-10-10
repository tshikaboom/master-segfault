- [Why care about concensus](#why-care-about-concensus)
- [Specification](#specification)
    - [Consensus](#consensus)
    - [**Uniform** Consensus](#uniform-consensus)
    - [Primitives](#primitives)
        - [`propose(v)`](#proposev)
        - [`decide(v)`](#decidev)
- [Modele](#modele)
    - [Types de fautes](#types-de-fautes)
        - [Processus](#processus)
        - [Canaux](#canaux)
    - [Modeles temporels - `(a)sync`](#modeles-temporels---async)
        - [Modele synchrone](#modele-synchrone)
        - [Modele asynchrone](#modele-asynchrone)
        - [Modeles partiellement synchrone](#modeles-partiellement-synchrone)
    - [Contourner FLP](#contourner-flp)
        - [Changer le probleme](#changer-le-probleme)
        - [Systemes partiellement synchrones [DDS87]](#systemes-partiellement-synchrones-dds87)
        - [Consensus imparfait](#consensus-imparfait)
- [Detecteurs des fautes](#detecteurs-des-fautes)
    - [Specs](#specs)
        - [Completude (completness)](#completude-completness)
        - [Justesse (accuracy)](#justesse-accuracy)
        - [Detecteurs de defaillance non fiables [CHT96]](#detecteurs-de-defaillance-non-fiables-cht96)

# Why care about concensus
- whether to commit a tx to a db 
- agree on the id of a leader 
- state machine replication 
- atomic broadcasts
- clock synchronization 
- PageRank
- smart power grids
- load balancing
- many other

# Specification 

## Consensus

- `validité (validity)` si un processus décide `v` alors `v` est une valeur proposée
- `terminaison (termination)` tous les processus corrects decident finalement
- `coherence (agreement)` deux processus corrects ne peuvent decider differemment 
- `integrite (integrity)` un processus doit decider au plus une fois

## **Uniform** Consensus 
- as above plus; 
- No two processes (whether faulty or not) decide differently

## Primitives

### `propose(v)`

le processus appelant propose une valeur initiale `v`

### `decide(v)`

le processus appelant décide `v`


```
 propose(4)      decide(7)
    v                v
-------------------------------->
 
 propose(4)     decide(7)
    v               v
-------------------------------->
 
 propose(4)         decide(7)
    v                   v
-------------------------------->
```

# Modele

## Types de fautes 

- `correct` : ne défaille pas pendant toute la dureé de l'exécution
- `fautif` : pas correct 

### Processus

- `franche (crash)` : le processus fautif n'emet plus ni ne recoit de message de facon _permanente_ -> silence sur defaillance (_fail-silent_), variante faute-visible (_fail-stop_)
- `omission` : transitoire 
- `temporaire` : trop tot ou trop tard
- `Byzantin` : malveillance

```
.______________________________.
|__________________. Byzantine |
|_______. omission |           |
| crash |          |           |
|_______|__________|___________|
```

### Canaux

- `Fiable (reliable)` : si `p` execute `send(m)` vers `q` et `q` _est correct_, alors `q` recevera `m`
- `Quasi-fiable (quasi-reliable)` : si `p` execute `send(m)` vers `q` et, `p` et `q` _sont corrects_, alors `q` recevera `m`
- `Equitable (fair-lossy)` : si un processus correct envoie un message `m` a `q` une infinite de fois, alors `q` recevera `m`

## Modeles temporels - `(a)sync`

### Modele synchrone

- `Borne delta (Δ) sur le temps de transmission` si un processus `p` envoie un message vers `q` a l'instant `t`, alors `q` recoit le message avant `t+Δ`
- `Borne phi (Φ) sur la vitesse relative des processus` si le processus le plus rapide prend `x` unites de temps pour un traitement, alors le processus le plus lent ne peut pas prendre plus `xΦ` temps pour faire le meme traitement

Permet une detection parfaite

```
     |    2Δ + xΦ   |
     |------------->|
p -------------------------------->
     | u alive?     |^ aha
      \.            /
q -------------------------------->
```

### Modele asynchrone

- Pas de borne sur les delais de transmission 
- Pas de borne sur les vitesses relatives des processus
- Impossibilite de Fischer, Lynch, Paterson [FLP85]

### Modeles partiellement synchrone 

Dwork, Lynch, Stockmeier - 1998

- 32 modeles intermediaires entre synchrone et asynchrone
- Bornes Δ et Φ du modele synchrone: 
    - Existent mais sont inconnues, ou;
    - Sont connues mais ont lieu a partir d'un temps `T` appele `GST` : global stabilization time
- Avant `GST` le systeme est instable (pas de bornes)
- Apres le `GST` le systeme est stable (bornes)
- `GST` est inconnu

## Contourner FLP

### Changer le probleme 

- *k*-agreement [Cha90] plusieurs valeurs peuvent etre decides  
------> //TODO What's the use for that? Examples?

### Systemes partiellement synchrones [DDS87]

- Les bornes sont non connues, valables a partir d'un moment
- Variantes : alternance de bonne et mauvaise periodes
- Borne restreinte a certains noeuds
    - 1 bi-source (ultime) : il existe (ultimement) une borne sur les liens entrants et sortants de la source
    - 1 source (ultime) : il existe (ultimement) une borne sur les liens sortants
- Algorithmique dependante du systeme

### Consensus imparfait

- Consensus probabiliste [BO83] : Des processus peuvent ne pas terminer
- Paxos [Lamport89] : Hypothese tres faible, terminaison non assuree

# Detecteurs des fautes

## Specs

### Completude (completness)

- `forte` : A partir d'un moment tout processus defaillant est suspecte par `tous` les processus corrects 
- `faible` : a partir d'un moment tout processus defaillant est suspecte par `un` processus correct

Elles sont equivalentes - on peut construir une forte a partir d'une faible.

1. La faible est incluse dans forte
2. Construction de la forte from faible

```
Task1: repeat forever
    {p queries its local failure detector module Dp}
    suspects_p <- Dp
    send(p, suspects_p) to all

Task2: when receive (q, suspects_q) from some q
    output_p <- (output_p U suspects_q) - {q}
```

### Justesse (accuracy)

- `forte` aucun processus correct n'est suspecte 
- `faible` il existe au moins un processus correct qui n'est jamais suspecte
- `finalement forte` *il existe un instant a partir duquel* tout processus correct n'est plus suspecte par aucun processus correct 
- `finalement faible` *il existe un instant a partir duquel* au moins un processus correct n'est suspecte par aucun processus correct

### Detecteurs de defaillance non fiables [CHT96]

- Algorithmique en asynchrone (indep dy systeme)
- Hypotheses plus facilement utilisables

- Introduit en '91
- Oracle local sur chaque noeud 
- Fournit une liste des processus suspectes d'etre defaillants 
- Informations non fiables (possibilite de fauses suspicions)

`Completude (Completeness)` : un processus defaillant doit etre detecte comme defaillant  
`Justesse (Accuracy)` : un processus correct ne doit pas etre considere comme defaillant


| | Forte | Faible | Finalement force | Finalement faible | 
|--|--|--|--|--|
| Completude forte | P | S | ♦P | ♦S |
| Completude faible | Q | W | ♦Q | ♦W | 

Completude `faible` et `forte` sont equivalentes (on peut construite une completude forte a partir d'une faible)

Force des detecteurs: 

```
P ---> ♦ P
|       |
v       v
S ---> ♦ S
```

omega -> ♦S
omega returne leader
♦s ==> Pi - leader
# Plan

- POV Theorique sur blockchains
- BTC wut dat?

# History

## The 'pizza day'

Two guys bouht two pizzas for 10k BTCs.

## JPMorgan

Ordered employees not to buy. Went out and bought some the same day.

## Russia

At some point BTC was #2 most used currency in Russia, next day it was banned.

# Wut dat?

## TX

Alice sends X amount of something to Bob.

### Made of

- Paye :
    - A envoie X a B. Ca implique:
        - A possede les BTC
        - +x BTC pour A dans l'historique
- Coinbase:
    - Pour les mineurs 50btc au debut
        - Halving tout les 210k blocks
        - On arrive a la fin vers year 2140

### Payment

- T0: Miner(CoinBase)
- T1: SigMiner(Paye(PubKey_Alice, H(T0), H(T1), ...))
- T2: SigAlice(Paye(PubKey_Bob), H(T1))

### Payment to multiple

- T0: `Miner(CoinBase)`
- T1: `SigMiner(Paye(PubKey_Alice, H(T0), H(T1), ...))`
- T2: `SigAlice(Paye( [<PubKey_Bob, 0.25>, <PubKey_Alice, 0.75>], H(T1)))`

## Merkle trees

Hashes of hashes of hashes of hashes

## Mining algo:

```
b <- block
do 
    Nonce <- rand
while H(PrevBlockHash + nonce) <= V
```

La valeur de `V` est la difficulte. Le but de `V` est de regler la difficulte sur le reseau, est de l'equilibrer de facon a avoir 1 block tout les 10min.

`V` c'est une fonction de l'histoire proche. Si le temps de decouverte est trop lent, on augmente le `V`, si trop court, on le diminue.

L'historique est evalue sur 2016 blocks precedents.

## Consensus

- Vote : Impossible car taille de set evolutive
- PoW : Max puissance
- C'est qui qui mine lel (deterministe et non previsible)
    - PoH (Hold)
        - Je possede des coins
    - PoS (Stake)
        - Je met en jeu mes coins
        - Problem: "Nothing at stake" problem. Miners put money in two branches. If 80% places their money on both branches (malicious users), and only 20% plays fair, all it takes is 20%+`Epsilon` to overtake the system.
    - PoU (Use)
        - On valorise ceux qui utilisent beaucoup la coin
        - Breakable by sending back and forth with your friend
        - Or huge taxes on txs
    - PoI (Importance)
        - On est important dans le reseau, les gens passent par nous bcp/nous envoient bcp de thunes/etc
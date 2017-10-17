# Types de fautes

```none
  ^ Cardinalite
  |
  .---> Type
 /
/  Temporalite
V
```

3 axes:

- `x` : Type
    - Crash : Just dies, kktybb
    - Temporelle : Je fais ce qui est demande mais trop vite ou trop lentment
    - Byzantine : Hors spec, je fais pas ce que je suis cense faire (mon programme est buge)
    - Malicieux : N'importe quoi expres. Les attaques
- `y` : Temporalite
    - Permenent
    - Periodique
    - Temporaire
- `z` : Cardinalite
    - 1
    - `k<n`
    - Tous

# Checkpointing

On considere que l'algo n'est pas tolerant, les sites finissent par revenir. Mais une fois revenu, il faut reprendre le bon fonctionement du systeme.

Fig1 from 1Note

Si l'algo est deterministe, et je log tout, tout le temps, sur hard.


Coupure choerente : ce qui est plus vieux qu la coupure ne depent pas de ce qui est plus recent que la coupure. 
Tout les elements du passe soient independents

Tout les evenement plus vieux que la coupure soient independent d'element qui soit plus recent que la coupure. (Arc arriere, quand un message vient du futur pour revenir dans le passe)

Algos:

- Coordonees
    - Les sites se mettent d'accord pour faire des checkpoints de facon "synchrone". Decident ensemble le moment de checkpoint
    - implicites: On tag des messages et en fonction du tag on peut generer une souvegarde de l'etat
    - explicite: les sites deroulent un protocol "let's checkpoint now"
- Spontanes
    - Je fais ce que je veux et je vous emmerde

```none
Spontanes -> Implicites Coordones -> Explicites Coordones
----------------------------------------------------------------->
                Cout d'overhead
```

## Algo1

Reculez  
Colorez  
Reculez  
Colorez  
Rince and repeat

## Algo du rollback-dependency

![asd](./images/chp-rollback.png)
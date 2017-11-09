# Question 1 Créez dans votre projet Eclipse un package tme1 et copiez-y dans le dossier correspondant les fchiers fournis dans vos ressources  TME. Rafraîchissez et assurez-vous que la compilation se passe bien (absence de croix rouge)

ok

# Question 2 Les fchiers qui vous ont été fournis implémentent un protocole applicatif (`ApplicativeProtocol.java`) qui se base sur des primitives de difusion. Déterminez ce que fait ce protocole

Broadcasts `plus` and `fois`. Either adds or multiplies, broadcasts sends out all the messages.

# Question 3 Analysez le code qui vous a ete fourni et produisez un fichier de configuration `peersim` de maniere a:

## d'avoir une graine aleatoire a la valeur 5 

`random.seed 5`

## ce que le protocole applicatif fonctionne sur 5 noeurs en se basant sur le protocol de diffusion basique (`BasicBroadcast.java`)

```bash
SIZE 5
network.size SIZE

protocol.emitterProtocol BasicBroadcast
```

## utiliser une couche transport fiable (prendre `UniformRandomTransport` de l'API `peersim`) et d'avoir une latence reseau comprise entre 5 et 500 unites de temps 

```
# parameters of message transfer
# delay values here are relative to cycle length, in percentage,
# eg 50 means half the cycle length, 200 twice the cycle length, etc.
MINDELAY 500
MAXDELAY 50000
```

## que le module d'initialisation soit la classe `Initialisation` 

## que le controleur `EndControler` soit execute une fois la simulation terminee 

## que le temps de simulation soit suffisamment grand pour que le protocole applicatif se termine

# Question 4 Testez la meme experience en simulant des pannes franches de noeud a l'emission. Pour cela utilisez la classe `DeadlyTransport` en parametrans que les noeuds `1` et `3` peuvent tomber en panne avec une probabilite de `0.1`. Notez les resultats verifiez que le broadcast ne respecte pas la specification d'un broadcast fiable

# Question 5 Testez la meme experience en appliquant cette fois ci le protocole de diffusion fiable (`ReliableBroadcast` dont l'algorithme est donne en cours) sur le protocole de transport non fiable. Notez les resultats et verifiez que la specification du broadcast fiable est respectee. 

# Question 6 En respectant les meme mecanismes que le code qui vous a ete fourni 

## codez une classe de broadcast (implementant l'interface `broadcast`) qui implemente l'algorithme de diffusion FIFO presente en cours. Cette classe se basera sur un protocole de broadcast qui sera specifie dans le fichier de configuration

## testez votre implementation dans un milieu fiable (pas de pannes franches)

## Verifiez que les affichage correspodantent bien a une diffusion FIFO.

# Question 7 Meme question mais en implementant une diffusion causale qui se basera sur un protocole de diffusion FIFO. Notez les resultats.

# Question 8 Creez une classe de broadcast qui implemente l'algorithme de diffusion total a sequenceur fixe. Testez cette classe en vous basant sur un transport fiable et le broadcast basique et verifiez que les valeurs de chaque replicas sont egales. L'identifiant du sequenceur pourra etre parametrable dans le fichier de configuration. 

# Question 9 Testez votre code avec plusieurs combinaisons de protocole

## `reliableTransport + reliableBroadcast + FIFObroadcast + applicativeProtocol`

## `reliableTransport + reliableBroadcast + FIFObroadcast + causalbroadcast + applicativeProtocol`

## `reliableTransport + reliable broadcast + total broadcast + applicative protocl`

## `reliable Tranport + reliable broadcast + total broadcast + fifo broadcast + applicative protocl`

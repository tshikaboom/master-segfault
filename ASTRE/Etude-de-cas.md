# Notes à l'arrache de l'étude de cas

## Partie 1

### Question  1

Dans le pire cas: 1 câble. On ne choisit pas son emplacement
Dans le meilleur cas: 6 câbles (ceux allant du noeud 5 au noeud 0). Il reste les
câbles des noeuds 0 à 4.

### Question 2


T*i* suit une loi exponentielle suivant un paramètre lambda. => ça donne la
chaîne de Markov de type

```
           0.00001
(câble OK) -----> (câble KO)
```

Ce type de chaîne de Markov s'applique sur chaque câble présent dans le système.
Du coup pour 10 câbles dans le système défini ci-dessus, on a 10 chaînes de Markov.


On prend comme valeurs:
- Câble*i* OK = 1
- Câble*i* KO = 0

Du coup, dans le pire des cas, *un* câble est rompu. Il reste 9 machines
connectées, on peut donc dire que `somme (i=0 -> 9) Câble*i* >= 9.`

#### PCTL

- Opérateur **P** représente une contrainte sur une séquence d'états.
Dans Prism, `P = ?` nous donne une valeur
`P <= 0.5 (expression)`
- expression == chemin d'exécution dans la chaîne de Markov.
- Du coup on calcule si la probabilité de l'expression est bien inférieure à
0.5 ou pas. On peut calculer donc le taux de failure.
- On utilise la logique LTL pour le calcul de probabilité.

G_I(Φ): Φ is globally true, mais seulement sur l'intervalle *I*

P = ? (G_[0; 5 mois en heures] (Câble*i* OK)

^ heures car le paramètre lambda a été donné pour une unité de temps
correspondant à **une heure**.
^ probabilité que **tous** les noeuds restent fonctionnels pendant 5 mois.

Ψ*[noeuds 0-5]* = Π *(câble i=0 -> 3)* Câble*i* + Π *(câble i=4 -> 9)* Câble*j* >= 1
^ Expression représentant le fait que les noeuds 0 et 5 **reste fonctionnels**.


#### Modélisation de la maintenance.

Maintenir trois machines fonctionnelles, c'est plus dur que de maintenir une seule
machine genre.

G[0-24h] s'écrit G[0-23] sous Prism.

### Q3.

P = ? FG[0; 24] nonΨ*0-5* mais l'opérateur F ne marche pas

mais par contre P = ? G[t, t + 23] nonΨ*0-5* marche. Prism va demander quel *t*
on veut choisir, on peut en prendre un très grand. <- on fait un experiment.

## Partie 2

On reprend l'architecture TMR.
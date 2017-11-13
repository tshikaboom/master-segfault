- [Exo1](#exo1)
    - [Q1.1 `maxEntiers`](#q11-maxentiers)
    - [Q1.2 `scEntiers`](#q12-scentiers)
    - [Q1.3 `moyEntiers`](#q13-moyentiers)
    - [Q2.1](#q21)
        - [Calculer pour l'année 2009 le maximum de ses températures](#calculer-pour-lann%C3%A9e-2009-le-maximum-de-ses-temp%C3%A9ratures)
        - [Idem pour la moyenne de ces température](#idem-pour-la-moyenne-de-ces-temp%C3%A9rature)

# Exo1

## Q1.1 `maxEntiers`

Definir la fonction `maxEntiers` qui retourne le plus grand des entiers d'une liste fournie en argument

```scala
def max(x:Int, y:Int):Int =
    if (x > y)
        x 
    else y

def maxEntier(list:List[Int]):Int = 
    list.reduce(max)

scala > maxEntier(listeEntiers)
```

## Q1.2 `scEntiers`

Définir la fonction scEntiers qui calcule la somme des carrés des entiers d'une liste fournie en entrée.

```scala
def sum(x:Int, y:Int):Int = x+y

def secEntier (l:List[Int]):Int = l.reduce(sum)

scala> secEntier(listeEntiers)
res: Int = 55
```

## Q1.3 `moyEntiers`

Définir la fonction moyEntiers qui calcule la moyenne des entiers d'une liste fournie en entrée.

```scala
def moyEntier (l:List[Int]):Int = l.reduce(sum)/l.length

scala> moyEntier(listeEntiers)
res: Int = 5
```

## Q2.1

Soit une liste chaine de caractères construite à l'aide de l'instruction suivante

```scala
val listeTemp = List("7,2010,04,27,75", "12,2009,01,31,78", "41,2009,03,25,95", "2,2008,04,28,76", "7,2010,02,32,91")
```

Chaque élément représente un enregistrement fictif de températures avec le format (station, année, mois, température, code_département).

### Calculer pour l'année 2009 le maximum de ses températures

```scala

```

### Idem pour la moyenne de ces température

Bien entendu, il faudra faire les transformations et les conversions de type nécessaires!
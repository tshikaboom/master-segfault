# Ex1

A recopier du site
---

## Example 1

### Repartir

```scala
// Logement: une personne vit dans une ou plusieurs villes
 
val a = List(
"Alice vit Paris",
"Dan vit Nice",
"Bob vit Lyon",
"Alice vit Aix",
"Claire vit Aix",
"Dan vit Paris"
)
```

> Répartir les données dans 4 partitions :
>
> Répartition par découpage suivant l'ordre des données initiales (ressemble à un round robin) 

```scala
val c = sc.parallelize(a, 4).map(s=> s.split(" ")).map(t => Triplet(t(0), t(1), t(2)))
 
// afficher les partitions
c.mapPartitionsWithIndex(entete).collect.foreach(println)
```

> Contenu des partitions

```scala
Contenu de la partition 0
Triplet(Alice,vit,Paris)
 
Contenu de la partition 1
Triplet(Dan,vit,Nice)
Triplet(Bob,vit,Lyon)
 
Contenu de la partition 2
Triplet(Alice,vit,Aix)
 
Contenu de la partition 3
Triplet(Claire,vit,Aix)
Triplet(Dan,vit,Paris)
```

### Repartir via hash

>  Répartition par sujet (i.e. par personne) 

```scala
val vitParPersonne = c.toDS().repartition(4, col("sujet"))
 
// afficher les partitions
vitParPersonne.rdd.mapPartitionsWithIndex(entete).collect.foreach(println)
```
>  Contenu des partitions : 

```scala
partition 0 :
Triplet(Alice,vit,Paris)
Triplet(Dan,vit,Nice)
Triplet(Alice,vit,Aix)
Triplet(Dan,vit,Paris)
 
partition 1 :
Triplet(Bob,vit,Lyon)
 
partition 2 :
Triplet(Claire,vit,Aix)
 
partition 3 : vide
```

>  Répartition par objet (i.e. par ville) 

```scala
val vitParVille = c.toDS().repartition(4, col("objet"))
 
// afficher les partitions
vitParVille.rdd.mapPartitionsWithIndex(entete).collect.foreach(println)
```

> Contenu des partitions

```scala
partition 0 :
Triplet(Dan,vit,Nice)
 
partition 1 : vide
 
partition 2 :
Triplet(Bob,vit,Lyon)
 
partition 3 :
Triplet(Alice,vit,Paris)
Triplet(Alice,vit,Aix)
Triplet(Claire,vit,Aix)
Triplet(Dan,vit,Paris)
```

> Répartition par prop (ici il n'y a qu'une seule valeur de propriété: “vit”)

```scala
val f = c.toDS().repartition(4, col("prop"))
 
// afficher les partitions
f.rdd.mapPartitionsWithIndex(entete).collect.foreach(println)
```
> Contenu des partitions

```scala
tous les triplets dans la partition 2
```

> Répartition sans préciser de clé de partitionnement:

```scala
val g = c.toDS().repartition(4)
 
// afficher les partitions
g.rdd.mapPartitionsWithIndex(entete).collect.foreach(println)
```

> Contenu des partitions :

```scala
partition 0 :
Triplet(Bob,vit,Lyon)
Triplet(Dan,vit,Paris)
 
partition 1 : vide
 
partition 2 : vide
 
partition 3 :
Triplet(Alice,vit,Paris)
Triplet(Dan,vit,Nice)
Triplet(Alice,vit,Aix)
Triplet(Claire,vit,Aix)
```

### TRI et répartition par intervalle
```scala
val t1 = c.toDS().orderBy("sujet")
 
// afficher les partitions
t1.rdd.mapPartitionsWithIndex(entete).collect.foreach(println)
```

>Contenu des partitions :

```scala
partition 0 :
Triplet(Alice,vit,Paris)
Triplet(Alice,vit,Aix)
 
partition 1 : 
Triplet(Bob,vit,Lyon)
 
partition 2 :
Triplet(Claire,vit,Aix)
 
partition 3 :
Triplet(Dan,vit,Nice)
Triplet(Dan,vit,Paris)
 
partition 4 vide
```

```scala
val t2 = c.toDS().orderBy("objet")
 
// afficher les partitions
t2.rdd.mapPartitionsWithIndex(entete).collect.foreach(println)
```

>Contenu des partitions :

```scala
partition 0 :
Triplet(Alice,vit,Aix)
Triplet(Claire,vit,Aix)
 
partition 1 :
Triplet(Bob,vit,Lyon)
 
partition 2 :
Triplet(Dan,vit,Nice)
 
partition 3 :
Triplet(Alice,vit,Paris)
Triplet(Dan,vit,Paris)
 
partition 4: vide
```

## Example 2

### JOINTURE ET PARTITIONNEMENT

>Sport: une personne pratique un ou plusieurs sports

```scala
val sport1 = List(
"Alice fait vélo",
"Claire fait surf",
"Claire fait vélo",
"Alice fait natation",
"Claire fait moto",
"Dan fait foot"
)
 
val sport2 = sc.parallelize(sport1, 4).map(s=> s.split(" ")).map(t => Triplet(t(0), t(1), t(2)))
 
// afficher les partitions
sport2.mapPartitionsWithIndex(entete).collect.foreach(println)
```

> JOINTURE sur des données partitionnées en round robin

```scala
//réinitialisation: vider le stockage persistent
sc.getPersistentRDDs.values.foreach(x => x.unpersist())
 
PERS.setName("VIT").persist.count
 
val SPORT = sport2
SPORT.setName("SPORT").persist.count
 
val J1 = PERS.toDS().join(SPORT.toDS(), "sujet")
 
J1.rdd.mapPartitionsWithIndex(entete).collect.foreach(println)
```

> JOINTURE sur des données partitionnées sur le sujet

```scala
// réinitialisation: vider le stockage persistent
sc.getPersistentRDDs.values.foreach(x => x.unpersist())
 
val VIT = vitParPersonne
VIT.persist.count
// afficher les partitions
vitParPersonne.rdd.mapPartitionsWithIndex(entete).collect.foreach(println)
 
//répartition du Sport par sujet (i.e. par personne)
val sportParPersonne = sport2.toDS().repartition(4, col("sujet"))
 
// afficher les partitions
sportParPersonne.rdd.mapPartitionsWithIndex(entete).collect.foreach(println)
 
val SPORT = sportParPersonne
SPORT.persist.count
 
val J2 = VIT.join(SPORT, "sujet")
 
J2.rdd.mapPartitionsWithIndex(entete).collect.foreach(println)
```

# Exo2a

## Equi-jointure parallèle et répartie

Dans cet exercice on utilise les données YAGO

Etudier la requête R1

```scala
?x <livesIn> ?y .
?x <isCitizenOf> ?z
```

### R1a) Partitioner les données en respectant l'ordre du fichier initial. Mesurer la taille en MB des données transférées (shuffle read) pendant la requête

```scala

```

### R1b) Partitioner les données de telle sorte que la requête s'exécute sans aucun transfert

```scala

```

### R3b) Ecrire 3 solutions équivalentes pour traiter cette requête (en faisant varier l'ordre des jointures). Quelle solution minimise les transferts?

```scala

```
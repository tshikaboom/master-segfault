# Prep

## Load `YAGO`

```scala
case class Triple(sujet: String, prop: String, objet: String)

val yagoFile = "/tmp/BDLE/dataset/yagoFacts5M.txt"

val yago = sc.textFile(yagoFile).
  map(ligne => ligne.split("\\t")).coalesce(8).
  map(tab => Triple(tab(0), tab(1), tab(2))).toDS()

yago.persist
yago.count
```

# Statistiques de base

## Q1: Retourner la liste des 10 propriétés les plus fréquentes. La sortie doit être une liste de couples `(prop, freq)` triée de manière décroissante

### Change `count` to `freq`

```scala
withColumnRenamed("count", "freq")
```

### Reponse

```scala
val q1 = yago
    .groupBy("prop")
    .count.withColumnRenamed("count", "freq")
    .orderBy($"count".desc)
    .takeAsList(10)
```

## Q2: Retourner la liste des 10 noeuds ayant le plus grand degré sortant

__Rappel__ Le degré sortant d'un noeud n est le nombre de triplets où n est le sujet. La sortie doit être une liste de couples (sujet, degré) triée de manière décroissante

```scala
val q2 = yago
    .map{case Triple(sujet, prop, object) => sujet}
    .withColumnRenamed("value", "sujet")
    .groupBy("sujet")
    .count
    .withColumnRenamed("count, degre")
    .orderBy($"degre".desc)
    .takeAsList(10)
```

## Q3: Pour chaque propriété, retourner le nombre de sujets distincts d'où elle démarre ainsi que le nombre d'objets distincts où elle arrive. La sortie doit être une liste de tuples `(pro, nb-sujets, nb-objets)`

**Attention** Un objet (sujet) peut avoir plusieurs fois la même propriété

```scala
case class PropObjet(prop: String, objet: String)
case class SujetProp(sujet: String, prop: String)

val propDegOut = yago.map{case Triple(sujet, prop, object) => PropObjet(prop, sujet) }
    .distinct
    .groupBy("prop")
    .count
    .wihColumnRenamed("count", "nb_objects")

val propDegIn = yago.map{case Triple(sujet, prop) => SujetProp(sujet, prop) }
    .distinct
    .groupBy("prop")
    .count
    .wihColumnRenamed("count", "nb_sujets")

val q3 = propDegIn.join(propDegreOut, "prop")
```

## Q4: Encoder la fonction `noeudDegre(d:entier)` qui retourne les noeuds de degrée d

Le degré d'un noeud = degré sortant + degré entrant
`n = n_in + n_out`

`na.fill(0, [Columns])` remplace les `null` dans les colonnes par des `0`.

```scala
def noeudDegre(d: Int) = {
    val dOut = yago.groupBy("sujet").count
    val dIn = yago.groupBy("objet").count
    val dgs = dIn.join(dOut, $"sujet" == $"objet", "full_outer")
    dgs.na
        .fill(0, Array("dIn", "dOut"))
        .select($"sujet", $"objet", $"dIn" + $"dOut")
        .withColumnRenamed("(din+dOut)", "deg")
        .filter($"deg" === d)
        .map(row => (row.getAs[String](0) == null) row.getAs[String](1) else row.getAs[String](0)
        )
    return dgs.takeAsList(dgs.count.toInt)
}
```

# Statistiques sur les chemins et co-occurences

## Q1: Pour chaque pattern de 2 propriétés qui se suivent, calculer sa fréquence dans les données. 

Exemple Si le triple pattern `(?x,influences,?y) (?y, livesIn, ?z)` retourne `1000` résultats alor la fréquence du pattern `(influences, livesIn)` vaut `1000`

```scala
val t1 = yago
    .withColumnRenamed("prop", "A")
    .withColumnRenamed("objet", "J")
    .select("sujet", "A", "J")
    
val t2 = yago
    .withColumnRenamed("prop", "B")
    .withColumnRenamed("sujet", "J")
    .select("objet", "B", "J")

val res = t1.join(t2, "J").groupBy("A", "B").count()
```

## Q2: Encoder la fonction `cheminNoeudLongueur(noeud: string, len:entier)` qui retourne, pour le sujet `noeud`, tous les chemins démarrant de noeud et ayant la longueur `len`

La longueur d'un chemin est le nombre de propriétés traversées

```scala

```

## Q3: Pour chaque paire de propriétés, donner le nombre de sujets qu'elles partagent. 
Exemple. Si le triple `pattern (x, livesIn, y) (x, citizenOf, z)` retourne 10 résultat alors les propriétés de la paire `(livesIn, citizenOf)` partagent 10 sujets

```scala
val q1 = yago
    .withColumnRenamed("sujet", "x")
    .select("x", "prop")

val q2 = yago
    .withColumnRenamed("sujet", "y")
    .select("y", "prop")

val q3 = q1.join(q2, "prop")
```

# Bonus

## Dans un premier temps, compléter les triplets de `dbpediaShortName8M` avec le type de leurs noeuds qui se trouvent dans `dbpediaShortNameTypeFor8M.txt`

```scala

```

## Pour chaque type, retourner son domaine, i.e le nombre de sujets distincts ayant ce type

```scala

```

## Pour chaque type, retourner son co-domaine, i.e le nombre d'objets distincts ayant ce type

```scala

```
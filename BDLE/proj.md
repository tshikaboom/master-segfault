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

### Output

``̀`scala

scala> yago.groupBy("prop").count.withColumnRenamed("count", "freq").orderBy($"count".desc).takeAsList(10)
res3: java.util.List[org.apache.spark.sql.Row] = [[<isAffiliatedTo>,1116512], [<playsFor>,772092], [<isCitizenOf>,731207], [<isLocatedIn>,512925], [<hasGender>,486528], [<wasBornIn>,405252], [<actedIn>,242436], [<diedIn>,131001], [<hasWonPrize>,115476], [<graduatedFrom>,112670]]

```


## Retourner la liste des 10 noeuds ayant le plus grand degré sortant

__Rappel__ Le degré sortant d'un noeud n est le nombre de triplets où n est le sujet. La sortie doit être une liste de couples (sujet, degré) triée de manière décroissante

```scala
val q2 = yago
    .map{case Triple(sujet: String, prop, object) => sujet}
    .withColumnRenamed("value", "sujet")
    .groupBy("sujet")
    .count
    .withColumnRenamed("count, degre")
    .orderBy($"degre".desc)
    .takeAsList(10)
```

### Output

```scala
res6: java.util.List[org.apache.spark.sql.Row] = [[<Ilaiyaraaja>,878], [<Prem_Nazir>,471], [<United_States>,456], [<Deva_(composer)>,440], [<Adoor_Bhasi>,383], [<M._S._Viswanathan>,378], [<Mammootty>,360], [<United_Kingdom>,345], [<Laxmikant–Pyarelal>,324], [<Mohanlal>,314]]
```


## Q3: Pour chaque propriété, retourner le nombre de sujets distincts d'où elle démarre ainsi que le nombre d'objets distincts où elle arrive. La sortie doit être une liste de tuples `(pro, nb-sujets, nb-objets)`

**Attention** Un objet (sujet) peut avoir plusieurs fois la même propriété

```scala
case class PropObjet(prop: String, objet: String)
case class SujetProp(sujet: String, prop: String)

val propDegOut = yago.map{case Triple(sujet, prop, objet) => PropObjet(prop, sujet) }.
    distinct.
    groupBy("prop").
    count.
    withColumnRenamed("count", "nb_objects")

val propDegIn = yago.map{case Triple(sujet, prop, objet) => SujetProp(sujet, prop) }.
    distinct.
    groupBy("prop").
    count.
    withColumnRenamed("count", "nb_sujets")

val q3 = propDegIn.join(propDegOut, "prop")
```

### Output

```scala
+--------------------+---------+----------+
|                prop|nb_sujets|nb_objects|
+--------------------+---------+----------+
|<hasOfficialLangu...|     1177|      1177|
|    <isInterestedIn>|      781|       781|
|         <dealsWith>|      262|       262|
|       <isLocatedIn>|   114649|    114649|
|<hasAcademicAdvisor>|     2318|      2318|
|        <isKnownFor>|       68|        68|
|       <isCitizenOf>|   488398|    488398|
|        <influences>|     4517|      4517|
|           <exports>|      167|       167|
|           <actedIn>|    35223|     35223|
|       <hasWonPrize>|    58927|     58927|
|         <wasBornIn>|   405252|    405252|
|     <isConnectedTo>|     1927|      1927|
|          <hasChild>|      839|       839|
|    <isPoliticianOf>|    20481|     20481|
|        <hasCapital>|     2012|      2012|
|     <graduatedFrom>|    77240|     77240|
|           <created>|    26547|     26547|
|       <hasNeighbor>|      151|       151|
|            <edited>|     1110|      1110|
+--------------------+---------+----------+
only showing top 20 rows

```


## Q4: Encoder la fonction `noeudDegre(d:entier)` qui retourne les noeuds de degrée d

```scala
import org.apache.spark.sql.Row
```

Le degré d'un noeud = degré sortant + degré entrant
`n = n_in + n_out`

`na.fill(0, [Columns])` remplace les `null` dans les colonnes par des `0`.

```scala
def noeudDegre(d: Int) : java.util.List[String] = {
    val dOut = yago.groupBy("sujet").count.withColumnRenamed("count", "dOut")
    val dIn = yago.groupBy("objet").count.withColumnRenamed("count", "dIn")
    val dgs = dIn.join(dOut, $"sujet" === $"objet", "full_outer").
    	na.
        fill(0, Array("dIn", "dOut")).
        select($"sujet", $"objet", $"dIn" + $"dOut").
        withColumnRenamed("(din + dOut)", "deg").
        filter($"deg" === d).
        map(row => if (row.getAs[String](0) == null) row.getAs[String](1) else row.getAs[String](0))
    return dgs.takeAsList(dgs.count.toInt)
}
```

### Output

```scala
scala> noeudDegre(5)
res23: java.util.List[String] = [<2016_Summer_Paralympics_opening_ceremony>, <2016–17_Men's_FIH_Hockey_World_League_Round_1>, <365_Days_(film)>, <4G_(film)>, <A._Richard_Newton>, <A_Dangerous_Life>, <A_Gamble_for_Love>, <A_Pigeon_Sat_on_a_Branch_Reflecting_on_Existence>, <A_Sister_to_Assist_'Er_(1938_film)>, <A_Whiter_Shade_of_Pale>, <Aaron_Watson>, <Ab_Barik-e_Kuchek>, <Ab_Garm,_Isfahan>, <Ab_Meshkin>, <Ab_Saunders>, <Abbas_Kola,_Babol>, <Abbasabad,_Mamasani>, <Abbasabad-e_Pamzar>, <Abby_Lee_(politician)>, <Abdolabad,_Abyek>, <Abdul_Jabbar_(singer)>, <Abdus_Salam_Award>, <Abi_Nam>, <Abilene,_Kansas>, <Abo_El_Seoud_El_Ebiary>, <Abu_Jadan_Mirza_Avaz>, <Aco_Šopov>, <Adrian_Henger>, <Aegna>, <Afkasht>, <Afra,_Iran>, <Aga_Khan_I>, <Aghuz_Chekeh>, <Agnes_Hewes>, <Ahmadabad,_Deyr>, <Ahmed_Sei...
scala>

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
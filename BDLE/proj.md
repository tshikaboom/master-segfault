# Rudek Michal - BDLE_Miniprojet - M2SAR 17/18

## Load `YAGO`

```scala
import org.apache.spark.sql._
case class Triple(sujet: String, prop: String, objet: String)

val yagoFile = "/tmp/BDLE/dataset/yagoFacts5M.txt"

val yago = sc.textFile(yagoFile).
  map(ligne => ligne.split("\\t")).coalesce(8).
  map(tab => Triple(tab(0), tab(1), tab(2))).toDS()

yago.persist
```

# Statistiques de base

## Q1: Retourner la liste des 10 propriétés les plus fréquentes. La sortie doit être une liste de couples `(prop, freq)` triée de manière décroissante

### Reponse

```scala
val q1 = yago
    .groupBy("prop")
    .count.withColumnRenamed("count", "freq")
    .orderBy($"count".desc)
    .takeAsList(10)
```


### Output

```scala
scala> yago.groupBy("prop").count.withColumnRenamed("count", "freq").orderBy($"count".desc).takeAsList(10)

res3: java.util.List[org.apache.spark.sql.Row] = [[<isAffiliatedTo>,1116512], [<playsFor>,772092], [<isCitizenOf>,731207], [<isLocatedIn>,512925], [<hasGender>,486528], [<wasBornIn>,405252], [<actedIn>,242436], [<diedIn>,131001], [<hasWonPrize>,115476], [<graduatedFrom>,112670]]


+----------------+-------+
|            prop|   freq|
+----------------+-------+
|<isAffiliatedTo>|1116512|
|      <playsFor>| 772092|
|   <isCitizenOf>| 731207|
|   <isLocatedIn>| 512925|
|     <hasGender>| 486528|
|     <wasBornIn>| 405252|
|       <actedIn>| 242436|
|        <diedIn>| 131001|
|   <hasWonPrize>| 115476|
| <graduatedFrom>| 112670|
+----------------+-------+
```


## Q2: Retourner la liste des 10 noeuds ayant le plus grand degré sortant


__Rappel__ Le degré sortant d'un noeud `n` est le nombre de triplets où `n` est le `sujet`. La sortie doit être une liste de couples `(sujet, degré)` triée de manière décroissante

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

+--------------------+-----+
|               sujet|degre|
+--------------------+-----+
|       <Ilaiyaraaja>|  878|
|        <Prem_Nazir>|  471|
|     <United_States>|  456|
|   <Deva_(composer)>|  440|
|       <Adoor_Bhasi>|  383|
| <M._S._Viswanathan>|  378|
|         <Mammootty>|  360|
|    <United_Kingdom>|  345|
|<Laxmikant–Pyarelal>|  324|
| <Jagathy_Sreekumar>|  314|
+--------------------+-----+
```


## Q3: Pour chaque propriété, retourner le nombre de sujets distincts d'où elle démarre ainsi que le nombre d'objets distincts où elle arrive. La sortie doit être une liste de tuples `(pro, nb-sujets, nb-objets)`

**Attention** Un objet (sujet) peut avoir plusieurs fois la même propriété

```scala
case class PropObjet(prop: String, objet: String)
case class SujetProp(sujet: String, prop: String)

val degOut = yago
    .map{case Triple(sujet, prop, objet) 
        => PropObjet(prop, objet) }
    .distinct
    .groupBy("prop")
    .count
    .withColumnRenamed("count", "nb_objects")

val degIn = yago.map{case Triple(sujet, prop, objet) 
        => SujetProp(sujet, prop) }
    .distinct
    .groupBy("prop")
    .count
    .withColumnRenamed("count", "nb_sujets")

val q3 = degIn.join(degOut, "prop")
```

### Output

```scala
+--------------------+---------+---------+
|                prop|nb_sujets|nb_objets|
+--------------------+---------+---------+
|<hasOfficialLangu...|     1177|      134|
|    <isInterestedIn>|      781|      160|
|         <dealsWith>|      262|      204|
|       <isLocatedIn>|   114649|    30544|
|<hasAcademicAdvisor>|     2318|      527|
|        <isKnownFor>|       68|       56|
|       <isCitizenOf>|   488398|      451|
|        <influences>|     4517|     1967|
|           <exports>|      167|       58|
|           <actedIn>|    35223|    58160|
|       <hasWonPrize>|    58927|     2271|
|         <wasBornIn>|   405252|    32467|
|     <isConnectedTo>|     1927|     1733|
|          <hasChild>|      839|      494|
|    <isPoliticianOf>|    20481|     1065|
|        <hasCapital>|     2012|     1692|
|     <graduatedFrom>|    77240|     3153|
|           <created>|    26547|    43816|
|       <hasNeighbor>|      151|      152|
|            <edited>|     1110|    16730|
+--------------------+---------+---------+

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
    val dgs = dIn
            .join(dOut, $"sujet" === $"objet", "full_outer")
            .na
            .fill(0, Array("dIn", "dOut"))
            .select($"sujet", $"objet", $"dIn" + $"dOut")
            .withColumnRenamed("(din + dOut)", "deg")
            .filter($"deg" === d)
            .map(row => 
                if (row.getAs[String](0) == null)
                    row.getAs[String](1) 
                else 
                    row.getAs[String](0))

    return dgs.takeAsList(dgs.count.toInt)
}
```

### Output

```text
scala> noeudDegre(5)

res23: java.util.List[String] = [<2016_Summer_Paralympics_opening_ceremony>, <2016–17_Men's_FIH_Hockey_World_League_Round_1>, <365_Days_(film)>, <4G_(film)>, <A._Richard_Newton>, <A_Dangerous_Life>, <A_Gamble_for_Love>, <A_Pigeon_Sat_on_a_Branch_Reflecting_on_Existence>, <A_Sister_to_Assist_'Er_(1938_film)>, <A_Whiter_Shade_of_Pale>, <Aaron_Watson>, <Ab_Barik-e_Kuchek>, <Ab_Garm,_Isfahan>, <Ab_Meshkin>, <Ab_Saunders>, <Abbas_Kola,_Babol>, <Abbasabad,_Mamasani>, <Abbasabad-e_Pamzar>, <Abby_Lee_(politician)>, <Abdolabad,_Abyek>, <Abdul_Jabbar_(singer)>, <Abdus_Salam_Award>, <Abi_Nam>, <Abilene,_Kansas>, <Abo_El_Seoud_El_Ebiary>, <Abu_Jadan_Mirza_Avaz>, <Aco_Šopov>, <Adrian_Henger>, <Aegna>, <Afkasht>, <Afra,_Iran>, <Aga_Khan_I>, <Aghuz_Chekeh>, <Agnes_Hewes>, <Ahmadabad,_Deyr>, <Ahmed_Sei...
scala>

```

# Statistiques sur les chemins et co-occurences

## Q1: Pour chaque pattern de 2 propriétés qui se suivent, calculer sa fréquence dans les données. 

Exemple Si le triple pattern `(?x,influences,?y) (?y, livesIn, ?z)` retourne `1000` résultats alor la fréquence du pattern `(influences, livesIn)` vaut `1000`

```scala
val t1 = yago.
    withColumnRenamed("prop", "A").
    withColumnRenamed("objet", "J").
    select("sujet", "A", "J")
    
val t2 = yago.
    withColumnRenamed("prop", "B").
    withColumnRenamed("sujet", "J").
    select("objet", "B", "J")

val res = t1.join(t2, "J").groupBy("A", "B").count()
```

### Output

```text
+--------------------+--------------------+------+
|                   A|                   B| count|
+--------------------+--------------------+------+
|           <livesIn>|        <hasCapital>| 20888|
|       <isCitizenOf>|       <isLocatedIn>|513870|
|            <diedIn>|<hasOfficialLangu...| 14015|
|    <isAffiliatedTo>|         <wasBornIn>|    85|
|          <playsFor>|    <isAffiliatedTo>|   838|
|         <wasBornIn>|       <hasNeighbor>|135628|
|              <owns>|           <imports>|   255|
|       <isMarriedTo>|        <isKnownFor>|     2|
|           <created>|            <edited>|     6|
|    <isInterestedIn>|     <graduatedFrom>|    97|
|<hasAcademicAdvisor>|       <isMarriedTo>|    21|
|           <created>|          <directed>|    80|
|        <happenedIn>|<hasOfficialLangu...| 20690|
|       <isLocatedIn>|       <hasCurrency>| 46766|
|        <hasCapital>|        <hasCapital>|     6|
|         <dealsWith>|              <owns>|  1833|
|           <created>|<hasOfficialLangu...|    78|
|          <hasChild>|           <livesIn>|    32|
|       <isMarriedTo>|    <isAffiliatedTo>|    67|
|        <isKnownFor>|       <isCitizenOf>|    56|
+--------------------+--------------------+------+
only showing top 20 rows
```


## Q2: Encoder la fonction `cheminNoeudLongueur(noeud: string, len:entier)` qui retourne, pour le sujet `noeud`, tous les chemins démarrant de noeud et ayant la longueur `len`

La longueur d'un chemin est le nombre de propriétés traversées

```scala
def abc(n: String, len: Int) : List[String] = {
    def aux(n: String, len: Int, acc:List[String]) = {
        if (len == 0) {
	      return acc
		}
        yago.where($"sujet" === "<David_Bowie>").select("objet")
    } // aux
    return aux(n, len, List())
} // Def
```

### Output

## Q3: Pour chaque paire de propriétés, donner le nombre de sujets qu'elles partagent. 
Exemple. Si le triple `pattern (x, livesIn, y) (x, citizenOf, z)` retourne 10 résultat alors les propriétés de la paire `(livesIn, citizenOf)` partagent 10 sujets

```scala
val q1 = yago
    .withColumnRenamed("sujet", "x")
    .select("x", "prop1")

val q2 = yago
    .withColumnRenamed("sujet", "y")
    .select("y", "prop2")

val q3 = q1.join(q2, "sujet").where($"prop1" < $"prop2").distinct.
  groupBy("prop1", "prop2").count
```

### Output

```scala
+--------------------+----------------+------+
|               prop1|           prop2| count|
+--------------------+----------------+------+
|     <graduatedFrom>|     <hasGender>| 76967|
|    <isAffiliatedTo>|       <worksAt>|    26|
|            <diedIn>|        <edited>|   314|
|           <exports>|          <owns>|    34|
|    <isAffiliatedTo>|     <wasBornIn>|210562|
|    <isPoliticianOf>|       <worksAt>|    21|
|           <created>|        <edited>|   282|
|         <hasGender>|          <owns>|   445|
|<hasAcademicAdvisor>|   <isMarriedTo>|     6|
|           <created>|      <directed>|  6739|
|          <directed>|     <hasGender>| 11106|
|            <diedIn>|    <isKnownFor>|    38|
|     <graduatedFrom>| <wroteMusicFor>|   225|
|            <diedIn>|       <worksAt>|  3945|
|           <exports>|    <hasCapital>|   134|
|       <isCitizenOf>|    <isKnownFor>|    65|
|         <dealsWith>|          <owns>|    43|
|          <hasChild>|       <livesIn>|    13|
|     <graduatedFrom>|<isInterestedIn>|   325|
|         <hasGender>|   <hasWonPrize>| 58040|
+--------------------+----------------+------+
```
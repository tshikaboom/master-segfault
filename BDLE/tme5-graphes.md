[GraphX Guide](http://spark.apache.org/docs/latest/graphx-programming-guide.html)
--

# Ex1

> Construire un graphe dirigé (voir la section “Graph Builders” dans la documentation GraphX) à partir des données de cet exercice tel que :
> - chaque sommet est identifié par l'attribut utilisateur et ayant pour propriétés les attributs prénom, nom et âge de facebook_users_prop.csv.
> - chaque arête reliant deux sommets a pour propriétés les attributs type_relation et nombre_messages_échanés de facebook_edges_prop.csv.
>
> Le type de la structure Spark qui stocke les sommets est :
>
> `org.apache.spark.rdd.RDD[(org.apache.spark.graphx.VertexId, (String, String, Int))]`
>
> Le type de la structure Spark qui stocke les arêtes est :
>
>`org.apache.spark.rdd.RDD[org.apache.spark.graphx.Edge[(String, Int)]]`
>
> Le type de la structure Spark qui stocke le graphe est :
>
>`org.apache.spark.graphx.Graph[(String, String, Int),(String, Int)]`

```scala
import org.apache.spark.graphx._
import org.apache.spark.rdd.RDD

val userFile = "path_to_dir/facebook_users_prop.csv"
val edgeFile = "path_to_dir/facebook_edges_prop.csv"

val users = sc.textFile(userFile).map(_.split(",")).
    map(x => x(0).toLong, (x(1)), (x(3).toInt))
val edges = sc.textFile(edgeFile).map(_.split(",")).
    map(x => Edge(x(0).toLong, x(1).toLong, (x(2), x(3).toInt)))

// Default link

val defaultUser = ("Non available", "Non available", 0)

// Create graph

val graph = Graph(users, edges, defaultUser)
```

# Ex2

> Afficher pour l'utilisateur dont le prénom est 'Kendall' son identifiant, nom et son âge (utiliser la vue `graph.vertices`).
>
> `2058 Brewbaker 49`

```scala
val kendall_triplet = graph.triplets.filter { t => t.srcAttr._1 == "Kendall"}
val ex2 = graph.vertices.fiter { case (id, (name, lastName, age)) => name == "Kendall" }
kendall.collect.foreach(println(_))
```


# Ex3

> Afficher les prénoms des amis de “Kendall” (utiliser la vue `graph.triplets`). On considère le graphe non-dirigé.

```scala
val k_friends = graph.triplets.filter{ t => t.srcAttr._1 == "Kendall" && t.attr._1 == "friend" }.
    map { triple => triple.dstAttr._1 }
k_friends.foreach.println(_)
```

# Ex4

> Afficher les identifiants des utilisateurs qui ont désigné “Kendall” comme collègue et qui ont échangé plus de 70 messages avec lui (utiliser la vue graph.triplets).
> 
> `1966, 1983, 1941`

```scala
val k_coll = kendall_triplet.filter{ t => t.dstAttr._1 == "Kendall" && t.attr._1 == "collegue" && t.attr._2 > 70 }.map{ _.srcId }
k_coll.foreach(println(_))
```

# Ex5

> Afficher l'identifiant de l'utilisateur qui a désigné le plus d'utilisateurs comme amis et avec lesquels il a échangé plus de 80 messages. Afficher également le nombre de ces amis (vous pouvez utiliser la méthode `Array.maxBy`).
>
>`(107,59)`

```scala
val max_friends = graph.triplets.filter { t => t.attr._1 == "friend" && t.attr._2 > 80 }.map{ t => (t.srcId) }.countByValue()
max_friends.maxBy(_._2)
```

# Ex6

> Afficher le nom, le prénom et l'âge des voisins de “Kendall”, en considérant que le graphe est non-dirigé (utiliser la vue graph.triplets) suivant l'une des trois manières suivantes :
>
> - En utilisant graph.triplets seulement
> - En utilisant collectNeighbors (innerJoin)
> - En utilisants aggregateMessage
>
> `Nombre de résultats : 76`

```scala

```

# Ex7

> Afficher le nombre de liens entrants minimum du graphe. Afficher les noms des utilisateurs qui ont le nombre minimum de liens entrants, les utilisateurs sans liens entrants ne seront pas affichés (utiliser graph.inDegrees et innerJoin).
>
> `Le degré entrant minimum est 1`  
> `Il existe 323 noeuds qui ont ce degré`

```scala

```

# Ex8

> Afficher les noms des utilisateurs qui n'ont pas de liens entrants (utiliser inDegrees et outerJoinVertices).
>
> `Dalvin, Floy.`

```scala

```

# Ex9

> Afficher les noms des utilisateurs dont le nombre de liens entrants est égal à leur nombre de liens sortants. Affichez également le nombre de liens entrants pour chaque utilisateur obtenu.

```scala

```

# Ex10

> Pour les utilisateurs Dalvin, Kendall et Elena affichez les prénoms et l'âge des utilisateurs les plus âgés parmi les utilisateurs qui les ont désigné comme amis. Si un utilisateur parmi les trois n'a pas de liens entrants le programme doit afficher un message.
>
> `Dalvin n'a pas de liens entrants.`  
> `Jonas 41 est le plus âgé parmi les utilisateurs qui référencent Kendall.`  
> `River 22 est le plus âgé parmi les utilisateurs qui référencent Elena.`

```scala

```

# Ex11

> Afficher les prénoms des utilisateurs qui ont désigné Kendall et Lilia comme étant leurs amis (amis communs de Kendall et de Lilia).

```scala

```

# Ex12

> Pour chaque utilisateur, trouver l'âge moyen de ses amis (on considère que le graphe est non-dirigé). Utiliser la méthode aggregateMessages.
>  Rappel Le graphe étant dirigé, il faudra s'assurer que tous les sommets envoient des messages à leurs successeurs ainsi qu'à leurs prédécesseurs.
>
>Pour consulter le résultat,
>
> `less /Infos/bd/spark/dataset/facebook/facebook_avg_age_per_user.txt`

```scala

```

# Ex13

>  Calculer pour chaque noeud la liste des différentes types de relations sur ses arcs sortants. L'attribut d'un noeud contenant le type de ses liens sortants sera de type Set[String]. Construire ensuite un nouveau graphe où chaque noeud a comme attributs son prénom et la liste des types de ses arcs sortants, ou une liste vide s'il n'a pas d'arcs sortants (utiliser outerJoinVertices). Afficher la liste des types des arcs sortants pour l'utilisateur Elena dans ce nouveau graphe.
>
> `acquaintance, friend`

```scala

```

# Ex14

> Affichez les prénoms des amis des amis de Deana (à distance 2 de Deana), pour un graphe dirigé sans utiliser la fonction pregel (suggestion: utilisez graph.aggregateMessages et graph.outerJoinVertices).
>
> `Sharita`  
> `Darrick`  
> `Idabelle`

```scala

```

# Ex15

> Affichez les prénoms des amis des amis de Deana (à distance 2 de Deana), pour un graphe dirigé en utilisant la fonction pregel. Vous pouvez prendre comme point de départ l'implantation de l'algorithme des plus courts chemins dans la section “Pregel API”.
> 
> `(2646,(2.0,Idabelle))`  
> `(2631,(2.0,Darrick))`  
> `(2655,(2.0,Sharita))`

```scala

```

# Ex16

> Calculez le sous-graphe contenant seulement l'utilisateur Deana et ses amis à une distance inférieure ou égale à 2. Affichez chaque noeud et chaque arc du graphe.
>
> `(2624,(0.0,Deana))`  
> `(2625,(1.0,Ethel))`  
> `(2646,(2.0,Idabelle))`  
> `(2631,(2.0,Darrick))`  
> `(2655,(2.0,Sharita))`

```scala

```
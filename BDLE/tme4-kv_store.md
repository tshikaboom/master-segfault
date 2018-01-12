# Ex3

> Implanter un algorithme simple de calcul de triangles dans un graphe (triangle: ensemble de ois noeuds qui sont adjacents mutuellement). On considère le graphe non-orienté.
>
>```java
>//Construire la liste de couples de noeuds adjacents (n1,n2) tels que n1<n2
> val edges = ....
> //Pour chaque noeud n1 construire la liste de couple de ses voisins: une entrée dans v_list t >(n1, (n2, n3))
> //où n2<n3 afin de considérer le couple (n2, n3) une seule fois
> val v_list = ....
> //Pour chaque couple (n1, n2) de voisins de la liste précédente, compter le nombre total de >euds dont n1 et n2 
> //sont voisins: une entrée dans v_total est ((n1,n2), total) 
> val v_total = .....
> //Garder dans v_total seulement les couples (n1, n2) qui sont eux-mêmes voisins dans le aphe 
> val triangles=....
> //Afficher le nombre total de triangles, réponse attendue: 1612010 
> ....
> //Afficher 10 triangles    
> ....
> ```
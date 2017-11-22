## Ressources

- [Algebre Spark ou Dataset (Nov16)](http://www-bd.lip6.fr/wiki/_media/site/enseignement/master/bdle/examen2016.pdf)
- [Spark - KV Store (Fev15)](http://www-bd.lip6.fr/wiki/_media/site/enseignement/master/bdle/bdle_exam_fevrier2015.pdf)
- [Spark (Fev15)](http://www-bd.lip6.fr/wiki/_media/site/enseignement/master/bdle/examen_2015_aspark.pdf)

# SQL

Soit table `R`: 

|A | B | C | D | 
|--|---|---|---|
|x|y|1|2|
|y|y|1|5|
|x|z|2|3|
|y|y|1|4|
|z|y|4|1|
|y|y|2|2|

## Cube

> Ecrivez la requete qui renvoie le resultat suivant

|A|B|N|
|--|--|--|
|x|y|1|
|x|z|1|
|x||2|
|y|y|3|
|y||3|
|z|y|1|
|z||1|
||y|5|
||z|1|
|||6|

```sql
select a, b, count(D) as n
from R
group by cube(a, b)
```

> `GROUP BY CUBE(A,B), CUBE(B,C)` retourne combien de group?

 16 = (2^2) * (2^2)

```sql
((AB), A), (B), ()) x ((BC), (C), ())
(ABC), (AB), (ABC), (AB), (ABC), (AB), (AC), (A)
(BC), (B), (BC), (B), (BC), (B), (C), ()
```

## Rollup

```sql
SELECT A, B, C, sum(D) as n FROM R
GROUP BY ROLLUP (A, B, C);
```

retourne 13 n-uplets:
|A|B|C|N|
|-|-|-|-|
|x|y|1|2|
|x|y||2|
|x|z|2|3|
|x|z||3|
|x|||5|
|y|y|1|9|
|y|y|2|2|
|y|y||11|
|y|||11|
|z|y|4|1|
|z|y||1|
|z|||1|
||||17|

## Rankings

> Liste des 5 produits les plus vendus en France. On affiche le nom du produit, la quantité vendue et top 5 produits plus vendus

```sql
with T as (
    Select p.nom, sum(v.quantité), rank over (order by sum(v.quantite) desc as rang )
    From Ventes v, Prod p, Magasin m, Zone z
    Where v.prod = p.prod and v.magasin = m.magasin
        And m.zone = z.zone and z.pays =’France’
    Group by p.nom)
Select * from T where rang <=5;
```

> Pour chacun des 10 premiers jours du mois de mai 2015, donnez la quantité moyenne de produits de catégorie alimentaire vendus les 7 jours précédents.

```sql
Select 
    num_date, 
    avg(v.quantite) over 
        (order by v.num_date 
        range between interval ‘7’ Day 
        preceding and current row) as qte-moyenne
From Vente v, Prod p, Classe c
Where 
    v .prod = p. prod and p.Classe = c.classe
    and c.categorie = ‘alimentaire’
    and v.num_date > ‘01-05-2015’ 
    and v.num_date < ’10-05-2015’
Order by v.num_date desc;
```

> Pour chaque client, donner le montant de ses achats par trimestre en 1993.

```sql
Select v.client, d.trimestre, sum(v.prix * v.quantite) over (partition by c.client,
order by sum(v.prix*v.quantite) as montant_par_trimestre
From Ventes v, Date d
Where v.num_date =d.num_date and d.annee = 1993
Group by v.client, d.trimestre
Order by v.client, montant_par_trimestre;
```

> On veut calculer le montant total des ventes selon les 3 dimensions suivantes : date, produit, magasin. On veut afficher la famille du produit et la superficie du magasin, et on veut pouvoir naviguer au travers des différents niveaux de date (année, trimestre, mois, jour).

```sql
Select c.famille, m.superficie, d.annee, d.trimestre, d.num_mois, d.num_jour, sum
(v.prix * v.quantite) as montant-total
From Vente v, prod p, classe c, date d, magasin m
Where v.prod=p.prod
And p.classe = c.classe
And v.num_date = d.num_date
And v.magasin = m.magasin
Group by c.famille, m.superficie, rollup(d.annee, d.trimestre, d.num_mois,
d.num_jour)
```

# Scala

## Chains

### ExoZero

```scala
scala> R // liste des tuples  (A, B, C)
scala> S // liste des tuples (C, D, E)

// calculer R join S on R.C=S.C
scala> R.map{case(a,b,c)=>(c, (a,b))}.join(S.map{case(c,d,e)=>(c,(d,e))}) 
//porduit une RDD de la forme (c, ((a,b), (d,e)))

x(0) // acces a l'element 0 de l'array x 
x._1 // acces a l'element 1 du tuple x
```

### Ex1

![partiel2016](./images/partiel.png)

```scala
scala> val triples = sc.textFile("...").map(x=>x.split(",")).map(x=>(x(0),x(1),x(2)))
scala> val p0 = triples.filter{case (s,p,o) => p.contains("p0")}
scala> val p1 = triples.filter{case (s,p,o) => p.contains("p1")}
scala> val p2 = triples.filter{case (s,p,o) => p.contains("p2")}
scala> val p3 = triples.filter{case (s,p,o) => p.contains("p3")}

> (q1) select ?x ?z where { ?x p0 ?y . ?y p1 ?w. ?w p2 ?z}
>>> x -> y
>>> y -> w
>>> w -> z

val q1 = p0.
    map{ case(x, p0, y)=>(y,x) }.
    join(p1.map{ case(y, p1, w) => (y,w) }). // J'ai (y, (x, w))
    map{ case(y,(x,w)) => (w, (x, y)) }.     // J'ai (w, (x, y))
    join(p2.map{ case(w, p, z) => (w, z) }). // J'ai (w, (x, y), z)
    map{ case(w, (x, y), z) => (x, z) }


> (q2) select ?x ?z where { ?x p2 ?z.  ?y p1 ?x. ?x p3 ?w}
>>> x -> z
>>> y -> x
>>> x -> w


val q2 = p2.
    map{ case(x, p1, z) => (x, z) }.
    join(p1.map{ case(y, p1, x) => (x, y) } ). // J'ai (x, (z, y)).
    join(p3.map{ case(x, p3, w) =>(x, w) }).   // J'ai (x, (z, y), w)
    map( case (x, (z,y), w) => (x, z))
```

### Ex2

```scala
select ?p ?s
where {?p studiedAt ?u. ?p supervisedBy ?s. ?s studiedAt ?u}

val q2 = studiedAt.
    map{case(p,sat,u)=>(p,u)}.
    join(supervisedBy.map{case(p,sby,s)=>(p,s)}).
    map{case(p,(u,s))=>(s,(p,u))}.
    join(studiedAt.map{case(ps,sat,us)=>(ps,us)}).
    map{case(s,((p,u),us))=>(p,u,s,us)}.
    filter{case(p,u,s,us)=>u==us}.map{case(p,u,s,us)=>(p,s)}
```

### Ex3

```scala
select ?p ?s
where ?p studiedAt ?u. ?p supervisedBy ?s. ?s studiedAt ?u

val q2 = studiedAt.
    map{case(p,sat,u)=>(p,u)}.
    join(supervisedBy.map{case(p,sby,s)=>(p,s)}).
    map{case(p,(u,s))=>(s,(p,u))}.
    join(studiedAt.map{case(ps,sat,us)=>(ps,us)}).
    map{case(s,((p,u),us))=>(p,u,s,us)}.
    filter{case(p,u,s,us)=>u==us}.map{case(p,u,s,us)=>(p,s)}
```
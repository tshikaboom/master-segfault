# [Sujet](http://www-bd.lip6.fr/wiki/_media/site/enseignement/master/bdle/examen2016.pdf)

## Helps

### Joins

```scala
scala> R // liste des tuples  (A, B, C)
scala> S // liste des tuples (C, D, E)

// calculer R join S on R.C=S.C
scala> R.map{case(a,b,c)=>(c, (a,b))}.join(S.map{case(c,d,e)=>(c,(d,e))}) 
//porduit une RDD de la forme (c, ((a,b), (d,e)))

x(0) // acces a l'element 0 de l'array x 
x._1 // acces a l'element 1 du tuple x
```

## Exprimer dans l'algebre RDD

![partiel](./images/partiel.png)

```scala
scala> val triples = sc.textFile("...").map(x=>x.split(",")).map(x=>(x(0),x(1),x(2)))
scala> val p0 = triples.filter{case (s,p,o) => p.contains("p0")}
scala> val p1 = triples.filter{case (s,p,o) => p.contains("p1")}
scala> val p2 = triples.filter{case (s,p,o) => p.contains("p2")}
scala> val p3 = triples.filter{case (s,p,o) => p.contains("p3")}

> (q1) select ?x ?z where { ?x p0 ?y . ?y p1 ?w. ?w p2 ?z}

// Links p0 with p1
val tq1 = p0.map{ case (x, p0, y) => (y, (x, p)) }.join(p1.map{ case (y, p, w)=>(y, (w, p))})
// ^ produit une RDD de la forme (y, (x, p0), (w, p1))

val tq2 = p1.map{ case(y, p, w) => (w, (y, p))}.join(p2.map{ case (w, p, z) => (w, (z, p))})
// ^ produit une RDD de la forme (w, (y, p1), (z, p2))

val tq2_alt = tq1.map{(y, (x, p0), (w, p1)) => (w, (x, p0, y, p1)}.join( p2.map{_ => _} )

val q1 = 
// ^ produit une RDD de la forme

> (q2) select ?x ?z where { ?x p2 ?z.  ?y p1 ?x. ?x p3 ?w}

val q2 = 

```

## Chains

### Ex1

```sql
select ?p ?s
where {?p studiedAt ?u. ?p supervisedBy ?s. ?s studiedAt ?u}
```

gives:

```scala
val q2 = studiedAt.
    map{case(p,sat,u)=>(p,u)}.
    join(supervisedBy.map{case(p,sby,s)=>(p,s)}).
    map{case(p,(u,s))=>(s,(p,u))}.
    join(studiedAt.map{case(ps,sat,us)=>(ps,us)}).
    map{case(s,((p,u),us))=>(p,u,s,us)}.
    filter{case(p,u,s,us)=>u==us}.map{case(p,u,s,us)=>(p,s)}
```

### Ex2

```sql
select ?p ?s
where ?p studiedAt ?u. ?p supervisedBy ?s. ?s studiedAt ?u
```

gives

```scala
val q2 = studiedAt.
    map{case(p,sat,u)=>(p,u)}.
    join(supervisedBy.map{case(p,sby,s)=>(p,s)}).
    map{case(p,(u,s))=>(s,(p,u))}.
    join(studiedAt.map{case(ps,sat,us)=>(ps,us)}).
    map{case(s,((p,u),us))=>(p,u,s,us)}.
    filter{case(p,u,s,us)=>u==us}.map{case(p,u,s,us)=>(p,s)}
```
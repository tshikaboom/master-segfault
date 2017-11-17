## Ressources

- [Algebre Spark ou Dataset (Nov16)](http://www-bd.lip6.fr/wiki/_media/site/enseignement/master/bdle/examen2016.pdf)
- [Spark - KV Store (Fev15)](http://www-bd.lip6.fr/wiki/_media/site/enseignement/master/bdle/bdle_exam_fevrier2015.pdf)
- [Spark (Fev15)](http://www-bd.lip6.fr/wiki/_media/site/enseignement/master/bdle/examen_2015_aspark.pdf)

## Stuff

### Joins



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
# SQL

## Definitions

- `OLTP` - Online Transaction Processing
    - Bien adaptes au stockage
    - Mal adaptes a la gestion d'info
- `TPC-H` - Transaction Processing Performance Council TPC-H is a Decision Support Benchmark

## Aggegate

- Multidimentional model
    - `mesure` critere d'evaluation du processus decisionnel (chiffre d'affaires, quantite en stock)
    - `dimension` sujet d'ineteret (temps, produit, localisation)
    - `hierarchie` decomposition d'une dimension en une arboresence de niveaux (temps en mois, trimestre, annee)
    - `attribut` caracteristique d'un niveau d'une hierarchie (prix d'un article)
    - `agregat` resultat d'un indicateur par rapport a des niveaux (chiffre d'affaires du mois)

## Aggregation SQL

### `Group by`

Grouping `n` dimentions

```sql
Group By d1, d2, ..., dn
```

Un groupe contient tous les faits qui ont les memes valeurs pour `(d1, ..., dn)`. Les groupes sont disjoints.  
Cause `SELECT` fonction d'agregation:

- `sum`
- `avg`
- `count`
- `min`
- `max`

Resultat: un n-uplet par groupe

Example:

```sql
Sales(StoreID, ItemID, CustID, Qty, Price)

select StoreID, ItemID, CustID, SUM(PRICE)
from Sales
Group by StoreID, ItemID, CustID
```

### `rollup` and `cube`

`ROLLUP (YEAR, MONTH, DAY)`

With a `ROLLUP`, it will have the following outputs:

```sql
YEAR, MONTH, DAY
YEAR, MONTH
YEAR
()
```

With `CUBE`, it will have the following:

```sql
YEAR, MONTH, DAY
YEAR, MONTH
YEAR, DAY
YEAR
MONTH, DAY
MONTH
DAY
()
```

`CUBE` essentially contains every possible `rollup` scenario for each node whereas `ROLLUP` will keep the hierarchy intact (__so it won't skip MONTH and show YEAR/DAY, whereas `CUBE` will__)

### `Rollup`

#### Definition

Syntax: `group b [D] rollup (D').  
`D` et `D'` sont des listes de dimensions `d1...dn`

Aggregation sur `n+1` niveau de regroupements:

- `N1` : group by `d1 .. d(n-1) dn`
- `N2` : group by `d1 .. d(n-1)`
- ...
- `Nn` : group by `d1`
- `Nn+1` : un seul groupe = la table des faits toute entiere

###### `Rollup` partiel

Moins de niveaux

```sql
group by D rollup(D')`
```

Ex: `group by e1 rollup (e2, e3)`, cree les sous-totaux `(e1, e2, e3), (e1, e2), (e1)`

Le `rollup` est utile lorsque les Di sont les niveaux d'une meme hierarchie `rollup(day, month, year)`.

#### Example1

```sql
Select StoreID, ItemID, CustID, sum(Price)
from Sales 
group by rollup(StoreId, ItemID, CustID)
```

outputs:

```text
[s1, i3, c1, 2] [s1, i1, c3, 1] ...
[s1, i1, null, 100] [s1, i2, null, 250] ... // Total par magasin par article
[s1, null, null, 4000] ... // Total par magasin
[nul, null, null, 1000000] // Total general
```

#### Example2

```sql
select regionID, storeID, clerkID, AVG(hourlyPay)
from Sales
group by regionID, rollup(storeID, clerkID)
```

Calcule les agregations au niveau `RegionID`, au niveau `regionID, storeID` et au niveau `RegionID, StoreID, ClerkID`. Pas de total sur l'ensemble.

#### Example3

![rollup](./images/sql-rollup.png)

### `Cube`

#### Definition

Syntax: `group by [D] cube(D')`  
`D` et `D'` sont des listes de dimensions `d1 ... dn`  
Agregation sur tous les niveaux de regroupements par face, arrete, sommet du cube __`(2^n groupes)`__

### `Grouping`

Grouping est une fonction qui renvoie `1` s'il y a un `NULL` cree par `rollup` ou `cube`, `0` sinon.

#### Example1

```sql
EX:  R(A, B, C, D) 
SELECT A, B, SUM(D) as Total, GROUPING(A) as A1, GROUPING(B) as B1
FROM   R
WHERE ...
GROUP BY ROLLUP(A, B)
```

![grouping](./images/sql-grouping.png)

### `Grouping sets`

Permet de definir l'ensemble de groupes sur lesquels on veut calculer des agregations. Evide de calculer tout le cube. Se definit dans la clause `group by`, est l'union de plusieurs `group by`.

```sql
SELECT A, B, C, SUM(D) 
FROM  R
WHERE...
GROUP BY GROUPING SETS ((A,B), (A,C), ()) 

/* Calcule les sous-totaux pour les groupes (A,B), (A,C), et le total global */

CUBE(a,b,c) est équivalent à
GROUPING SETS ((a,b,c), (a,b), (a,c), (b,c), (a), (b), (c) , () )

/* 
Equivalences 
*/

GROUP BY GROUPING SETS (a, b, c) est équivalent à
GROUP BY a UNION ALL
GROUP BY b UNION ALL
GROUP BY c
GROUP BY GROUPING SETS (a, ROLLUP(b,c))

/* est équivalent à */

GROUP BY a UNION ALL
GROUP BY ROLLUP (b, c
```

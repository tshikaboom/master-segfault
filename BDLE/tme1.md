- [Requêtes décisionnelles avec TPC-H](#requ%C3%AAtes-d%C3%A9cisionnelles-avec-tpc-h)
    - [R1 Résumé des prix](#r1-r%C3%A9sum%C3%A9-des-prix)
    - [R2:Fournisseur proposant les meilleurs prix](#r2fournisseur-proposant-les-meilleurs-prix)
    - [R3: Commandes à expédier en priorité](#r3-commandes-%C3%A0-exp%C3%A9dier-en-priorit%C3%A9)
- [Requêtes analytiques](#requ%C3%AAtes-analytiques)
    - [A1: Le top 10 des clients ayant dépensé le plus (a1.sql ou a1.txt).](#a1-le-top-10-des-clients-ayant-d%C3%A9pens%C3%A9-le-plus-a1sql-ou-a1txt)
        - [Afficher la liste des clients avec le montant total de leurs commandes. Pour chaque client, donner son n° et le montant total de ses commandes. Trier le résultat par ordre décroissant du montant.](#afficher-la-liste-des-clients-avec-le-montant-total-de-leurs-commandes-pour-chaque-client-donner-son-n%C2%B0-et-le-montant-total-de-ses-commandes-trier-le-r%C3%A9sultat-par-ordre-d%C3%A9croissant-du-montant)
        - [Afficher seulement les 10 premiers clients.](#afficher-seulement-les-10-premiers-clients)
        - [Compléter la requête précédente pour afficher le rang de chaque client. Voir a1-res.](#compl%C3%A9ter-la-requ%C3%AAte-pr%C3%A9c%C3%A9dente-pour-afficher-le-rang-de-chaque-client-voir-a1-res)
    - [A3: Le top 5 des pays avec le plus grand nombre de clients](#a3-le-top-5-des-pays-avec-le-plus-grand-nombre-de-clients)
        - [Afficher la liste des pays, référencés dans Nation, avec leur nombre de clients. Donner le n° du pays, son nom et le nb de clients. Classer le résultat par ordre décroissant du nombre de clients. Y a-t-il des pays ex-aequo ?](#afficher-la-liste-des-pays-r%C3%A9f%C3%A9renc%C3%A9s-dans-nation-avec-leur-nombre-de-clients-donner-le-n%C2%B0-du-pays-son-nom-et-le-nb-de-clients-classer-le-r%C3%A9sultat-par-ordre-d%C3%A9croissant-du-nombre-de-clients-y-a-t-il-des-pays-ex-aequo)
        - [Afficher seulement les tuples du résultat dont le rang est inférieur ou égal à 5. Est-ce toujours exactement les 5 premiers tuples du résultat ? Quelle est la différence entre les fonctions rank() et dense_rank() ? Voir a3-res.](#afficher-seulement-les-tuples-du-r%C3%A9sultat-dont-le-rang-est-inf%C3%A9rieur-ou-%C3%A9gal-%C3%A0-5-est-ce-toujours-exactement-les-5-premiers-tuples-du-r%C3%A9sultat-quelle-est-la-diff%C3%A9rence-entre-les-fonctions-rank-et-denserank-voir-a3-res)
        - [Expliquer brièvement comment traiter cette requête sans utiliser les fonctions analytiques prédéfinies du SGBD.](#expliquer-bri%C3%A8vement-comment-traiter-cette-requ%C3%AAte-sans-utiliser-les-fonctions-analytiques-pr%C3%A9d%C3%A9finies-du-sgbd)
    - [A4: Le top 20% des pays avec le plus grand nombre de clients.](#a4-le-top-20-des-pays-avec-le-plus-grand-nombre-de-clients)
        - [Afficher seulement les pays (référencés dans Nation) classés parmi les 20% meilleurs. Le résultat a4-res est-il correct? Justifier.](#afficher-seulement-les-pays-r%C3%A9f%C3%A9renc%C3%A9s-dans-nation-class%C3%A9s-parmi-les-20-meilleurs-le-r%C3%A9sultat-a4-res-est-il-correct-justifier)
        - [Pourquoi le dénominateur est-il `(N - 1)` au lieu de `N`?](#pourquoi-le-d%C3%A9nominateur-est-il-n---1-au-lieu-de-n)
    - [A5: Classement national des produits vendus en plus grande quantité.](#a5-classement-national-des-produits-vendus-en-plus-grande-quantit%C3%A9)
        - [Pour chaque pays (référencé dans Nation), donner le classement national des produits les plus achetés par des clients de ce pays](#pour-chaque-pays-r%C3%A9f%C3%A9renc%C3%A9-dans-nation-donner-le-classement-national-des-produits-les-plus-achet%C3%A9s-par-des-clients-de-ce-pays)
    - [A6: Fenêtre temporelle glissante](#a6-fen%C3%AAtre-temporelle-glissante)
        - [a1) (facultatif) Pour chaque mois, quel est le prix moyen des commandes du dernier trimestre ?](#a1-facultatif-pour-chaque-mois-quel-est-le-prix-moyen-des-commandes-du-dernier-trimestre)
        - [a2) Pour chaque jour, donner le prix moyen des commandes effectuées les 90 jours précédents. La moyenne est calculée sur l'ensemble des commandes enregistrées pendant les 90 jours précédents](#a2-pour-chaque-jour-donner-le-prix-moyen-des-commandes-effectu%C3%A9es-les-90-jours-pr%C3%A9c%C3%A9dents-la-moyenne-est-calcul%C3%A9e-sur-lensemble-des-commandes-enregistr%C3%A9es-pendant-les-90-jours-pr%C3%A9c%C3%A9dents)
        - [b) Pour chaque jour, quel est le chiffre d'affaire des 30 derniers jours ?](#b-pour-chaque-jour-quel-est-le-chiffre-daffaire-des-30-derniers-jours)
- [Cube](#cube)
    - [Rollup](#rollup)
    - [Question 1 : Opérations algébriques](#question-1-op%C3%A9rations-alg%C3%A9briques)
        - [a) Quelles sont, d'après le cours, les opérations à appliquer sur C1 pour obtenir C2 ? Donner l'ordre dans lequel les opérations sont appliquées](#a-quelles-sont-dapr%C3%A8s-le-cours-les-op%C3%A9rations-%C3%A0-appliquer-sur-c1-pour-obtenir-c2-donner-lordre-dans-lequel-les-op%C3%A9rations-sont-appliqu%C3%A9es)
        - [b) Donner la requête SQL calculant toute les cellules de C2 (264 cellules)](#b-donner-la-requ%C3%AAte-sql-calculant-toute-les-cellules-de-c2-264-cellules)
    - [Question 2 : Agrégation sur les niveaux d'une dimension](#question-2-agr%C3%A9gation-sur-les-niveaux-dune-dimension)
        - [a) T2R: Ecrire T1 en utilisant le mot-clé ROLLUP. Voir le résultat de T2R](#a-t2r-ecrire-t1-en-utilisant-le-mot-cl%C3%A9-rollup-voir-le-r%C3%A9sultat-de-t2r)
        - [b) T2U: Ecrire T1 sans utiliser rollup mais UNION](#b-t2u-ecrire-t1-sans-utiliser-rollup-mais-union)

# Requêtes décisionnelles avec TPC-H

## R1 Résumé des prix
Quels sont les articles commandés, livrés avant la date d (incluse), regroupés selon leur état d'expédition et de réception ? Le résultat de la requête contient le nombre d'articles, le prix total HT (avant et après la réduction) et TTC, la quantité moyenne, le prix moyen, la réduction moyenne et le nombre le lignes de commandes.
```sql
select
  L_returnflag,
  L_linestatus,
  sum(L_quantity) as nb_article,
  count(*) as nb_cmd,
  sum(L_extendedprice) as TotalPrixTTC,
  sum(L_extendedprice/(1+L_tax)) as TotalPrixHT,
  avg(L_extendedprice) as PrixMoyen,
  avg(L_quantity) as QuantitéMoyenne
from Lineitem
where L_receiptdate <= '01-01-1993'
group by L_returnflag, L_linestatus;
```

## R2:Fournisseur proposant les meilleurs prix
Soient une région R, et les produits de taille S et de type T. Quels sont les fournisseurs de la région R proposant les meilleurs prix pour les produits de type T et de taille S ? Trier le résultat selon le solde du fournisseur (ordre décroissant). Donner le solde et le nom du fournisseur, le nom de son pays, le numéro et la marque du produit, l'adresse et le n° de téléphone du fournisseur.
```sql
select min(ps_supplycost)
from
  supplier s,
  nation n,
  partsupp ps,
  part p,
  region r
where
  r_name='EUROPE'
  and p_type like '%COPPER'
  and p_size=26
  and s_nationkey = n_nationkey
  and ps_suppkey=s_suppkey
  and p_partkey=ps_partkey;
```

## R3: Commandes à expédier en priorité
Pour un type de marché S, quelles sont les commandes, non encore expédiées à la date d, les plus importantes en terme de montant restant à encaisser (i.e., la somme du prix HT, après réduction, des articles non expédiés de la commande) ? Trier le résultat par ordre décroissant de prix.
```sql
select o_totalprice
from lineitem, orders
where l_linestatus='0'
  and o_oderdate <= '01-01-1993'
  and l_orderkey=o_orderkey
order by o_totalprice;
```

# Requêtes analytiques

## A1: Le top 10 des clients ayant dépensé le plus (a1.sql ou a1.txt).
### Afficher la liste des clients avec le montant total de leurs commandes. Pour chaque client, donner son n° et le montant total de ses commandes. Trier le résultat par ordre décroissant du montant.
```sql
select
  c_custkey,
  sum(o_totalprice) as montant_total,
  rank() over (order by sum(o_totalprice) DESC) as rang
from Customer, Orders
where c_custkey = o_custkey
group by c_custkey;
```


### Afficher seulement les 10 premiers clients.
Ajouter une contition de selection sur l'attribut implicite nomme `rownum`:
`with T as (req 1) select * from T where rownum <= N`
```sql
with Req1 as (
  select
    c_custkey,
    sum(o_totalprice) as montant_total,
    rank() over (order by sum(o_totalprice) DESC) as rang
  from Customer, Orders
  where c_custkey = o_custkey
  group by c_custkey
  )
select *
from Req1
where rownum <=10
```

### Compléter la requête précédente pour afficher le rang de chaque client. Voir a1-res.
```sql
--a1-res
 C_CUSTKEY MONTANT_TOTAL       RANG
---------- ------------- ----------
      1332	 3964495	  1
       235    3646402,37	  2
       224     3489742,9	  3
       945	 3425793	  4
       688    3418466,09	  5
	 5    3300749,81	  6
       359    3300696,04	  7
      1383    3296214,23	  8
       589    3266721,84	  9
       615     3257380,5	 10
```

## A3: Le top 5 des pays avec le plus grand nombre de clients
Formatting columns : `column name format A20`
### Afficher la liste des pays, référencés dans Nation, avec leur nombre de clients. Donner le n° du pays, son nom et le nb de clients. Classer le résultat par ordre décroissant du nombre de clients. Y a-t-il des pays ex-aequo ?
```sql
with r2 as (
  select
    n_nationkey as cle,
    n_name,
    count(c_nationkey) as nb_client
    dense_rank() over( order by count(c_nationkey) DESC) as rang
  from
    Nation, Customer
  where c_nationkey = n_nationkey
  group by n_nationkey, n_name
)
select *
from r2
where rownum <= 10
```

### Afficher seulement les tuples du résultat dont le rang est inférieur ou égal à 5. Est-ce toujours exactement les 5 premiers tuples du résultat ? Quelle est la différence entre les fonctions rank() et dense_rank() ? Voir a3-res.
`dense_rank` valeurs en continue - 1, 1, 1, 2, 2, 2, 3, 3, 3 etc
`rank` valeurs skip - 1, 1, 3, 3, 3, 6 etc

```sql
A3:

  TOP 5 : les pays de rang <= à 5


N_NATIONKEY N_NAME     NB_DE_CLIENTS	   RANG
----------- ---------- ------------- ----------
	 10 IRAN		  72	      1
	 15 MOROCCO		  72	      1
	  3 CANADA		  69	      3
	  2 BRAZIL		  68	      4
	 12 JAPAN		  67	      5
	 20 SAUDI ARAB		  67	      5
```

### Expliquer brièvement comment traiter cette requête sans utiliser les fonctions analytiques prédéfinies du SGBD.
Au lieu de faire un `rang` on fait un `order by`


## A4: Le top 20% des pays avec le plus grand nombre de clients.

### Afficher seulement les pays (référencés dans Nation) classés parmi les 20% meilleurs. Le résultat a4-res est-il correct? Justifier.

Fixer le format d'affichage du rang avec la commande:
  `column rang_pourcent format 9.99`
La formule calculant le rang relatif (entre 0 et 1 inclus) du tuple t parmi N tuples du résultat est :
`rang_relatif(t) = (rang(t) - 1) / ( N - 1)`

```sql
with r3 as (
  select
    n_nationkey as cle,
    n_name as name,
    count(c_nationkey) as nb_client,
    percent_rank() over(order by count(c_natiokey) desc) as rang_pourcent
  from
    Nation,
    Customer
  where
    c_nationkey = n_nationkey
  group by
    n_nationkey, n_name
)
select *
from r3
where rang_pourcent <= 0.2
```

```sql
--- a4
A4: top 20% des pays

Les pays avec le plus grand nombre de clients.
Afficher seulement les pays classés parmi les 20% meilleurs.

N_NATIONKEY N_NAME     NB_DE_CLIENTS RANG_POURCENT
----------- ---------- ------------- -------------
	 10 IRAN		  72	       .00
	 15 MOROCCO		  72	       .00
	  3 CANADA		  69	       .08
	  2 BRAZIL		  68	       .13
	 12 JAPAN		  67	       .17
	 20 SAUDI ARAB		  67	       .17
```

### Pourquoi le dénominateur est-il `(N - 1)` au lieu de `N`?

Les index commencent a 0?

## A5: Classement national des produits vendus en plus grande quantité.

### Pour chaque pays (référencé dans Nation), donner le classement national des produits les plus achetés par des clients de ce pays

Pour réduire le résultat, on ne veut afficher que les produits dont la quantité achetée (pour un pays et un produit) est supérieure à 150. Afficher les attributs pays, produit, quantité_achetée et rang.

```sql
with r5 as (
  select
    p_name as produit,
    n_name as nation,
    sum(l_quantity) as quantite
    rank() over(partition by n_name order by sum(l_quantity) DESC) as rang
  from
    Nation,
    Lineitem,
    Part,
    Customer,
    Orders
  where
    l_partkey = p_partkey
    AND c_nationkey = n_nationkey
    AND c_custkey = o_custkey
    AND l_orderkey = o_orderkey
  group by n_name, p_name
)
select *
from r5
where quant > 180 and rownum <= 10;
```

## A6: Fenêtre temporelle glissante

- __Répondre aux questions a2) au lieu de a1), puis b)__

### a1) (facultatif) Pour chaque mois, quel est le prix moyen des commandes du dernier trimestre ?

### a2) Pour chaque jour, donner le prix moyen des commandes effectuées les 90 jours précédents. La moyenne est calculée sur l'ensemble des commandes enregistrées pendant les 90 jours précédents

```sql
with R5 as(
  select
    o_orderdate as dates,
    avg(sum(o_totalprice)) over (order by o_orderdate range between interval '90' day preceding and current row) as moyenne
  from
    Orders
  where
    o_orderdate>'01-04-1998'
  group by o_orderdate)
select *
from R5
where rownum <= 62
```

### b) Pour chaque jour, quel est le chiffre d'affaire des 30 derniers jours ?

```sql
with R6 as (
  select
    o_orderdate as dates,
    sum(o_totalprice) as total,
    sum(sum(o_totalprice)) over (order by o_orderdate range between interval '30' day preceding and current row) as ChiffA
  from
    Orders
  where
    o_orderdate >= '01-06-1998'
  group by o_orderdate)
select *
from R6
where rownum <= 5
```

# Cube

```sql
C1:
Extrait de C1

 P_PARTKEY  O_CUSTKEY JOUR	      TOTAL
---------- ---------- ---------- ----------
    1	   62 23/09/1995   52351,65
    1	  196 24/06/1995   32454,76
    1	  199 11/12/1994   80146,84
    1	  379 11/02/1994      27254
    1	  469 03/01/1993   15303,44
    1	  470 04/04/1994   53071,69
    1	  540 09/10/1994   47523,06
    1	  587 25/12/1997   30942,36
    1	  705 23/04/1994   47172,27
    1	  749 08/06/1993    26018,4
    1	  764 16/09/1992   46786,36
    1	  849 17/07/1992   33168,87
    1	  886 03/06/1994   48460,88
    1	  907 11/02/1993   91130,65
    1	  979 07/01/1993   90582,75
    1	 1071 16/01/1992   28477,45
    1	 1105 14/01/1993   54543,68
    1	 1380 21/07/1998   11386,41
    2	   90 10/09/1992    9940,44
    2	  100 14/03/1994   57691,44
    2	  105 25/07/1995   51337,84
    2	  131 16/11/1997   36104,31
    2	  205 13/01/1996   69651,05
    2	  222 08/06/1998    35797,4
    2	  294 24/01/1992   56502,18
    2	  299 04/09/1997   46787,97
    2	  379 19/02/1998   28363,35
    2	  400 02/10/1997    7827,45
    2	  401 13/11/1993   47675,04
    2	  418 12/06/1992   62110,05
    2	  472 01/07/1998    39872,2
    2	  572 01/08/1998   76060,98
    2	  575 13/07/1993   80029,42
    2	  582 20/07/1993   23908,14
    2	  672 26/05/1994    3635,56
    2	  674 11/05/1996   71704,86
    2	  739 21/03/1992   55823,46
    2	  748 28/11/1997    31517,2
    2	  759 24/07/1995   29035,84
    2	  761 05/12/1994   11683,98
    2	  870 15/07/1996    37025,7
    2	  918 21/10/1995   27633,22
    2	  967 15/08/1996   30427,79

```

## Rollup 

`rollup(a, b, c)` 3 niveaux de la meme dimention. `a` niveau le plus haut, `c` niveau le plus bas.

`cube(a, b, c)` -> 3 dimentions differentes

## Question 1 : Opérations algébriques

On considère le cube C2 à deux dimensions obtenu à partir de C1 tel que :

On s'intéresse seulement aux ventes de produits en cuivre (dont le type se termine par COPPER : like '%COPPER') effectuées après le 1er juin 1998.
On considère seulement le niveau type de la dimensions produit et le niveau nom de la dimension client.
Les valeurs des cellules de C2 représentent la somme des ventes quelle que soit la date.

### a) Quelles sont, d'après le cours, les opérations à appliquer sur C1 pour obtenir C2 ? Donner l'ordre dans lequel les opérations sont appliquées

```sql
- 0: initial
    ^
    | type produit
    |
    |
    x-----------> nom client
   /
  / jour
 /
<


- 1: `slice` sur la date, jour > 01-06-1998

      ^
      | type produit
      |
      |
      x-----------> nom client
     /
-----------
   / jour
-----------
 /
<

- 2: remonter dans la dimention produit -> `rollup` de nom de produit a type de produit

- 3: `slice` sur la dimention produit type de produit `like '%COPPER'`

       ^
     -----------------
       | type produit
     ----------------
       |
       |
       x-----------> nom client
     /
-----------
   / jour
-----------
 /
<

- 4: enlever une dimention: projection aggregative sur produit, client
```

### b) Donner la requête SQL calculant toute les cellules de C2 (264 cellules)

Voir un extrait de C2:

```sql
Extrait de C2

P_PARTKEY  O_CUSTKEY	   TOTAL
---------- ---------- ----------
  1	 1380	11386,41
  7	 1019	   30911
  28	 1481	79794,82
  29	 1383	 7932,12
  36	 1068	16760,25
  54	  193	 18167,5
  60	 1274	 18387,6
  60	 1473	 41349,7
  77	  884	 37984,8
  77	 1346	   46612
  110	  656	40960,76
  138	  301	17255,15
  145	  614	 5555,58
  145	 1425	 43936,2
  160	   94	62924,91
  162	  416	 68867,5
  162	  973	36697,65
  168	  571	 21905,4
  168	  862	55356,07
  170	  945	28861,92
  171	  205	 33238,8
  171	 1007	52035,84
  192	 1097	11911,34
  197	 1161	62996,34
  197	 1392	   26901
  ```

## Question 2 : Agrégation sur les niveaux d'une dimension

On veut calculer, en une seule requête T2, tous les cubes obtenus à partir de C2 par agrégation sur la dimension client (ayant 3 niveaux). Le résultat de la requête contient toutes les données des cubes à tous les niveaux d'agrégation sur la dimension client.

### a) T2R: Ecrire T1 en utilisant le mot-clé ROLLUP. Voir le résultat de T2R

### b) T2U: Ecrire T1 sans utiliser rollup mais UNION
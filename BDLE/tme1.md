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
# Requêtes décisionnelles avec TPC-H 

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

### Pour chaque pays (référencé dans Nation), donner le classement national des produits les plus achetés par des clients de ce pays. 
Pour réduire le résultat, on ne veut afficher que les produits dont la quantité achetée (pour un pays et un produit) est supérieure à 150. Afficher les attributs pays, produit, quantité_achetée et rang.

```sql
with r5 as (
  select 
    p_name as produit, 
    n_name as nation, 
    sum(l_quantity) as quantite
  from
    Nation, 
    Lineitem,
    Part
  where 
    
)
select * 
from r5
where 
```


## A6: Fenêtre temporelle glissante

__Répondre aux questions a2) au lieu de a1), puis b)__

### a1) (facultatif) Pour chaque mois, quel est le prix moyen des commandes du dernier trimestre ?
### a2) Pour chaque jour, donner le prix moyen des commandes effectuées les 90 jours précédents. La moyenne est calculée sur l'ensemble des commandes enregistrées pendant les 90 jours précédents.
### b) Pour chaque jour, quel est le chiffre d'affaire des 30 derniers jours ?

# Cube

## Operations

### Structure

- rotate (pivot)
- switch
- split
- nest/unnest
- push/pull
- slice

### Content

- dice (sélection)
- roll-up (grain supérieur)
- drill-down (grain inférieur)

### Multi-cubes

- jointure
- ops ensemblistes (union, intersection, difference)

##

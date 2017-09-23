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

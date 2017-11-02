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


### Group by

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

### Rollup

### Cube
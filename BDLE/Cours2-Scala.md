Native Map-Reduce
    - Hive
    - FB Presto
    - MapR Drill, LinkedIn Tajo

Generation d'un `DAG` Map-Reduce.

# Jointures

## Par partitionnement

- `Map` : etiqueter chaque relation, exposer attributs de jointure comme cle 
- `Reduce` : fusionner les valeurs de la meme cle


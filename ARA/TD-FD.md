- [Q1 Quelles proprietes doit assurer un detecteur de faute `omega`?](#q1-quelles-proprietes-doit-assurer-un-detecteur-de-faute-omega)
    - [GST](#gst)
- [Q2 Completez l'algo avec la tache Task4 afin d'implementer un detecteur Omega. A terme tous les processus doivent elire le meme processus comme leader.](#q2-completez-lalgo-avec-la-tache-task4-afin-dimplementer-un-detecteur-omega-a-terme-tous-les-processus-doivent-elire-le-meme-processus-comme-leader)

On considere un ensemble de processus {p1, p2, ..., pn} communiquant par messages. Les liens de commmunication sont bidirectionnels et fiables. On ne considere que des fautes du type crash. Le reseau forme un graphe complet partiellement synchrone : apres le temps GST (inconnu), il existe des bornes sur les details de transmissions. Il y a au moins un processus correct. 

# Q1 Quelles proprietes doit assurer un detecteur de faute `omega`?

- retourne 1 leader
- a terme 1 seul leader correct

## GST

- Tous les liens sont finalement synchrones
- Apres GST il existe 1 borne sur les delais de tranmission/traitement

// Algo du cours

# Q2 Completez l'algo avec la tache Task4 afin d'implementer un detecteur Omega. A terme tous les processus doivent elire le meme processus comme leader. 

```none
Task4 : Upon invocation of leader()
    return trusted;
```
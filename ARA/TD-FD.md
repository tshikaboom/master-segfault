- [Q1 Quelles proprietes doit assurer un detecteur de faute `omega`?](#q1-quelles-proprietes-doit-assurer-un-detecteur-de-faute-omega)
    - [GST](#gst)
- [Q2 Completez l'algo avec la tache Task4 afin d'implementer un detecteur Omega. A terme tous les processus doivent elire le meme processus comme leader.](#q2-completez-lalgo-avec-la-tache-task4-afin-dimplementer-un-detecteur-omega-a-terme-tous-les-processus-doivent-elire-le-meme-processus-comme-leader)
- [Q3 Supposons qu'aucun processus ne tombe en panne. Quel sera le processus leader? En presence de fautes, quel sera le processus elu?](#q3-supposons-quaucun-processus-ne-tombe-en-panne-quel-sera-le-processus-leader-en-presence-de-fautes-quel-sera-le-processus-elu)
    - [Sans faute](#sans-faute)
    - [Avec fautes](#avec-fautes)
- [Q4 Est-ce que temporairement des processus differents peuvent etre elus? Illustrez votre reponse par un scenario](#q4-est-ce-que-temporairement-des-processus-differents-peuvent-etre-elus-illustrez-votre-reponse-par-un-scenario)
- [Q5 Quel mecanisme assure qu'a terme ces erreurs seront corrigees?](#q5-quel-mecanisme-assure-qua-terme-ces-erreurs-seront-corrigees)
- [Q6 Montrez qu'il existe un `t` tel que pour tout `t'` superieur a `t`, pour tout `pi` appartenant aux corrects, leadeur du `t'` est le `pleader`](#q6-montrez-quil-existe-un-t-tel-que-pour-tout-t-superieur-a-t-pour-tout-pi-appartenant-aux-corrects-leadeur-du-t-est-le-pleader)
- [Q6'](#q6)
- [Q7](#q7)

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

# Q3 Supposons qu'aucun processus ne tombe en panne. Quel sera le processus leader? En presence de fautes, quel sera le processus elu?

## Sans faute

```none
p1 -> p2
p1 -> p3
p1 -> p4
....
p1 -> pn
```

leader = p1 si les messages sont recues a temps

## Avec fautes

- Faute leader -> condition `task2` vrai (on arme les timeout)
    - Changement du leader de tous les processus corrects
- Leader ultime -> processus correct de plus petit id

# Q4 Est-ce que temporairement des processus differents peuvent etre elus? Illustrez votre reponse par un scenario

Temporairement on peut avoir plusieurs leaders suspectés.

# Q5 Quel mecanisme assure qu'a terme ces erreurs seront corrigees?

- Task3 -> Correction erreur
    - Correction leader (`trusted = j`)
- augmentation timeout (pour éviter que l'erreur se reproduise souvent)
- Apres le GST il existe une borne
    - Apres *n* erreurs, *Δ* a atteint la borne inconnue

C'est grace a l'augmentation du timeout qu'on assure un leader a terme.

# Q6 Montrez qu'il existe un `t` tel que pour tout `t'` superieur a `t`, pour tout `pi` appartenant aux corrects, leadeur du `t'` est le `pleader`

On considere que:

- `correct` l'ensemble qui contient les processus corrects
- `pleader` le processus correct elu comme leader
- `leader(t)` invocation de leader a l'instant `t`


Quelques processus tq p*1* p*2* p*k* crashent, p3 survit.
On peut avoir à quelques instants plusieurs (faux) leaders.

Après GST, il existe un instant t auquel les messages de p3 sont reçus à temps.


```none
p1 p2 p3 ... pn
x   x  ^  x   
```
`^` indique le leadeur



Pour tous les corrects, réception du Δ(i, pleader)
- => quelque soit t' > t, tout le monde aura son leader
- => leader(t') = pleader


Il existe un `t >= GST` ,message `pleader`
# Q6'

`<>S`:

- Completude forte 
    - Tous les fautifs sont detectes a terme
- Justesse finalement faible
    - A terme il existe un correct qui n'est pas (faussement) suspecte


A terme correct : `suspected = {pi} - {trusted_i}` <- permet de passer d'`<>S` a `Omega`

**deltaP** == complétude forte, et à terme il n'y a plus d'erreurs

=> à terme il n'existe pas d'intersection entre la liste des corrects et de suspects.
du coup justesse finalement forte

```
Task 2: trusted(i) < i and (did not receive I_AM_THE_LEADER, suspected(j)) during the last delta(i), trusted(i)
	trusted(i) <- trusted(i)+1
	if (trusted(i) == i)
		suspected(i) <- { p1, ... p(i-1) }

Task3: when(received(I_AM_THE_LEADER, suspected(j)) from p(j) and (j<= trusted(j)))
	if (j < trusted(i)) {
		trusted(i) <- j;
		delta(i, j) <- delta(i, j)+1;
	}
Task4: when (trusted(i) = i) and (did not receive I_AM_ALIVE from p(j) during the last delta(i, j) and (j < i)
	suspected(i) <- suspected(i) union {p(j)}
Task5: when (trusted(i) = i) and (received I_AM_ALIVE from p(i)) and (p(j) in suspected(i)
	suspected(i) <- suspected(i) - {p(j)}
	delta(i, j) <- delta(i, j) + 1

```


# Q7

A completer

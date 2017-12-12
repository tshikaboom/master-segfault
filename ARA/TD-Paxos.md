# TD Consensus/Paxos

**Hypothèses**
- Π = { p*1*, *..*, p*n* }
- Système asynchrone. On considère aussi les crash
- Détecteur ⋄S
- `R_broadcast(m)` (reliable broadcast)
- `R_deliver(m)`

## Q1.
> Rappelez les proprietes de la diffusion fiable et du consensus

**Diffusion fiable**
- **Validité.** Si 1 processus correct *p* `R_broadcast(m)`, alors *p* `R_deliver(m)`.
- **Accord.** Si un processus correct `R_deliver(m)`, alors tous les corrects vont `R_deliver(m)`.
- **Intégrité uniforme.** Pour tout message *m*, chaque processus *p* fait **au plus 1 fois** `R_deliver(m)`.

**Consensus**
- **Terminaison.** Tout processus décide.
- **Intégrité.** Tout processus décide **une fois**.
- **Accord.** 2 processus corrects ne peuvent décider différemment.
- **Validité.** Décision sur **une** valeur proposée.

## Q2.
> Proposez un algo simple de diffusion fiable

Remarque1. Canaux insuffisants.

**Diffusion fiable**
```
R_broadcast(m):
	Send m to all

Upon reception of m:
	if m received first time:
		if sender(m) != p:
			send m to all
			R_deliver(m)

```
> \>\>fig1 ARA/td4:consnesus here<<

Dès qu'on reçoit un message, on retransmet.

## Q3.
> Exécutez l'algorithme sans faute en considérant 3 processus.


> \>\>fig2 ARA/td4:consnesus here<<

## Q4.
> Exécutez l'algorithme en considérant 3 processus avec p2 qui tombe en panne.

Si un processus est considéré comme mort, on lui envoie un NACK.

## Q5.
> Quel est l'impact d'une fausse suspicion? Est-il possible que deux coordinateurs
différents diffusent une décision? Illustrez votre réponse par un scénario.

**Cas 1** Coordinateur suspecté par une majorité.

**Cas 2** Une minorité qui suspecte.

## Q6.
> Qu'est-ce qu'un accord uniforme? Démontrez quel type d'accord assure cet algorithme.

Ces propriétés s'appliquent à tous les processus, fautifs ou non. **On enlève le terme "correct"**
- **Terminaison**
- **Intégrité**
- **Accord (uniforme)**: deux processus ne peuvent décider de valeurs différentes.
- **Validité**

**Preuve**. 1 processus *p* décide à une ronde *r*.  
Pour toute ronde *r'* > *r* la même valeur sera décidée.  
Ligne 33: *p* a décidé => *p* a `R_deliver()`  
=> Il y a eu `R_broadcast()` car intégrité uniforme.  
=> Il existe donc un coordinateur qui a collecté une majorité (*(n+1)/2*) de `ack` (ligne 29), la valeur dont il a décidé est *estimate_c*

**Il faut montrer que pour tout *r'* >= *r*, *estimate_c'* = *estimate_c*.**  
Preuve par induction.

1. Pour *r* = *r'*, la propriété est vraie.

2. On dit que la propriété est vérifiée pour *r* <= *r'* < *k*.  
*c_k* = (k mod n) + 1.  
*c_k* propose => *c_k* a reçu (*(n+1)/2*) `ack`.  
=> choisir *estimate_p* tel que *ts_p* (timestamp de p) soit le plus grand  
*ts_p* >= r  
*estimate_p* = *estimate_c_p* par hypothèse, et *estimate_c_p* = *estimate_c*  
**=> *estimate_c_k* = *estimate_c***

## Q7.
> Quelle est l'utilité du message nack?

Si on fait pas de message `nack`, on risque de bloquer tout l'algorithme. Le coordinateur attend de recevoir une majorité de messages (`ack` ou `nack`) avant de décider de quoi que ce soit. On assure donc la propriété de la terminaison.

## Q8.
> On souhaite maintenant démontrer la terminaison de l'algorithme. On considère différents cas:  
> a) un processus correct décide: quelle propriété de la diffusion fiable assure la terminaison?  
> b) En vous appuyant sur la complétude forte du détecteur ⋄Sn montre que les processus ne peuvent rester indéfiniment bloqués dans une ronde.  
> c) En vous appuyent sur la justesse fiable de ⋄S, montrez qu'il existe un temps à partir duquel tous les processus décident.


a) 1 processus décide => il y a eu 1 `R_deliver()`  
=> tous les processus corrects font `R_deliver()`  
^ *implication car accord Reliable Broadcast*

b) **Attente en phase 2.**  
Le coordinateur attend (*(n+1)/2*) estimations.  
(n+1)/2 processus corrects => il y a eu au moins (n+1)/2 envois d'estimation (ligne 10  
=>Les canaux sont fiables, donc le coordinateur n'est pas bloqué.

**Phase 4**. Systématiquement (n+1)/2 processus envoient soit un `ack`, soit un `nack`.  
=> La phase 4 est non bloquante (car canaux fiables).

**Phase 3**. Deux cas.
- **Coordinateur correct**. S'il n'est pas en panne, il va envoyer son estimation. Les canaux étant fiables, l'estimation sera reçue par tous les autres *p* corrects au bout d'un moment. (ligne 16)  
=> pas de blocage.
- **Coordinateur tombe en panne**. (ligne 19) ⋄S assurant la complétude forte, la panne **sera détectée** au bout d'un moment => le coordinateur sera dans *suspectedp*  
=> pas de blocage.

c) **Décision à terme**.  
**Hypothèse**: tous les fautifs sont tombés en panne.

Justesse faible ⋄S => il existe un processus **correct** *q* parmi Π qui ne sera jamais faussement suspecté. (il existe un instant *t* à partir duquel *q* ne sera jamais suspecté)  
=> *q* devient coordinateur à la ronde *r*. *q* étant coordinateur, il va forcément recevoir *(n+1)/2* estimations. À la réception d'une majorité d'estimations, il envoie son *estimate_q* et cet envoi sera forcément reçu par tous les processus corrects.  
=> tous les autres processus renvoient 1 `ack` (pas de `nack`)

## Q9.
> On souhaite maintenant améliorer les performances de cet algorithme en réduisant sa latence (son nombre de phases). Proposez une version qui réduit la phase d'acquittement. Indication: on ne cherche pas à réduire le nombre de messages.

# Consensus probabilisté (Ben-Or 1983).

Algorithme semi-mystique vu l'utilisation du random.. Pourtant on converge vers une valeur.

## Q10.
> Lors d'une ronde *k*, est-il possible qu'un processus propose 0 et l'autre 1?

**Non**. Preuve par contradiction.

Supposons que *p* propose 0 et *q* propose 1.  
*p* a collecté donc (n/2)+1 valeurs égales à 0.  
*q* a collecté donc (n/2)+1 valeurs égales à 1.  
=> Il existe un processus en commun aux deux ayant envoyé 0 à *p* et 1 à *q* à la ronde *k*. **Impossible, CQFD**.

## Q11.
> Si un processus *p* décide *v* à la ronde *k*, montrez que tout processus *q* qui commence à la phase *k+1* positionne sa variable *x_q* à *v* et décide *v* à la fin de la phase *k+1*.

*p* décide *v* à la ronde *k*  
=> *p* a reçu *f+1* valeurs *v*.  
Tout processus (correct) *q* reçoit au moins **une** valeur *v*. (ligne 15)  
=> Il n'existe pas *v'* != *v* (Q10)  
=> à la ronde *k+1* tout le monde va proposer *v*, ils vont donc décider *v*.

## Q12.
> Dans quel cas une valeur aléatoire est choisie? Montrez que cet algorithme termine avce une probabilité de 1.

S'il n'y a pas de majorité sur *v*:  
=> tous vont envoyer "`?`", aucune valeur n'est reçue  
=> tous font un choix aléatoire  
=> Probabilité que tous les processus tirent la même valeur = (1/2) * (1/2) * ... = (1/2)^*n*  
=> Probabilité que la valeur soit différente donc = 1 - (1/2)^*n*

Probabilité(valeur différente après *k* rondes) = (1 - (1/2)^*n*)^*k*

e = lim(n -> +Inf)(1+ (1/n))^n  
(1/e) = lim(n -> +Inf)(1 - 1/n)

On pose k = 2n.  
P(diff_k) ~ 1/e vaguement par limite.

On pose k = c*2^n  
P(diff_k) = 1/(e^c)  
P(même valeur) = 1 - 1/e^c  
Avec c -> +Inf, **P(même valeur) ~ 1**. **CQFD**.
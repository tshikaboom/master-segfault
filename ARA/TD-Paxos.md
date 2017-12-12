# TD Consensus/Paxos

**Hypothèses**
- Π = { p*1*, *..*, p*n* }
- Système asynchrone. On considère aussi les crash
- Détecteur ⋄S
- `R_broadcast(m)` (reliable)
- `R_deliver(m)`

## Q1.
> Rappelez les proprietes de la diffusion fiable et du consensus

- Validité. Si 1 processus correct p R_broadcast(m), alors p R_deliver(m).
- Accord. Si un processus correct R_deliver(m), alors tous les corrects vont R_deliver(m).
- Intégrité uniforme. Pour tout message m, chaque processus p fait au plus 1 fois R_deliver(m).

**Consensus**
- Terminaison. Tout processus décide.
- Intégrité. Tout processus décide **une fois**.
- Accord. 2 processus corrects ne peuvent décider différemment.
- Validité. Décision sur **une** valeur proposée.

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
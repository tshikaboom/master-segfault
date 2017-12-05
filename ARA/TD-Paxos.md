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

## Q5.
> Quel est l'impact d'une fausse suspicion? Est-il possible que deux coordinateurs
différents diffusent une décision? Illustrez votre réponse par un scénario.


## Q6.
> Qu'est-ce qu'un accord uniforme? Démontrez quel type d'accord assure cet algorithme.

## Q7.
> Quelle est l'utilité du message nack?



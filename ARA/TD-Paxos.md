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

Dès qu'on reçoit un message, on retransmet.



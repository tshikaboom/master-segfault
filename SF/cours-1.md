# SF - Cours 1

Rappel des cours de SF:

- Une formule LTL est une formule $\varphi$
- soit la négation d'un formule
- soit une formule $\land$ une autre formule
- Le $X (next)$ d'une autre formule
- le Finaly $(F)$ d'une formule
- le Globaly $(G)$ d'une formule


### Exercice

vérifer que: t,i $\models$ F$\varphi$ ssi t,i $\models$ TU$\varphi$
Définition: Il existe j$\geq$ i, t,i$\models \varphi$
il existe j$\geq$i, t,j$\models\varphi$ et pour tout $i\leq k <j$ t, k $\models \top$

vérifier que:
$G\varphi\models\lnot F(\lnot \varphi)$

### Exo 1
a) G(trainGvoie => F($\lnot$ trainGvoie)) $\lor$ G(trianDvoie=>F($\lnot$ trainDvoie))
A) G(trainDvoie $\lor$ trainGvoie => F($\lnot$trianDvoie $\land$ $\lnot$trianGvoie))

b) GF(trainGvoie $\lor$ trianDvoie)


c) G(trainGvoie $\land$ trainD => ($\lnot$trainDvoieU$\lnot$trianGvoie))

### Exo 4
a)
1) vrai
2) vrai
3) faux
4) vrai
5) vrai


### Exercice

a) S(EXp): {[1:3], 5, 6}
b) S(AXp): {3, 6}
c) S(EFp): {TOUS}
d) S(AFp): {TOUS !{1,4,8}}
e) S(EqUr): {2, 3, 4, 5, 6}
f) S(AqUr): {2, 3, 4, 5, 6}

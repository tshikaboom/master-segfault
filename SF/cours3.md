# Cours 3

### diner philosophes

0: <1 + 2, $\emptyset$, $\emptyset$, 1 + 2>
1: <1, 2, $\emptyset$, 1>
2: <1, $\emptyset$, 2, $\emptyset$>
3: <$\emptyset$, 1 + 2, $\emptyset$, $\emptyset$>
4: <2, 1, $\emptyset$, 2>

Interblocage possible.

**Q4:** pour résoudre le problème, prendre les 2 fourchettes en même temps.

**Q5:**
Prise des 2 fourchettes en même temps
0: <1 + 2, $\emptyset$, 1 + 2>
1: <2, 1, $\emptyset$>
2: <1, 2, $\emptyset$>


```mermaid
graph LR
0((pense))-->|p|1[prendre]
1-->|p,f|2((Mange))
2-->|p,f|3[rendre]
3-->|p|0
3-->|f + f++1|4((Fourchettes))
4-->|f + f++1|1
```

### Problème du train et des segments

```mermaid
graph TB
1((Libre))-->|s++1 + s++2|0[Passer]
0-->|s++1, t|2((Occupé))
2-->|s, t|0
0-->|s + s++2|1
```

Etat du système: Couple <numtrain, num section

0: < <1,1> + <4,2>, <2> + <3> + <5>>
1: < <2,1> + <4,2>, <1> + <3> + <5>>
2: < <2,1> + <5,2>, <2> + <3> + <4>>
3: < <3,1> + <5,2>, <1> + <2> + <4>>
...

**Q2: propriétées**
P1: le nombre de trains restent constants: Si toujours 2 trains dans l'état `occupé` alors toujours 2 trains dans le système. on ne résone pas sur les arcs, mais sur le graph de marquage.
en pertry net:
`query node (card(EtatSysteme) != 2)`

P2: On a jamais 2 fois le même S dans les jetons.
en petry net:
`query node (card(EtatSysteme:(Field[0] == 1)) > 1 ...)`


### Dépliage réseau de petri coloré

```mermaid
graph TB
0((p1_1))-->8[t1_x11_x21]
1((p1_2))-->9[t1_x12_x22]
2((p2_1))-->8
3((p2_2))-->9
4((p3_1))
5((p3_2))
6((p3_3))
7((p3_4))
0-->10[t1_x1_x22]
3-->10
```

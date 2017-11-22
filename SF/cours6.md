# Structure kripke

### Exercice

$M \vDash \Phi$

$\Phi = FGc: \bot$
$\Phi = GFc: \top$
$\Phi = Ga: \bot$
$\Phi = aU(G(b\lor c)): \top$
$\Phi = X\lnot c \to XXc: \top$


### automates de Büchi

l'alphabet $\omega$ composé de ... est acceptant si il contient un nombre infinie de a, car l'automate lit un `a` pour sortir de 2 et aller dans 1, puis il reste dans 1 en lisant infiniement souvent 1.

On ne regarde pas les traces qui ne sont pas acceptante, que celle acceptantes.

**TEST:**
L(A) = ${\omega \in {a, b}^\omega | |w|_b = \omega }$

Réponse: $\Sigma^*.b^\omega$
Une suite finis de a suivi d'une infinité de b ou notre alphabet complet suivit d'une suite infinie de b.

### Exercice
construire automat buchi pour: p, Xp.
Lit: p
```mermaid
graph LR
0((0))-->|Sigma_p|e((1))
e-->|Sigma|e
style 0 stroke:green
style e stroke:red
```

Lit: Xp
```mermaid
graph LR
0((0))-->|Sigma|1((1))
1-->|Sigma_p|e((2))
e-->|sigma|e
style 0 stroke:green
style e stroke:red
```

Lit: Fp
```mermaid
graph LR
0((0))-->|Sigma|0
0-->|Sigma_p|e((1))
e-->|sigma|e
style 0 stroke:green
style e stroke:red
```

lit: XXp
```mermaid
graph LR
0((0))-->|Sigma|1((1))
1-->|Sigma|3((3))
3-->|Sigma_p|e((2))
e-->|sigma|e
style 0 stroke:green
style e stroke:red
```

Lit: Gp
```mermaid
graph LR
0((0))-->|Sigma_p|0
style 0 stroke:green
style 0 stroke:red
```

Lit: FGp
```mermaid
graph LR
0((0))-->|Sigma|0
0-->|Sigma_p|e((2))
e-->|Sigma_p|e
style 0 stroke:green
style e stroke:red
```

Lit: GFp
```mermaid
graph LR
0((0))-->|Sigma|0
0-->|Sigma_p|e((2))
e-->|Sigma_p|e
e-->|Sigma|0
style 0 stroke:green
style e stroke:red
```

Lit: pUq
```mermaid
graph LR
0((0))-->|Sigma_p|0
0-->|Sigma_q|e((1))
e-->|sigma|e
style 0 stroke:green
style e stroke:red
```

Lit: pRq
```mermaid
graph LR
0((0))-->|Sigma_q|0
0-->|Sigma_p|e((1))
e-->|sigma|e
style 0 stroke:green
style e stroke:red
```

### Exercice

G(p-->Fq) ==> NNF

$(false R ( \lnot p \lor (true  U q)))$

lit aU(Xa)

```mermaid
graph LR
0-->|a|0((0))
0-->|Sigma|e((1))
style 0 stroke:green
style e stroke:red
```

```mermaid
graph TB
0["a U (Xa)"]-->1["a, X(a U Xa)"]
0-->2["Xa"]
1-->1
2-->|Sigma|3[a]
```

```mermaid
graph TB
0["false R (!p || ( true U q))"]-->1["!p || true U q, X(!p || true U q)"]
2-->0
3-->4["true, X(tue U q)"]
1-->3["true U q, X(true U q)"]
3-->5["q"]
1-->2["!p, X(!p || true U q)"]
5-->0
```

```wavedrom {align="center"}
{signal:
    [{name: "P1", wave: "x...l...", data: ["P1"]},
    {name: "P2", wave: "0...xxl.", date: ["P2"]}]}
```

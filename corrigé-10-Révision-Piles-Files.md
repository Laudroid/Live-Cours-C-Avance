
# Corrigé Exercice Implémentation d’une Pile (Stack)

## Rappel

Une **pile** fonctionne selon le principe :

> LIFO – Last In, First Out
> (Dernier entré → Premier sorti)

On utilise :

* Un tableau `pile[1..MAX]`
* Une variable `sommet`
* `sommet = 0` signifie pile vide

---

## Algorithme complet

```id="p1g7ak"
Algorithme GestionPile

Constante :
    MAX ← 5

Variables :
    pile : tableau[1..MAX] d’entiers
    sommet : entier


Procédure Initialiser()
Début
    sommet ← 0
FinProcédure


Procédure Empiler(valeur : entier)
Début
    Si sommet = MAX Alors
        Afficher "Erreur : pile pleine"
    Sinon
        sommet ← sommet + 1
        pile[sommet] ← valeur
    FinSi
FinProcédure


Procédure Depiler()
Début
    Si sommet = 0 Alors
        Afficher "Erreur : pile vide"
    Sinon
        Afficher "Élément retiré : ", pile[sommet]
        sommet ← sommet - 1
    FinSi
FinProcédure


Procédure AfficherSommet()
Début
    Si sommet = 0 Alors
        Afficher "Pile vide"
    Sinon
        Afficher "Sommet : ", pile[sommet]
    FinSi
FinProcédure


Début
    Initialiser()
Fin
```

---

## Explication détaillée

### 1️⃣ Initialisation

```id="6l2j9m"
sommet ← 0
```

La pile est vide si `sommet = 0`.

---

### 2️⃣ Empiler

```id="z0mq4f"
sommet ← sommet + 1
pile[sommet] ← valeur
```

On :

1. Avance le sommet
2. Stocke la valeur

 Vérification importante :

```id="w0q6jx"
Si sommet = MAX
```

Sinon → **overflow** (dépassement).

---

### 3️⃣ Dépiler

```id="x38lps"
Afficher pile[sommet]
sommet ← sommet - 1
```

On :

1. Lit la valeur au sommet
2. Diminue l’indice

 Vérification :

```id="xq8fr2"
Si sommet = 0
```

Sinon → **underflow** (pile vide).

---

## Exemple d’exécution

Empiler : 10, 20, 30

Pile :

| Index | Valeur |
| ----- | ------ |
| 1     | 10     |
| 2     | 20     |
| 3     | 30     |

Sommet = 3

Dépiler → retire 30

Sommet = 2

---

# Corrigé Exercice Implémentation d’une File (Queue)

## Rappel

Une **file** fonctionne selon :

> FIFO – First In, First Out
> (Premier entré → Premier sorti)

On utilise :

* Un tableau `file[1..MAX]`
* Deux indices :

  * `tete`
  * `queue`

---

## Hypothèse simplifiée B1

File non circulaire.

File vide si :

```id="tz9v7c"
tete > queue
```

---

## Algorithme complet

```id="y6w3kr"
Algorithme GestionFile

Constante :
    MAX ← 5

Variables :
    file : tableau[1..MAX] d’entiers
    tete, queue : entier


Procédure Initialiser()
Début
    tete ← 1
    queue ← 0
FinProcédure


Procédure Enfiler(valeur : entier)
Début
    Si queue = MAX Alors
        Afficher "Erreur : file pleine"
    Sinon
        queue ← queue + 1
        file[queue] ← valeur
    FinSi
FinProcédure


Procédure Defiler()
Début
    Si tete > queue Alors
        Afficher "Erreur : file vide"
    Sinon
        Afficher "Élément retiré : ", file[tete]
        tete ← tete + 1
    FinSi
FinProcédure


Procédure AfficherTete()
Début
    Si tete > queue Alors
        Afficher "File vide"
    Sinon
        Afficher "Premier élément : ", file[tete]
    FinSi
FinProcédure


Début
    Initialiser()
Fin
```

---

## Explication détaillée

### 1️⃣ Initialisation

```id="v4n2qa"
tete ← 1
queue ← 0
```

File vide car :

```id="iy4k9t"
tete > queue
```

---

### 2️⃣ Enfiler

```id="h2s8zw"
queue ← queue + 1
file[queue] ← valeur
```

On ajoute en fin de file.

---

### 3️⃣ Défiler

```id="1l0pz8"
Afficher file[tete]
tete ← tete + 1
```

On retire au début.

---

## Exemple d’exécution

Enfiler : 5, 8, 12

| Index | Valeur |
| ----- | ------ |
| 1     | 5      |
| 2     | 8      |
| 3     | 12     |

tete = 1
queue = 3

Defiler → retire 5

tete = 2



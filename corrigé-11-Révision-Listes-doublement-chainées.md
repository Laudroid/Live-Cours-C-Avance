
# Corrigé Exercice 1 – Liste Doublement Chaînée

## Rappel

Un nœud contient :

* `valeur`
* `suivant`
* `precedent`

On utilise deux pointeurs globaux :

* `tete`
* `queue`

---

## Structure du nœud

```id="ldc1"
Structure Noeud
    valeur : entier
    suivant : ^Noeud
    precedent : ^Noeud
FinStructure
```

---

## Algorithme complet

```id="ldc2"
Algorithme ListeDoublementChainee

Variables globales :
    tete, queue : ^Noeud


Procédure Initialiser()
Début
    tete ← NULL
    queue ← NULL
FinProcédure


Procédure InsererEnTete(valeur : entier)
Variables :
    nouveau : ^Noeud
Début
    Allouer nouveau
    nouveau.valeur ← valeur
    nouveau.precedent ← NULL
    nouveau.suivant ← tete

    Si tete ≠ NULL Alors
        tete.precedent ← nouveau
    Sinon
        queue ← nouveau
    FinSi

    tete ← nouveau
FinProcédure
```

---

## Explication – Insertion en tête

Cas 1 : liste vide
→ `tete` et `queue` deviennent le nouveau nœud.

Cas 2 : liste non vide
→ On met à jour :

* le précédent de l’ancienne tête
* le suivant du nouveau

---

## Suppression d’un élément

```id="ldc3"
Procédure Supprimer(valeur : entier)
Variables :
    courant : ^Noeud
Début
    courant ← tete

    TantQue courant ≠ NULL ET courant.valeur ≠ valeur Faire
        courant ← courant.suivant
    FinTantQue

    Si courant = NULL Alors
        Afficher "Valeur non trouvée"
    Sinon
        Si courant.precedent ≠ NULL Alors
            courant.precedent.suivant ← courant.suivant
        Sinon
            tete ← courant.suivant
        FinSi

        Si courant.suivant ≠ NULL Alors
            courant.suivant.precedent ← courant.precedent
        Sinon
            queue ← courant.precedent
        FinSi

        Liberer courant
    FinSi
FinProcédure
```

---

## Explication – Suppression

On distingue 3 cas :

1. Suppression en tête
2. Suppression en queue
3. Suppression au milieu

On met à jour les deux liens (`suivant` et `precedent`).

---

## Affichage avant

```id="ldc4"
Procédure AfficherAvant()
Variables :
    courant : ^Noeud
Début
    courant ← tete

    TantQue courant ≠ NULL Faire
        Afficher courant.valeur
        courant ← courant.suivant
    FinTantQue
FinProcédure
```

---

## Affichage arrière

```id="ldc5"
Procédure AfficherArriere()
Variables :
    courant : ^Noeud
Début
    courant ← queue

    TantQue courant ≠ NULL Faire
        Afficher courant.valeur
        courant ← courant.precedent
    FinTantQue
FinProcédure
```

---


# Corrigé Exercice 2 – Buffer Circulaire

## Rappel

Un buffer circulaire utilise :

* Un tableau fixe
* Deux indices : `tete` et `queue`
* L’opérateur `mod`

---

## Hypothèse

On laisse **une case libre** pour différencier plein et vide.

---

## Algorithme complet

```id="bc1"
Algorithme BufferCirculaire

Constante :
    MAX ← 5

Variables :
    buffer : tableau[0..MAX-1] d’entiers
    tete, queue : entier
```

---

## Initialisation

```id="bc2"
Procédure Initialiser()
Début
    tete ← 0
    queue ← 0
FinProcédure
```

---

## Condition vide

```id="bc3"
Fonction EstVide() : booléen
Début
    Retourner tete = queue
FinFonction
```

---

## Condition plein

```id="bc4"
Fonction EstPlein() : booléen
Début
    Retourner (queue + 1) mod MAX = tete
FinFonction
```

---

## Explication

On considère que le buffer est plein si :

```id="bc5"
(queue + 1) mod MAX = tete
```

Cela signifie qu’on reviendrait sur la position de lecture.

---

## Écrire (Enfiler)

```id="bc6"
Procédure Ecrire(valeur : entier)
Début
    Si EstPlein() Alors
        Afficher "Buffer plein"
    Sinon
        buffer[queue] ← valeur
        queue ← (queue + 1) mod MAX
    FinSi
FinProcédure
```

---

## Lire (Défiler)

```id="bc7"
Procédure Lire()
Début
    Si EstVide() Alors
        Afficher "Buffer vide"
    Sinon
        Afficher "Valeur lue : ", buffer[tete]
        tete ← (tete + 1) mod MAX
    FinSi
FinProcédure
```

---

## Exemple d’exécution

MAX = 5

Écriture : 10, 20, 30

| Index | Valeur |
| ----- | ------ |
| 0     | 10     |
| 1     | 20     |
| 2     | 30     |
| 3     |        |
| 4     |        |

Si `queue` atteint 4 :

```id="bc8"
queue ← (4 + 1) mod 5 = 0
```

Il revient au début → circulaire.


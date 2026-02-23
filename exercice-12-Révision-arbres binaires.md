
# Exercice Arbre Binaire de Recherche (ABR)

## Objectif

Comprendre la structure d’un **arbre binaire de recherche** et implémenter les opérations fondamentales.

---

## Rappel théorique

Un **arbre binaire de recherche** respecte la propriété suivante :

> Pour chaque nœud :
>
> * Les valeurs du sous-arbre gauche sont **strictement inférieures**
> * Les valeurs du sous-arbre droit sont **strictement supérieures**

Exemple :

```
        15
       /  \
     10    20
    /  \     \
   8   12     25
```

---

## Énoncé

Écrire un algorithme en pseudo-code permettant :

1. De définir la structure d’un nœud
2. D’insérer une valeur dans un ABR
3. De rechercher une valeur
4. D’afficher l’arbre en parcours **infixe** (gauche – racine – droite)

---

## Contraintes

* Utiliser une structure `Noeud`
* Implémenter l’insertion de manière **récursive**
* Ne pas utiliser de tableau

---

## Indications pédagogiques

L’insertion dans un ABR repose sur la comparaison :

```
Si valeur < noeud.valeur → aller à gauche
Sinon → aller à droite
```

Le parcours infixe doit afficher les valeurs **dans l’ordre croissant**.

---

## Compétences travaillées

✔ Structures dynamiques
✔ Récursion
✔ Comparaison logique
✔ Parcours d’arbre

---

---

# Exercice Arbre Binaire Équilibré (Notion d’équilibrage)

## Objectif

Comprendre l’importance de l’équilibrage dans un arbre binaire.

---

## Rappel théorique

Un arbre est **équilibré** si la différence de hauteur entre le sous-arbre gauche et droit d’un nœud est au maximum 1.

Un arbre déséquilibré :

```
    10
      \
       20
         \
          30
```

Un arbre équilibré :

```
       20
      /  \
    10    30
```

---

## Énoncé

Écrire un algorithme en pseudo-code qui :

1. Calcule la hauteur d’un arbre
2. Vérifie si un arbre est équilibré
3. Affiche "Arbre équilibré" ou "Arbre non équilibré"

---

## Contraintes

* Utiliser une fonction récursive
* Ne pas utiliser de tableau
* Utiliser une fonction auxiliaire `Hauteur`

---

## Indications pédagogiques

Hauteur d’un nœud :

```
Hauteur = 1 + max(hauteur gauche, hauteur droite)
```

Condition d’équilibre :

```
|hauteur gauche - hauteur droite| ≤ 1
```



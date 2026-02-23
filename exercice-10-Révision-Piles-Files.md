
# Exercice 1 – Manipulation d’une Pile (Stack)

## Objectif

Comprendre et implémenter les opérations fondamentales d’une **pile** (LIFO – Last In, First Out).

---

## Énoncé

On souhaite gérer une pile d’entiers de taille maximale 5.

Écrire un algorithme qui :

1. Initialise une pile vide
2. Permet d’empiler (Push) un entier
3. Permet de dépiler (Pop) un entier
4. Affiche l’élément au sommet (Peek)
5. Empêche :

   * L’empilement si la pile est pleine
   * Le dépilement si la pile est vide

---

## Rappel théorique

Une **pile** fonctionne selon le principe :

> Dernier entré → Premier sorti

Exemple concret : une pile d’assiettes.

---

## Contraintes

* Utiliser un tableau
* Utiliser une variable `sommet`
* Créer des procédures :

  * `Empiler`
  * `Depiler`
  * `AfficherSommet`

---

## Structure attendue

```
Procédure Empiler(...)
Procédure Depiler(...)
Procédure AfficherSommet(...)
```


---

# Exercice 2 – Manipulation d’une File (Queue)

## Objectif

Comprendre et implémenter une **file** (FIFO – First In, First Out).

---

## Énoncé

On souhaite gérer une file d’entiers de taille maximale 5.

Écrire un algorithme qui :

1. Initialise une file vide
2. Permet d’ajouter un élément (Enfiler)
3. Permet de retirer un élément (Défiler)
4. Affiche le premier élément
5. Empêche :

   * L’ajout si la file est pleine
   * Le retrait si la file est vide

---

## Rappel théorique

Une **file** fonctionne selon le principe :

> 🎟 Premier entré → Premier sorti

Exemple concret : une file d’attente au guichet.

---

## Contraintes

* Utiliser un tableau
* Utiliser deux indices :

  * `tete`
  * `queue`
* Créer des procédures :

  * `Enfiler`
  * `Defiler`
  * `AfficherTete`

---

## Structure attendue

```
Procédure Enfiler(...)
Procédure Defiler(...)
Procédure AfficherTete(...)
```


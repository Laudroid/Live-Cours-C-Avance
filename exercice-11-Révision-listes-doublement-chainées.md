
# Exercice 1 – Liste Doublement Chaînée

## Objectif

Comprendre la structure d’une **liste doublement chaînée** et manipuler les pointeurs `suivant` et `precedent`.

---

## Énoncé

On souhaite gérer une liste doublement chaînée d’entiers.

Écrire un algorithme qui permet de :

1. Créer un nœud
2. Insérer un élément en tête de liste
3. Supprimer un élément donné
4. Afficher la liste dans :

   * le sens normal (avant → arrière)
   * le sens inverse (arrière → avant)

---

## Rappel théorique

Dans une liste doublement chaînée, chaque nœud contient :

* une valeur
* un pointeur vers le suivant
* un pointeur vers le précédent

Représentation :

```
NULL ← [10] ⇄ [20] ⇄ [30] → NULL
```

---

## Contraintes

* Définir une structure `Noeud`
* Utiliser deux pointeurs globaux :

  * `tete`
  * `queue`
* Gérer correctement les cas :

  * Liste vide
  * Suppression en tête
  * Suppression en fin
  * Suppression au milieu


---

# Exercice 2 – Buffer Circulaire

## Objectif

Implémenter un **buffer circulaire** (file circulaire) en pseudo-code.

---

## Énoncé

On souhaite gérer un buffer circulaire de taille 5 permettant :

1. D’ajouter un élément (`Ecrire`)
2. De lire un élément (`Lire`)
3. De détecter si le buffer est :

   * Vide
   * Plein

---

## Rappel théorique

Un buffer circulaire réutilise les positions libérées.

Exemple avec taille 5 :

```
[ 10 ][ 20 ][ 30 ][   ][   ]
         ↑
        lecture
```

Quand on atteint la fin du tableau, on revient au début.

Formule clé :

```
indice ← (indice + 1) mod MAX
```

---

## Contraintes

* Utiliser :

  * `tete`
  * `queue`
* Utiliser l’opérateur `mod`
* Gérer la condition de buffer plein

Condition classique :

```
(tete = (queue + 1) mod MAX)
```



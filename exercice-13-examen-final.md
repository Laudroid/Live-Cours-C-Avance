Nom :

Prénom :


Les questions 6, 18, 19, 30, 40, 41 ne sont pas notées

##############################
####### Partie 1 - QCM #######
##############################

1. Que signifie LIFO ?

   A) Last In, First Out

   B) Last In, First Open

   C) Last Input, First Output

   D) Logical Input First Output

   Réponse : 


2. Factorielle

```id="algo"
Fonction Factorielle(n)
    Si n = 0 Alors
        Retourner 1
    Sinon
        Retourner n * Factorielle(n-1)
FinFonction
```

Quelle est la valeur de `Factorielle(3)` ?

A) 3

B) 6

C) 9

D) 1

Réponse : 


3. Dans un algorithme récursif, pourquoi faut-il un cas de base ?

   A) Pour accélérer l’algorithme

   B) Pour arrêter les appels récursifs

   C) Pour afficher le résultat

   D) Pour créer une pile

   Réponse : 


4. Quelle structure permet d’accumuler des valeurs avant de calculer une moyenne ?

   A) Pile

   B) Tableau

   C) Accumulateur

   D) File

   Réponse : 


5. Somme récursive

```id="algo"
Fonction Somme(n)
    Si n = 1 Alors
        Retourner 1
    Sinon
        Retourner n + Somme(n-1)
FinFonction
```

Quelle est la sortie de `Somme(6)` ?

A) 15

B) 21

C) 20

D) 6

Réponse : 


6. Quelle est la complexité moyenne de la recherche dans un tableau trié par dichotomie ?

   A) O(1)

   B) O(n)

   C) O(log n)

   D) O(n²)

   Réponse : 


7. Que fait un parcours infixe sur un ABR ?

   A) Affiche gauche → racine → droite

   B) Affiche racine → gauche → droite

   C) Affiche droite → racine → gauche

   D) Affiche racine → droite → gauche

   Réponse : 


8. Dans une fonction récursive, que fait l’appel récursif ?

   A) Rappelle la fonction sur une version plus simple du problème

   B) Crée une nouvelle variable

   C) Remplace le cas de base

   D) Détruit la pile

   Réponse : 


9. Fonction Mult

```id="algo"
Fonction Mult(n)
    Si n = 1 Alors
        Retourner 2
    Sinon
        Retourner n * Mult(n-1)
FinFonction
```

**Question :** Quelle est la sortie de `Mult(3)` ?

A) 12

B) 6

C) 8

D) 10

Réponse : 


10. Que permet d’éviter une boucle dans une fonction récursive ?

    A) Une pile trop grande

    B) L’écriture d’instructions répétitives

    C) L’utilisation d’un tableau

    D) L’initialisation d’une variable

    Réponse : 


---

## **Partie 2 : Piles et files (10 questions)**

11. Quelle structure est LIFO ?

    A) Pile

    B) File

    C) Liste chaînée

    D) Tableau

    Réponse : 



12. Quelle structure est FIFO ?

    A) Pile

    B) File

    C) Liste doublement chaînée

    D) Arbre

    Réponse : 


13. Que se passe-t-il si l’on dépile une pile vide ?

    A) Underflow

    B) Overflow

    C) Crash du programme

    D) Rien

    Réponse : 


14. Que se passe-t-il si l’on empile dans une pile pleine ?

    A) Underflow

    B) Overflow

    C) La pile se réinitialise

    D) La pile s’inverse

    Réponse : 


15. Buffer circulaire indices

```id="algo"
Taille buffer ← 5
queue ← 3
tete ← 1

// Lecture d’un élément
tete ← (tete + 1) mod 5

// Ajout d’un élément
queue ← (queue + 1) mod 5
```

**Question :** Quelles seront les nouvelles valeurs de `tete` et `queue` après ces opérations ?

A) queue=4, tete=2

B) queue=3, tete=1

C) queue=0, tete=2

D) queue=1, tete=3

Réponse : 



16. Que signifie "tête" dans une file ?

    A) Dernier élément

    B) Premier élément

    C) Taille de la file

    D) Index du tableau

    Réponse : 


17. Que signifie "queue" dans une file ?

    A) Premier élément

    B) Dernier élément

    C) Taille maximale

    D) Index du tableau

    Réponse : 


18. Quelle est la complexité d’insertion dans une pile statique ?

    A) O(1)

    B) O(n)

    C) O(log n)

    D) O(n²)

    Réponse : 


19. Quelle est la complexité d’insertion dans une file statique circulaire ?

    A) O(1)

    B) O(n)

    C) O(log n)

    D) O(n²)

    Réponse : 



20. Pile dépilement

```id="algo"
Pile empilée dans l’ordre : 5, 10, 15
Depiler()
Depiler()
```

Quelle est la valeur obtenue après ces deux dépilements ?

A) 5

B) 10

C) 15

D) 20

Réponse : 


---

## **Partie 3 : Listes chaînées et doublement chaînées (10 questions)**

21. Quelle est la principale différence entre une liste simple et doublement chaînée ?

    A) La doublement chaînée a un tableau

    B) La doublement chaînée a un pointeur `precedent`

    C) La simple liste est circulaire

    D) La doublement chaînée n’a pas de racine

    Réponse : 


22. Que signifie NULL dans une liste chaînée ?

    A) Valeur du nœud

    B) Fin de la liste

    C) Début de la liste

    D) Nœud temporaire

    Réponse : 



23. Pour insérer en tête d’une liste doublement chaînée, il faut :

    A) Mettre le pointeur suivant du nouveau nœud sur la tête

    B) Supprimer la tête

    C) Déplacer tous les nœuds

    D) Faire A, B et C

    Réponse : 



24. Pour supprimer un nœud au milieu d’une liste doublement chaînée, il faut :

    A) Mettre à jour le `suivant` du précédent et le `precedent` du suivant

    B) Supprimer toute la liste

    C) Déplacer la tête

    D) Faire C, B, puis A

    Réponse : 



25. Quelle opération est plus rapide dans une liste doublement chaînée par rapport à une simple ?

    A) Parcours

    B) Suppression en fin

    C) Recherche

    D) Insertion en tête

    Réponse : 


26. Quelle structure utilise des pointeurs gauche/droite ?

    A) Liste simple

    B) Liste doublement chaînée

    C) Arbre

    D) File

    Réponse : 


27. Que fait l’insertion en queue dans une liste doublement chaînée ?

    A) Ajoute un nœud à la fin

    B) Supprime le dernier nœud

    C) Supprime le premier nœud

    D) Rien

    Réponse : 


28. Que permet le parcours arrière dans une liste doublement chaînée ?

    A) Lire les éléments dans l’ordre inverse

    B) Supprimer le dernier nœud

    C) Insérer au milieu

    D) Calculer la taille

    Réponse : 


29. Quel est l’avantage d’une liste doublement chaînée ?

    A) Utilisation mémoire minimale

    B) Parcours dans les deux sens

    C) Tri automatique

    D) Accès direct par index

    Réponse : 


30. Dans une liste chaînée, combien d’accès coûte pour trouver un nœud au milieu ?

    A) O(1)

    B) O(n)

    C) O(log n)

    D) O(n²)

    Réponse : 


---

## **Partie 4 : Buffers circulaires (5 questions)**

31. Quelle est la taille effective d’un buffer circulaire si on laisse une case libre ?

    A) MAX

    B) MAX-1

    C) MAX+1

    D) 1

    Réponse : 


32. Buffer circulaire et modulo (mod)

```id="algo"
Taille buffer ← 5
queue ← 4
tete ← 2

// Insertion d’un élément
queue ← (queue + 1) mod 5
```

Quelle sera la nouvelle valeur de `queue` ?

A) 0

B) 1

C) 2

D) 5

Réponse : 


33. Condition pour un buffer circulaire plein ?

    A) tete = queue

    B) (queue +1) mod MAX = tete

    C) queue > MAX

    D) tete = 0

    Réponse : 


34. Condition pour un buffer circulaire vide ?

    A) tete = queue

    B) queue > MAX

    C) (queue +1) mod MAX = tete

    D) tete > queue

    Réponse : 


35. Buffer circulaire lecture

```id="algo"
Buffer = [10, 20, 30, _, _]
tete = 0
queue = 3

// Lecture d’un élément
valeurLue ← buffer[tete]
tete ← (tete + 1) mod 5
```

Quelle valeur est lue et quelles sont les nouvelles valeurs de `tete` et `queue` ?

A) valeur lue=10, tete=1, queue=3

B) valeur lue=20, tete=0, queue=3

C) valeur lue=30, tete=1, queue=2

D) valeur lue=10, tete=0, queue=2

Réponse :


---

## **Partie 5 : Arbres binaires de recherche (ABR) (10 questions)**

36. Dans un ABR, toutes les valeurs du sous-arbre gauche sont :

    A) Supérieures

    B) Inférieures

    C) Égales

    D) Aléatoires

    Réponse : 


37. Dans un ABR, toutes les valeurs du sous-arbre droit sont :

    A) Supérieures

    B) Inférieures

    C) Égales

    D) Aléatoires

    Réponse : 


38. Quelle méthode permet de parcourir un ABR en ordre croissant ?

    A) Parcours préfixe

    B) Parcours infixe

    C) Parcours suffixe

    D) Parcours circulaire

    Réponse : 


39. Quelle opération est récursive pour un ABR ?

    A) Insertion

    B) Dépiler

    C) Enfiler

    D) Peek

    Réponse : 


40. Quel est le meilleur cas de recherche dans un ABR équilibré ?

    A) O(1)

    B) O(log n)

    C) O(n)

    D) O(n²)

    Réponse : 


41. Quel est le pire cas de recherche dans un ABR non équilibré ?

    A) O(1)

    B) O(log n)

    C) O(n)

    D) O(n²)

    Réponse : 


42. Quelle structure a des nœuds avec `gauche` et `droite` ?

    A) File

    B) Pile

    C) ABR

    D) Liste simple

    Réponse : 


43. Que signifie un parcours préfixe ?

    A) Racine → gauche → droite

    B) Gauche → racine → droite

    C) Gauche → droite → racine

    D) Droite → racine → gauche

    Réponse : 


44. Que signifie un parcours suffixe ?

    A) Racine → gauche → droite

    B) Gauche → racine → droite

    C) Gauche → droite → racine

    D) Droite → racine → gauche

    Réponse : 


45. Pourquoi utilise-t-on un ABR ?

    A) Pour trier les valeurs à chaque insertion

    B) Pour stocker un tableau

    C) Pour une pile

    D) Pour une file

    Réponse : 


---

## **Partie 6 : Arbres équilibrés (5 questions)**

46. Un arbre est équilibré si :

    A) Chaque nœud a 2 enfants

    B) Hauteur sous-arbre gauche – droit ≤ 1

    C) Racine = NULL

    D) Hauteur sous-arbre gauche = 0

    Réponse : 



47. Quel problème un arbre déséquilibré peut-il provoquer ?

    A) Recherche lente

    B) Insertion rapide

    C) Moins de mémoire

    D) Pile vide

    Réponse : 


48. La hauteur d’un arbre est :

    A) Nombre de nœuds

    B) Nombre de niveaux

    C) Nombre de feuilles

    D) Nombre de racines

    Réponse : 


49. Pour vérifier si un arbre est équilibré, on peut utiliser :

    A) Une boucle

    B) Une fonction récursive

    C) Un tableau

    D) Une file

    Réponse : 



50. Quelle est la différence entre un ABR et un ABR équilibré ?

    A) L’ABR est toujours trié, l’équilibré non

    B) L’ABR peut être déséquilibré, l’équilibré garde log(n)

    C) L’ABR n’a pas de racine

    D) L’équilibré est une liste

    Réponse : 


---

#########################
####### PARTIE 2 ########
#########################

# **Tables de hachage et gestion de collisions**

## **Énoncé**

On souhaite stocker les **identifiants d’étudiants** (entiers) dans une **table de hachage de taille 7**. La fonction de hachage est :

```
h(x) = x mod 7
```

### 1️⃣ Gestion des collisions

* On utilise **une liste chaînée** pour chaque case (chaînage).
* Les opérations demandées :
  a) `Inserer(id)` : ajoute un identifiant à la table
  b) `Supprimer(id)` : supprime un identifiant de la table
  c) `Rechercher(id)` : retourne vrai/faux selon que l’id est présent

---

### 2️⃣ Bonus structurel

Pour chaque case de la table de hachage :

* Les identifiants sont **empilés dans une pile** lors de l’insertion.
* Si la pile atteint **3 éléments**, la nouvelle insertion doit se faire dans une **file circulaire** associée à cette case.

---

### 3️⃣ Travaux à réaliser

1. Définir la **structure de nœud** pour la liste chaînée.
Écrire la structure `Noeud` pour une liste chaînée contenant un identifiant.

Réponse :







2. Écrire en **pseudo-code** les fonctions :

   * `Inserer(id)`
   * `Supprimer(id)`
   * `Rechercher(id)`

Écrire le pseudo-code de `Inserer(id)` avec gestion de pile et file circulaire.

Réponse :






Écrire le pseudo-code de `Supprimer(id)`.

Réponse :






Écrire le pseudo-code de `Rechercher(id)`.

Réponse :







3. Dessiner la table après insertion des identifiants :

```
15, 22, 5, 29, 8, 14, 1
```

Réponse :





4. Décrire ce qui se passe si on insère `36` sachant que la pile est pleine pour la case correspondante.

Réponse :







---

## **Contraintes et rappels**

* Table de hachage : tableau `table[0..6]`
* Chaque case contient :

  * Une **pile** (max 3 éléments)
  * Une **file circulaire** (taille 5) si pile pleine
* Les opérations de pile : `Push`, `Pop`
* Les opérations de file circulaire : `Enfiler`, `Defiler`
* Les listes chaînées doivent gérer correctement `NULL` et les cas limites

---

## Exemple attendu de réflexion

* Calcul de la **clé de hachage** :

```
h(15) = 15 mod 7 = 1
h(22) = 22 mod 7 = 1
h(5) = 5 mod 7 = 5
...
```

* Insertion dans la pile si < 3 éléments
* Passage à la file circulaire si pile pleine






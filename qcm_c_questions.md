# QCM - Langage C : Gestion de la Mémoire et Structures de Données

Ce QCM comporte 15 questions à choix unique, basées sur la gestion avancée de la mémoire (allocation dynamique, pointeurs) et les structures de données (listes, piles, files, arbres, graphes).

---

### Question 1
Quelle est la différence fondamentale entre `malloc` et `calloc` lors de l'allocation mémoire ?
- A) `malloc` alloue la mémoire sur la pile, tandis que `calloc` l'alloue sur le tas (heap).
- B) `malloc` laisse la mémoire non initialisée, alors que `calloc` initialise tous les octets à zéro.
- C) `calloc` permet de redimensionner un bloc déjà existant, contrairement à `malloc`.
- D) `malloc` retourne un pointeur de type `int*`, tandis que `calloc` retourne `void*`.

---

### Question 2
Soit le code C suivant :
```c
int *ptr = malloc(10 * sizeof(int));
// ... utilisation du pointeur ...
free(ptr);
free(ptr);
```
Quel est le problème principal présent dans ce code ?
- A) Une fuite mémoire.
- B) Une erreur de syntaxe à la compilation.
- C) Un "double free", entraînant un comportement indéfini.
- D) Une déférenciation d'un pointeur `NULL`.

---

### Question 3
Considérez la tentative suivante de redimensionnement de mémoire :
```c
int *ptr = malloc(5 * sizeof(int));
// ...
ptr = realloc(ptr, 1000000 * sizeof(int));
```
Pourquoi cette pratique est-elle considérée comme risquée ?
- A) Si `realloc` échoue et retourne `NULL`, l'adresse mémoire d'origine stockée dans `ptr` est perdue, créant une fuite mémoire.
- B) `realloc` libère toujours la mémoire d'origine même en cas d'échec d'allocation.
- C) Il est interdit d'affecter le retour de `realloc` à la même variable en C.
- D) `realloc` ne peut pas être utilisé si le bloc a été alloué initialement avec `malloc`.

---

### Question 4
Quelle est la syntaxe correcte pour déclarer un pointeur de fonction `pf` prenant un paramètre de type `int` et retournant un `int` ?
- A) `int *pf(int);`
- B) `int (*pf)(int);`
- C) `(int*) pf(int);`
- D) `void* pf(int);`

---

### Question 5
Parmi les affirmations suivantes concernant le pointeur générique `void *` en C, laquelle est VRAIE ?
- A) Il est possible d'effectuer de l'arithmétique de pointeur directement sur un `void*` selon la norme standard ISO C.
- B) Un pointeur de fonction peut être librement converti en `void*` selon la norme C sans provoquer de comportement indéfini.
- C) `void*` peut pointer vers n'importe quel type de donnée et peut être converti implicitement vers d'autres types de pointeurs de données en C.
- D) On peut déréférencer directement un pointeur `void*` sans faire de conversion (cast).

---

### Question 6
Considérez le fragment de code suivant manipulant un tableau de chaînes de caractères :
```c
char **noms = malloc(2 * sizeof(char*));
noms[0] = strdup("Alice");
noms[1] = strdup("Bob");

// Libération de la mémoire
free(noms);
```
Quel constat peut-on faire concernant la gestion de la mémoire ?
- A) La mémoire est parfaitement libérée.
- B) Il y a une fuite mémoire car `noms[0]` et `noms[1]` (alloués par `strdup`) ne sont pas libérés avant `free(noms)`.
- C) Le code va provoquer un crash lors de l'appel à `free(noms)`.
- D) `strdup` n'alloue pas de mémoire dynamique, la libération est donc inutile.

---

### Question 7
Si `ptr` est un pointeur vers une structure `struct Point { int x; int y; };`, quelle expression est équivalente à `ptr->x` ?
- A) `*ptr.x`
- B) `(*ptr).x`
- C) `&ptr.x`
- D) `ptr.[x]`

---

### Question 8
Dans une implémentation de **pile (stack)** basée sur une liste simplement chaînée, quelle est la complexité temporelle optimale des opérations `push` (empiler) et `pop` (dépiler) lorsqu'elles sont réalisées en tête de liste ?
- A) $O(1)$ pour `push` et $O(1)$ pour `pop`
- B) $O(1)$ pour `push` et $O(n)$ pour `pop`
- C) $O(n)$ pour `push` et $O(1)$ pour `pop`
- D) $O(n)$ pour `push` et $O(n)$ pour `pop`

---

### Question 9
Soit la structure de file (Queue) suivante basée sur une liste simplement chaînée :
```c
typedef struct Queue {
    Node *front;
    Node *rear;
} Queue;
```
Lors de l'opération `enqueue` (ajout d'un élément dans la file), où le nouvel élément est-il inséré ?
- A) Toujours à l'indice 0 du tableau interne.
- B) Après l'élément pointé par `rear`.
- C) Avant l'élément pointé par `front`.
- D) Au milieu de la liste.

---

### Question 10
Quel est l'avantage principal d'une liste **doublement chaînée** par rapport à une liste simplement chaînée lors de la suppression d'un nœud intermédiaire dont on possède déjà le pointeur ?
- A) Elle consomme deux fois moins de mémoire.
- B) La suppression s'effectue en $O(1)$ sans avoir besoin de parcourir la liste pour retrouver le nœud précédent.
- C) Les éléments sont automatiquement triés lors de l'insertion.
- D) Elle permet d'éviter l'utilisation de la fonction `free`.

---

### Question 11
Dans un **buffer circulaire** représenté par un tableau de capacité `max`, si l'indice d'écriture est `head`, quelle formule permet de faire avancer `head` à la position suivante de manière circulaire ?
- A) `head = head + 1;`
- B) `head = (head + 1) % max;`
- C) `head = (head + 1) / max;`
- D) `head = head * max;`

---

### Question 12
Dans un **Arbre Binaire de Recherche (ABR)**, quelle propriété doit être respectée pour chaque nœud $N$ ?
- A) Le fils gauche et le fils droit doivent obligatoirement avoir la même hauteur.
- B) Tous les nœuds du sous-arbre gauche ont une clé inférieure à celle de $N$, et tous ceux du sous-arbre droit ont une clé supérieure à celle de $N$.
- C) La clé de $N$ doit être égale à la somme des clés de ses fils.
- D) Le nœud $N$ ne peut pas avoir plus d'un sous-arbre.

---

### Question 13
Quelle est la complexité temporelle dans le **pire des cas** pour la recherche d'un élément dans un ABR **non équilibré** de $n$ nœuds ?
- A) $O(1)$
- B) $O(\log n)$
- C) $O(n)$
- D) $O(n \log n)$

---

### Question 14
Dans un **Arbre AVL**, que vaut le facteur d'équilibre (FE) autorisé pour chaque nœud (défini par : hauteur sous-arbre gauche - hauteur sous-arbre droit) ?
- A) $\text{FE} \in \{-1, 0, 1\}$
- B) $\text{FE} = 0$ obligatoirement
- C) $\text{FE} \in \{-2, 0, 2\}$
- D) $\text{FE} \ge 1$

---

### Question 15
Soit un graphe clairsemé (avec peu d'arêtes par rapport au nombre de sommets $N$). Quelle représentation en mémoire est la plus adaptée pour optimiser l'espace mémoire ?
- A) La matrice d'adjacence, car sa complexité spatiale est $O(N)$.
- B) La matrice d'adjacence, car sa complexité spatiale est $O(N^2)$.
- C) La liste d'adjacence, car sa complexité spatiale est $O(N + E)$, où $E$ est le nombre d'arêtes.
- D) Le tableau à deux dimensions statique $N \times N$.
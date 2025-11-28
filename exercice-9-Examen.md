Nom :
Prénom :


############################
######### PARTIE 1 #########
############################

## QCM – C Avancé 

1. Quel est le standard le plus récent du langage C parmi les suivants ?
   A) C99
   B) C11
   C) C17
   D) C23

   Réponse :

2. Quel mot-clé permet de déclarer une variable dont la valeur ne doit pas être modifiée ?
   A) static
   B) volatile
   C) const
   D) restrict

   Réponse :

3. Quelle fonction permet d’allouer dynamiquement de la mémoire en C ?
   A) alloc()
   B) malloc()
   C) new()
   D) create()

   Réponse :

4. Quel est le résultat de l’opération suivante ?
   ```c
   int x = 5;
   int y = x++;
   printf("%d %d", x, y);
   ```
   A) 5 5
   B) 6 5
   C) 5 6
   D) 6 6

   Réponse :

5. Quel est le type de retour de la fonction malloc() ?
   A) int*
   B) void*
   C) char*
   D) size_t

   Réponse :

6. Quelle est la bonne façon de libérer de la mémoire allouée dynamiquement ?
   A) delete(ptr);
   B) free(ptr);
   C) release(ptr);
   D) ptr = NULL;

   Réponse :

7. Quel est le rôle du qualificatif volatile ?
   A) Indiquer que la variable ne peut pas être modifiée
   B) Indiquer que la variable est globale
   C) Indiquer que la variable est statique
   D) Indiquer que la variable peut être modifiée de manière imprévisible

   Réponse :

8. Quelle fonction permet de déplacer le curseur dans un fichier ?
   A) fmove()
   B) fseek()
   C) fsetpos()
   D) fgoto()

   Réponse :

9. Quel est le résultat de ce code ?
   ```c
   int a = 3, b = 2;
   int c = a > b ? a : b;
   printf("%d", c);
   ```
   A) 2
   B) 3
   C) 0
   D) 1

   Réponse : 

10. Quel est le rôle de la fonction fflush() ?
    A) Lire un fichier
    B) Écrire dans un fichier
    C) Vider le tampon de sortie
    D) Fermer un fichier

   Réponse :

11. Quelle est l’erreur dans ce code ?
    ```c
    int *p = malloc(sizeof(int));
    *p = 10;
    free(p);
    *p = 20;
    ```
    A) Double free
    B) Fuite mémoire
    C) Accès après libération
    D) Mauvaise allocation

    Réponse :

12. Quel est le résultat de ce code ?
    ```c
    int a[] = {1, 2, 3, 4};
    int *p = a;
    printf("%d", *(p+2));
    ```
    A) 1
    B) 2
    C) 3
    D) 4

    Réponse :

13. Quelle est la différence entre malloc() et calloc() ?
    A) calloc() initialise la mémoire à zéro
    B) malloc() initialise la mémoire à zéro
    C) calloc() alloue plus de mémoire
    D) malloc() est plus rapide

    Réponse :

14. Quel est le rôle du qualificatif restrict ?
    A) Indiquer que le pointeur est constant
    B) Indiquer que le pointeur est le seul à accéder à la zone mémoire
    C) Indiquer que le pointeur est volatile
    D) Indiquer que le pointeur est global

    Réponse :

15. Quelle est la bonne déclaration d’un pointeur de fonction ?
    A) int (*f)();
    B) int *f();
    C) int f*();
    D) int f();

    Réponse :

16. Quel est le résultat de ce code ?
    ```c
    int x = 10;
    int y = 20;
    int *p = &x;
    int *q = &y;
    *p = *q;
    printf("%d %d", x, y);
    ```
    A) 10 20
    B) 20 20
    C) 20 10
    D) 10 10

    Réponse :

17. Quelle fonction permet de créer un nouveau processus en C ?
    A) fork()
    B) exec()
    C) spawn()
    D) clone()

    Réponse :

18. Quelle est la bonne façon de déclarer une structure auto-référentielle pour une liste chaînée ?
    A) struct Node { int data; struct Node next; };
    B) struct Node { int data; struct Node* next; };
    C) struct Node { int data; Node* next; };
    D) struct Node { int data; Node next; };

    Réponse :

19. Quel est le rôle de la fonction realloc() ?
    A) Libérer de la mémoire
    B) Réallouer de la mémoire avec une nouvelle taille
    C) Allouer de la mémoire initialisée à zéro
    D) Copier de la mémoire

    Réponse :

20. Quelle est l’erreur dans ce code ?
    ```c
    int *p = malloc(10 * sizeof(int));
    int *q = p;
    free(p);
    free(q);
    ```
    A) Double free
    B) Fuite mémoire
    C) Accès après libération
    D) Mauvaise allocation

    Réponse :

21. Quel est le résultat de ce code ?
    ```c
    int a = 5;
    int b = (a++, a + a);
    printf("%d", b);
    ```
    A) 10
    B) 11
    C) 12
    D) 13

    Réponse :

22. Quelle est la bonne façon de déclarer un tableau de pointeurs de fonctions ?
    A) int (*f[3])();
    B) int *f[3]();
    C) int (*f)()[3];
    D) int f[3]();

    Réponse :

23. Quel est le rôle de la macro _Generic en C11 ?
    A) Définir un type générique
    B) Choisir une expression en fonction du type
    C) Créer une fonction générique
    D) Définir une constante générique

    Réponse :

24. Quelle est l’erreur dans ce code ?
    ```c
    int *p = malloc(sizeof(int));
    int *q = p;
    *p = 10;
    free(q);
    p = malloc(sizeof(int));
    *p = 20;
    ```
    A) Double free
    B) Fuite mémoire
    C) Accès après libération
    D) Aucune erreur

    Réponse :

25. Quel est le résultat de ce code ?
    ```c
    int a = 1;
    int b = 0;
    int c = a && b++;
    printf("%d %d %d", a, b, c);
    ```
    A) 1 0 0
    B) 1 1 0
    C) 1 0 1
    D) 0 1 0

    Réponse :

26. Quelle est la bonne façon de déclarer un pointeur vers une fonction prenant un int et retournant un int ?
    A) int (*f)(int);
    B) int *f(int);
    C) int f(int*);
    D) int (*f)(int*);

    Réponse :

27. Quel est le rôle de la fonction fsync() ?
    A) Vider le tampon de sortie
    B) Synchroniser les données sur le disque
    C) Fermer un fichier
    D) Lire un fichier

    Réponse :

28. Quelle est l’erreur dans ce code ?
    ```c
    int *p = malloc(10 * sizeof(int));
    p[10] = 0;
    ```
    A) Accès hors limites
    B) Fuite mémoire
    C) Double free
    D) Mauvaise allocation

    Réponse :

29. Quel est le résultat de ce code ?
    ```c
    int a = 5;
    int b = (a = 3, a + 1);
    printf("%d", b);
    ```
    A) 3
    B) 4
    C) 5
    D) 6

    Réponse :

30. Quelle est la bonne façon de déclarer une énumération en C ?
    A) union { int a; float b; };
    B) struct { int a; float b; };
    C) enum { int a; float b; };
    D) Aucune bonne réponse

   Réponse :

31. Quel est le résultat de ce code ?
    ```c
    int a = 1;
    int b = (a++, a + a++, a);
    printf("%d", b);
    ```
    A) 1
    B) 2
    C) 3
    D) Comportement indéfini

    Réponse :

32. Quelle est la bonne façon de déclarer un pointeur vers un tableau de 10 entiers ?
    A) int (*p)[10];
    B) int *p[10];
    C) int p[10];
    D) int (*p)(10);

    Réponse :

33. Quel est le rôle de la fonction setbuf() ?
    A) Définir la taille du tampon
    B) Vider le tampon
    C) Synchroniser le tampon
    D) Fermer le tampon

    Réponse :

34. Quelle est l’erreur dans ce code ?
    ```c
    int *p = malloc(sizeof(int));
    free(p);
    p = NULL;
    free(p);
    ```
    A) Double free
    B) Fuite mémoire
    C) Accès après libération
    D) Aucune erreur

    Réponse :

35. Quel est le résultat de ce code ?
    ```c
    int a = 1;
    int b = 0;
    int c = a || b++;
    printf("%d %d %d", a, b, c);
    ```
    A) 1 0 1
    B) 1 1 1
    C) 1 0 0
    D) 0 1 0

    Réponse :

36. Quelle est la bonne façon de déclarer un pointeur vers une fonction prenant un void* et retournant un void* ?
    A) void* (*f)(void*);
    B) void* f(void*);
    C) void* (*f)(void);
    D) void* f(void*);

    Réponse :

37. Quel est le rôle de la fonction exec() ?
    A) Créer un nouveau processus
    B) Remplacer l’image mémoire du processus courant
    C) Attendre la fin d’un processus
    D) Synchroniser deux processus

    Réponse :

38. Quelle est l’erreur dans ce code ?
    ```c
    int *p = malloc(10 * sizeof(int));
    int *q = realloc(p, 20 * sizeof(int));
    if (q == NULL) free(p);
    p = q;
    ```
    A) Double free
    B) Fuite mémoire
    C) Accès après libération
    D) Aucune erreur

    Réponse :

39. Quel est le résultat de ce code ?
    ```c
    int a = 1;
    int b = (a = a + 1, ++a + 1);
    printf("%d", b);
    ```
    A) 1
    B) 2
    C) 3
    D) 4

    Réponse :

40. Quelle est la bonne façon de déclarer une structure contenant un pointeur vers elle-même ?
    A) struct Node { int data; struct Node* next; };
    B) struct Node { int data; Node* next; };
    C) struct Node { int data; struct Node next; };
    D) struct Node { int data; Node next; };

    Réponse :


############################
######### PARTIE 2 #########
############################

**Instructions :**

* Toutes les questions demandent une analyse argumentée.
* Vous pouvez proposer des corrections ou améliorations lorsque cela est pertinent.
* Justifiez toujours vos réponses en vous appuyant sur le fonctionnement interne du C (mémoire, pointeurs, standards, I/O, système…).

---

## Exercice 1 — Gestion de la mémoire et pointeurs

On vous fournit le code suivant :

```c
#include <stdio.h>
#include <stdlib.h>

int* build_array(size_t n) {
    int* arr = malloc(n * sizeof(int));
    for (size_t i = 0; i <= n; i++) {
        arr[i] = i * 2;
    }
    return arr;
}

int main() {
    int* data = build_array(10);
    free(data);
    printf("%d\n", data[5]);
    return 0;
}
```

### **Questions :**

1. Identifiez toutes les erreurs (logiques, mémoire, sécurité) présentes dans ce programme.
   Expliquez pourquoi elles sont problématiques.

Réponse :




2. Proposez une version corrigée de la fonction `build_array`.
   Justifiez vos choix (conditions, tailles, gestion d’erreur, const correctness éventuelle…).

Réponse :




3. Expliquez pourquoi l’accès `data[5]` après `free(data)` est dangereux même si cela “semble fonctionner”.
   Argumentez en termes de modèle mémoire et comportement indéfini.

Réponse :





---

## Exercice 2 — Pointeurs de fonction et `restrict`

Considérez le code suivant :

```c
void apply(int *restrict a, int *restrict b, size_t n, int (*op)(int, int)) {
    for (size_t i = 0; i < n; i++) {
        a[i] = op(a[i], b[i]);
    }
}

int add(int x, int y) { return x + y; }

int main() {
    int t[] = {1, 2, 3};
    apply(t, t, 3, add);
    return 0;
}
```

### **Questions :**

1. Expliquez pourquoi ce code viole les garanties liées au qualificatif `restrict`.

Réponse :





2. Décrivez les conséquences potentielles sur l’optimisation ou le comportement du programme.

Réponse :





3. Proposez une manière correcte d’utiliser cette fonction dans `main` ou modifiez la fonction pour éviter le problème.

Réponse :





---

## Exercice 3 — Analyse de structures de données

On vous fournit un extrait de gestion d’une liste doublement chaînée :

```c
typedef struct Node {
    int value;
    struct Node* next;
    struct Node* prev;
} Node;

void insert_after(Node* n, Node* new_node) {
    new_node->next = n->next;
    n->next = new_node;
    new_node->prev = n;
    if (n->next != NULL) {
        n->next->prev = new_node;
    }
}
```

### **Questions :**

1. Expliquez pourquoi ce code contient un bug. (Vous devrez analyser la séquence des affectations next, prev)

Réponse :





2. Proposez une version corrigée en expliquant l’ordre nécessaire des opérations.

Réponse :





---

## Exercice 4 — Manipulation de fichiers binaires

Voici un code censé écrire puis lire une structure dans un fichier binaire :

```c
typedef struct {
    int id;
    double score;
} Record;

void save(const Record* r) {
    FILE* f = fopen("data.bin", "w");
    fwrite(r, sizeof(Record), 1, f);
    fclose(f);
}

Record load() {
    Record r;
    FILE* f = fopen("data.bin", "r");
    fread(&r, sizeof(Record), 1, f);
    fclose(f);
    return r;
}
```

### **Questions :**

1. Analysez les erreurs dans l’utilisation des modes d’ouverture des fichiers.
   Expliquez leur impact sur les flux binaires.

Réponse :





2. Expliquez pourquoi il est nécessaire de vérifier le résultat des `fopen`, `fread`, `fwrite`.
   Donnez des exemples de scénarios réels où cela échouerait.

Réponse :





3. Proposez des corrections et vos justifications.

Réponse :






---

## Exercice 5 — Programmation système : processus et threads

Examinez le code suivant :

```c
#include <stdio.h>
#include <unistd.h>
#include <pthread.h>

void* routine(void* arg) {
    printf("Thread message\n");
    return NULL;
}

int main() {
    fork();
    pthread_t t;
    pthread_create(&t, NULL, routine, NULL);
    // pthread_join(t, NULL);  // volontairement commenté
    return 0;
}
```

### **Questions :**

1. Expliquez ce que ce programme affiche potentiellement et pourquoi.
   Analysez les interactions entre `fork()` et `pthread_create()`.

Réponse :






2. Identifiez les risques ou comportements indéfinis liés à l’absence de `pthread_join`.

Réponse :






3. Proposez une modification rendant ce programme correct et sûr.

Réponse :





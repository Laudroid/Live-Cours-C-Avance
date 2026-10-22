# 2-Séance 2 : Gestion Avancée de la Mémoire  
## 2-Pointeurs Avancés et Arithmétique des Pointeurs  
### 2-Tableaux de pointeurs, pointeurs et structures  

---

## Introduction  
La manipulation de tableaux de pointeurs et de pointeurs pointant sur des structures en C permet une gestion flexible et performante des données complexes, notamment pour créer des tableaux dynamiques d’objets, gérer des collections hétérogènes, ou construire des structures de données efficaces.

Ce cours explore les concepts, la syntaxe et les bonnes pratiques liées à ces usages majeurs.

---

## 1. Tableaux de pointeurs  

### 1.1 Concept  
Un tableau de pointeurs est simplement un tableau dont les éléments sont des pointeurs. Par exemple, un tableau de pointeurs vers des chaînes de caractères (`char *tab[]`) est très courant pour stocker une liste de chaînes.

### 1.2 Syntaxe et définition  

```c
char *jours[] = {"Lundi", "Mardi", "Mercredi", "Jeudi", "Vendredi"};
```

Ici, `jours` est un tableau de 5 pointeurs vers des chaînes constantes.

### 1.3 Utilisation dynamique  

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main() {
    int n = 3;
    char **noms = malloc(n * sizeof(char*));
    if (noms == NULL) return 1;

    noms[0] = strdup("Alice");
    noms[1] = strdup("Bob");
    noms[2] = strdup("Charlie");

    for (int i = 0; i < n; i++) {
        printf("%s\n", noms[i]);
        free(noms[i]);
    }
    free(noms);
    
    return 0;
}
```

`strdup` alloue une nouvelle chaîne copiée, donc pensez à libérer chaque élément avant de libérer le tableau.

---

## 2. Pointeurs vers structures  

### 2.1 Déclaration et accès  

Définir une structure et utiliser un pointeur sur cette structure :

```c
struct Point {
    int x, y;
};

int main() {
    struct Point p = {10, 20};
    struct Point *ptr = &p;

    printf("Coordonnées : (%d, %d)\n", ptr->x, ptr->y);
    return 0;
}
```

Le membre `x` est accédé via l’opérateur `->` quand on manipule un pointeur vers une structure.

### 2.2 Allocation dynamique de structures  

```c
struct Point *creer_point(int x, int y) {
    struct Point *p = malloc(sizeof(struct Point));
    if (p != NULL) {
        p->x = x;
        p->y = y;
    }
    return p;
}

int main() {
    struct Point *p = creer_point(5, 7);
    if (p != NULL) {
        printf("(%d, %d)\n", p->x, p->y);
        free(p);
    }
    return 0;
}
```

---

## 3. Tableaux de pointeurs vers structures  

Permet de créer des collections dynamiques de structures.

### Exemple  

```c
int main() {
    int n = 2;
    struct Point **tab_points = malloc(n * sizeof(struct Point*));
    if (tab_points == NULL) return 1;

    tab_points[0] = creer_point(1, 2);
    tab_points[1] = creer_point(3, 4);

    for (int i = 0; i < n; i++) {
        if (tab_points[i] != NULL)
            printf("Point %d: (%d, %d)\n", i, tab_points[i]->x, tab_points[i]->y);
    }

    for (int i = 0; i < n; i++) {
        free(tab_points[i]);
    }
    free(tab_points);

    return 0;
}
```

---

## 4. Diagramme Mermaid : interactions pointeurs et structures  

```mermaid
graph TD
    A[Tableau de pointeurs vers structures] --> B[Pointeurs vers structures]
    B --> C[Allocation dynamique de structure]
    A --> D[Tableau dynamique]
    D --> E[Allocation mémoire du tableau]
    E --> F[Libération mémoire (free)]
```

---

## 5. Bonnes pratiques  

- Toujours vérifier le résultat des `malloc` et `strdup` avant utilisation.  
- Libérer toutes les zones mémoire allouées, qu’il s’agisse des structures ou des chaînes.  
- Utiliser `->` pour accéder aux membres de structure via un pointeur, et `.` pour une structure directe.  
- Pour des tableaux dynamiques complexes, envisager des fonctions dédiées pour création et destruction afin d’assurer la cohérence mémoire.  

---

## 6. Sources utilisées  

- [Using pointers to structs - GeeksforGeeks](https://www.geeksforgeeks.org/pointers-to-structures-in-c/)  
- [Arrays of pointers - C Programming](https://www.programiz.com/c-programming/c-pointers#array-of-pointers)  
- [Dynamic array of pointers - Stack Overflow](https://stackoverflow.com/questions/35122690/dynamic-array-of-pointers-in-c)  
- [C Structures and Pointers - Tutorialspoint](https://www.tutorialspoint.com/cprogramming/c_structures.htm)  
- [ISO/IEC 9899:2018 Standard C](https://webstore.ansi.org/Standards/ISO/ISOIEC9899-2018)

---

Ce cours clarifie l’usage des tableaux de pointeurs et des pointeurs sur structures afin de manipuler efficacement des données complexes, dynamiques et extensibles en C.
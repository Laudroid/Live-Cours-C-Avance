# 2-Séance 2 : Gestion Avancée de la Mémoire  
## 2-Pointeurs Avancés et Arithmétique des Pointeurs  
### 1-Pointeurs de fonctions, pointeurs génériques (`void*`)  

---

## Introduction  
Les pointeurs en C ne se limitent pas à référencer des données ; ils permettent aussi de manipuler des fonctions via des pointeurs de fonctions, et de gérer des adresses de mémoire de type inconnu à l’aide de pointeurs génériques (`void*`). Ces concepts sont au cœur de la flexibilité et de la puissance offertes par le langage C.

Ce cours explicite la syntaxe, l’usage pratique et les précautions à prendre avec ces pointeurs avancés.

---

## 1. Pointeurs de fonctions  

### 1.1 Définition  
Un pointeur de fonction est une variable qui peut stocker l’adresse d’une fonction. Cela permet d’appeler la fonction via ce pointeur, ce qui offre un mécanisme puissant pour réaliser des callbacks, des tables de dispatch dynamiques et des formes limitées de polymorphisme.

### 1.2 Syntaxe  

Déclaration d’un pointeur vers une fonction qui prend un `int` et retourne un `int` :  
```c
int (*ptr_func)(int);
```

### 1.3 Exemple simple  

```c
#include <stdio.h>

int carre(int x) {
    return x * x;
}

int main() {
    int (*fptr)(int) = carre;  // Affectation de l’adresse de fonction

    int val = 5;
    printf("Carré de %d = %d\n", val, fptr(val));  // Appel via pointeur de fonction
    
    return 0;
}
```

### 1.4 Usage typique  
- Callbacks dans des API (exemple : fonctions de comparaison pour `qsort`)  
- Tables de fonctions dynamiques (ex : menu interactif)  
- Implémentations de machines à états  

---

## 2. Pointeurs génériques (`void *`)  

### 2.1 Définition  
`void*` est un pointeur sans type; il peut pointer vers n’importe quel type de donnée. En C, `void*` est utilisé pour écrire des fonctions et structures génériques. 

### 2.2 Conversion implicite et explicite  
- Un pointeur typé peut être converti implicitement en `void*`.  
- Pour utiliser un `void*`, il faut caster explicitement en pointeur du type attendu.  

### Exemple  

```c
void affiche_int(void *ptr) {
    int *iptr = (int *)ptr;
    printf("%d\n", *iptr);
}

int main() {
    int a = 42;
    affiche_int(&a);
    return 0;
}
```

### 2.3 Utilisations courantes  
- Fonctions génériques de gestion mémoire (`malloc` retourne `void*`).  
- APIs génériques (`qsort`, `bsearch`, etc.)  
- Stockage générique dans des structures (exemple : listes chaînées).  

---

## 3. Relation entre pointeurs de fonctions et pointeurs génériques  

Il n'est **pas possible** de convertir directement un pointeur de fonction en `void*` ou vice versa sans provoquer un comportement indéfini (cf. norme C). Ces pointeurs sont distincts et doivent être traités différemment.

---

## 4. Diagramme Mermaid : usages des pointeurs avancés  

```mermaid
graph LR
    A[Pointeurs avancés]
    A --> B[Pointeurs de fonctions]
    A --> C[Pointeurs génériques (void*)]

    B --> B1[Callbacks]
    B --> B2[Tables de fonctions]
    B --> B3[Machine à états]

    C --> C1[Gestion mémoire]
    C --> C2[Fonctions génériques]
    C --> C3[Structures de données génériques]
```

---

## 5. Précautions et bonnes pratiques  

- Toujours s’assurer que le pointeur de fonction pointe vers une fonction compatible avec la signature attendue.  
- Eviter les conversions entre pointeurs de fonction et `void*`.  
- Quand vous utilisez `void*`, cast explicite obligatoire avant accès pour éviter des erreurs et garantir la portabilité.  
- À l’utilisation de pointeurs de fonctions dans des contextes multithread, il faut garantir la synchronisation si elles référencent des données globales modifiables.  

---

## 6. Sources utilisées  

- [Pointer to function in C - GeeksforGeeks](https://www.geeksforgeeks.org/pointers-to-functions-in-c/)  
- [Void pointer in C - Tutorialspoint](https://www.tutorialspoint.com/cprogramming/c_void_pointers.htm)  
- [ISO/IEC 9899:2018 - Standard C](https://webstore.ansi.org/standards/iso/isoiec9899-2018)  
- [The GNU C Library - Function pointers](https://www.gnu.org/software/libc/manual/html_node/Function-Pointers.html)  
- [Wikipedia - Void pointer](https://en.wikipedia.org/wiki/Void_pointer)  

---

Ce cours explicite l’utilisation avancée des pointeurs de fonctions et des pointeurs génériques en C, composants indispensables pour une programmation flexible et modulaire.
# 1-Séance 1 : Rappels et Approfondissements  
## 1-Rappels essentiels  
### 2-Portée des variables (Storage Classes) en langage C  

---

## Introduction  
En langage C, la portée, la durée de vie (lifetime) et la visibilité des variables sont régies par les classes de stockage (« storage classes »). Ces classes définissent où la variable est accessible, combien de temps elle existe en mémoire, et si elle est visible en dehors d’un fichier source. Comprendre ces concepts permet d’écrire un code clair, efficace et évite des erreurs liées à la gestion des variables.

---

## 1. Définitions clés  

- **Portée (Scope)** : Région du programme où une variable est accessible.  
- **Durée de vie (Lifetime)** : Temps pendant lequel une variable conserve une valeur en mémoire.  
- **Lien (Linkage)** : Visibilité d’un symbole (variable/fonction) entre plusieurs fichiers sources.  

L'ensemble de ces caractéristiques sont définies via les classes de stockage suivantes.

---

## 2. Les classes de stockage en C  

| Classe de stockage | Portée        | Durée de vie       | Linkage      | Description |
|--------------------|---------------|--------------------|--------------|-------------|
| **auto**           | Locale (bloc) | Durée automatique  | Aucun        | Variable locale classique, créée à l’entrée du bloc, détruite à la sortie. Par défaut pour les variables locales. |
| **register**       | Locale (bloc) | Durée automatique  | Aucun        | Variable locale suggérée pour stockage dans registre CPU, accès rapide. Variable locale avec possibilité d’optimisation. |
| **static**         | Locale ou globale | Persistante (durée du programme) | Aucun (locale) ou Externe (globale) | Local statique : variable locale qui conserve sa valeur entre les appels.<br>Globale statique : variable ou fonction visible seulement dans le fichier source (linkage interne). |
| **extern**         | Globale       | Persistante (durée du programme) | Externe     | Variable/fonction définie dans un autre fichier ou dans le même fichier, déclarée pour usage externe.  |

---

## 3. Explications détaillées  

### 3.1 `auto`  
- Par défaut, toutes les variables locales ont cette classe de stockage si rien d’autre n’est spécifié.  
- Leur durée correspond à la vie du bloc dans lequel elles sont définies (par exemple une fonction).  
- Pas de visibilité hors du bloc.

### Exemple `auto` :  
```c
void fonction() {
    int val = 5;  // auto implicite
    printf("%d\n", val);
}
```

---

### 3.2 `register`  
- Indique au compilateur que la variable sera fréquemment utilisée, et recommande son stockage dans un registre CPU.  
- Ne peut pas avoir l'adresse prise via `&` (dans la plupart des compilateurs).  
- Durée de vie similaire à `auto`.

### Exemple `register` :  
```c
void boucle() {
    register int i;  
    for(i = 0; i < 10; i++) {
        printf("%d ", i);
    }
}
```

---

### 3.3 `static`  
- Permet à une variable locale de conserver sa valeur entre plusieurs appels à la fonction.  
- Pour les variables globales, la qualifier de `static` limite sa visibilité au fichier source (linkage interne).  
- Sa durée de vie est celle de tout le programme (initialisée une seule fois).  

### Exemple `static` local :  
```c
void compteur() {
    static int count = 0;  // initialisée une seule fois
    count++;
    printf("Compteur = %d\n", count);
}
```
Chaque appel à `compteur` affichera une valeur incrémentée.

---

### 3.4 `extern`  
- Sert à déclarer une variable ou fonction définie dans un autre fichier source.  
- Elle n’est pas allouée dans le fichier où elle est déclarée.  
- Le linkage est externe.  

### Exemple `extern` :  
Dans fichier1.c :  
```c
int valeur = 42;
```
Dans fichier2.c :  
```c
extern int valeur;
void afficher() {
    printf("%d\n", valeur);
}
```

---

## 4. Résumé visuel des scopes, durées, et visibilités  

```mermaid
graph TD
    subgraph Portée
        Bloc[Bloc (fonction...)]
        Fichier[Fichier source]
        Global[Global hors fichier]
    end

    subgraph Durée
        Auto[Durée automatique (temporaire)]
        Pers[Durée persistante (programme entier)]
    end

    subgraph Linkage
        Aucun[Sans linkage]
        Interne[Linkage interne]
        Externe[Linkage externe]
    end

    auto[auto] --> Bloc
    auto --> Auto
    auto --> Aucun

    register[register] --> Bloc
    register --> Auto
    register --> Aucun

    static_local[static (local)] --> Bloc
    static_local --> Pers
    static_local --> Aucun

    static_global[static (global)] --> Fichier
    static_global --> Pers
    static_global --> Interne

    extern[extern] --> Global
    extern --> Pers
    extern --> Externe
```

---

## 5. Exemples combinés  

```c
#include <stdio.h>

int global_var = 100;       // Durée persistante, linkage externe par défaut
static int static_var = 200; // Durée persistante, linkage interne

void demo() {
    auto int auto_var = 10;     // portée locale, durée automatique
    register int reg_var = 20;  // portée locale, durée automatique

    static int static_local = 0; // durée persistante, portée locale
    static_local++;
    printf("auto_var=%d, reg_var=%d, static_local=%d\n", auto_var, reg_var, static_local);
}

int main() {
    extern int global_var;
    printf("global_var=%d, static_var=%d\n", global_var, static_var);

    demo();
    demo();
    demo();

    return 0;
}
```

---

## Sources utilisées  

- [Storage Classes in C - GeeksforGeeks](https://www.geeksforgeeks.org/c/storage-classes-in-c/)  
- [Module 8 C - Storage Classes, UHCL](https://sceweb.sce.uhcl.edu/helm/WEBPAGE-C/my_files/TableContents/Module-8/module8page.html)  
- [Storage classes in C - Augustine Joseph - Medium](https://augustinejoseph.medium.com/storage-classes-in-c-873698bec728)  
- [Scope, Visibility and Lifetime of a Variable in C - Scaler Topics](https://www.scaler.com/topics/c/scope-visibility-lifetime-of-variable-in-c/)  

---

Ce cours synthétise les notions fondamentales de portée et durée de vie en C à travers les classes de stockage, afin de clarifier comment les variables sont gérées par le compilateur et exécutées en mémoire.
Bonjour à toutes et à tous !

Le langage C, loin d'être figé, continue d'évoluer pour répondre aux défis de la programmation moderne. Les standards C11, C17 et C23 ont introduit des fonctionnalités significatives qui améliorent la robustesse, la flexibilité et l'expressivité de notre code. Ce TP est conçu pour vous faire explorer ces nouveautés de manière concrète.

---

## TP: Le C Moderne en Action – `_Generic` et les Standards C11/C17/C23

### Contexte

Comprendre l'évolution du langage C est essentiel pour écrire du code performant, maintenable et compatible avec les pratiques actuelles. Ce TP se concentre sur des fonctionnalités clés qui vous permettront de moderniser vos approches de développement en C.

### Objectif du TP

Explorer les fonctionnalités clés des standards C11, C17 et C23 à travers un mini-projet pratique.

### Prérequis

*   Bonne connaissance du langage C (syntaxe, pointeurs, structures, macros).
*   Familiarité avec la compilation de programmes C.

### Environnement de Travail

Vous aurez besoin d'un compilateur C supportant au moins le standard C11, idéalement C23 (ou C2x).

*   **GCC (GNU Compiler Collection)** : Version 13 ou supérieure est recommandée pour C23.
    *   Pour C11 : `gcc -std=c11 mon_fichier.c -o mon_programme`
    *   Pour C17 : `gcc -std=c17 mon_fichier.c -o mon_programme`
    *   Pour C23 : `gcc -std=c2x mon_fichier.c -o mon_programme` (ou `gcc -std=gnu2x` pour les extensions GNU en plus)
*   **Clang/LLVM** : Version 16 ou supérieure est recommandée pour C23.
    *   Pour C11 : `clang -std=c11 mon_fichier.c -o mon_programme`
    *   Pour C17 : `clang -std=c17 mon_fichier.c -o mon_programme`
    *   Pour C23 : `clang -std=c2x mon_fichier.c -o mon_programme`

---

### Mini-Projet : "Le Journal de Bord Universel"

**Problématique :** Comment créer une fonction de journalisation (logging) capable de gérer différents types de données de manière élégante et sûre, en tirant parti des avancées du langage C ? Nous allons développer une petite bibliothèque de logging générique.

---

#### Étape 1 : `_Generic` pour un affichage simple (C11)

Le sélecteur `_Generic` permet de choisir une expression en fonction du type de son argument. C'est un outil puissant pour l'écriture de fonctions génériques au moment de la compilation.

1.  **Créez un fichier `logger.h` et `logger.c`.**
2.  Dans `logger.c`, implémentez des fonctions d'affichage simples pour différents types :
    ```c
    // logger.c
    #include <stdio.h>

    void _log_int(int val) {
        printf("[INT] %d\n", val);
    }

    void _log_double(double val) {
        printf("[DBL] %f\n", val);
    }

    void _log_string(const char* val) {
        printf("[STR] %s\n", val);
    }

    // Ajoutez d'autres types si vous le souhaitez (char, long, float, void*)
    ```
3.  Dans `logger.h`, définissez une macro `LOG_VAL` utilisant `_Generic` pour appeler la fonction d'affichage appropriée :
    ```c
    // logger.h
    #ifndef LOGGER_H
    #define LOGGER_H

    // Déclarations des fonctions internes (à implémenter dans logger.c)
    void _log_int(int val);
    void _log_double(double val);
    void _log_string(const char* val);
    // ... autres déclarations ...

    #define LOG_VAL(X) _Generic((X), \
        int: _log_int, \
        double: _log_double, \
        const char*: _log_string, \
        char*: _log_string, \
        default: _log_string /* Fallback pour les types non gérés, ou erreur */ \
    )(X)

    #endif
    ```
4.  **Testez** dans un `main.c` :
    ```c
    // main.c
    #include "logger.h"
    #include <stdio.h>

    int main() {
        int a = 42;
        double b = 3.14;
        const char* c = "Bonjour le monde !";
        char d[] = "Test de string modifiable";

        LOG_VAL(a);
        LOG_VAL(b);
        LOG_VAL(c);
        LOG_VAL(d); // Devrait utiliser char* ou const char*

        // Que se passe-t-il si vous passez un type non géré ?
        // long l = 1234567890L;
        // LOG_VAL(l); // Devrait utiliser le 'default' si défini, sinon erreur de compilation

        return 0;
    }
    ```
    *Compilez avec `-std=c11`.*

#### Étape 2 : Amélioration avec des attributs (C23)

Les attributs permettent de fournir des informations supplémentaires au compilateur, améliorant la qualité du code et la détection d'erreurs potentielles.

1.  **`[[nodiscard]]` :** Appliquez cet attribut à une fonction qui retourne une valeur importante et dont le résultat ne devrait pas être ignoré.
    *   Créez une fonction `int check_status(int code)` qui retourne `0` si `code` est `0`, `1` sinon. Marquez-la avec `[[nodiscard]]`.
    *   Dans `main.c`, appelez cette fonction et *ignorez volontairement sa valeur de retour*. Observez l'avertissement du compilateur.
2.  **`[[maybe_unused]]` :** Utilisez cet attribut pour indiquer qu'une variable, un paramètre ou une fonction peut ne pas être utilisée, supprimant ainsi les avertissements du compilateur.
    *   Dans `logger.c` ou `main.c`, déclarez une variable ou un paramètre que vous savez ne pas utiliser, et marquez-le avec `[[maybe_unused]]`.
    *   Vérifiez que le compilateur ne génère plus d'avertissement pour cette entité.

    *Compilez avec `-std=c2x` ou `-std=gnu2x`.*

#### Étape 3 : `typeof` et `_VA_OPT_` pour plus de flexibilité (C23)

Ces fonctionnalités de C23 augmentent considérablement la puissance des macros et la généricité.

1.  **`typeof` :** Permet d'obtenir le type d'une expression. C'est extrêmement utile avec `_Generic` pour gérer des types plus complexes ou pour éviter de lister explicitement toutes les variantes de types (ex: `unsigned int`, `long long`, etc.).
    *   Modifiez votre macro `LOG_VAL` pour utiliser `typeof` afin de gérer plus finement les types numériques sans avoir à les lister tous explicitement dans `_Generic`. Par exemple, vous pourriez avoir un `_log_integer(typeof(X) val)` et un `_log_floating(typeof(X) val)`.
    *   **Défi :** Créez une fonction `_log_any_integer(const char* type_name, long long val)` et utilisez `typeof` dans `_Generic` pour mapper tous les types entiers (signés et non signés) à cette fonction, en passant le nom du type comme chaîne de caractères.
    *   *Indice :* Vous devrez peut-être caster la valeur vers `long long` pour la fonction générique, mais `typeof` vous aidera à identifier le type original.

2.  **`_VA_OPT_` :** Permet d'inclure ou d'exclure des arguments variadiques dans une macro en fonction de leur présence.
    *   Créez une macro `LOG_MSG(level, format, ...)` qui affiche un message avec un niveau de journalisation.
    *   Utilisez `_VA_OPT_` pour permettre à `LOG_MSG` d'être appelée avec ou sans arguments variadiques.
        ```c
        // Exemple d'utilisation de _VA_OPT_
        #define LOG_MSG(level, format, ...) \
            printf("[%s] " format _VA_OPT_(, __VA_ARGS__) "\n", level)

        // Testez :
        LOG_MSG("INFO", "Opération réussie.");
        LOG_MSG("ERROR", "Erreur %d: %s", 500, "Problème serveur");
        ```

    *Compilez avec `-std=c2x` ou `-std=gnu2x`.*

#### Étape 4 : Assertions et alignement (C11)

Ces fonctionnalités sont cruciales pour la robustesse du code et l'optimisation mémoire.

1.  **`static_assert` :** Permet de vérifier des conditions au moment de la compilation. Si la condition est fausse, la compilation échoue avec un message d'erreur.
    *   Dans `logger.h` ou `logger.c`, ajoutez un `static_assert` pour vérifier que la taille d'un `int` est bien de 4 octets (ou une autre condition pertinente pour votre système).
    *   Ajoutez un autre `static_assert` pour vérifier que `sizeof(void*)` est égal à `sizeof(size_t)`.
    *   *Note :* En C23, `static_assert` peut être utilisé sans message d'erreur.

2.  **`_Alignof` et `_Alignas` :**
    *   `_Alignof` : Permet d'obtenir l'alignement requis pour un type.
    *   `_Alignas` : Permet de spécifier l'alignement d'une variable ou d'un membre de structure.
    *   Créez une structure simple, par exemple `typedef struct { char c; int i; double d; } MyData;`.
    *   Utilisez `_Alignof` pour afficher l'alignement de `MyData`, de ses membres individuels, et de quelques types fondamentaux (`int`, `double`, `char*`).
    *   Créez une deuxième structure `MyAlignedData` et utilisez `_Alignas` pour forcer un alignement spécifique (par exemple, 16 ou 32 octets) pour la structure elle-même ou pour un de ses membres. Comparez la taille et l'alignement avec `MyData`.

    *Compilez avec `-std=c11`.*

#### Étape 5 : `nullptr` et `nullptr_t` (C23)

C23 standardise `nullptr` et son type `nullptr_t`, apportant une sécurité de type similaire à C++.

1.  **Utilisation de `nullptr` :**
    *   Modifiez toutes les occurrences de `NULL` dans votre code (si vous en avez) par `nullptr`.
    *   Créez une fonction `void process_pointer(void* ptr)` et une autre `void process_nullptr(nullptr_t np)`.
    *   Dans `main.c`, appelez `process_pointer` avec `nullptr`.
    *   Essayez d'appeler `process_nullptr` avec un pointeur `void*` non `nullptr`. Observez l'erreur de compilation.
    *   Appelez `process_nullptr` avec `nullptr`.

    *Compilez avec `-std=c2x` ou `-std=gnu2x`.*

#### Étape 6 : Bonus / Exploration Libre

Choisissez une ou plusieurs des fonctionnalités suivantes et intégrez-les à votre "Journal de Bord Universel" ou explorez-les séparément :

*   **`_Noreturn` (C11) :** Appliquez cet attribut à une fonction qui ne retourne jamais (par exemple, une fonction qui termine le programme ou lève une exception).
*   **`[[fallthrough]]` (C17) :** Utilisez cet attribut dans un `switch` pour indiquer qu'une chute intentionnelle entre les `case` est prévue, supprimant les avertissements du compilateur.
*   **`_BitInt(N)` (C23) :** Expérimentez avec les entiers de largeur fixe. Déclarez une variable `_BitInt(24)` et observez son comportement.
*   **`#pragma once` (C23) :** Remplacez vos gardes d'inclusion (`#ifndef/#define/#endif`) dans `logger.h` par `#pragma once`.
*   **`bool`, `true`, `false` (C23) :** Si vous utilisiez `_Bool` ou des définitions manuelles, passez aux versions standardisées.

---

### Ressources Utiles

*   **C11 Standard Draft :** [https://www.open-std.org/jtc1/sc22/wg14/www/docs/n1570.pdf](https://www.open-std.org/jtc1/sc22/wg14/www/docs/n1570.pdf)
*   **C17 Standard Draft :** [https://www.open-std.org/jtc1/sc22/wg14/www/docs/n2176.pdf](https://www.open-std.org/jtc1/sc22/wg14/www/docs/n2176.pdf)
*   **C23 Standard Draft :** [https://www.open-std.org/jtc1/sc22/wg14/www/docs/n3096.pdf](https://www.open-std.org/jtc1/sc22/wg14/www/docs/n3096.pdf) (ou la version la plus récente disponible sur le site du WG14)
*   **Documentation GCC/Clang :** Pour les options de compilation et les extensions.

---

### Conseils du Formateur

*   **L'IA est un outil puissant :** N'hésitez pas à l'utiliser pour obtenir des explications sur une fonctionnalité, des exemples de code, ou pour débugger un problème de compilation lié à un standard spécifique. Demandez-lui "Comment utiliser `_Generic` pour gérer des pointeurs de fonction ?" ou "Donne-moi un exemple de `static_assert` en C11".
*   **Expérimentez :** Le meilleur moyen d'apprendre est de tester. Modifiez le code, introduisez des erreurs volontairement pour voir comment le compilateur réagit.
*   **Lisez les messages d'erreur :** Ils sont vos meilleurs amis. Avec les nouvelles fonctionnalités, le compilateur vous donnera souvent des indices précis sur ce qui ne va pas.
*   **Le but n'est pas de tout mémoriser :** L'objectif est de savoir que ces fonctionnalités existent et de comprendre quand et pourquoi les utiliser. Vous pourrez toujours vous référer à la documentation ou à l'IA lorsque vous en aurez besoin.

Amusez-vous bien avec ce TP et découvrez la puissance du C moderne !
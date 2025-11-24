Voici deux solutions complètes pour le TP "Le C Moderne en Action", chacune présentée dans un chapitre Markdown distinct. Ces solutions intègrent les fonctionnalités des standards C11, C17 et C23, avec des commentaires et des explications pour chaque concept.

---

# Solution 1 : Implémentation Détaillée des Fonctionnalités

Cette solution propose une implémentation pas à pas des exigences du TP, en détaillant l'utilisation de chaque fonctionnalité moderne du C. L'accent est mis sur la clarté et la démonstration des concepts.

## Fichier `logger.h`


```c
#ifndef LOGGER_H
#define LOGGER_H

#include <stddef.h>  // Pour size_t, utilisé dans static_assert
#include <stdbool.h> // Pour bool, true, false (standardisé en C23)
#include <stdio.h>   // Pour printf dans les macros si nécessaire

#pragma once // C23: Alternative standardisée aux gardes d'inclusion #ifndef/#define/#endif

// --- Étape 1: _Generic pour un affichage simple (C11) ---
// Déclarations des fonctions d'affichage spécifiques par type
void _log_int(int val);
void _log_unsigned_int(unsigned int val);
void _log_long(long val);
void _log_unsigned_long(unsigned long val);
void _log_long_long(long long val);
void _log_unsigned_long_long(unsigned long long val);
void _log_float(float val);
void _log_double(double val);
void _log_long_double(long double val);
void _log_char(char val);
void _log_string(const char* val);
void _log_pointer(const void* val);
// Fonction de fallback générique pour les types non gérés par _Generic
void _log_generic_fallback(const char* type_name, const void* ptr);

// Macro LOG_VAL utilisant _Generic pour sélectionner la fonction d'affichage
#define LOG_VAL(X) _Generic((X), \
    int: _log_int, \
    unsigned int: _log_unsigned_int, \
    long: _log_long, \
    unsigned long: _log_unsigned_long, \
    long long: _log_long_long, \
    unsigned long long: _log_unsigned_long_long, \
    float: _log_float, \
    double: _log_double, \
    long double: _log_long_double, \
    char: _log_char, \
    const char*: _log_string, \
    char*: _log_string, \
    void*: _log_pointer, \
    const void*: _log_pointer, \
    default: _log_generic_fallback \
)("unknown type for LOG_VAL", (const void*)&(X)) // Pour le fallback, on passe un nom de type et l'adresse de la variable

// --- Étape 3: typeof et _VA_OPT_ pour plus de flexibilité (C23) ---
// Fonctions génériques pour les types numériques (défi _log_any_integer)
void _log_any_integer(const char* type_name, long long val);
void _log_any_unsigned_integer(const char* type_name, unsigned long long val);
void _log_any_floating(const char* type_name, long double val);

// Macro LOG_VAL_V2 utilisant typeof dans _Generic pour une gestion plus fine des numériques
#define LOG_VAL_V2(X) _Generic((X), \
    /* Entiers signés */ \
    signed char: _log_any_integer("signed char", (long long)(X)), \
    short: _log_any_integer("short", (long long)(X)), \
    int: _log_any_integer("int", (long long)(X)), \
    long: _log_any_integer("long", (long long)(X)), \
    long long: _log_any_integer("long long", (long long)(X)), \
    /* Entiers non signés */ \
    unsigned char: _log_any_unsigned_integer("unsigned char", (unsigned long long)(X)), \
    unsigned short: _log_any_unsigned_integer("unsigned short", (unsigned long long)(X)), \
    unsigned int: _log_any_unsigned_integer("unsigned int", (unsigned long long)(X)), \
    unsigned long: _log_any_unsigned_integer("unsigned long", (unsigned long long)(X)), \
    unsigned long long: _log_any_unsigned_integer("unsigned long long", (unsigned long long)(X)), \
    /* Flottants */ \
    float: _log_any_floating("float", (long double)(X)), \
    double: _log_any_floating("double", (long double)(X)), \
    long double: _log_any_floating("long double", (long double)(X)), \
    /* Autres types */ \
    const char*: _log_string, \
    char*: _log_string, \
    void*: _log_pointer, \
    const void*: _log_pointer, \
    default: _log_generic_fallback \
)("unknown type for LOG_VAL_V2", (const void*)&(X)) // Fallback avec nom et adresse

// Macro LOG_MSG utilisant _VA_OPT_ pour gérer les arguments variadiques (C23)
#define LOG_MSG(level, format, ...) \
    printf("[%s] " format _VA_OPT_(, __VA_ARGS__) "\n", level)

// --- Étape 4: Assertions et alignement (C11) ---
// Macro pour static_assert avec message (C11)
#define STATIC_ASSERT_C11(cond, msg) _Static_assert(cond, msg)

// Vérifications au moment de la compilation pour la robustesse
STATIC_ASSERT_C11(sizeof(int) == 4, "La taille d'un int n'est pas 4 octets sur cette plateforme.");
STATIC_ASSERT_C11(sizeof(void*) == sizeof(size_t), "La taille de void* n'est pas égale à celle de size_t.");

// --- Étape 2: Attributs (C23) ---
// Fonction avec l'attribut [[nodiscard]] pour indiquer que sa valeur de retour ne doit pas être ignorée
[[nodiscard]] int check_status(int code);

// --- Étape 5: nullptr et nullptr_t (C23) ---
// Fonctions pour démontrer l'utilisation de nullptr et nullptr_t
void process_pointer(void* ptr);
void process_nullptr(nullptr_t np);

// --- Étape 6: Bonus - _Noreturn (C11) ---
// Fonction avec l'attribut [[noreturn]] (C23) ou _Noreturn (C11) pour indiquer qu'elle ne retourne jamais
[[noreturn]] void exit_program_error(const char* msg);

#endif // LOGGER_H
```


## Fichier `logger.c`


```c
#include "logger.h"
#include <stdio.h>
#include <stdlib.h>   // Pour exit() utilisé par exit_program_error
#include <stdalign.h> // Pour _Alignof, _Alignas (C11)

// --- Étape 1: _Generic pour un affichage simple (C11) ---
// Implémentations des fonctions d'affichage spécifiques
void _log_int(int val) {
    printf("[INT] %d\n", val);
}
void _log_unsigned_int(unsigned int val) {
    printf("[UINT] %u\n", val);
}
void _log_long(long val) {
    printf("[LONG] %ld\n", val);
}
void _log_unsigned_long(unsigned long val) {
    printf("[ULONG] %lu\n", val);
}
void _log_long_long(long long val) {
    printf("[LLONG] %lld\n", val);
}
void _log_unsigned_long_long(unsigned long long val) {
    printf("[ULLONG] %llu\n", val);
}
void _log_float(float val) {
    printf("[FLT] %f\n", val);
}
void _log_double(double val) {
    printf("[DBL] %f\n", val);
}
void _log_long_double(long double val) {
    printf("[LDBL] %Lf\n", val);
}
void _log_char(char val) {
    printf("[CHAR] '%c' (%d)\n", val, val);
}
void _log_string(const char* val) {
    printf("[STR] \"%s\"\n", val);
}
void _log_pointer(const void* val) {
    printf("[PTR] %p\n", val);
}

// Implémentation de la fonction de fallback générique
void _log_generic_fallback(const char* type_name, const void* ptr) {
    printf("[UNHANDLED] Type '%s' non géré. Adresse: %p\n", type_name, ptr);
}

// --- Étape 3: typeof et _VA_OPT_ pour plus de flexibilité (C23) ---
// Implémentations des fonctions génériques pour les types numériques
void _log_any_integer(const char* type_name, long long val) {
    printf("[ANY_INT] Type: %s, Valeur: %lld\n", type_name, val);
}
void _log_any_unsigned_integer(const char* type_name, unsigned long long val) {
    printf("[ANY_UINT] Type: %s, Valeur: %llu\n", type_name, val);
}
void _log_any_floating(const char* type_name, long double val) {
    printf("[ANY_FLT] Type: %s, Valeur: %Lf\n", type_name, val);
}

// --- Étape 2: Attributs (C23) ---
// Implémentation de la fonction avec [[nodiscard]]
[[nodiscard]] int check_status(int code) {
    printf("check_status appelé avec code: %d\n", code);
    return code == 0 ? 0 : 1;
}

// --- Étape 5: nullptr et nullptr_t (C23) ---
// Implémentations des fonctions pour nullptr
void process_pointer(void* ptr) {
    if (ptr == nullptr) { // Utilisation de nullptr pour la comparaison
        printf("process_pointer: Le pointeur est nullptr.\n");
    } else {
        printf("process_pointer: Le pointeur n'est pas nullptr: %p\n", ptr);
    }
}

void process_nullptr(nullptr_t np) {
    // nullptr_t est un type distinct qui ne peut être que nullptr.
    // L'argument 'np' est ici pour la démonstration de type.
    printf("process_nullptr: Reçu un nullptr_t.\n");
    (void)np; // Supprime l'avertissement si np n'est pas utilisé
}

// --- Étape 6: Bonus - _Noreturn (C11) ---
// Implémentation de la fonction qui ne retourne jamais
[[noreturn]] void exit_program_error(const char* msg) {
    fprintf(stderr, "Erreur fatale: %s\n", msg);
    exit(EXIT_FAILURE); // Termine le programme
}
```


## Fichier `main.c`


```c
#include "logger.h"
#include <stdio.h>
#include <stdlib.h>   // Pour NULL (avant C23)
#include <stdalign.h> // Pour _Alignof, _Alignas (C11)

// --- Étape 4: Assertions et alignement (C11) ---
// Structure simple pour tester l'alignement
typedef struct {
    char c;
    int i;
    double d;
} MyData;

// Structure avec alignement forcé à 32 octets pour la structure et 16 octets pour 'i'
typedef struct _Alignas(32) {
    char c;
    _Alignas(16) int i;
    double d;
} MyAlignedData;

int main() {
    printf("--- Étape 1: _Generic pour un affichage simple (C11) ---\n");
    int a = 42;
    double b = 3.14;
    const char* c_str = "Bonjour le monde !";
    char d_arr[] = "Test de string modifiable";
    long l = 1234567890L;
    unsigned int ui = 100u;
    void* ptr = &a;
    char ch = 'X';
    float f = 1.23f;
    long double ld = 1.234567890123456789L;

    LOG_VAL(a);
    LOG_VAL(b);
    LOG_VAL(c_str);
    LOG_VAL(d_arr);
    LOG_VAL(l);
    LOG_VAL(ui);
    LOG_VAL(ptr);
    LOG_VAL(ch);
    LOG_VAL(f);
    LOG_VAL(ld);

    // Test d'un type non géré (une structure)
    MyData my_struct_val = {'A', 10, 20.5};
    LOG_VAL(my_struct_val); // Devrait utiliser le 'default' et afficher le message de fallback

    printf("\n--- Étape 3: typeof et _VA_OPT_ (C23) ---\n");
    printf("--- Défi _log_any_integer avec LOG_VAL_V2 ---\n");
    LOG_VAL_V2(a); // int
    LOG_VAL_V2(l); // long
    LOG_VAL_V2(ui); // unsigned int
    unsigned long long ull = 9876543210ULL;
    LOG_VAL_V2(ull); // unsigned long long
    LOG_VAL_V2(b); // double
    LOG_VAL_V2(ld); // long double

    printf("\n--- Test de LOG_MSG avec _VA_OPT_ ---\n");
    LOG_MSG("INFO", "Opération réussie.");
    LOG_MSG("ERROR", "Erreur %d: %s", 500, "Problème serveur");
    LOG_MSG("DEBUG", "Valeur de a: %d, b: %f", a, b);

    printf("\n--- Étape 2: Amélioration avec des attributs (C23) ---\n");
    printf("--- Test de [[nodiscard]] ---\n");
    // Ignorer volontairement la valeur de retour de check_status
    check_status(0); // Le compilateur devrait émettre un avertissement ici
    check_status(1); // Le compilateur devrait émettre un avertissement ici

    printf("\n--- Test de [[maybe_unused]] ---\n");
    [[maybe_unused]] int unused_var = 10; // Cette variable ne sera pas utilisée
    // Le compilateur ne devrait pas générer d'avertissement pour unused_var

    printf("\n--- Étape 4: Assertions et alignement (C11) ---\n");
    printf("--- Test de _Alignof ---\n");
    printf("Alignement de char: %zu\n", _Alignof(char));
    printf("Alignement de int: %zu\n", _Alignof(int));
    printf("Alignement de double: %zu\n", _Alignof(double));
    printf("Alignement de char*: %zu\n", _Alignof(char*));
    printf("Alignement de MyData: %zu\n", _Alignof(MyData));
    printf("Alignement de MyData.c: %zu\n", _Alignof(MyData.c));
    printf("Alignement de MyData.i: %zu\n", _Alignof(MyData.i));
    printf("Alignement de MyData.d: %zu\n", _Alignof(MyData.d));

    printf("\n--- Test de _Alignas ---\n");
    printf("Taille de MyData: %zu octets\n", sizeof(MyData));
    printf("Alignement de MyData: %zu octets\n", _Alignof(MyData));
    printf("Taille de MyAlignedData: %zu octets\n", sizeof(MyAlignedData));
    printf("Alignement de MyAlignedData: %zu octets\n", _Alignof(MyAlignedData));
    printf("Alignement de MyAlignedData.c: %zu octets\n", _Alignof(MyAlignedData.c));
    printf("Alignement de MyAlignedData.i: %zu octets\n", _Alignof(MyAlignedData.i));
    printf("Alignement de MyAlignedData.d: %zu octets\n", _Alignof(MyAlignedData.d));


    printf("\n--- Étape 5: nullptr et nullptr_t (C23) ---\n");
    void* generic_ptr = nullptr; // Utilisation de nullptr (C23)
    process_pointer(generic_ptr);
    process_pointer(&a);

    // Tentative d'appeler process_nullptr avec un void* non nullptr (erreur de compilation attendue)
    // process_nullptr(&a);
    printf("Tentative d'appeler process_nullptr avec un void* non nullptr (commentée car erreur de compilation).\n");
    printf("Erreur attendue: 'error: passing 'int *' to parameter of type 'nullptr_t' is incompatible'\n");

    process_nullptr(nullptr); // Appel correct avec nullptr

    printf("\n--- Étape 6: Bonus / Exploration Libre ---\n");
    printf("--- Test de _Noreturn (C11) ---\n");
    // exit_program_error("Une erreur critique est survenue."); // Décommenter pour tester la fonction de sortie

    printf("--- Test de bool, true, false (C23) ---\n");
    bool my_bool = true;
    if (my_bool) {
        printf("my_bool est true.\n");
    }
    my_bool = false;
    if (!my_bool) {
        printf("my_bool est false.\n");
    }

    printf("--- Test de _BitInt(N) (C23) ---\n");
    // Nécessite un compilateur C23 (GCC 13+ ou Clang 16+ avec -std=c2x)
    _BitInt(24) small_int = 1234567; // Entier de 24 bits
    printf("Valeur de _BitInt(24) small_int: %d\n", (int)small_int);
    printf("Taille de _BitInt(24): %zu octets\n", sizeof(small_int));
    _BitInt(5) five_bit_int = 10; // Entier de 5 bits (ex: -16 à 15 pour signé)
    printf("Valeur de _BitInt(5) five_bit_int: %d\n", (int)five_bit_int);
    five_bit_int = 20; // Dépassement de capacité pour un entier signé de 5 bits
    printf("Valeur de _BitInt(5) five_bit_int après dépassement: %d\n", (int)five_bit_int);


    return 0;
}
```


## Explications des Concepts Clés (Solution 1)

*   **`_Generic` (C11)**: Ce sélecteur permet de choisir une expression (souvent un appel de fonction) en fonction du type de son premier argument. Il est utilisé dans les macros `LOG_VAL` et `LOG_VAL_V2` pour rediriger vers la fonction d'affichage appropriée. C'est une forme de polymorphisme au moment de la compilation, évitant les casts manuels et améliorant la sécurité des types.
*   **`typeof` (C23)**: Cette extension (standardisée en C23) permet d'obtenir le type d'une expression. Dans `LOG_VAL_V2`, `typeof` est implicitement utilisé par `_Generic` pour faire correspondre les types d'entiers et de flottants à des fonctions génériques (`_log_any_integer`, `_log_any_floating`) en leur passant le nom du type comme chaîne de caractères. Cela réduit la duplication de code pour des types similaires.
*   **`_VA_OPT_` (C23)**: Cette fonctionnalité de macro permet d'inclure ou d'exclure des arguments variadiques dans une macro en fonction de leur présence. La macro `LOG_MSG` l'utilise pour permettre des appels avec ou sans arguments supplémentaires, rendant la macro plus flexible et évitant les virgules superflues.
*   **`[[nodiscard]]` (C23)**: Cet attribut indique que la valeur de retour d'une fonction ne doit pas être ignorée. Le compilateur émettra un avertissement si l'appelant ne gère pas le résultat. Il est crucial pour les fonctions qui signalent un succès/échec ou un résultat important (comme `check_status`).
*   **`[[maybe_unused]]` (C23)**: Cet attribut supprime les avertissements du compilateur concernant les variables, paramètres ou fonctions qui pourraient ne pas être utilisés. Il est utile pour le débogage, les interfaces génériques ou le code conditionnel où certaines entités peuvent rester inutilisées.
*   **`static_assert` (C11)**: Permet de vérifier des conditions au moment de la compilation. Si la condition est fausse, la compilation échoue avec le message fourni. Il est essentiel pour valider des hypothèses sur l'environnement (taille des types, alignement) avant l'exécution, garantissant la portabilité et la robustesse du code.
*   **`_Alignof` et `_Alignas` (C11)**:
    *   `_Alignof` retourne l'alignement requis pour un type en octets. Il est utile pour comprendre comment les données sont organisées en mémoire.
    *   `_Alignas` permet de spécifier un alignement minimum pour une variable ou un membre de structure. Il est crucial pour l'optimisation des performances (accès mémoire plus rapide) ou pour l'interopérabilité avec du matériel ou des bibliothèques externes qui exigent un alignement spécifique.
*   **`nullptr` et `nullptr_t` (C23)**: C23 standardise `nullptr` comme un littéral de pointeur nul typé, et `nullptr_t` comme son type. Contrairement à `NULL` (qui est souvent un `int` ou `void*`), `nullptr` offre une meilleure sécurité de type, permettant au compilateur de détecter les erreurs de type à la compilation (comme tenter de passer `int*` à une fonction attendant `nullptr_t`).
*   **`[[noreturn]]` (C23) / `_Noreturn` (C11)**: Cet attribut (ou mot-clé) indique au compilateur qu'une fonction ne retourne jamais à son appelant (par exemple, elle termine le programme, lève une exception). Cela aide le compilateur à optimiser le code et à détecter les chemins de code inatteignables.
*   **`#pragma once` (C23)**: Standardisé en C23, c'est une directive de préprocesseur qui assure qu'un fichier d'en-tête n'est inclus qu'une seule fois dans une unité de compilation, remplaçant les gardes d'inclusion traditionnelles (`#ifndef/#define/#endif`).
*   **`bool`, `true`, `false` (C23)**: Bien que disponibles via `<stdbool.h>` depuis C99, C23 les standardise comme mots-clés intégrés, simplifiant l'utilisation des types booléens.
*   **`_BitInt(N)` (C23)**: Permet de déclarer des entiers avec une largeur de bits spécifique (par exemple, `_BitInt(24)` pour 24 bits). Cela est particulièrement utile dans les systèmes embarqués ou pour l'optimisation de la mémoire, où un contrôle précis de la taille des entiers est nécessaire.

---

# Solution 2 : Approche Combinée et Variantes

Cette solution explore une approche légèrement différente, notamment en combinant les étapes 1 et 3 pour `_Generic` et en démontrant d'autres fonctionnalités bonus.

## Fichier `logger.h`


```c
#ifndef LOGGER_H
#define LOGGER_H

#include <stddef.h>  // Pour size_t
#include <stdbool.h> // Pour bool, true, false (C23)
#include <stdio.h>   // Pour printf dans les macros

#pragma once // C23: Alternative standardisée aux gardes d'inclusion

// --- Étape 1 & 3: Fonctions de logging génériques (utilisées par _Generic et typeof) ---
// Fonctions pour gérer les types numériques (entiers signés/non signés, flottants)
void _log_integer_val(const char* type_name, long long val);
void _log_unsigned_integer_val(const char* type_name, unsigned long long val);
void _log_floating_val(const char* type_name, long double val);
// Fonctions pour les types spécifiques
void _log_string_val(const char* val);
void _log_pointer_val(const void* val);
// Fonction de fallback générique pour les types non gérés
void _log_unhandled_type_val(const char* type_name, const void* ptr);

// Macro LOG_ANY_VAL utilisant _Generic et typeof pour un affichage universel (C11/C23)
#define LOG_ANY_VAL(X) _Generic((X), \
    /* Entiers signés */ \
    signed char: _log_integer_val("signed char", (long long)(X)), \
    short: _log_integer_val("short", (long long)(X)), \
    int: _log_integer_val("int", (long long)(X)), \
    long: _log_integer_val("long", (long long)(X)), \
    long long: _log_integer_val("long long", (long long)(X)), \
    /* Entiers non signés */ \
    unsigned char: _log_unsigned_integer_val("unsigned char", (unsigned long long)(X)), \
    unsigned short: _log_unsigned_integer_val("unsigned short", (unsigned long long)(X)), \
    unsigned int: _log_unsigned_integer_val("unsigned int", (unsigned long long)(X)), \
    unsigned long: _log_unsigned_integer_val("unsigned long", (unsigned long long)(X)), \
    unsigned long long: _log_unsigned_integer_val("unsigned long long", (unsigned long long)(X)), \
    /* Flottants */ \
    float: _log_floating_val("float", (long double)(X)), \
    double: _log_floating_val("double", (long double)(X)), \
    long double: _log_floating_val("long double", (long double)(X)), \
    /* Autres types */ \
    const char*: _log_string_val, \
    char*: _log_string_val, \
    void*: _log_pointer_val, \
    const void*: _log_pointer_val, \
    default: _log_unhandled_type_val \
)("unknown type", (const void*)&(X)) // Pour le fallback, on passe un nom de type et l'adresse de la variable

// --- Étape 3: _VA_OPT_ pour plus de flexibilité (C23) ---
// Macro LOG_MSG utilisant _VA_OPT_ pour gérer les arguments variadiques
#define LOG_MSG(level, format, ...) \
    printf("[%s] " format _VA_OPT_(, __VA_ARGS__) "\n", level)

// --- Étape 4: Assertions et alignement (C11) ---
// Macro pour static_assert avec message (C11)
#define STATIC_ASSERT_C11(cond, msg) _Static_assert(cond, msg)

// Vérifications au moment de la compilation avec des conditions différentes
STATIC_ASSERT_C11(sizeof(int) >= 2, "La taille d'un int est trop petite pour les systèmes modernes.");
STATIC_ASSERT_C11(sizeof(long) >= sizeof(int), "La taille de long est inférieure à celle de int, ce qui est inhabituel.");

// --- Étape 2: Attributs (C23) ---
// Fonction avec l'attribut [[nodiscard]]
[[nodiscard]] int perform_operation(int input);

// --- Étape 5: nullptr et nullptr_t (C23) ---
// Fonctions pour démontrer l'utilisation de nullptr et nullptr_t
void handle_ptr_or_nullptr(void* ptr);
void handle_only_nullptr(nullptr_t np);

// --- Étape 6: Bonus - [[fallthrough]] (C17) ---
// Fonction pour démontrer l'utilisation de [[fallthrough]] dans un switch
void process_state(int state);

#endif // LOGGER_H
```


## Fichier `logger.c`


```c
#include "logger.h"
#include <stdio.h>
#include <stdlib.h>   // Pour exit()
#include <stdalign.h> // Pour _Alignof, _Alignas (C11)

// --- Étape 1 & 3: Fonctions de logging génériques (utilisées par _Generic et typeof) ---
// Implémentations des fonctions génériques pour les types numériques
void _log_integer_val(const char* type_name, long long val) {
    printf("[INT_GENERIC] Type: %s, Valeur: %lld\n", type_name, val);
}
void _log_unsigned_integer_val(const char* type_name, unsigned long long val) {
    printf("[UINT_GENERIC] Type: %s, Valeur: %llu\n", type_name, val);
}
void _log_floating_val(const char* type_name, long double val) {
    printf("[FLT_GENERIC] Type: %s, Valeur: %Lf\n", type_name, val);
}
// Implémentations des fonctions pour les types spécifiques
void _log_string_val(const char* val) {
    printf("[STR] \"%s\"\n", val);
}
void _log_pointer_val(const void* val) {
    printf("[PTR] %p\n", val);
}
// Implémentation de la fonction de fallback générique
void _log_unhandled_type_val(const char* type_name, const void* ptr) {
    printf("[UNHANDLED_GENERIC] Type '%s' non géré. Adresse: %p\n", type_name, ptr);
}

// --- Étape 2: Attributs (C23) ---
// Implémentation de la fonction avec [[nodiscard]]
[[nodiscard]] int perform_operation(int input) {
    printf("perform_operation appelé avec input: %d\n", input);
    return input * 2;
}

// --- Étape 5: nullptr et nullptr_t (C23) ---
// Implémentations des fonctions pour nullptr
void handle_ptr_or_nullptr(void* ptr) {
    if (ptr == nullptr) {
        printf("handle_ptr_or_nullptr: Le pointeur est nullptr.\n");
    } else {
        printf("handle_ptr_or_nullptr: Le pointeur n'est pas nullptr: %p\n", ptr);
    }
}

void handle_only_nullptr(nullptr_t np) {
    // nullptr_t est un type distinct, il ne peut être que nullptr.
    printf("handle_only_nullptr: Reçu un nullptr_t.\n");
    (void)np; // Supprime l'avertissement si np n'est pas utilisé
}

// --- Étape 6: Bonus - [[fallthrough]] (C17) ---
// Implémentation de la fonction avec [[fallthrough]]
void process_state(int state) {
    printf("Traitement de l'état %d:\n", state);
    switch (state) {
        case 1:
            printf("  État 1: Initialisation.\n");
            [[fallthrough]]; // C17: Indique une chute intentionnelle entre les cases
        case 2:
            printf("  État 2: Exécution normale.\n");
            break;
        case 3:
            printf("  État 3: Finalisation.\n");
            break;
        default:
            printf("  État inconnu.\n");
            break;
    }
}
```


## Fichier `main.c`


```c
#include "logger.h"
#include <stdio.h>
#include <stdlib.h>   // Pour NULL
#include <stdalign.h> // Pour _Alignof, _Alignas (C11)

// --- Étape 4: Assertions et alignement (C11) ---
// Structure simple pour tester l'alignement
typedef struct {
    char a;
    short b;
    long c;
} ComplexData;

// Structure avec alignement forcé à 64 octets pour la structure et 32 octets pour 'b'
typedef struct _Alignas(64) {
    char a;
    _Alignas(32) long long b;
    double c;
} SuperAlignedData;

int main() {
    printf("--- Étape 1 & 3: _Generic et typeof pour un affichage générique (C11/C23) ---\n");
    int i_val = 100;
    unsigned long ul_val = 200000UL;
    double d_val = 123.456;
    const char* s_val = "Message générique";
    void* p_val = &i_val;
    float f_val = 7.89f;
    _BitInt(12) bit_val = 4000; // C23 _BitInt, entier de 12 bits

    LOG_ANY_VAL(i_val);
    LOG_ANY_VAL(ul_val);
    LOG_ANY_VAL(d_val);
    LOG_ANY_VAL(s_val);
    LOG_ANY_VAL(p_val);
    LOG_ANY_VAL(f_val);
    LOG_ANY_VAL(bit_val); // Test de _BitInt avec LOG_ANY_VAL

    // Test d'un type non géré (une structure)
    ComplexData my_complex_data = {'X', 10, 100000L};
    LOG_ANY_VAL(my_complex_data);

    printf("\n--- Étape 3: LOG_MSG avec _VA_OPT_ (C23) ---\n");
    LOG_MSG("DEBUG", "Démarrage de l'application.");
    LOG_MSG("WARNING", "Mémoire faible: %d%% utilisée.", 85);
    LOG_MSG("CRITICAL", "Erreur système %d: %s", 101, "Disque plein");

    printf("\n--- Étape 2: Attributs [[nodiscard]] et [[maybe_unused]] (C23) ---\n");
    printf("--- Test de [[nodiscard]] ---\n");
    perform_operation(5); // Avertissement attendu car la valeur de retour est ignorée
    int result = perform_operation(10);
    printf("Résultat de l'opération: %d\n", result);

    printf("\n--- Test de [[maybe_unused]] ---\n");
    [[maybe_unused]] int local_unused_var = 99; // Cette variable ne sera pas utilisée
    // Le compilateur ne devrait pas générer d'avertissement pour local_unused_var

    printf("\n--- Étape 4: Assertions et alignement (C11) ---\n");
    printf("--- Test de _Alignof ---\n");
    printf("Alignement de ComplexData: %zu\n", _Alignof(ComplexData));
    printf("Alignement de ComplexData.a: %zu\n", _Alignof(ComplexData.a));
    printf("Alignement de ComplexData.b: %zu\n", _Alignof(ComplexData.b));
    printf("Alignement de ComplexData.c: %zu\n", _Alignof(ComplexData.c));

    printf("\n--- Test de _Alignas ---\n");
    printf("Taille de ComplexData: %zu octets\n", sizeof(ComplexData));
    printf("Alignement de ComplexData: %zu octets\n", _Alignof(ComplexData));
    printf("Taille de SuperAlignedData: %zu octets\n", sizeof(SuperAlignedData));
    printf("Alignement de SuperAlignedData: %zu octets\n", _Alignof(SuperAlignedData));
    printf("Alignement de SuperAlignedData.a: %zu octets\n", _Alignof(SuperAlignedData.a));
    printf("Alignement de SuperAlignedData.b: %zu octets\n", _Alignof(SuperAlignedData.b));
    printf("Alignement de SuperAlignedData.c: %zu octets\n", _Alignof(SuperAlignedData.c));

    printf("\n--- Étape 5: nullptr et nullptr_t (C23) ---\n");
    void* null_ptr_void = nullptr; // Utilisation de nullptr (C23)
    handle_ptr_or_nullptr(null_ptr_void);
    handle_ptr_or_nullptr(&d_val);

    // Tentative d'appeler handle_only_nullptr avec un void* non nullptr (erreur de compilation attendue)
    // handle_only_nullptr(&i_val);
    printf("Tentative d'appeler handle_only_nullptr avec un void* non nullptr (commentée car erreur de compilation).\n");
    printf("Erreur attendue: 'error: passing 'int *' to parameter of type 'nullptr_t' is incompatible'\n");

    handle_only_nullptr(nullptr); // Appel correct avec nullptr

    printf("\n--- Étape 6: Bonus / Exploration Libre ---\n");
    printf("--- Test de [[fallthrough]] (C17) ---\n");
    process_state(1); // Exécute les cases 1 et 2
    process_state(2); // Exécute la case 2
    process_state(3); // Exécute la case 3
    process_state(99); // Exécute le default

    printf("\n--- Test de _BitInt(N) (C23) ---\n");
    _BitInt(3) three_bit_int = 5; // Entier de 3 bits (ex: -4 à 3 pour signé)
    printf("Valeur de _BitInt(3) three_bit_int: %d\n", (int)three_bit_int);
    printf("Taille de _BitInt(3): %zu octets\n", sizeof(three_bit_int));
    three_bit_int = 10; // Dépassement de capacité pour un entier signé de 3 bits
    printf("Valeur de _BitInt(3) three_bit_int après dépassement: %d\n", (int)three_bit_int);

    return 0;
}
```


## Explications des Concepts Clés (Solution 2)

*   **`_Generic` (C11) et `typeof` (C23)**: Dans cette solution, `_Generic` est utilisé de manière plus consolidée via la macro `LOG_ANY_VAL`. Au lieu de fonctions `_log_int`, `_log_double`, etc., elle mappe directement tous les types numériques (signés, non signés, flottants) à des fonctions génériques (`_log_integer_val`, `_log_unsigned_integer_val`, `_log_floating_val`). `typeof` est implicitement utilisé par `_Generic` pour identifier le type de `X` et passer son nom comme chaîne de caractères, rendant le système de logging très flexible et extensible sans modifier la macro `LOG_ANY_VAL`.
*   **`_VA_OPT_` (C23)**: La macro `LOG_MSG` utilise `_VA_OPT_` pour gérer la présence d'arguments variadiques. Cela permet d'appeler la macro avec ou sans arguments supplémentaires pour le formatage, évitant les problèmes de virgules conditionnelles dans les macros.
*   **`[[nodiscard]]` (C23)**: L'attribut est appliqué à `perform_operation` pour signaler que sa valeur de retour est importante et ne doit pas être ignorée. Le compilateur émettra un avertissement si l'appelant ne traite pas le résultat.
*   **`[[maybe_unused]]` (C23)**: Utilisé pour supprimer les avertissements du compilateur pour les entités (variables, fonctions, paramètres) qui sont déclarées mais potentiellement non utilisées, ce qui est courant dans le code générique ou de débogage.
*   **`static_assert` (C11)**: Des assertions de compilation sont utilisées pour vérifier des propriétés fondamentales du système, comme la taille minimale d'un `int` ou la relation de taille entre `int` et `long`. Cela aide à garantir la portabilité et la conformité aux attentes du code.
*   **`_Alignof` et `_Alignas` (C11)**: Ces opérateurs sont utilisés pour inspecter et contrôler l'alignement des structures et de leurs membres. `_Alignof` révèle les exigences d'alignement par défaut, tandis que `_Alignas` permet de forcer un alignement plus strict, ce qui peut être crucial pour l'optimisation des performances ou l'interfaçage avec le matériel.
*   **`nullptr` et `nullptr_t` (C23)**: `nullptr` est utilisé comme un littéral de pointeur nul typé, et `nullptr_t` comme son type. Cette standardisation apporte une sécurité de type accrue par rapport à `NULL`, permettant au compilateur de détecter les erreurs d'affectation ou de comparaison de pointeurs nuls à la compilation.
*   **`[[fallthrough]]` (C17)**: Cet attribut est utilisé dans les instructions `switch` pour indiquer explicitement qu'une "chute" intentionnelle d'une `case` à la suivante est prévue. Cela supprime les avertissements du compilateur qui signaleraient normalement une `case` sans `break`, améliorant la clarté et la maintenabilité du code.
*   **`_BitInt(N)` (C23)**: Cette fonctionnalité permet de déclarer des entiers avec un nombre de bits arbitraire (par exemple, `_BitInt(3)` pour 3 bits). Elle est particulièrement utile pour la programmation de bas niveau, l'optimisation de la mémoire ou l'interfaçage avec des protocoles qui utilisent des champs de bits de taille non standard.
*   **`#pragma once` (C23)**: Utilisé comme garde d'inclusion standardisée, il assure que le contenu d'un fichier d'en-tête n'est traité qu'une seule fois par unité de compilation, simplifiant la gestion des dépendances.

---
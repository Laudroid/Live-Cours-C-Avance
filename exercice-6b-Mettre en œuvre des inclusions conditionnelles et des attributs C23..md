
## **TP: Un Logger Multi-Plateforme**

**Objectif du TP :**
Maîtriser l'utilisation des inclusions conditionnelles pour l'adaptation multi-plateforme et l'application des attributs C23 pour améliorer la qualité et la maintenabilité du code.

**Contexte :**
Dans le développement logiciel moderne, la portabilité et la robustesse du code sont primordiales. Un bon logger est un outil essentiel pour le débogage et la surveillance. Nous allons créer un logger simple capable de fonctionner sur Windows et les systèmes de type POSIX (Linux, macOS), en tirant parti des directives de préprocesseur. Parallèlement, nous explorerons comment les nouveaux attributs du standard C23 nous aident à exprimer nos intentions de code et à détecter des erreurs potentielles dès la compilation.

**Prérequis :**
*   Bonnes bases en C (gestion de fichiers, structures, pointeurs, fonctions).
*   Connaissance des directives de préprocesseur (`#define`, `#ifdef`, `#ifndef`, `#endif`).
*   Notions de compilation (utilisation de `gcc` ou `clang`).

**Environnement de Travail :**
*   Un compilateur C supportant le standard C23 (par exemple, GCC 13+ ou Clang 16+).
*   Un système d'exploitation Windows ou un système de type POSIX (Linux, macOS).
*   Un éditeur de texte ou un IDE de votre choix.

---

**Consignes Générales :**

*   Créez un projet avec au moins deux fichiers : `logger.h` (pour les déclarations) et `logger.c` (pour les implémentations). Un `main.c` servira à tester votre logger.
*   Compilez votre code avec l'option `-std=c23` (ex: `gcc -std=c23 main.c logger.c -o my_logger`).
*   N'hésitez pas à solliciter les outils d'IA pour vous aider à générer des ébauches de code ou à comprendre des concepts spécifiques. L'objectif est d'apprendre et de comprendre, pas de réinventer la roue !

---

### **Exercices**

#### **Partie 1 : Le Cœur du Logger (Base Fonctionnelle)**

1.  **Définition des Niveaux de Log :**
    *   Dans `logger.h`, définissez une énumération `LogLevel` pour représenter les différents niveaux de journalisation : `LOG_INFO`, `LOG_WARNING`, `LOG_ERROR`, `LOG_DEBUG`.
    *   Ajoutez une fonction `const char* log_level_to_string(LogLevel level);` qui retourne la chaîne de caractères correspondante (ex: "INFO", "WARNING").

2.  **Initialisation et Fermeture du Logger :**
    *   Déclarez une fonction `int init_logger(const char* filename);` qui initialise le logger. Elle devrait ouvrir un fichier spécifié pour y écrire les logs. En cas d'échec (fichier non ouvrable), elle retournera une valeur non nulle.
    *   Déclarez une fonction `void close_logger(void);` qui ferme proprement le fichier de log.

3.  **Fonction de Log Générique :**
    *   Déclarez une fonction `int log_message(LogLevel level, const char* format, ...);` qui prend un niveau de log, une chaîne de format et des arguments variables (comme `printf`). Cette fonction écrira le message formaté dans le fichier de log. Elle retournera 0 en cas de succès, une valeur non nulle en cas d'échec.

4.  **Test Initial :**
    *   Dans `main.c`, initialisez le logger, écrivez quelques messages de différents niveaux, puis fermez le logger. Vérifiez que le fichier de log est créé et contient les messages.

#### **Partie 2 : Adaptation Multi-Plateforme (Horodatage)**

Le format d'horodatage varie souvent entre Windows et les systèmes POSIX. Nous allons gérer cela avec des inclusions conditionnelles.

1.  **Fonction d'Horodatage Spécifique :**
    *   Dans `logger.c`, implémentez une fonction `void get_current_timestamp_string(char* buffer, size_t buffer_size);`. Cette fonction doit remplir `buffer` avec l'horodatage actuel au format `AAAA-MM-JJ HH:MM:SS`.
    *   Utilisez les directives de préprocesseur (`#ifdef _WIN32`, `#else`, `#endif`) pour adapter l'implémentation :
        *   **Pour Windows (`_WIN32` est défini) :** Utilisez les fonctions de l'API Windows comme `GetLocalTime` et `sprintf`. N'oubliez pas d'inclure `windows.h`.
        *   **Pour POSIX (Linux, macOS) :** Utilisez les fonctions standards C comme `time`, `localtime` et `strftime`. N'oubliez pas d'inclure `time.h`.

2.  **Intégration dans le Logger :**
    *   Modifiez la fonction `log_message` pour qu'elle préfixe chaque message de log avec l'horodatage obtenu via `get_current_timestamp_string`. Le format dans le fichier de log devrait être `[AAAA-MM-JJ HH:MM:SS] [NIVEAU] Message`.

3.  **Test Multi-Plateforme :**
    *   Compilez et testez votre logger sur au moins deux plateformes différentes (si possible, Windows et Linux/macOS). Vérifiez que l'horodatage est correctement généré sur chaque système.

#### **Partie 3 : Enrichissement avec les Attributs C23**

Les attributs C23 permettent d'ajouter des métadonnées au code, aidant le compilateur et les outils d'analyse statique à détecter des problèmes ou à optimiser.

1.  **`[[nodiscard]]` : Vérification des Retours de Fonction**
    *   Appliquez l'attribut `[[nodiscard]]` aux déclarations des fonctions `init_logger` et `log_message` dans `logger.h`.
    *   Modifiez `main.c` pour *ne pas* vérifier la valeur de retour de `init_logger` ou `log_message` à un endroit. Compilez. Que se passe-t-il ? Le compilateur devrait émettre un avertissement. Corrigez `main.c` pour toujours vérifier ces retours.

2.  **`[[deprecated]]` : Marquer une Fonction Obsolète**
    *   Créez une nouvelle fonction `int log_simple_message(const char* message);` dans `logger.c` et `logger.h`. Cette fonction se contente d'écrire un message sans niveau ni formatage complexe.
    *   Appliquez l'attribut `[[deprecated("Utiliser log_message() à la place")]]` à la déclaration de `log_simple_message` dans `logger.h`.
    *   Dans `main.c`, utilisez `log_simple_message` une fois. Compilez. Que se passe-t-il ? Le compilateur devrait vous avertir que vous utilisez une fonction dépréciée.

3.  **`[[maybe_unused]]` : Indiquer une Utilisation Conditionnelle**
    *   Modifiez la fonction `init_logger` pour qu'elle prenne un nouveau paramètre `int debug_mode`. Ce paramètre ne sera utilisé que si une macro `DEBUG_BUILD` est définie.
    *   Appliquez l'attribut `[[maybe_unused]]` à ce paramètre `debug_mode` dans la déclaration de `init_logger` dans `logger.h`.
    *   Compilez votre code sans définir `DEBUG_BUILD`. Le compilateur ne devrait pas émettre d'avertissement concernant un paramètre non utilisé.

4.  **`[[unreachable]]` : Indiquer un Code Inaccessible**
    *   Dans `logger.c`, dans la fonction `log_level_to_string`, ajoutez un `default` à votre `switch` sur `LogLevel`. Dans ce `default`, placez l'attribut `[[unreachable]]` après un message d'erreur (par exemple, `fprintf(stderr, "Erreur: Niveau de log inconnu!\n");`).
    *   L'idée est que, logiquement, tous les niveaux de `LogLevel` devraient être gérés. Si le code atteint le `default`, c'est une erreur de programmation grave. Le compilateur peut utiliser `[[unreachable]]` pour des optimisations ou des diagnostics.

#### **Partie 4 : Réflexion et Améliorations (Optionnel mais Recommandé)**

1.  **Gestion des Erreurs :** Comment pourriez-vous améliorer la gestion des erreurs dans votre logger (par exemple, si l'écriture dans le fichier échoue) ?
2.  **Macros de Log :** Pour simplifier l'utilisation, comment pourriez-vous créer des macros comme `LOG_INFO("Mon message %d", 42);` qui appellent automatiquement `log_message` avec le bon niveau ?
3.  **Plus de Plateformes :** Si vous deviez supporter une autre plateforme (ex: un OS embarqué), comment adapteriez-vous votre code ?
4.  **Attributs C23 :** Quels autres attributs C23 pourraient être pertinents pour un logger ou un autre type de bibliothèque ? (Recherchez `[[fallthrough]]`, `[[assume]]`).

---

**Ressources Utiles :**

*   Documentation de votre compilateur (GCC, Clang) sur les options C23.
*   Références sur les attributs C23 (par exemple, cppreference.com).
*   Documentation de l'API Windows pour `GetLocalTime`.
*   Documentation des fonctions `time.h` pour POSIX.

Amusez-vous bien avec ce TP ! C'est une excellente occasion de voir comment le C moderne nous aide à écrire du code plus sûr et plus portable.
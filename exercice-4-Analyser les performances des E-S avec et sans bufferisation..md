Bonjour à toutes et à tous,

Ce TP est une opportunité d'explorer en profondeur un aspect fondamental de la programmation système en C : la gestion des entrées/sorties (E/S) et l'impact crucial de la bufferisation sur les performances. Comprendre ces mécanismes vous permettra d'écrire des applications plus efficaces et de diagnostiquer des goulots d'étranglement liés aux E/S.

---

### **TP: Mesure et Analyse des Performances d'E/S : Bufferisation vs. Non-Bufferisation**

**Objectif du TP :**
*   Distinguer les mécanismes d'E/S de bas niveau (appels système) des E/S de la bibliothèque standard C.
*   Mesurer et comparer l'impact de la bufferisation (implicite et explicite) sur les performances des opérations de lecture/écriture de fichiers.
*   Développer une compréhension des compromis et des cas d'usage pour chaque approche.

**Contexte :**
Les opérations d'entrée/sortie sont souvent le facteur limitant dans la performance d'une application. Le langage C offre deux grandes familles d'API pour interagir avec les fichiers :
1.  **Les appels système de bas niveau** (`open`, `read`, `write`, `close`) qui interagissent directement avec le noyau du système d'exploitation. Par défaut, ces opérations sont souvent considérées comme "non-bufferisées" du point de vue de l'application, bien que le noyau utilise ses propres caches.
2.  **Les fonctions de la bibliothèque standard C** (`fopen`, `fread`, `fwrite`, `fclose`) qui ajoutent une couche de bufferisation en espace utilisateur par-dessus les appels système.

La manière dont ces buffers sont gérés peut avoir un impact spectaculaire sur le nombre d'appels système effectués et, par conséquent, sur le temps d'exécution.

---

**Mini-Projet : Implémentation d'un utilitaire de copie de fichier**

Vous allez développer deux versions d'un programme de copie de fichier (`cp`) : une utilisant des E/S non-bufferisées et l'autre des E/S bufferisées. L'objectif est de mesurer et d'analyser leurs performances respectives lors de la copie d'un fichier de taille significative.

---

**Partie 1 : Copie de fichier sans bufferisation (Appels système)**

1.  **Implémentation :**
    *   Créez un programme `mycp_unbuffered.c`. Ce programme prendra deux arguments en ligne de commande : le chemin du fichier source et le chemin du fichier de destination.
    *   Utilisez les appels système `open()`, `read()`, `write()` et `close()` pour réaliser la copie.
    *   La lecture et l'écriture devront s'effectuer par blocs. Vous définirez une taille de bloc (`BUFFER_SIZE`) qui sera une variable d'expérimentation.
    *   Assurez une gestion robuste des erreurs (vérification des retours d'appels système, affichage de messages d'erreur pertinents).

2.  **Mesure des performances :**
    *   Intégrez un mécanisme de mesure du temps d'exécution dans votre programme. La fonction `clock_gettime()` avec `CLOCK_MONOTONIC` est recommandée pour une précision élevée.
    *   Testez votre programme avec différentes tailles de blocs de lecture/écriture. Commencez par 1 octet, puis 64 octets, 512 octets, 4096 octets, 65536 octets, et pourquoi pas 1 Mo.
    *   Utilisez un fichier source de taille conséquente pour vos tests (par exemple, un fichier de 50 Mo à 500 Mo). Vous pouvez en générer un avec `dd if=/dev/urandom of=test_file bs=1M count=X` (remplacez X par la taille en Mo souhaitée).

---

**Partie 2 : Copie de fichier avec bufferisation (Bibliothèque standard C)**

1.  **Implémentation :**
    *   Créez un programme `mycp_buffered.c` avec la même interface en ligne de commande que le précédent.
    *   Utilisez les fonctions de la bibliothèque standard C `fopen()`, `fread()`, `fwrite()`, `fclose()` pour effectuer la copie.
    *   Comme pour la partie 1, la lecture et l'écriture devront s'effectuer par blocs de taille variable (`BUFFER_SIZE`).
    *   Gérez les erreurs potentielles (vérification des retours de fonctions, affichage de messages d'erreur).

2.  **Mesure des performances :**
    *   Intégrez le même mécanisme de mesure du temps d'exécution (`clock_gettime()`).
    *   Testez votre programme avec les mêmes tailles de blocs de lecture/écriture que pour la partie 1.
    *   Utilisez le même fichier source de taille conséquente.
    *   **Optionnel mais recommandé :** Expérimentez avec `setvbuf()` pour modifier le type et la taille du buffer utilisé par `fopen`. Essayez `_IONBF` (non-bufferisé), `_IOLBF` (ligne-bufferisé) et `_IOFBF` (full-bufferisé) avec différentes tailles.

---

**Partie 3 : Analyse et Comparaison**

1.  **Collecte des résultats :**
    *   Compilez un tableau récapitulatif clair des temps d'exécution pour chaque programme (`mycp_unbuffered`, `mycp_buffered`) et pour chaque taille de bloc testée.
    *   Indiquez le système d'exploitation et les spécifications matérielles de votre machine de test (processeur, RAM, type de disque - SSD/HDD).

2.  **Discussion :**
    *   Comparez les performances des deux approches. Quelles sont les différences majeures observées ?
    *   Comment la taille du bloc de lecture/écriture influence-t-elle les performances dans chaque cas ? Expliquez pourquoi.
    *   Expliquez le rôle de la bufferisation (au niveau de la bibliothèque C et au niveau du noyau) dans les résultats observés.
    *   Dans quelles situations privilégieriez-vous les appels système de bas niveau ? Et les fonctions de la bibliothèque standard ? Justifiez vos réponses.
    *   Quels autres facteurs pourraient influencer les performances des E/S (type de disque, fragmentation, cache système, charge du système, etc.) ?

---

**Conseils et Bonnes Pratiques :**

*   **Précision de la mesure :** Pour des résultats fiables, exécutez chaque test plusieurs fois (par exemple, 5 à 10 fois) et calculez une moyenne. Cela aidera à lisser les variations dues à l'activité du système.
*   **Fichier de test :** Un fichier trop petit ne révélera pas les différences de performance. Visez au moins plusieurs dizaines de mégaoctets.
*   **Nettoyage :** Pensez à supprimer les fichiers de destination après chaque test ou à les renommer pour éviter toute interférence entre les exécutions.
*   **Utilisation de l'IA :** N'hésitez pas à solliciter les outils d'IA pour des points de syntaxe, des rappels sur les prototypes de fonctions (`man 2 open`, `man 3 fopen`), ou des explications initiales sur les concepts. Cependant, l'analyse critique des résultats, la compréhension des mécanismes sous-jacents et la rédaction de vos conclusions sont votre valeur ajoutée et le cœur de cet exercice. C'est là que votre intelligence et votre esprit d'analyse feront la différence.

---

**Livraison :**

*   Les fichiers source `mycp_unbuffered.c` et `mycp_buffered.c`.
*   Un rapport (format Markdown ou PDF) incluant :
    *   Le tableau récapitulatif de vos mesures de performances.
    *   Votre analyse détaillée des résultats et vos réponses aux questions de la Partie 3.
    *   Toute observation pertinente ou difficulté rencontrée.

Bon courage pour ce TP, j'ai hâte de découvrir vos analyses !
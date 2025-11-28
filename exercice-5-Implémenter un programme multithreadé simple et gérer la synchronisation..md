Bonjour à toutes et à tous,

Ce TP vous invite à explorer la programmation multithreadée en C, en mettant l'accent sur la gestion des accès concurrents aux ressources partagées. Nous allons simuler un système où plusieurs "travailleurs" (threads) traitent des "tâches" (données) issues d'une file d'attente commune, tout en accumulant un résultat global.

---

### **TP: Gestion de Tâches Concurrentes avec Mutex en C**

**Objectif du TP :** Implémenter un programme multithreadé simple et gérer la synchronisation des accès aux ressources partagées à l'aide de mutex.

---

#### **Contexte du Mini-Projet : Le Centre de Traitement Numérique**

Imaginez un "Centre de Traitement Numérique" où des requêtes arrivent en continu. Pour optimiser le traitement, nous allons déployer plusieurs "unités de traitement" (threads) qui piocheront des requêtes dans une file d'attente partagée.

Chaque unité traitera une requête, simulera un temps de travail, puis ajoutera la valeur de la requête à un "total général" des requêtes traitées.

L'enjeu est double :
1.  Garantir que le total général soit correct, même si plusieurs unités tentent de l'actualiser simultanément.
2.  Assurer que chaque requête de la file d'attente soit traitée exactement une fois.

#### **Description Détaillée des Tâches**

Votre programme devra être un exécutable unique en C.

**Étape 1: La Structure de Base**

1.  **Définir les données :**
    *   Un tableau statique d'entiers (`int tasks[]`) pour simuler la file d'attente des tâches. Initialisez-le avec une vingtaine de valeurs arbitraires (ex: `10, 20, 5, 15, 30, ...`).
    *   Un entier global (`int task_index = 0;`) pour suivre l'index de la prochaine tâche à prendre dans le tableau.
    *   Une variable globale `long long total_processed_value = 0;` pour accumuler la somme des valeurs des tâches traitées. Utilisez `long long` pour éviter les débordements si les valeurs sont grandes ou nombreuses.

**Étape 2: Les Mutex**

1.  **Identifier les ressources partagées :** Quelles variables globales seront accédées et modifiées par plusieurs threads ? (Indice : `task_index` et `total_processed_value`).
2.  **Déclarer les mutex :** Déclarez les variables de type `pthread_mutex_t` nécessaires pour protéger ces ressources.
3.  **Initialiser et détruire les mutex :**
    *   Dans la fonction `main`, initialisez vos mutex avant la création des threads.
    *   Dans la fonction `main`, détruisez vos mutex après que tous les threads aient terminé.

**Étape 3: La Fonction de Traitement des Tâches (Thread Worker)**

1.  **Créer la fonction `void* worker_function(void* arg)` :** Cette fonction sera exécutée par chaque thread.
2.  **Logique du travailleur :**
    *   Chaque thread doit boucler tant qu'il y a des tâches disponibles dans la file d'attente.
    *   **Acquisition de tâche :**
        *   Avant d'accéder à `task_index` pour récupérer une tâche, le thread doit **acquérir le mutex** protégeant l'accès à la file d'attente.
        *   Vérifier si `task_index` est inférieur à la taille totale du tableau de tâches. Si oui, récupérer la valeur de `tasks[task_index]` et incrémenter `task_index`.
        *   **Relâcher le mutex** immédiatement après avoir récupéré la tâche et mis à jour l'index.
        *   Si toutes les tâches ont été prises, le thread doit sortir de sa boucle.
    *   **Simulation de travail :** Après avoir récupéré une tâche, simulez un temps de traitement en utilisant `usleep(rand() % 100000);` (pour des microsecondes, incluez `<unistd.h>`). Cela simule un travail réel et augmente les chances de conditions de concurrence.
    *   **Mise à jour du total :**
        *   Avant d'ajouter la valeur de la tâche au `total_processed_value`, le thread doit **acquérir le mutex** protégeant cette variable.
        *   Ajouter la valeur de la tâche au `total_processed_value`.
        *   **Relâcher le mutex** immédiatement après la mise à jour.
    *   La fonction doit retourner `NULL` à la fin.

**Étape 4: Le Programme Principal (`main`)**

1.  **Définir le nombre de threads :** Choisissez un nombre de threads (ex: 4 ou 8) à créer.
2.  **Créer les threads :** Utilisez `pthread_create` pour lancer vos threads, en leur passant la fonction `worker_function`. Stockez les identifiants de thread (`pthread_t`) dans un tableau.
3.  **Attendre la fin des threads :** Utilisez `pthread_join` pour attendre que tous les threads aient terminé leur exécution.
4.  **Afficher le résultat :** Une fois tous les threads terminés, affichez la valeur finale de `total_processed_value`.
5.  **Vérification :** Calculez manuellement la somme de toutes les tâches initiales et comparez-la avec le `total_processed_value` affiché. Ils doivent être identiques.

---

#### **Points d'Attention et Pistes de Réflexion**

*   **Testez sans mutex au début :** Commentez temporairement les appels à `pthread_mutex_lock` et `pthread_mutex_unlock`. Exécutez le programme plusieurs fois. Observez le résultat final de `total_processed_value`. Est-il toujours correct ? Pourquoi ou pourquoi pas ? Cela vous aidera à comprendre concrètement le problème des conditions de concurrence.
*   **Granularité des mutex :** Avez-vous utilisé un seul mutex pour tout, ou plusieurs mutex pour protéger des ressources distinctes ? Réfléchissez aux avantages et inconvénients de chaque approche en termes de performance et de complexité.
*   **Deadlock :** Dans ce scénario simple, le risque de deadlock est faible. Cependant, imaginez un cas où un thread aurait besoin d'acquérir deux mutex dans un ordre spécifique, et un autre thread les acquerrait dans l'ordre inverse. Comment cela pourrait-il mener à un deadlock ?
*   **Utilisation de l'IA :** N'hésitez pas à utiliser l'IA pour générer des bouts de code, des explications sur les fonctions `pthread_*`, ou pour vous aider à débugger. Cependant, l'objectif de ce TP est de *comprendre* et d'*assembler* ces éléments correctement. L'IA peut vous donner les briques, mais c'est à vous de construire la maison et de vous assurer qu'elle tient debout et que vous en maîtrisez l'architecture. La phase de test sans mutex est particulièrement importante pour valider votre compréhension, au-delà de la simple génération de code.

---

#### **Ressources Utiles**

*   Fonctions de la bibliothèque `pthread` : `pthread_create`, `pthread_join`, `pthread_mutex_init`, `pthread_mutex_lock`, `pthread_mutex_unlock`, `pthread_mutex_destroy`.
*   `usleep()` pour simuler un temps de travail (nécessite `<unistd.h>`).
*   Compilation : `gcc -o tp_threads tp_threads.c -pthread`

Bon courage pour ce TP ! N'hésitez pas à poser des questions si vous rencontrez des difficultés. C'est en expérimentant que l'on apprend le mieux ces concepts fondamentaux.
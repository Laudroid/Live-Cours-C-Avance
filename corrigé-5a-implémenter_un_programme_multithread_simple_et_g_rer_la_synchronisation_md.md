Voici deux solutions complètes pour le TP "Gestion de Tâches Concurrentes avec Mutex en C11", chacune présentée dans un chapitre Markdown distinct. Chaque solution inclut le code C11 portable et des explications détaillées, ainsi que les réponses aux points de réflexion.

---

# Solution 1 : Deux Mutex Distincts pour une Granularité Fine

Cette solution utilise deux mutex C11 distincts : un pour protéger l'accès à l'index des tâches (`task_index`) et un autre pour protéger l'accès à la somme totale (`total_processed_value`). Cette approche offre une granularité de verrouillage plus fine, améliorant ainsi le parallélisme.

## Fichier `tp_threads_s1.c`

```c
#include <stdio.h>    // Pour printf, fprintf
#include <stdlib.h>   // Pour EXIT_SUCCESS, EXIT_FAILURE, rand, srand
#include <threads.h>  // Pour thrd_*, mtx_* (C11)
#include <time.h>     // Pour time, timespec

// --- Étape 1: La Structure de Base ---

// Tableau statique simulant la file d'attente des tâches
#define NUM_TASKS 20
int tasks[NUM_TASKS] = {10, 20, 5, 15, 30, 25, 12, 8, 40, 18,
                        22, 7, 35, 11, 28, 14, 3, 45, 19, 27};

// Index de la prochaine tâche à prendre
int task_index = 0;

// Variable globale pour accumuler la somme des valeurs des tâches traitées
long long total_processed_value = 0;

// --- Étape 2: Les Mutex C11 ---

// Mutex pour protéger l'accès à task_index (ressource partagée)
mtx_t task_index_mutex;

// Mutex pour protéger l'accès à total_processed_value (ressource partagée)
mtx_t total_value_mutex;

// --- Étape 3: La Fonction de Traitement des Tâches (Thread Worker C11) ---

/**
 * @brief Fonction exécutée par chaque thread travailleur en C11.
 *        Récupère des tâches, simule un traitement et met à jour un total global.
 * @param arg Argument passé au thread (identifiant casté en void*).
 * @return thrd_success à la fin de l'exécution du thread.
 */
int worker_function(void* arg) {
    long thread_id = (long)arg; // Récupérer l'ID du thread (pour l'affichage)
    int current_task_value;
    int local_task_index; // Variable locale pour stocker l'index récupéré

    printf("Thread %ld: Démarrage du worker.\n", thread_id);

    while (1) {
        // --- Acquisition de tâche : Protéger l'accès à task_index ---
        mtx_lock(&task_index_mutex);

        if (task_index < NUM_TASKS) {
            local_task_index = task_index; // Récupérer l'index avant de l'incrémenter
            current_task_value = tasks[task_index];
            task_index++;
            printf("Thread %ld: A pris la tâche %d (valeur %d) à l'index %d.\n",
                   thread_id, local_task_index, current_task_value, local_task_index);
        } else {
            // Toutes les tâches ont été prises, sortir de la boucle
            mtx_unlock(&task_index_mutex); // Relâcher le mutex avant de quitter
            break;
        }

        mtx_unlock(&task_index_mutex); // Relâcher le mutex après avoir pris la tâche

        // --- Simulation de travail (C11 Portable) ---
        // Remplace usleep par thrd_sleep pour être compatible Windows et Linux
        struct timespec sleep_time = {
            .tv_sec = 0,
            .tv_nsec = (rand() % 10) * 1000000L // Pause de 0 à 10 ms
        };
        thrd_sleep(&sleep_time, NULL);

        // --- Mise à jour du total : Protéger l'accès à total_processed_value ---
        mtx_lock(&total_value_mutex);

        total_processed_value += current_task_value;
        printf("Thread %ld: A traité la tâche %d. Total actuel: %lld.\n",
               thread_id, current_task_value, total_processed_value);

        mtx_unlock(&total_value_mutex); // Relâcher le mutex après la mise à jour
    }

    printf("Thread %ld: Termine son travail.\n", thread_id);
    return thrd_success;
}

// --- Étape 4: Le Programme Principal (main) ---

int main(void) {
    // Initialisation du générateur de nombres aléatoires
    srand((unsigned int)time(NULL));

    // Définir le nombre de threads à créer
    int num_threads = 4; // Par exemple, 4 threads
    thrd_t threads[num_threads]; // Tableau pour stocker les identifiants de thread C11

    printf("--- Démarrage du Centre de Traitement Numérique (C11) ---\n");
    printf("Nombre total de tâches : %d\n", NUM_TASKS);
    printf("Nombre de threads : %d\n", num_threads);

    // Initialiser les mutex avant la création des threads (mtx_plain = mutex standard)
    if (mtx_init(&task_index_mutex, mtx_plain) != thrd_success) {
        fprintf(stderr, "Erreur: Échec de l'initialisation de task_index_mutex.\n");
        return EXIT_FAILURE;
    }
    if (mtx_init(&total_value_mutex, mtx_plain) != thrd_success) {
        fprintf(stderr, "Erreur: Échec de l'initialisation de total_value_mutex.\n");
        mtx_destroy(&task_index_mutex); // Nettoyer le premier mutex
        return EXIT_FAILURE;
    }

    // Créer les threads
    for (long i = 0; i < num_threads; i++) {
        if (thrd_create(&threads[i], worker_function, (void*)i) != thrd_success) {
            fprintf(stderr, "Erreur: Échec de la création du thread %ld.\n", i);
            mtx_destroy(&task_index_mutex);
            mtx_destroy(&total_value_mutex);
            return EXIT_FAILURE;
        }
    }

    // Attendre la fin de tous les threads
    for (int i = 0; i < num_threads; i++) {
        if (thrd_join(threads[i], NULL) != thrd_success) {
            fprintf(stderr, "Erreur: Échec de l'attente du thread %d.\n", i);
        }
    }

    // Détruire les mutex après que tous les threads aient terminé
    mtx_destroy(&task_index_mutex);
    mtx_destroy(&total_value_mutex);

    printf("\n--- Tous les threads ont terminé leur exécution ---\n");
    printf("Valeur totale des tâches traitées: %lld\n", total_processed_value);

    // --- Vérification ---
    long long expected_total = 0;
    for (int i = 0; i < NUM_TASKS; i++) {
        expected_total += tasks[i];
    }
    printf("Valeur totale attendue (calcul manuel): %lld\n", expected_total);

    if (total_processed_value == expected_total) {
        printf("Résultat CORRECT: La somme des tâches traitées correspond à la somme attendue.\n");
    } else {
        printf("Résultat INCORRECT: La somme des tâches traitées NE correspond PAS à la somme attendue.\n");
    }

    return EXIT_SUCCESS;
}
```

**Compilation :**
* Linux / macOS : `gcc -std=c11 -pthread tp_threads_s1.c -o tp_threads_s1`
* Windows (MSVC) : `cl /std:c11 tp_threads_s1.c`
* Windows (MinGW-w64) : `gcc -std=c11 tp_threads_s1.c -o tp_threads_s1.exe`

## Points d'Attention et Pistes de Réflexion (Solution 1)

### Test sans mutex au début

Si l'on commente temporairement les appels à `mtx_lock` et `mtx_unlock`, le programme produira très probablement des résultats incorrects pour `total_processed_value`.

* **Pourquoi incorrect ?**
  * **Pour `task_index`** : Plusieurs threads pourraient lire la même valeur de `task_index` avant qu'un autre thread n'ait eu le temps de l'incrémenter. Des tâches seraient traitées plusieurs fois ou sautées.
  * **Pour `total_processed_value`** : L'incrémentation `total_processed_value += current_task_value;` n'est pas atomique au niveau assembleur (lecture, modification, écriture). Si deux threads exécutent cette instruction en même temps, l'un des ajouts sera écrasé.

### Granularité des mutex

Dans cette solution, nous avons utilisé **deux mutex C11 distincts** (`task_index_mutex` et `total_value_mutex`).

* **Avantages de cette approche (granularité fine)** :
  * **Meilleur parallélisme potentiel** : Un thread peut mettre à jour `total_processed_value` pendant qu'un autre récupère une nouvelle tâche en modifiant `task_index`.
  * **Moins de contention** : Réduit le temps d'attente sur chaque verrou.
* **Inconvénients** :
  * **Complexité accrue** : Gestion de plusieurs verrous.
  * **Risque de deadlock** : Nécessite une rigueur sur l'ordre d'acquisition si un thread devait réserver plusieurs mutex.

### Deadlock

Un interblocage (*deadlock*) se produit lorsque deux threads attendent mutuellement des verrous détenus par l'autre. Pour l'éviter, il faut toujours acquérir les mutex dans un ordre global identique dans tous les threads.

---

# Solution 2 : Un Seul Mutex Global pour la Simplicité

Cette solution utilise un seul mutex global `mtx_t` pour protéger l'ensemble des ressources partagées (`task_index` et `total_processed_value`).

## Fichier `tp_threads_s2.c`

```c
#include <stdio.h>    // Pour printf, fprintf
#include <stdlib.h>   // Pour EXIT_SUCCESS, EXIT_FAILURE, rand, srand
#include <threads.h>  // Pour thrd_*, mtx_* (C11)
#include <time.h>     // Pour time, timespec

// --- Étape 1: La Structure de Base ---

#define NUM_TASKS 20
int tasks[NUM_TASKS] = {10, 20, 5, 15, 30, 25, 12, 8, 40, 18,
                        22, 7, 35, 11, 28, 14, 3, 45, 19, 27};

int task_index = 0;
long long total_processed_value = 0;

// --- Étape 2: Les Mutex C11 ---

// Un seul mutex global
mtx_t global_mutex;

// --- Étape 3: La Fonction de Traitement des Tâches (Thread Worker C11) ---

int worker_function(void* arg) {
    long thread_id = (long)arg;
    int current_task_value = 0;
    int task_taken = 0;

    printf("Thread %ld: Démarrage du travailleur.\n", thread_id);

    while (1) {
        task_taken = 0;

        // --- Section critique: Accès à task_index et récupération ---
        mtx_lock(&global_mutex);

        if (task_index < NUM_TASKS) {
            current_task_value = tasks[task_index];
            task_index++;
            task_taken = 1;
            printf("Thread %ld: A pris la tâche %d (valeur %d) à l'index %d.\n",
                   thread_id, task_index - 1, current_task_value, task_index - 1);
        }

        mtx_unlock(&global_mutex);

        if (!task_taken) {
            break; // Plus de tâches disponibles
        }

        // --- Simulation de travail (hors section critique) ---
        struct timespec sleep_time = {
            .tv_sec = 0,
            .tv_nsec = (rand() % 10) * 1000000L // 0 à 10 ms
        };
        thrd_sleep(&sleep_time, NULL);

        // --- Section critique: Mise à jour du total ---
        mtx_lock(&global_mutex);

        total_processed_value += current_task_value;
        printf("Thread %ld: A traité la tâche %d. Total actuel: %lld.\n",
               thread_id, current_task_value, total_processed_value);

        mtx_unlock(&global_mutex);
    }

    printf("Thread %ld: Termine son travail.\n", thread_id);
    return thrd_success;
}

// --- Étape 4: Le Programme Principal (main) ---

int main(void) {
    srand((unsigned int)time(NULL));

    int num_threads = 4;
    thrd_t threads[num_threads];

    printf("--- Démarrage du Centre de Traitement Numérique (Solution 2 - C11) ---\n");
    printf("Nombre total de tâches: %d\n", NUM_TASKS);
    printf("Nombre de threads travailleurs: %d\n", num_threads);

    // Initialiser le mutex global
    if (mtx_init(&global_mutex, mtx_plain) != thrd_success) {
        fprintf(stderr, "Erreur: Échec de l'initialisation du mutex global.\n");
        return EXIT_FAILURE;
    }

    // Créer les threads
    for (long i = 0; i < num_threads; i++) {
        if (thrd_create(&threads[i], worker_function, (void*)i) != thrd_success) {
            fprintf(stderr, "Erreur: Échec de la création du thread %ld.\n", i);
            mtx_destroy(&global_mutex);
            return EXIT_FAILURE;
        }
    }

    // Attendre la fin de tous les threads
    for (int i = 0; i < num_threads; i++) {
        if (thrd_join(threads[i], NULL) != thrd_success) {
            fprintf(stderr, "Erreur: Échec de l'attente du thread %d.\n", i);
        }
    }

    // Détruire le mutex global
    mtx_destroy(&global_mutex);

    printf("\n--- Tous les threads ont terminé leur exécution ---\n");
    printf("Valeur totale des tâches traitées: %lld\n", total_processed_value);

    // --- Vérification ---
    long long expected_total = 0;
    for (int i = 0; i < NUM_TASKS; i++) {
        expected_total += tasks[i];
    }
    printf("Valeur totale attendue (calcul manuel): %lld\n", expected_total);

    if (total_processed_value == expected_total) {
        printf("Résultat CORRECT: La somme des tâches traitées correspond à la somme attendue.\n");
    } else {
        printf("Résultat INCORRECT: La somme des tâches traitées NE correspond PAS à la somme attendue.\n");
    }

    return EXIT_SUCCESS;
}
```

**Compilation :**
* Linux / macOS : `gcc -std=c11 -pthread tp_threads_s2.c -o tp_threads_s2`
* Windows (MSVC) : `cl /std:c11 tp_threads_s2.c`
* Windows (MinGW-w64) : `gcc -std=c11 tp_threads_s2.c -o tp_threads_s2.exe`

## Comparaison des deux solutions

* **Solution 1 (deux mutex)** : Offre de meilleures performances sur des systèmes à forte concurrence en permettant le chevauchement des opérations indépendantes.
* **Solution 2 (un mutex global)** : Plus simple à concevoir, élimine les risques de deadlocks complexes, mais crée un goulot d'étranglement centralisé si les sections critiques augmentent.
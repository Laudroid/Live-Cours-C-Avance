Voici deux solutions complètes pour le TP "Gestion de Tâches Concurrentes avec Mutex en C", chacune présentée dans un chapitre Markdown distinct. Chaque solution inclut le code C et des explications détaillées, ainsi que les réponses aux points de réflexion.

---

# Solution 1 : Deux Mutex Distincts pour une Granularité Fine

Cette solution utilise deux mutex distincts : un pour protéger l'accès à l'index des tâches (`task_index`) et un autre pour protéger l'accès à la somme totale (`total_processed_value`). Cette approche offre une granularité de verrouillage plus fine, potentiellement améliorant le parallélisme.

## Fichier `tp_threads_s1.c`



```c
#include <stdio.h>    // Pour printf, fprintf
#include <stdlib.h>   // Pour EXIT_SUCCESS, EXIT_FAILURE, rand, srand
#include <pthread.h>  // Pour pthread_*, mutex_*
#include <unistd.h>   // Pour usleep
#include <time.h>     // Pour time

// --- Étape 1: La Structure de Base ---

// Tableau statique simulant la file d'attente des tâches
#define NUM_TASKS 20
int tasks[NUM_TASKS] = {10, 20, 5, 15, 30, 25, 12, 8, 40, 18,
                        22, 7, 35, 11, 28, 14, 3, 45, 19, 27};

// Index de la prochaine tâche à prendre
int task_index = 0;

// Variable globale pour accumuler la somme des valeurs des tâches traitées
long total_processed_value = 0;

// --- Étape 2: Les Mutex ---

// Mutex pour protéger l'accès à task_index (ressource partagée)
pthread_mutex_t task_index_mutex;

// Mutex pour protéger l'accès à total_processed_value (ressource partagée)
pthread_mutex_t total_value_mutex;

// --- Étape 3: La Fonction de Traitement des Tâches (Thread Worker) ---

/**
 * @brief Fonction exécutée par chaque thread travailleur.
 *        Récupère des tâches, simule un traitement et met à jour un total global.
 * @param arg Argument passé au thread (non utilisé ici, peut être NULL).
 * @return NULL à la fin de l'exécution du thread.
 */
void* worker_function(void* arg) {
    long thread_id = (long)arg; // Récupérer l'ID du thread (pour l'affichage)
    int current_task_value;
    int local_task_index; // Variable locale pour stocker l'index récupéré

    printf("Thread %ld: Démarrage du travailleur.\n", thread_id);

    while (1) {
        // --- Acquisition de tâche : Protéger l'accès à task_index ---
        pthread_mutex_lock(&task_index_mutex);

        if (task_index < NUM_TASKS) {
            local_task_index = task_index; // Récupérer l'index avant de l'incrémenter
            current_task_value = tasks[task_index];
            task_index++;
            printf("Thread %ld: A pris la tâche %d (valeur %d) à l'index %d.\n",
                   thread_id, local_task_index, current_task_value, local_task_index);
        } else {
            // Toutes les tâches ont été prises, sortir de la boucle
            pthread_mutex_unlock(&task_index_mutex); // Relâcher le mutex avant de quitter
            break;
        }

        pthread_mutex_unlock(&task_index_mutex); // Relâcher le mutex après avoir pris la tâche

        // --- Simulation de travail ---
        // usleep(rand() % 100000); // Simule un temps de traitement aléatoire (0-100 ms)
        // Pour des tests plus rapides, on peut réduire ou commenter usleep
        usleep(rand() % 10000); // Simule un temps de traitement plus court (0-10 ms)


        // --- Mise à jour du total : Protéger l'accès à total_processed_value ---
        pthread_mutex_lock(&total_value_mutex);

        total_processed_value += current_task_value;
        printf("Thread %ld: A traité la tâche %d. Total actuel: %lld.\n",
               thread_id, current_task_value, total_processed_value);

        pthread_mutex_unlock(&total_value_mutex); // Relâcher le mutex après la mise à jour
    }

    printf("Thread %ld: Termine son travail.\n", thread_id);
    return NULL;
}

// --- Étape 4: Le Programme Principal (main) ---

int main() {
    // Initialisation du générateur de nombres aléatoires pour usleep
    srand(time(NULL));

    // Définir le nombre de threads à créer
    int num_threads = 4; // Par exemple, 4 threads travailleurs
    pthread_t threads[num_threads]; // Tableau pour stocker les identifiants de thread

    printf("--- Démarrage du Centre de Traitement Numérique ---\n");
    printf("Nombre total de tâches: %d\n", NUM_TASKS);
    printf("Nombre de threads travailleurs: %d\n", num_threads);

    // Initialiser les mutex avant la création des threads
    if (pthread_mutex_init(&task_index_mutex, NULL) != 0) {
        fprintf(stderr, "Erreur: Échec de l'initialisation de task_index_mutex.\n");
        return EXIT_FAILURE;
    }
    if (pthread_mutex_init(&total_value_mutex, NULL) != 0) {
        fprintf(stderr, "Erreur: Échec de l'initialisation de total_value_mutex.\n");
        pthread_mutex_destroy(&task_index_mutex); // Nettoyer le premier mutex
        return EXIT_FAILURE;
    }

    // Créer les threads
    for (int i = 0; i < num_threads; i++) {
        if (pthread_create(&threads[i], NULL, worker_function, (void*)i) != 0) {
            fprintf(stderr, "Erreur: Échec de la création du thread %ld.\n", i);
            // Gérer l'erreur (ex: détruire les mutex et attendre les threads déjà créés)
            // Pour ce TP, on peut simplement quitter.
            pthread_mutex_destroy(&task_index_mutex);
            pthread_mutex_destroy(&total_value_mutex);
            return EXIT_FAILURE;
        }
    }

    // Attendre la fin de tous les threads
    for (int i = 0; i < num_threads; i++) {
        if (pthread_join(threads[i], NULL) != 0) {
            fprintf(stderr, "Erreur: Échec de l'attente du thread %d.\n", i);
            // Gérer l'erreur, mais le programme peut continuer pour afficher le total
        }
    }

    // Détruire les mutex après que tous les threads aient terminé
    pthread_mutex_destroy(&task_index_mutex);
    pthread_mutex_destroy(&total_value_mutex);

    printf("\n--- Tous les threads ont terminé leur exécution ---\n");
    printf("Valeur totale des tâches traitées: %lld\n", total_processed_value);

    // --- Vérification ---
    long expected_total = 0;
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
`gcc -o tp_threads_s1 tp_threads_s1.c -pthread`

## Points d'Attention et Pistes de Réflexion (Solution 1)

### Test sans mutex au début

Si l'on commente temporairement les appels à `pthread_mutex_lock` et `pthread_mutex_unlock` (ou si on ne les met pas en place du tout), le programme produira très probablement des résultats incorrects pour `total_processed_value`.

*   **Pourquoi incorrect ?**
    *   **Pour `task_index`**: Plusieurs threads pourraient lire la même valeur de `task_index` avant qu'un autre thread n'ait eu le temps de l'incrémenter. Cela entraînerait que certaines tâches soient traitées plusieurs fois, ou que certaines soient complètement ignorées si l'incrémentation se fait de manière non atomique et que des lectures intermédiaires se produisent.
    *   **Pour `total_processed_value`**: L'opération `total_processed_value += current_task_value;` n'est pas atomique. Elle se décompose en plusieurs étapes :
        1.  Lire la valeur actuelle de `total_processed_value`.
        2.  Ajouter `current_task_value` à cette valeur.
        3.  Écrire le nouveau résultat dans `total_processed_value`.
        Si deux threads effectuent cette opération simultanément, ils pourraient tous deux lire la *même* valeur ancienne à l'étape 1, effectuer leur addition, puis écrire leur résultat. L'une des mises à jour serait alors perdue.
*   **Observation**: En exécutant le programme sans mutex, on observerait que `total_processed_value` est souvent inférieur à `expected_total`, et parfois même supérieur si des tâches sont prises plusieurs fois. Les résultats seraient non déterministes.

### Granularité des mutex

Dans cette solution, nous avons utilisé **deux mutex distincts** : `task_index_mutex` et `total_value_mutex`.

*   **Avantages de cette approche (granularité fine)** :
    *   **Meilleur parallélisme potentiel** : Un thread peut être en train de mettre à jour `total_processed_value` tandis qu'un autre thread est en train de récupérer une nouvelle tâche en modifiant `task_index`. Les deux opérations peuvent se dérouler simultanément si elles ne sont pas bloquées par le même mutex. Cela réduit le temps d'attente des threads et peut améliorer les performances globales.
    *   **Moins de contention** : Les threads passent moins de temps à attendre un mutex si les ressources qu'ils protègent sont différentes et ne sont pas toujours nécessaires en même temps.
*   **Inconvénients** :
    *   **Complexité accrue** : Gérer plusieurs mutex peut être plus complexe et augmenter le risque d'erreurs (oublier un verrou, acquérir le mauvais verrou).
    *   **Risque de deadlock** : Si un thread a besoin d'acquérir plusieurs mutex, l'ordre d'acquisition devient crucial pour éviter les deadlocks.

*   **Alternative (un seul mutex pour tout)** : On aurait pu utiliser un seul mutex pour protéger à la fois `task_index` et `total_processed_value`.
    *   **Avantages** : Plus simple à implémenter et moins de risque de deadlock dans des scénarios simples.
    *   **Inconvénients** : Réduit le parallélisme car toutes les opérations sur les ressources partagées sont sérialisées. Si un thread est bloqué sur une opération (ex: `usleep`), il retient le mutex, empêchant les autres threads d'accéder à *toutes* les ressources partagées, même celles qui ne sont pas directement impliquées dans l'opération bloquante.

### Deadlock

Un deadlock (interblocage) se produit lorsque deux ou plusieurs threads sont bloqués indéfiniment, chacun attendant une ressource que l'autre détient.

*   **Exemple de deadlock dans un scénario avec deux mutex (A et B)** :
    *   Thread 1 acquiert Mutex A.
    *   Thread 2 acquiert Mutex B.
    *   Thread 1 tente d'acquérir Mutex B (mais il est détenu par Thread 2).
    *   Thread 2 tente d'acquérir Mutex A (mais il est détenu par Thread 1).
    *   Résultat : Les deux threads sont bloqués, attendant indéfiniment l'un l'autre.

*   **Comment l'éviter ?**
    *   **Ordre d'acquisition cohérent** : Si plusieurs mutex doivent être acquis par un thread, assurez-vous que tous les threads acquièrent ces mutex dans le même ordre. Par exemple, toujours acquérir A avant B.
    *   **Verrouillage hiérarchique** : Définir un ordre strict pour l'acquisition des verrous.
    *   **Verrouillage à durée limitée** : Utiliser des fonctions comme `pthread_mutex_trylock` ou `pthread_mutex_timedlock` qui tentent d'acquérir un verrou et retournent immédiatement si ce n'est pas possible, permettant au thread de faire autre chose ou de réessayer plus tard.
    *   **Éviter les cycles d'attente** : S'assurer qu'il n'y a pas de dépendances circulaires entre les ressources.

---

# Solution 2 : Un Seul Mutex Global pour la Simplicité

Cette solution utilise un seul mutex global pour protéger toutes les ressources partagées (`task_index` et `total_processed_value`). C'est une approche plus simple à implémenter et à comprendre, mais elle peut réduire le parallélisme.

## Fichier `tp_threads_s2.c`



```c
#include <stdio.h>    // Pour printf, fprintf
#include <stdlib.h>   // Pour EXIT_SUCCESS, EXIT_FAILURE, rand, srand
#include <pthread.h>  // Pour pthread_*, mutex_*
#include <unistd.h>   // Pour usleep
#include <time.h>     // Pour time

// --- Étape 1: La Structure de Base ---

// Tableau statique simulant la file d'attente des tâches
#define NUM_TASKS 20
int tasks[NUM_TASKS] = {10, 20, 5, 15, 30, 25, 12, 8, 40, 18,
                        22, 7, 35, 11, 28, 14, 3, 45, 19, 27};

// Index de la prochaine tâche à prendre
int task_index = 0;

// Variable globale pour accumuler la somme des valeurs des tâches traitées
long long total_processed_value = 0;

// --- Étape 2: Les Mutex ---

// Un seul mutex global pour protéger toutes les ressources partagées
pthread_mutex_t global_mutex;

// --- Étape 3: La Fonction de Traitement des Tâches (Thread Worker) ---

/**
 * @brief Fonction exécutée par chaque thread travailleur.
 *        Récupère des tâches, simule un traitement et met à jour un total global.
 *        Utilise un seul mutex global pour toutes les sections critiques.
 * @param arg Argument passé au thread (non utilisé ici, peut être NULL).
 * @return NULL à la fin de l'exécution du thread.
 */
void* worker_function(void* arg) {
    long thread_id = (long)arg;
    int current_task_value = 0; // Initialiser pour éviter les avertissements
    int task_taken = 0; // Drapeau pour savoir si une tâche a été prise

    printf("Thread %ld: Démarrage du travailleur.\n", thread_id);

    while (1) {
        task_taken = 0; // Réinitialiser pour chaque itération

        // --- Section critique: Accès à task_index et récupération de la tâche ---
        pthread_mutex_lock(&global_mutex);

        if (task_index < NUM_TASKS) {
            current_task_value = tasks[task_index];
            task_index++;
            task_taken = 1; // Une tâche a été prise
            printf("Thread %ld: A pris la tâche %d (valeur %d) à l'index %d.\n",
                   thread_id, task_index - 1, current_task_value, task_index - 1); // task_index-1 est l'index réel
        }

        pthread_mutex_unlock(&global_mutex); // Relâcher le mutex après avoir accédé à task_index

        if (!task_taken) {
            // Si aucune tâche n'a été prise, toutes les tâches sont traitées
            break;
        }

        // --- Simulation de travail (hors section critique) ---
        usleep(rand() % 10000); // Simule un temps de traitement (0-10 ms)

        // --- Section critique: Mise à jour du total ---
        pthread_mutex_lock(&global_mutex);

        total_processed_value += current_task_value;
        printf("Thread %ld: A traité la tâche %d. Total actuel: %lld.\n",
               thread_id, current_task_value, total_processed_value);

        pthread_mutex_unlock(&global_mutex); // Relâcher le mutex après la mise à jour
    }

    printf("Thread %ld: Termine son travail.\n", thread_id);
    return NULL;
}

// --- Étape 4: Le Programme Principal (main) ---

int main() {
    srand(time(NULL));

    int num_threads = 4;
    pthread_t threads[num_threads];

    printf("--- Démarrage du Centre de Traitement Numérique (Solution 2) ---\n");
    printf("Nombre total de tâches: %d\n", NUM_TASKS);
    printf("Nombre de threads travailleurs: %d\n", num_threads);

    // Initialiser le mutex global
    if (pthread_mutex_init(&global_mutex, NULL) != 0) {
        fprintf(stderr, "Erreur: Échec de l'initialisation du mutex global.\n");
        return EXIT_FAILURE;
    }

    // Créer les threads
    for (long i = 0; i < num_threads; i++) {
        if (pthread_create(&threads[i], NULL, worker_function, (void*)i) != 0) {
            fprintf(stderr, "Erreur: Échec de la création du thread %ld.\n", i);
            pthread_mutex_destroy(&global_mutex);
            return EXIT_FAILURE;
        }
    }

    // Attendre la fin de tous les threads
    for (int i = 0; i < num_threads; i++) {
        if (pthread_join(threads[i], NULL) != 0) {
            fprintf(stderr, "Erreur: Échec de l'attente du thread %d.\n", i);
        }
    }

    // Détruire le mutex global
    pthread_mutex_destroy(&global_mutex);

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
`gcc -o tp_threads_s2 tp_threads_s2.c -pthread`

## Points d'Attention et Pistes de Réflexion (Solution 2)

### Test sans mutex au début

Le comportement sans mutex serait le même que celui décrit dans la Solution 1 : résultats incorrects et non déterministes pour `total_processed_value` et potentiellement `task_index`, en raison des conditions de concurrence.

### Granularité des mutex

Dans cette solution, nous avons utilisé un **seul mutex global** (`global_mutex`) pour protéger à la fois `task_index` et `total_processed_value`.

*   **Avantages de cette approche (granularité grossière)** :
    *   **Simplicité d'implémentation** : Un seul mutex à gérer, ce qui réduit la complexité du code et le risque d'oublier un verrou.
    *   **Moins de risque de deadlock** : Dans des scénarios simples comme celui-ci, l'utilisation d'un seul mutex élimine le risque de deadlock lié à l'ordre d'acquisition de plusieurs verrous.
*   **Inconvénients** :
    *   **Parallélisme réduit** : Toutes les sections critiques qui accèdent à des ressources partagées sont sérialisées. Si un thread détient le mutex pour mettre à jour `task_index`, aucun autre thread ne peut mettre à jour `total_processed_value`, même si ces opérations sont logiquement indépendantes.
    *   **Contention accrue** : Les threads passent plus de temps à attendre le même mutex, ce qui peut devenir un goulot d'étranglement dans des applications avec beaucoup de contention ou des sections critiques longues.

### Deadlock

Le risque de deadlock est très faible avec un seul mutex, car il n'y a pas de dépendance circulaire entre les ressources. Un thread acquiert le mutex, l'utilise, puis le relâche. Il n'attend jamais un deuxième mutex alors qu'il en détient déjà un.

Cependant, si ce mutex global était utilisé pour protéger des ressources qui nécessitent des acquisitions dans un ordre spécifique dans d'autres parties du code, le risque de deadlock réapparaîtrait.

### Comparaison des deux solutions

*   **Solution 1 (deux mutex)** : Offre un meilleur potentiel de parallélisme car les opérations sur `task_index` et `total_processed_value` peuvent se chevaucher. C'est généralement l'approche préférée pour des systèmes plus complexes où la performance est critique et où les ressources partagées sont distinctes. Cependant, elle est plus complexe à concevoir et à débugger.
*   **Solution 2 (un mutex global)** : Est plus simple à implémenter et à comprendre, ce qui la rend adaptée pour des scénarios où la contention est faible ou la complexité du code est une préoccupation majeure. Cependant, elle peut entraîner une perte de parallélisme si les sections critiques sont longues ou si de nombreuses ressources distinctes sont protégées par le même verrou.

Le choix de la granularité des mutex est un compromis entre la simplicité de l'implémentation et le niveau de parallélisme souhaité. Pour ce TP, les deux solutions sont valides et démontrent l'utilisation correcte des mutex.

---
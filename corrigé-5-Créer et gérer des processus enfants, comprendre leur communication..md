Voici deux solutions complètes pour le TP "Chasse aux Nombres Premiers Distribuée", chacune présentée dans un chapitre Markdown distinct. Chaque solution inclut le code C et des explications détaillées.

---

# Solution 1 : `K` Pipes Indépendants (Un par Enfant)

Cette solution implémente la communication enfant-parent en utilisant une paire de pipes distincte pour chaque processus enfant. C'est une approche simple et robuste pour la communication unidirectionnelle.

## Fichier `prime_finder_s1.c`



```c
#include <stdio.h>    // Pour printf, fprintf, perror
#include <stdlib.h>   // Pour atoi, exit, EXIT_FAILURE, malloc, realloc, free
#include <unistd.h>   // Pour fork, pipe, read, write, close
#include <sys/wait.h> // Pour wait
#include <string.h>   // Pour memset (si nécessaire, mais non utilisé ici)

// --- Étape 1 : La fonction is_prime ---
/**
 * @brief Vérifie si un nombre est premier.
 * @param num Le nombre à vérifier.
 * @return 1 si le nombre est premier, 0 sinon.
 */
int is_prime(int num) {
    if (num <= 1) return 0; // Les nombres inférieurs ou égaux à 1 ne sont pas premiers
    if (num <= 3) return 1; // 2 et 3 sont premiers
    if (num % 2 == 0 || num % 3 == 0) return 0; // Élimine les multiples de 2 et 3

    // Vérification optimisée pour les nombres premiers
    for (int i = 5; i * i <= num; i = i + 6) {
        if (num % i == 0 || num % (i + 2) == 0)
            return 0;
    }
    return 1;
}

// Fonction pour trier un tableau d'entiers (simple tri à bulles pour ce TP)
void sort_primes(int *primes, int count) {
    for (int i = 0; i < count - 1; i++) {
        for (int j = 0; j < count - i - 1; j++) {
            if (primes[j] > primes[j + 1]) {
                int temp = primes[j];
                primes[j] = primes[j + 1];
                primes[j + 1] = temp;
            }
        }
    }
}

int main(int argc, char *argv[]) {
    int N, K; // N: nombre maximum, K: nombre de processus enfants
    pid_t pid;
    int *all_primes = NULL; // Tableau dynamique pour stocker tous les nombres premiers
    int prime_count = 0;    // Nombre total de nombres premiers trouvés
    int prime_capacity = 10; // Capacité initiale du tableau all_primes

    // --- Étape 2 : Parsing des arguments ---
    if (argc != 3) {
        fprintf(stderr, "Utilisation: %s <nombre_maximum_N> <nombre_processus_K>\n", argv[0]);
        exit(EXIT_FAILURE);
    }

    N = atoi(argv[1]);
    K = atoi(argv[2]);

    if (N < 2 || K < 1) {
        fprintf(stderr, "Erreur: N doit être >= 2 et K doit être >= 1.\n");
        exit(EXIT_FAILURE);
    }

    // Allouer la capacité initiale pour le tableau de nombres premiers
    all_primes = (int *)malloc(prime_capacity * sizeof(int));
    if (all_primes == NULL) {
        perror("Erreur d'allocation mémoire pour all_primes");
        exit(EXIT_FAILURE);
    }

    // Tableau pour stocker les descripteurs de lecture des pipes (pour le parent)
    int *read_fds = (int *)malloc(K * sizeof(int));
    if (read_fds == NULL) {
        perror("Erreur d'allocation mémoire pour read_fds");
        free(all_primes);
        exit(EXIT_FAILURE);
    }

    printf("Parent (PID %d): Recherche de nombres premiers jusqu'à %d avec %d processus.\n", getpid(), N, K);

    // --- Étape 3 : Création des processus enfants et communication via pipes ---
    for (int i = 0; i < K; i++) {
        int pipefd[2]; // pipefd[0] pour la lecture, pipefd[1] pour l'écriture

        // Créer un pipe avant chaque fork
        if (pipe(pipefd) == -1) {
            perror("Erreur lors de la création du pipe");
            // Nettoyage des descripteurs de fichiers déjà ouverts et de la mémoire
            for (int j = 0; j < i; j++) close(read_fds[j]);
            free(read_fds);
            free(all_primes);
            exit(EXIT_FAILURE);
        }

        pid = fork();

        if (pid == -1) {
            perror("Erreur lors du fork");
            // Nettoyage en cas d'échec du fork
            close(pipefd[0]); close(pipefd[1]);
            for (int j = 0; j < i; j++) close(read_fds[j]);
            free(read_fds);
            free(all_primes);
            exit(EXIT_FAILURE);
        } else if (pid == 0) { // Code du processus enfant
            // Fermer l'extrémité de lecture du pipe dans l'enfant
            close(pipefd[0]);

            // Calculer la sous-plage pour cet enfant
            int start = (N / K) * i + 2; // +2 car on commence à chercher à partir de 2
            int end = (N / K) * (i + 1) + 1;
            if (i == K - 1) { // Le dernier enfant prend la fin de la plage
                end = N;
            }
            // Ajustement pour s'assurer que start ne dépasse pas end si N est petit
            if (start > N) start = N + 1; // Pour que la boucle ne s'exécute pas
            if (end < start) end = start -1; // Pour que la boucle ne s'exécute pas

            printf("  Enfant %d (PID %d): Recherche dans la plage [%d, %d]\n", i + 1, getpid(), start, end);

            // Chercher les nombres premiers dans la sous-plage
            for (int num = start; num <= end; num++) {
                if (is_prime(num)) {
                    // Écrire le nombre premier dans le pipe
                    if (write(pipefd[1], &num, sizeof(int)) == -1) {
                        perror("Erreur lors de l'écriture dans le pipe par l'enfant");
                        close(pipefd[1]);
                        exit(EXIT_FAILURE); // L'enfant termine en cas d'erreur
                    }
                }
            }

            // Fermer l'extrémité d'écriture du pipe après avoir terminé
            close(pipefd[1]);
            exit(EXIT_SUCCESS); // L'enfant termine son exécution
        } else { // Code du processus parent
            // Fermer l'extrémité d'écriture du pipe dans le parent
            close(pipefd[1]);
            // Stocker le descripteur de lecture pour cet enfant
            read_fds[i] = pipefd[0];
        }
    }

    // --- Étape 4 : Assemblage et affichage ---
    // Le parent lit les nombres premiers de tous les pipes
    int received_prime;
    for (int i = 0; i < K; i++) {
        // Lire de chaque pipe jusqu'à ce que l'enfant ferme son extrémité d'écriture (EOF)
        while (read(read_fds[i], &received_prime, sizeof(int)) > 0) {
            // Redimensionner le tableau si nécessaire
            if (prime_count >= prime_capacity) {
                prime_capacity *= 2;
                int *temp_primes = (int *)realloc(all_primes, prime_capacity * sizeof(int));
                if (temp_primes == NULL) {
                    perror("Erreur de réallocation mémoire pour all_primes");
                    // Nettoyage des descripteurs de fichiers restants
                    for (int j = i; j < K; j++) close(read_fds[j]);
                    free(read_fds);
                    free(all_primes);
                    exit(EXIT_FAILURE);
                }
                all_primes = temp_primes;
            }
            all_primes[prime_count++] = received_prime;
        }
        // Fermer le descripteur de lecture du pipe après avoir tout lu
        close(read_fds[i]);
    }

    // Attendre la terminaison de tous les processus enfants
    for (int i = 0; i < K; i++) {
        wait(NULL); // Attendre n'importe quel enfant
    }

    printf("\nParent (PID %d): Tous les enfants ont terminé.\n", getpid());

    // Trier les nombres premiers collectés (si les plages ne garantissent pas l'ordre)
    // Dans cette implémentation, les plages sont séquentielles, donc un tri n'est pas strictement nécessaire
    // si les lectures sont faites dans l'ordre des pipes, mais c'est une bonne pratique.
    sort_primes(all_primes, prime_count);

    // Afficher tous les nombres premiers
    printf("Nombres premiers trouvés: ");
    for (int i = 0; i < prime_count; i++) {
        printf("%d ", all_primes[i]);
    }
    printf("\n");

    // Libérer la mémoire allouée
    free(all_primes);
    free(read_fds);

    return EXIT_SUCCESS;
}
```



## Explications des Concepts Clés (Solution 1)

*   **`is_prime(int num)`**: Une fonction utilitaire classique pour vérifier la primalité d'un nombre. Elle utilise des optimisations de base (vérification des multiples de 2 et 3, puis boucle par pas de 6).
*   **`fork()`**: L'appel système `fork()` est utilisé pour créer un nouveau processus.
    *   Dans le parent, `fork()` retourne le PID de l'enfant.
    *   Dans l'enfant, `fork()` retourne `0`.
    *   En cas d'erreur, `fork()` retourne `-1`.
*   **`pipe(int pipefd[2])`**: Cet appel système crée un canal de communication unidirectionnel. `pipefd[0]` est le descripteur de fichier pour la lecture, et `pipefd[1]` pour l'écriture.
    *   **Un pipe par enfant**: Pour `K` enfants, `K` paires de pipes sont créées. Chaque enfant communique avec le parent via son propre pipe.
*   **Gestion des descripteurs de fichiers**: C'est un aspect crucial des pipes.
    *   **Parent**: Après `fork()`, le parent ferme l'extrémité d'écriture (`pipefd[1]`) du pipe qu'il vient de créer, car il ne fera que lire les résultats de cet enfant. Il stocke l'extrémité de lecture (`pipefd[0]`) dans le tableau `read_fds`.
    *   **Enfant**: Après `fork()`, l'enfant ferme l'extrémité de lecture (`pipefd[0]`) car il ne fera qu'écrire ses nombres premiers.
    *   **Pourquoi fermer ?**: Ne pas fermer les descripteurs inutilisés peut entraîner des blocages. Par exemple, si le parent ne ferme pas l'extrémité d'écriture, il pourrait ne jamais recevoir un EOF de l'enfant, même si l'enfant a terminé et fermé son côté.
*   **Division des tâches**: La plage `[2, N]` est divisée en `K` sous-plages approximativement égales. Le calcul `(N / K) * i + 2` et `(N / K) * (i + 1) + 1` (avec ajustement pour le dernier enfant) assure que chaque enfant a une plage à traiter.
*   **Communication**:
    *   **Enfant**: Après avoir trouvé un nombre premier, l'enfant utilise `write(pipefd[1], &num, sizeof(int))` pour l'envoyer au parent.
    *   **Parent**: Le parent boucle sur le tableau `read_fds`, lisant de chaque pipe avec `read(read_fds[i], &received_prime, sizeof(int))` jusqu'à ce que `read` retourne `0` (indiquant EOF, c'est-à-dire que l'enfant a fermé son extrémité d'écriture et terminé).
*   **`wait(NULL)`**: Le parent utilise `wait()` dans une boucle pour attendre la terminaison de tous ses processus enfants. C'est important pour éviter les "zombies" (processus terminés mais dont l'entrée dans la table des processus n'a pas été nettoyée).
*   **Collecte et tri des résultats**: Les nombres premiers reçus sont stockés dans un tableau dynamique `all_primes`. Le tableau est redimensionné avec `realloc` si nécessaire. Un tri final est appliqué pour garantir l'ordre croissant, bien que dans cette configuration (plages séquentielles et lecture séquentielle des pipes), les nombres premiers devraient déjà être dans l'ordre.
*   **Gestion des erreurs**: Chaque appel système potentiellement échouant (`pipe`, `fork`, `open`, `read`, `write`, `malloc`, `realloc`, `close`) est vérifié, et `perror()` est utilisé pour afficher des messages d'erreur système.

---

# Solution 2 : Un Seul Pipe pour Tous les Enfants (avec Tri Final)

Cette solution utilise un seul pipe pour la communication de tous les processus enfants vers le parent. Cela simplifie la gestion des descripteurs de pipes pour le parent (un seul descripteur de lecture à gérer), mais nécessite un tri final des nombres premiers car ils peuvent arriver dans un ordre arbitraire.

## Fichier `prime_finder_s2.c`



```c
#include <stdio.h>    // Pour printf, fprintf, perror
#include <stdlib.h>   // Pour atoi, exit, EXIT_FAILURE, malloc, realloc, free
#include <unistd.h>   // Pour fork, pipe, read, write, close
#include <sys/wait.h> // Pour wait
#include <string.h>   // Pour memset (si nécessaire)

// --- Étape 1 : La fonction is_prime ---
/**
 * @brief Vérifie si un nombre est premier.
 * @param num Le nombre à vérifier.
 * @return 1 si le nombre est premier, 0 sinon.
 */
int is_prime(int num) {
    if (num <= 1) return 0;
    if (num <= 3) return 1;
    if (num % 2 == 0 || num % 3 == 0) return 0;

    for (int i = 5; i * i <= num; i = i + 6) {
        if (num % i == 0 || num % (i + 2) == 0)
            return 0;
    }
    return 1;
}

// Fonction pour trier un tableau d'entiers (qsort pour une meilleure performance)
int compare_ints(const void *a, const void *b) {
    return (*(int*)a - *(int*)b);
}

int main(int argc, char *argv[]) {
    int N, K;
    pid_t pid;
    int pipefd[2]; // Un seul pipe pour tous les enfants
    int *all_primes = NULL;
    int prime_count = 0;
    int prime_capacity = 10;

    if (argc != 3) {
        fprintf(stderr, "Utilisation: %s <nombre_maximum_N> <nombre_processus_K>\n", argv[0]);
        exit(EXIT_FAILURE);
    }

    N = atoi(argv[1]);
    K = atoi(argv[2]);

    if (N < 2 || K < 1) {
        fprintf(stderr, "Erreur: N doit être >= 2 et K doit être >= 1.\n");
        exit(EXIT_FAILURE);
    }

    all_primes = (int *)malloc(prime_capacity * sizeof(int));
    if (all_primes == NULL) {
        perror("Erreur d'allocation mémoire pour all_primes");
        exit(EXIT_FAILURE);
    }

    // --- Étape 3 : Création du pipe (un seul) ---
    if (pipe(pipefd) == -1) {
        perror("Erreur lors de la création du pipe");
        free(all_primes);
        exit(EXIT_FAILURE);
    }

    printf("Parent (PID %d): Recherche de nombres premiers jusqu'à %d avec %d processus.\n", getpid(), N, K);

    // --- Création des processus enfants ---
    for (int i = 0; i < K; i++) {
        pid = fork();

        if (pid == -1) {
            perror("Erreur lors du fork");
            // Nettoyage en cas d'échec du fork
            close(pipefd[0]); close(pipefd[1]);
            // Attendre les enfants déjà créés pour éviter les zombies
            while (wait(NULL) != -1);
            free(all_primes);
            exit(EXIT_FAILURE);
        } else if (pid == 0) { // Code du processus enfant
            // Fermer l'extrémité de lecture du pipe dans l'enfant
            close(pipefd[0]);

            // Calculer la sous-plage pour cet enfant
            int start = (N / K) * i + 2;
            int end = (N / K) * (i + 1) + 1;
            if (i == K - 1) {
                end = N;
            }
            if (start > N) start = N + 1;
            if (end < start) end = start -1;

            printf("  Enfant %d (PID %d): Recherche dans la plage [%d, %d]\n", i + 1, getpid(), start, end);

            for (int num = start; num <= end; num++) {
                if (is_prime(num)) {
                    if (write(pipefd[1], &num, sizeof(int)) == -1) {
                        perror("Erreur lors de l'écriture dans le pipe par l'enfant");
                        close(pipefd[1]);
                        exit(EXIT_FAILURE);
                    }
                }
            }

            // Fermer l'extrémité d'écriture du pipe après avoir terminé
            close(pipefd[1]);
            exit(EXIT_SUCCESS);
        }
    }

    // Code du processus parent
    // Fermer l'extrémité d'écriture du pipe dans le parent
    close(pipefd[1]);

    // --- Lecture par le parent ---
    int received_prime;
    // Lire du pipe jusqu'à ce que tous les enfants aient fermé leur extrémité d'écriture
    // et que le descripteur de lecture du parent soit le seul ouvert.
    // read() retournera 0 (EOF) lorsque tous les écrivains auront fermé leur côté.
    while (read(pipefd[0], &received_prime, sizeof(int)) > 0) {
        if (prime_count >= prime_capacity) {
            prime_capacity *= 2;
            int *temp_primes = (int *)realloc(all_primes, prime_capacity * sizeof(int));
            if (temp_primes == NULL) {
                perror("Erreur de réallocation mémoire pour all_primes");
                close(pipefd[0]);
                free(all_primes);
                exit(EXIT_FAILURE);
            }
            all_primes = temp_primes;
        }
        all_primes[prime_count++] = received_prime;
    }
    // Fermer le descripteur de lecture du pipe après avoir tout lu
    close(pipefd[0]);

    // Attendre la terminaison de tous les processus enfants
    for (int i = 0; i < K; i++) {
        wait(NULL);
    }

    printf("\nParent (PID %d): Tous les enfants ont terminé.\n", getpid());

    // Trier les nombres premiers collectés (nécessaire car l'ordre d'arrivée est arbitraire)
    qsort(all_primes, prime_count, sizeof(int), compare_ints);

    // Afficher tous les nombres premiers
    printf("Nombres premiers trouvés: ");
    for (int i = 0; i < prime_count; i++) {
        printf("%d ", all_primes[i]);
    }
    printf("\n");

    // Libérer la mémoire allouée
    free(all_primes);

    return EXIT_SUCCESS;
}
```



## Explications des Concepts Clés (Solution 2)

*   **`is_prime(int num)`**: Identique à la Solution 1.
*   **`compare_ints` et `qsort`**: Une fonction de comparaison est définie pour être utilisée avec `qsort` de la bibliothèque standard C. `qsort` est un algorithme de tri plus efficace que le tri à bulles pour des tableaux de taille significative.
*   **Un seul pipe (`pipefd[2]`)**: C'est la principale différence. Un seul pipe est créé *avant* la boucle `fork()`.
    *   **Parent**: Ferme l'extrémité d'écriture (`pipefd[1]`). Il ne gère qu'un seul descripteur de lecture (`pipefd[0]`) pour tous les enfants.
    *   **Enfant**: Ferme l'extrémité de lecture (`pipefd[0]`). Tous les enfants écrivent dans la même extrémité d'écriture (`pipefd[1]`).
*   **Gestion des descripteurs de fichiers**:
    *   Le parent ferme `pipefd[1]` une seule fois.
    *   Chaque enfant ferme `pipefd[0]` une seule fois.
    *   Chaque enfant ferme `pipefd[1]` avant de terminer.
    *   Le parent ferme `pipefd[0]` après avoir lu toutes les données.
    *   **Important**: Le `read()` du parent ne retournera `0` (EOF) que lorsque *tous* les processus qui ont ouvert l'extrémité d'écriture du pipe l'auront fermée. C'est pourquoi le parent doit attendre que tous les enfants aient terminé leur écriture et fermé leur côté avant de pouvoir détecter la fin du flux.
*   **Division des tâches**: Identique à la Solution 1.
*   **Communication**:
    *   **Enfant**: Tous les enfants écrivent dans le même pipe (`write(pipefd[1], &num, sizeof(int))`). Les données peuvent être entrelacées dans le pipe si les enfants écrivent simultanément.
    *   **Parent**: Lit du pipe (`read(pipefd[0], &received_prime, sizeof(int))`) jusqu'à recevoir l'EOF.
*   **`wait(NULL)`**: Identique à la Solution 1.
*   **Collecte et tri des résultats**: Les nombres premiers sont collectés dans `all_primes`. Comme l'ordre d'arrivée des nombres premiers dans le pipe est non déterministe (dépend de l'ordonnancement des processus), un tri final avec `qsort` est **indispensable** pour afficher les nombres premiers dans l'ordre croissant.
*   **Gestion des erreurs**: Similaire à la Solution 1, avec des vérifications des retours d'appels système et `perror()`.

### Avantages et Inconvénients de l'approche avec un seul pipe

*   **Avantages**:
    *   **Simplicité pour le parent**: Le parent n'a qu'un seul descripteur de lecture à gérer, ce qui simplifie la logique de lecture.
    *   **Moins de descripteurs ouverts**: Moins de descripteurs de fichiers sont ouverts globalement par le parent.
*   **Inconvénients**:
    *   **Ordre non garanti**: Les données des enfants peuvent arriver dans le pipe dans un ordre arbitraire, nécessitant un tri final.
    *   **Potentiels goulots d'étranglement**: Si de nombreux enfants écrivent simultanément dans le même pipe, cela peut créer un goulot d'étranglement sur l'extrémité d'écriture du pipe.
    *   **Détection d'EOF**: Le parent ne peut détecter la fin du flux qu'après que *tous* les enfants aient fermé leur extrémité d'écriture, ce qui peut rendre la détection de la fin de la communication un peu moins réactive si un enfant met beaucoup de temps à terminer.

Le choix entre `K` pipes et un seul pipe dépend des exigences spécifiques du projet. Pour une communication simple et unidirectionnelle où l'ordre final est important, un seul pipe avec un tri final peut être suffisant. Pour un contrôle plus fin ou des communications plus complexes, `K` pipes ou des mécanismes plus avancés (comme `select`/`poll`) seraient préférables.

---
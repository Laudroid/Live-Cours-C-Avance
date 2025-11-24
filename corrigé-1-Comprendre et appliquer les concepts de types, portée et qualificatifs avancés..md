Voici deux solutions possibles pour le TP, chacune présentée dans un chapitre distinct au format Markdown, avec des explications et des commentaires.

---

# Solution 1 : Implémentation Directe

Cette solution suit de près les instructions du TP, en mettant l'accent sur la clarté et la conformité aux exigences. Les variables `SENSOR_ID` et `sensor_status_register` sont déclarées `extern` dans `sensor.h` pour permettre les tests d'accès et de modification depuis `main.c`, comme demandé par le TP, tout en expliquant les implications de `const` et `volatile`.

## Fichier `sensor.h`


```c
#ifndef SENSOR_H
#define SENSOR_H

#include <time.h> // Nécessaire pour le type long timestamp

// 1. Définition des Types et Structures

// Définition d'une énumération SensorType_t pour les types de capteurs
typedef enum {
    TEMPERATURE,
    HUMIDITY,
    PRESSURE,
    UNKNOWN_SENSOR_TYPE // Type par défaut ou inconnu
} SensorType_t;

// Définition d'une structure SensorData_t pour les données du capteur
typedef struct {
    SensorType_t type;    // Type du capteur
    int value;            // Valeur lue par le capteur
    long timestamp;       // Horodatage de la lecture
} SensorData_t;

// Définition d'un type de pointeur de fonction SensorReadCallback_t
// Cette fonction sera appelée par le module sensor pour obtenir les données réelles.
// Elle prend un pointeur vers SensorData_t pour y stocker la lecture et retourne un int (0 succès, -1 erreur).
typedef int (*SensorReadCallback_t)(SensorData_t *output_data);

// 2. Déclarations du Module Capteur

// Prototypes des fonctions publiques du module sensor
extern void init_sensor(SensorReadCallback_t callback_func, SensorType_t type);
extern int read_sensor(SensorData_t *output_data);

// Déclarations extern pour permettre l'accès à ces variables depuis d'autres fichiers (comme main.c)
// Ceci est fait spécifiquement pour les besoins de test du TP concernant 'const' et 'volatile'.
// Dans un système réel, ces variables pourraient rester 'static' si leur accès est strictement interne au module.
extern const int SENSOR_ID;                     // Identifiant du capteur, non modifiable
extern volatile unsigned int sensor_status_register; // Registre de statut, sujet à des modifications externes

#endif // SENSOR_H
```


## Fichier `sensor.c`


```c
#include "sensor.h"
#include <stdio.h>  // Pour fprintf (affichage d'erreurs)
#include <stdlib.h> // Pour rand() si une simulation plus complexe était nécessaire
#include <time.h>   // Pour time() (horodatage)

// 2. Implémentation du Module Capteur

// Déclaration et initialisation de SENSOR_ID. Non 'static' pour être accessible via 'extern'.
const int SENSOR_ID = 12345;

// Déclaration et initialisation de sensor_status_register. Non 'static' pour être accessible via 'extern'.
// 'volatile' indique au compilateur que cette variable peut être modifiée par des moyens externes
// (ex: matériel, autre thread), empêchant des optimisations agressives.
volatile unsigned int sensor_status_register = 0x01; // 0x01 = prêt, 0x00 = erreur/non prêt

// Variables internes au module (static)
static SensorData_t last_reading;                   // Stocke la dernière lecture du capteur
static SensorReadCallback_t sensor_callback = NULL; // Pointeur vers la fonction de lecture externe
static SensorType_t current_sensor_type = UNKNOWN_SENSOR_TYPE; // Type du capteur configuré

/**
 * @brief Initialise le module capteur.
 * @param callback_func Pointeur vers la fonction de rappel pour la lecture des données.
 * @param type Le type de capteur à simuler.
 */
void init_sensor(SensorReadCallback_t callback_func, SensorType_t type) {
    if (callback_func == NULL) {
        fprintf(stderr, "Erreur: La fonction de rappel ne peut pas être NULL lors de l'initialisation du capteur.\n");
        return;
    }
    sensor_callback = callback_func;
    current_sensor_type = type;
    sensor_status_register = 0x01; // Marque le capteur comme prêt après initialisation
    printf("Capteur %d initialisé avec le type %d.\n", SENSOR_ID, current_sensor_type);
}

/**
 * @brief Lit les données du capteur.
 * @param output_data Pointeur vers une structure SensorData_t où stocker les données lues.
 * @return 0 en cas de succès, -1 en cas d'erreur.
 */
int read_sensor(SensorData_t *output_data) {
    if (output_data == NULL) {
        fprintf(stderr, "Erreur: Le pointeur output_data est NULL dans read_sensor.\n");
        return -1;
    }

    // Vérifie le registre de statut. 'volatile' assure que cette lecture est toujours effectuée en mémoire.
    if (sensor_status_register == 0) {
        fprintf(stderr, "Erreur: Le capteur n'est pas prêt ou est en erreur (status_register = 0).\n");
        return -1;
    }

    if (sensor_callback == NULL) {
        fprintf(stderr, "Erreur: La fonction de rappel de lecture n'est pas définie dans read_sensor.\n");
        return -1;
    }

    // Appelle la fonction de rappel pour obtenir les données réelles
    if (sensor_callback(&last_reading) != 0) {
        fprintf(stderr, "Erreur lors de l'appel de la fonction de rappel du capteur.\n");
        return -1;
    }

    // Met à jour le type et l'horodatage de la dernière lecture
    last_reading.type = current_sensor_type;
    last_reading.timestamp = time(NULL);

    // Copie les données dans la structure fournie par l'appelant
    *output_data = last_reading;

    return 0; // Succès
}
```


## Fichier `logger.h`


```c
#ifndef LOGGER_H
#define LOGGER_H

#include "sensor.h" // Nécessaire pour utiliser SensorData_t

// 3. Déclarations du Module Journalisation

// Prototypes des fonctions publiques du module logger
extern void log_sensor_data(const SensorData_t *data); // 'const' indique que les données ne seront pas modifiées
extern void display_all_logs();

#endif // LOGGER_H
```


## Fichier `logger.c`


```c
#include "logger.h"
#include <stdio.h> // Pour printf

// 3. Implémentation du Module Journalisation

// Constante de préprocesseur pour la taille maximale du journal
#define MAX_LOG_ENTRIES 10

// Tableau global de fichier (static) pour stocker les entrées de log
static SensorData_t log_entries[MAX_LOG_ENTRIES];

// Variable globale de fichier (static) pour suivre le nombre actuel d'entrées
static int log_count = 0;

// Variable globale de fichier (static) pour gérer l'index d'écriture dans le buffer circulaire
static int log_write_index = 0;

/**
 * @brief Ajoute une donnée de capteur au journal.
 *        Gère un buffer circulaire en écrasant les entrées les plus anciennes si le journal est plein.
 * @param data Pointeur vers la structure SensorData_t à logger. Le pointeur est 'const' pour garantir
 *             que la fonction ne modifiera pas les données originales.
 */
void log_sensor_data(const SensorData_t *data) {
    if (data == NULL) {
        fprintf(stderr, "Erreur: Le pointeur de données à logger est NULL.\n");
        return;
    }

    // Variable locale 'static' : son état est conservé entre les appels de fonction.
    // Elle compte le nombre total d'appels à cette fonction.
    static int call_count = 0;
    call_count++;

    // Ajoute la donnée au tableau de logs à l'index actuel
    log_entries[log_write_index] = *data; // Copie la structure entière

    printf("[Log #%d, Appel #%d] Donnée capteur de type %d loggée: Valeur = %d, Timestamp = %ld\n",
           log_write_index, call_count, data->type, data->value, data->timestamp);

    // Incrémente l'index d'écriture et gère le débordement (buffer circulaire)
    log_write_index = (log_write_index + 1) % MAX_LOG_ENTRIES;

    // Incrémente le compteur d'entrées tant que le tableau n'est pas plein
    if (log_count < MAX_LOG_ENTRIES) {
        log_count++;
    }
}

/**
 * @brief Affiche toutes les entrées actuellement stockées dans le journal.
 */
void display_all_logs() {
    printf("\n--- Affichage de toutes les entrées de log (%d entrées) ---\n", log_count);

    // Détermine l'index de départ pour l'affichage dans un buffer circulaire
    // Si le buffer est plein, on commence à partir de l'entrée la plus ancienne (celle qui sera écrasée ensuite).
    // Sinon, on commence à partir de l'index 0.
    int start_index = (log_count == MAX_LOG_ENTRIES) ? log_write_index : 0;

    for (int i = 0; i < log_count; ++i) {
        int current_index = (start_index + i) % MAX_LOG_ENTRIES;
        printf("Log %d: Type = %d, Valeur = %d, Timestamp = %ld\n",
               current_index, log_entries[current_index].type, log_entries[current_index].value, log_entries[current_index].timestamp);
    }
    printf("--------------------------------------------------\n");
}
```


## Fichier `main.c`


```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

// Gestion de la fonction de délai pour la compatibilité multi-plateforme
#ifdef _WIN32
#include <windows.h> // Pour Sleep() sur Windows
#define SLEEP_SEC(x) Sleep(x * 1000)
#else
#include <unistd.h> // Pour sleep() sur Linux/macOS
#define SLEEP_SEC(x) sleep(x)
#endif

#include "sensor.h" // Inclut les définitions du module capteur
#include "logger.h" // Inclut les définitions du module journalisation

// 4. Implémentation de l'Application Principale

/**
 * @brief Fonction de rappel simulant la lecture d'un capteur de température.
 * @param data Pointeur vers la structure SensorData_t à remplir.
 * @return 0 en cas de succès, -1 en cas d'erreur.
 */
static int simulate_temperature_read(SensorData_t *data) {
    if (data == NULL) {
        return -1;
    }
    // Génère une valeur aléatoire entre 18 et 30
    data->value = (rand() % 13) + 18; // (0-12) + 18 => 18-30
    // Le champ 'type' et 'timestamp' seront mis à jour par read_sensor
    return 0;
}

int main() {
    // Initialise le générateur de nombres aléatoires avec l'heure actuelle
    srand(time(NULL));

    // Initialise le capteur en lui passant la fonction de simulation et le type
    init_sensor(simulate_temperature_read, TEMPERATURE);

    // Boucle principale pour simuler 15 lectures et journalisations
    for (int i = 0; i < 15; ++i) {
        SensorData_t current_reading;
        // Tente de lire les données du capteur
        if (read_sensor(&current_reading) == 0) {
            // Si la lecture est réussie, journalise les données
            log_sensor_data(&current_reading);
        } else {
            fprintf(stderr, "Échec de la lecture du capteur à l'itération %d.\n", i);
            // Simule une condition d'erreur du capteur à l'itération 5
            if (i == 5) {
                printf("Main: Simulation d'une panne capteur en mettant sensor_status_register à 0.\n");
                sensor_status_register = 0; // Modifie le registre de statut pour simuler une erreur
            }
        }
        SLEEP_SEC(1); // Introduit un délai d'une seconde
    }

    // Affiche toutes les entrées de log après la boucle
    display_all_logs();

    // --- Tests des concepts avancés ---

    // Tente de modifier la SENSOR_ID (déclarée 'const' dans sensor.c) depuis main.c
    printf("\n--- Test de modification de SENSOR_ID ---\n");
    printf("Valeur actuelle de SENSOR_ID: %d\n", SENSOR_ID);
    // SENSOR_ID = 54321; // Décommenter cette ligne pour observer l'erreur de compilation
    printf("Tentative de modification de SENSOR_ID (commentée car génère une erreur de compilation).\n");
    printf("Si décommenté, cela produirait une erreur de compilation du type:\n");
    printf("  'error: assignment of read-only variable 'SENSOR_ID''\n");
    printf("Ceci est dû au qualificatif 'const' qui rend la variable non modifiable après son initialisation.\n");

    // Tente de modifier directement le sensor_status_register (déclaré 'volatile' dans sensor.c) depuis main.c
    printf("\n--- Test de modification de sensor_status_register ---\n");
    printf("Valeur actuelle de sensor_status_register: 0x%x\n", sensor_status_register);
    sensor_status_register = 0x02; // Modification directe
    printf("sensor_status_register modifié à 0x%x depuis main.c.\n", sensor_status_register);
    printf("Le qualificatif 'volatile' garantit que cette modification sera écrite en mémoire\n");
    printf("et que les lectures ultérieures de cette variable iront chercher sa valeur en mémoire,\n");
    printf("sans que le compilateur n'optimise son accès (par exemple, en la mettant en cache).\n");

    return 0; // Termine le programme
}
```


## Explications et Concepts Clés

*   **`typedef`**: Utilisé pour créer des alias de types existants (`SensorType_t`, `SensorData_t`, `SensorReadCallback_t`). Cela améliore la lisibilité du code et facilite la maintenance en masquant la complexité des déclarations de types (notamment pour les pointeurs de fonctions et les structures).
*   **`enum`**: L'énumération `SensorType_t` fournit un ensemble de constantes nommées (`TEMPERATURE`, `HUMIDITY`, etc.). Elle rend le code plus clair et moins sujet aux erreurs que l'utilisation de simples entiers magiques.
*   **Pointeurs de fonctions**: `SensorReadCallback_t` est un pointeur de fonction. Il permet au module `sensor` d'appeler une fonction de lecture spécifique (ici `simulate_temperature_read`) définie dans `main.c` ou ailleurs. C'est un mécanisme puissant pour l'injection de dépendances et la conception de systèmes modulaires (callbacks).
*   **`static` (portée fichier)**: Les variables `last_reading`, `sensor_callback`, `current_sensor_type` dans `sensor.c` et `log_entries`, `log_count`, `log_write_index` dans `logger.c` sont déclarées `static`. Cela signifie qu'elles ne sont visibles et accessibles qu'à l'intérieur de leur fichier de compilation (`.c`). Elles ne peuvent pas être accédées directement depuis `main.c`, ce qui aide à l'encapsulation et à éviter les conflits de noms globaux.
*   **`static` (portée fonction)**: La variable `call_count` dans `log_sensor_data` est `static`. Elle est initialisée une seule fois et conserve sa valeur entre les appels de la fonction. Elle agit comme un compteur persistant propre à cette fonction.
*   **`extern`**: Les fonctions `init_sensor`, `read_sensor`, `log_sensor_data`, `display_all_logs` sont déclarées `extern` dans leurs fichiers d'en-tête (`.h`). Cela indique que leur définition se trouve dans un autre fichier (`.c`). Pour les besoins du TP, `SENSOR_ID` et `sensor_status_register` sont également déclarées `extern` dans `sensor.h` pour permettre leur accès depuis `main.c` et tester les qualificatifs `const` et `volatile`.
*   **`const`**:
    *   `const int SENSOR_ID`: Rend la variable `SENSOR_ID` non modifiable après son initialisation. Toute tentative de modification (comme `SENSOR_ID = 54321;` dans `main.c`) entraînera une **erreur de compilation** du type `error: assignment of read-only variable 'SENSOR_ID'`. Cela garantit l'intégrité des données et permet au compilateur d'effectuer des optimisations.
    *   `const SensorData_t *data` dans `log_sensor_data`: Indique que la fonction `log_sensor_data` ne modifiera pas les données pointées par `data`. C'est une bonne pratique pour la sécurité du code et pour communiquer l'intention de la fonction.
*   **`volatile`**:
    *   `volatile unsigned int sensor_status_register`: Le qualificatif `volatile` informe le compilateur que la valeur de cette variable peut changer à tout moment, de manière imprévue, par des entités externes au programme (par exemple, un registre matériel, une interruption, un autre thread). Le compilateur est alors contraint de lire la valeur de la variable directement en mémoire à chaque accès et de ne pas la mettre en cache ou réordonner les opérations la concernant.
    *   *Intérêt dans un contexte réel*: `volatile` est crucial dans les systèmes embarqués ou les environnements multi-threadés. Par exemple, un registre de statut matériel (`sensor_status_register`) pourrait être modifié par le capteur lui-même ou par un contrôleur DMA. Sans `volatile`, le compilateur pourrait optimiser les lectures en supposant que la valeur ne change pas entre deux accès, ce qui conduirait à un comportement incorrect. En le déclarant `volatile`, on s'assure que le programme interagit toujours avec la valeur la plus à jour en mémoire.
*   **Aide de l'IA**: L'IA a été utilisée pour structurer le code selon les exigences du TP, générer les ébauches de fonctions et de structures, et s'assurer que tous les concepts avancés demandés sont correctement intégrés. Elle a aidé à la gestion des `#include`, des `typedef`, `enum`, et à la mise en place des `static`, `extern`, `const`, `volatile` aux emplacements appropriés. La révision de l'accessibilité de `SENSOR_ID` et `sensor_status_register` pour les tests spécifiques du TP a été un point où l'IA a ajusté la solution pour mieux coller à l'intention de l'exercice.

---

# Solution 2 : Approche avec Améliorations Mineures

Cette deuxième solution propose quelques légères variations par rapport à la première, notamment dans la gestion du buffer de journalisation et l'utilisation de codes d'erreur plus spécifiques, tout en respectant les objectifs du TP. Les déclarations `extern` pour `SENSOR_ID` et `sensor_status_register` sont maintenues pour les mêmes raisons que dans la solution 1.

## Fichier `sensor.h`


```c
#ifndef SENSOR_H
#define SENSOR_H

#include <time.h> // Nécessaire pour le type long timestamp

// 1. Définition des Types et Structures

// Définition d'une énumération SensorType_t pour les types de capteurs
typedef enum {
    TEMPERATURE,
    HUMIDITY,
    PRESSURE,
    UNKNOWN_SENSOR_TYPE
} SensorType_t;

// Définition d'une structure SensorData_t pour les données du capteur
typedef struct {
    SensorType_t type;
    int value;
    long timestamp;
} SensorData_t;

// Définition d'un type de pointeur de fonction SensorReadCallback_t
typedef int (*SensorReadCallback_t)(SensorData_t *output_data);

// 2. Déclarations du Module Capteur

// Prototypes des fonctions publiques du module sensor
extern void init_sensor(SensorReadCallback_t callback_func, SensorType_t type);
extern int read_sensor(SensorData_t *output_data);

// Déclarations extern pour permettre l'accès à ces variables depuis d'autres fichiers (comme main.c)
extern const int SENSOR_ID;
extern volatile unsigned int sensor_status_register;

#endif // SENSOR_H
```


## Fichier `sensor.c`


```c
#include "sensor.h"
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

// 2. Implémentation du Module Capteur

// Déclaration et initialisation de SENSOR_ID. Non 'static' pour être accessible via 'extern'.
const int SENSOR_ID = 12345;

// Déclaration et initialisation de sensor_status_register. Non 'static' pour être accessible via 'extern'.
volatile unsigned int sensor_status_register = 0x01; // 0x01 = prêt, 0x00 = erreur/non prêt

// Variables internes au module (static)
static SensorData_t last_reading;
static SensorReadCallback_t sensor_callback = NULL;
static SensorType_t current_sensor_type = UNKNOWN_SENSOR_TYPE;

/**
 * @brief Initialise le module capteur.
 * @param callback_func Pointeur vers la fonction de rappel pour la lecture des données.
 * @param type Le type de capteur à simuler.
 */
void init_sensor(SensorReadCallback_t callback_func, SensorType_t type) {
    if (callback_func == NULL) {
        fprintf(stderr, "Erreur: La fonction de rappel ne peut pas être NULL lors de l'initialisation du capteur.\n");
        return;
    }
    sensor_callback = callback_func;
    current_sensor_type = type;
    sensor_status_register = 0x01; // Marque le capteur comme prêt
    printf("Capteur %d initialisé avec le type %d.\n", SENSOR_ID, current_sensor_type);
}

/**
 * @brief Lit les données du capteur.
 * @param output_data Pointeur vers une structure SensorData_t où stocker les données lues.
 * @return 0 en cas de succès, ou un code d'erreur négatif spécifique.
 */
int read_sensor(SensorData_t *output_data) {
    if (output_data == NULL) {
        fprintf(stderr, "Erreur: Le pointeur output_data est NULL dans read_sensor.\n");
        return -101; // Code d'erreur: pointeur invalide
    }

    if (sensor_status_register == 0) {
        fprintf(stderr, "Erreur: Le capteur n'est pas prêt ou est en erreur (status_register = 0).\n");
        return -102; // Code d'erreur: capteur non prêt
    }

    if (sensor_callback == NULL) {
        fprintf(stderr, "Erreur: La fonction de rappel de lecture n'est pas définie dans read_sensor.\n");
        return -103; // Code d'erreur: callback non défini
    }

    if (sensor_callback(&last_reading) != 0) {
        fprintf(stderr, "Erreur lors de l'appel de la fonction de rappel du capteur.\n");
        return -104; // Code d'erreur: échec du callback
    }

    last_reading.type = current_sensor_type;
    last_reading.timestamp = time(NULL);

    *output_data = last_reading;

    return 0; // Succès
}
```


## Fichier `logger.h`


```c
#ifndef LOGGER_H
#define LOGGER_H

#include "sensor.h" // Nécessaire pour utiliser SensorData_t

// 3. Déclarations du Module Journalisation

// Prototypes des fonctions publiques du module logger
extern void log_sensor_data(const SensorData_t *data);
extern void display_all_logs();

#endif // LOGGER_H
```


## Fichier `logger.c`


```c
#include "logger.h"
#include <stdio.h> // Pour printf

// 3. Implémentation du Module Journalisation

// Constante de préprocesseur pour la taille maximale du journal
#define MAX_LOG_ENTRIES 10

// Tableau global de fichier (static) pour stocker les entrées de log
static SensorData_t log_entries[MAX_LOG_ENTRIES];

// Variable globale de fichier (static) pour suivre le nombre actuel d'entrées
static int log_count = 0;
// Variable globale de fichier (static) pour gérer l'index d'écriture dans le buffer circulaire
static int log_head_index = 0; // Index où la prochaine entrée sera écrite

/**
 * @brief Ajoute une donnée de capteur au journal.
 *        Implémente un buffer circulaire pour gérer les entrées.
 * @param data Pointeur vers la structure SensorData_t à logger. Le pointeur est 'const'.
 */
void log_sensor_data(const SensorData_t *data) {
    if (data == NULL) {
        fprintf(stderr, "Erreur: Le pointeur de données à logger est NULL.\n");
        return;
    }

    static int call_count = 0;
    call_count++;

    // Écrit la nouvelle donnée à l'emplacement 'log_head_index'
    log_entries[log_head_index] = *data;

    printf("[Log #%d, Appel #%d] Donnée capteur de type %d loggée: Valeur = %d, Timestamp = %ld\n",
           log_head_index, call_count, data->type, data->value, data->timestamp);

    // Avance l'index de tête, en gérant le wrap-around pour le buffer circulaire
    log_head_index = (log_head_index + 1) % MAX_LOG_ENTRIES;

    // Incrémente le compteur d'entrées si le buffer n'est pas encore plein
    if (log_count < MAX_LOG_ENTRIES) {
        log_count++;
    }
}

/**
 * @brief Affiche toutes les entrées actuellement stockées dans le journal.
 *        L'affichage respecte l'ordre chronologique des entrées dans le buffer circulaire.
 */
void display_all_logs() {
    printf("\n--- Affichage de toutes les entrées de log (%d entrées) ---\n", log_count);

    // Calcule l'index de la plus ancienne entrée pour un affichage chronologique
    // Si le buffer est plein (log_count == MAX_LOG_ENTRIES), la plus ancienne est à log_head_index.
    // Sinon, c'est l'index 0.
    int start_display_index = (log_count == MAX_LOG_ENTRIES) ? log_head_index : 0;

    for (int i = 0; i < log_count; ++i) {
        int current_index = (start_display_index + i) % MAX_LOG_ENTRIES;
        printf("Log %d: Type = %d, Valeur = %d, Timestamp = %ld\n",
               current_index, log_entries[current_index].type, log_entries[current_index].value, log_entries[current_index].timestamp);
    }
    printf("--------------------------------------------------\n");
}
```


## Fichier `main.c`


```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

// Gestion de la fonction de délai pour la compatibilité multi-plateforme
#ifdef _WIN32
#include <windows.h> // Pour Sleep() sur Windows
#define SLEEP_SEC(x) Sleep(x * 1000)
#else
#include <unistd.h> // Pour sleep() sur Linux/macOS
#define SLEEP_SEC(x) sleep(x)
#endif

#include "sensor.h"
#include "logger.h"

// 4. Implémentation de l'Application Principale

static int simulate_temperature_read(SensorData_t *data) {
    if (data == NULL) {
        return -1;
    }
    data->value = (rand() % 13) + 18; // 18-30
    return 0;
}

int main() {
    srand(time(NULL));

    init_sensor(simulate_temperature_read, TEMPERATURE);

    for (int i = 0; i < 15; ++i) {
        SensorData_t current_reading;
        int read_status = read_sensor(&current_reading);

        if (read_status == 0) {
            log_sensor_data(&current_reading);
        } else {
            fprintf(stderr, "Échec de la lecture du capteur à l'itération %d. Code d'erreur: %d\n", i, read_status);
            // Si le capteur est en erreur (-102), on tente de le "réparer" après quelques itérations
            if (read_status == -102 && i > 5) {
                printf("Main: Tentative de réinitialisation du capteur en mettant sensor_status_register à 0x01.\n");
                sensor_status_register = 0x01; // Remet le capteur en état de marche
            }
        }
        SLEEP_SEC(1);
    }

    display_all_logs();

    // --- Tests des concepts avancés ---

    // Tente de modifier la SENSOR_ID (déclarée 'const' dans sensor.c) depuis main.c
    printf("\n--- Test de modification de SENSOR_ID ---\n");
    printf("Valeur actuelle de SENSOR_ID: %d\n", SENSOR_ID);
    // SENSOR_ID = 54321; // Décommenter cette ligne pour observer l'erreur de compilation
    printf("Tentative de modification de SENSOR_ID (commentée car génère une erreur de compilation).\n");
    printf("Si décommenté, cela produirait une erreur de compilation du type:\n");
    printf("  'error: assignment of read-only variable 'SENSOR_ID''\n");
    printf("Ceci est dû au qualificatif 'const' qui rend la variable non modifiable après son initialisation.\n");

    // Tente de modifier directement le sensor_status_register (déclaré 'volatile' dans sensor.c) depuis main.c
    printf("\n--- Test de modification de sensor_status_register ---\n");
    printf("Valeur actuelle de sensor_status_register: 0x%x\n", sensor_status_register);
    // Simule une modification externe après la boucle principale
    printf("Simulating an external event modifying sensor_status_register to 0xFF.\n");
    sensor_status_register = 0xFF; // Modification directe
    printf("sensor_status_register modifié à 0x%x depuis main.c.\n", sensor_status_register);
    printf("Le qualificatif 'volatile' garantit que cette modification sera écrite en mémoire\n");
    printf("et que les lectures ultérieures de cette variable iront chercher sa valeur en mémoire,\n");
    printf("sans que le compilateur n'optimise son accès (par exemple, en la mettant en cache).\n");

    return 0;
}
```


## Explications et Concepts Clés

*   **`typedef`**: Identique à la solution 1. Facilite la gestion des types complexes et améliore la lisibilité.
*   **`enum`**: Identique à la solution 1. Offre des constantes nommées pour une meilleure sémantique.
*   **Pointeurs de fonctions**: Identique à la solution 1. Permet une architecture flexible où le comportement de lecture du capteur peut être défini dynamiquement.
*   **`static` (portée fichier)**: Identique à la solution 1. Assure l'encapsulation des variables internes aux modules `sensor` et `logger`, prévenant les interférences externes.
*   **`static` (portée fonction)**: Identique à la solution 1. `call_count` dans `log_sensor_data` maintient son état entre les appels, servant de compteur d'exécution.
*   **`extern`**: Identique à la solution 1. Déclare les fonctions et les variables globales (`SENSOR_ID`, `sensor_status_register`) qui sont définies dans d'autres fichiers, permettant leur utilisation inter-modules.
*   **`const`**:
    *   `const int SENSOR_ID`: Rend `SENSOR_ID` non modifiable. Tenter de l'assigner dans `main.c` résultera en une **erreur de compilation** (`error: assignment of read-only variable 'SENSOR_ID'`). Ce qualificatif est essentiel pour définir des constantes au moment de la compilation et garantir l'immuabilité des données critiques.
    *   `const SensorData_t *data` dans `log_sensor_data`: Indique que la fonction `log_sensor_data` s'engage à ne pas modifier les données pointées par `data`. C'est une garantie pour l'appelant et une aide pour le compilateur.
*   **`volatile`**:
    *   `volatile unsigned int sensor_status_register`: Le qualificatif `volatile` est utilisé pour `sensor_status_register`. Il force le compilateur à ne pas optimiser les accès à cette variable. Chaque lecture de `sensor_status_register` ira chercher sa valeur en mémoire, et chaque écriture sera directement appliquée en mémoire.
    *   *Intérêt dans un contexte réel*: Dans cette solution, nous avons simulé une modification du `sensor_status_register` depuis `main.c` (pour le test) et imaginé qu'une erreur de capteur pourrait le mettre à 0. Le fait qu'il soit `volatile` est vital pour les interactions avec le matériel (registres d'E/S, périphériques) ou dans des systèmes multi-threadés où une autre partie du système (non contrôlée par le compilateur actuel) pourrait modifier la variable. Sans `volatile`, le compilateur pourrait mettre la valeur en cache et utiliser une valeur obsolète, menant à un comportement imprévisible.
*   **Aide de l'IA**: L'IA a été un assistant pour affiner la logique du buffer circulaire dans `logger.c` (en rendant l'indexation plus explicite pour l'affichage chronologique) et pour diversifier les codes d'erreur dans `read_sensor`. Elle a également aidé à peaufiner les explications des concepts, en particulier pour les nuances de `const` et `volatile` dans le contexte des tests demandés par le TP, en s'assurant que les erreurs de compilation attendues soient bien mises en évidence.

---
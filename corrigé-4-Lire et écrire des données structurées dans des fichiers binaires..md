Voici deux solutions complètes pour le TP "Lecture/écriture de données structurées dans un fichier binaire", chacune présentée dans un chapitre Markdown distinct. Ces solutions incluent les fichiers de code et des explications détaillées.

---

# Solution 1 : Implémentation Modulaire et Robuste

Cette solution organise le code en plusieurs fichiers (`song.h`, `library.h`, `library.c`, `main.c`) pour une meilleure modularité. Elle implémente toutes les exigences du TP avec une gestion robuste des erreurs et utilise `fgets` pour la saisie utilisateur.

## Fichier `song.h`



```c
#ifndef SONG_H
#define SONG_H

// Définition de la structure Song
typedef struct Song {
    char title[100];         // Titre de la chanson (taille fixe)
    char artist[100];        // Artiste (taille fixe)
    int duration_seconds;    // Durée en secondes
    int track_number;        // Numéro de piste
} Song;

#endif // SONG_H
```



## Fichier `library.h`



```c
#ifndef LIBRARY_H
#define LIBRARY_H

#include "song.h" // Nécessaire pour la structure Song

// Fonctions de gestion de la bibliothèque en mémoire
/**
 * @brief Ajoute une nouvelle chanson à la bibliothèque en demandant les informations à l'utilisateur.
 *        Redimensionne la bibliothèque si nécessaire.
 * @param library Pointeur vers le pointeur de la bibliothèque (tableau dynamique de Song).
 * @param song_count Pointeur vers le compteur du nombre de chansons.
 * @param library_capacity Pointeur vers la capacité actuelle de la bibliothèque.
 */
void add_song_to_library(Song **library, int *song_count, int *library_capacity);

/**
 * @brief Affiche toutes les chansons actuellement dans la bibliothèque.
 * @param library Pointeur constant vers le tableau de Song.
 * @param song_count Le nombre de chansons à afficher.
 */
void display_library(const Song *library, int song_count);

// Fonctions de persistance (lecture/écriture binaire)
/**
 * @brief Sauvegarde l'intégralité de la bibliothèque en mémoire dans un fichier binaire.
 *        Écrit d'abord le nombre de chansons, puis chaque structure Song.
 * @param filename Le nom du fichier binaire.
 * @param library Pointeur constant vers le tableau de Song à sauvegarder.
 * @param song_count Le nombre de chansons à sauvegarder.
 */
void save_library_to_file(const char *filename, const Song *library, int song_count);

/**
 * @brief Charge une bibliothèque depuis un fichier binaire dans la mémoire.
 *        Lit d'abord le nombre de chansons, puis chaque structure Song.
 *        Libère l'ancienne bibliothèque en mémoire et alloue la nouvelle.
 * @param filename Le nom du fichier binaire.
 * @param library Pointeur vers le pointeur de la bibliothèque (sera mis à jour).
 * @param song_count Pointeur vers le compteur du nombre de chansons (sera mis à jour).
 * @param library_capacity Pointeur vers la capacité actuelle de la bibliothèque (sera mis à jour).
 */
void load_library_from_file(const char *filename, Song **library, int *song_count, int *library_capacity);

#endif // LIBRARY_H
```



## Fichier `library.c`



```c
#include "library.h"
#include <stdio.h>  // Pour FILE, fopen, fclose, printf, fprintf, perror, fgets
#include <stdlib.h> // Pour malloc, realloc, free, EXIT_FAILURE
#include <string.h> // Pour strncpy, strlen, strcspn

// Taille initiale et facteur de croissance pour la bibliothèque dynamique
#define INITIAL_CAPACITY 5
#define GROWTH_FACTOR    2

/**
 * @brief Lit une ligne de l'entrée standard de manière sécurisée.
 * @param buffer Le tampon où stocker la ligne lue.
 * @param size La taille maximale du tampon.
 */
static void read_line(char *buffer, int size) {
    if (fgets(buffer, size, stdin) != NULL) {
        // Supprimer le caractère de nouvelle ligne si présent
        buffer[strcspn(buffer, "\n")] = '\0';
    } else {
        // Gérer l'erreur de lecture (ex: EOF)
        fprintf(stderr, "Erreur de lecture de l'entrée.\n");
        buffer[0] = '\0'; // Assurer que le buffer est vide
    }
}

void add_song_to_library(Song **library, int *song_count, int *library_capacity) {
    // Vérifier si la capacité est suffisante, sinon la doubler
    if (*song_count >= *library_capacity) {
        *library_capacity = (*library_capacity == 0) ? INITIAL_CAPACITY : (*library_capacity * GROWTH_FACTOR);
        Song *temp_library = (Song *)realloc(*library, *library_capacity * sizeof(Song));
        if (temp_library == NULL) {
            perror("Erreur de réallocation mémoire pour la bibliothèque");
            // Ne pas modifier *library pour éviter de perdre l'ancienne référence
            // Le programme devrait idéalement quitter ou gérer cette erreur plus en amont
            return;
        }
        *library = temp_library;
        printf("Capacité de la bibliothèque augmentée à %d.\n", *library_capacity);
    }

    Song new_song;
    printf("\n--- Ajout d'une nouvelle chanson ---\n");

    printf("Titre: ");
    read_line(new_song.title, sizeof(new_song.title));

    printf("Artiste: ");
    read_line(new_song.artist, sizeof(new_song.artist));

    printf("Durée (secondes): ");
    if (scanf("%d", &new_song.duration_seconds) != 1) {
        fprintf(stderr, "Entrée invalide pour la durée.\n");
        while (getchar() != '\n'); // Nettoyer le buffer d'entrée
        return;
    }
    // Nettoyer le buffer après scanf
    while (getchar() != '\n');

    printf("Numéro de piste: ");
    if (scanf("%d", &new_song.track_number) != 1) {
        fprintf(stderr, "Entrée invalide pour le numéro de piste.\n");
        while (getchar() != '\n'); // Nettoyer le buffer d'entrée
        return;
    }
    // Nettoyer le buffer après scanf
    while (getchar() != '\n');

    // Ajouter la nouvelle chanson au tableau
    (*library)[*song_count] = new_song;
    (*song_count)++;
    printf("Chanson '%s' ajoutée à la bibliothèque.\n", new_song.title);
}

void display_library(const Song *library, int song_count) {
    printf("\n--- Bibliothèque Musicale (%d chansons) ---\n", song_count);
    if (song_count == 0) {
        printf("La bibliothèque est vide.\n");
        return;
    }

    for (int i = 0; i < song_count; i++) {
        printf("  %d. Titre: %s, Artiste: %s, Durée: %d s, Piste: %d\n",
               i + 1, library[i].title, library[i].artist,
               library[i].duration_seconds, library[i].track_number);
    }
    printf("-----------------------------------------\n");
}

void save_library_to_file(const char *filename, const Song *library, int song_count) {
    FILE *file = fopen(filename, "wb"); // Ouvrir en mode écriture binaire
    if (file == NULL) {
        perror("Erreur lors de l'ouverture du fichier pour la sauvegarde");
        return;
    }

    // Écrire d'abord le nombre de chansons
    if (fwrite(&song_count, sizeof(int), 1, file) != 1) {
        perror("Erreur lors de l'écriture du nombre de chansons");
        fclose(file);
        return;
    }

    // Écrire toutes les structures Song
    if (fwrite(library, sizeof(Song), song_count, file) != song_count) {
        perror("Erreur lors de l'écriture des chansons");
        fclose(file);
        return;
    }

    printf("Bibliothèque sauvegardée dans '%s' (%d chansons).\n", filename, song_count);
    fclose(file); // Toujours fermer le fichier
}

void load_library_from_file(const char *filename, Song **library, int *song_count, int *library_capacity) {
    FILE *file = fopen(filename, "rb"); // Ouvrir en mode lecture binaire
    if (file == NULL) {
        perror("Erreur lors de l'ouverture du fichier pour le chargement");
        // Si le fichier n'existe pas, on peut considérer que la bibliothèque est vide
        // et ne pas afficher d'erreur fatale, mais c'est un choix de conception.
        printf("Création d'une nouvelle bibliothèque vide.\n");
        if (*library != NULL) {
            free(*library);
            *library = NULL;
        }
        *song_count = 0;
        *library_capacity = 0; // Sera réinitialisé à INITIAL_CAPACITY lors du prochain add
        return;
    }

    int loaded_song_count = 0;
    // Lire le nombre de chansons
    if (fread(&loaded_song_count, sizeof(int), 1, file) != 1) {
        perror("Erreur lors de la lecture du nombre de chansons");
        fclose(file);
        return;
    }

    // Libérer l'ancienne bibliothèque si elle existe
    if (*library != NULL) {
        free(*library);
        *library = NULL;
    }

    // Allouer la mémoire nécessaire pour la nouvelle bibliothèque
    *library_capacity = loaded_song_count; // La capacité est exactement le nombre de chansons chargées
    *library = (Song *)malloc(*library_capacity * sizeof(Song));
    if (*library == NULL) {
        perror("Erreur d'allocation mémoire pour le chargement de la bibliothèque");
        fclose(file);
        *song_count = 0;
        *library_capacity = 0;
        return;
    }

    // Lire toutes les structures Song
    if (fread(*library, sizeof(Song), loaded_song_count, file) != loaded_song_count) {
        perror("Erreur lors de la lecture des chansons");
        free(*library); // Libérer la mémoire allouée si la lecture échoue
        *library = NULL;
        *song_count = 0;
        *library_capacity = 0;
        fclose(file);
        return;
    }

    *song_count = loaded_song_count;
    printf("Bibliothèque chargée depuis '%s' (%d chansons).\n", filename, *song_count);
    fclose(file);
}
```



## Fichier `main.c`



```c
#include "library.h"
#include <stdio.h>
#include <stdlib.h> // Pour EXIT_SUCCESS, EXIT_FAILURE

// Nom du fichier de sauvegarde par défaut
#define LIBRARY_FILENAME "library.bin"

int main() {
    Song *my_library = NULL; // Pointeur vers le tableau dynamique de chansons
    int song_count = 0;      // Nombre actuel de chansons
    int library_capacity = 0; // Capacité allouée pour le tableau

    int choice;

    // Tenter de charger la bibliothèque au démarrage
    printf("Tentative de chargement de la bibliothèque au démarrage...\n");
    load_library_from_file(LIBRARY_FILENAME, &my_library, &song_count, &library_capacity);

    do {
        printf("\n--- Menu de la Bibliothèque Musicale ---\n");
        printf("1. Ajouter une chanson\n");
        printf("2. Afficher la bibliothèque\n");
        printf("3. Sauvegarder la bibliothèque\n");
        printf("4. Charger la bibliothèque\n");
        printf("5. Quitter\n");
        printf("Entrez votre choix: ");

        if (scanf("%d", &choice) != 1) {
            fprintf(stderr, "Entrée invalide. Veuillez entrer un nombre.\n");
            while (getchar() != '\n'); // Nettoyer le buffer d'entrée
            choice = 0; // Réinitialiser le choix pour éviter une boucle infinie
            continue;
        }
        // Nettoyer le buffer après scanf
        while (getchar() != '\n');

        switch (choice) {
            case 1:
                add_song_to_library(&my_library, &song_count, &library_capacity);
                break;
            case 2:
                display_library(my_library, song_count);
                break;
            case 3:
                save_library_to_file(LIBRARY_FILENAME, my_library, song_count);
                break;
            case 4:
                load_library_from_file(LIBRARY_FILENAME, &my_library, &song_count, &library_capacity);
                break;
            case 5:
                printf("Quitter le programme. Au revoir !\n");
                break;
            default:
                printf("Choix invalide. Veuillez réessayer.\n");
                break;
        }
    } while (choice != 5);

    // Libérer la mémoire allouée pour la bibliothèque avant de quitter
    if (my_library != NULL) {
        free(my_library);
        my_library = NULL;
    }

    return EXIT_SUCCESS;
}
```



## Compilation

Pour compiler cette solution, utilisez la commande suivante :
`gcc -Wall -Wextra -std=c11 library.c main.c -o music_library_s1`

## Explications des Concepts Clés (Solution 1)

### Exercice 1 : Définition de la Structure de Données

*   **`struct Song`**: Utilise des tableaux de caractères de taille fixe (`char title[100];`) pour les chaînes. C'est crucial pour l'écriture binaire directe car cela garantit que la taille de la structure `Song` est toujours la même, quel que soit le contenu des chaînes. Si des pointeurs (`char *title;`) avaient été utilisés, seule l'adresse du pointeur serait écrite, pas le contenu de la chaîne, ce qui rendrait la lecture impossible sans une gestion complexe.

### Exercice 2 & 3 : Lecture/Écriture d'une Chanson dans un Fichier Binaire

*   **`fwrite(&song, sizeof(Song), 1, file)`**: Écrit le contenu binaire exact de la structure `Song` dans le fichier. `sizeof(Song)` garantit que tous les octets de la structure sont écrits.
*   **`fread(&song, sizeof(Song), 1, file)`**: Lit un bloc d'octets de la taille d'une `Song` depuis le fichier et le place directement dans la variable `song`.
*   **Modes binaires (`"wb"`, `"rb"`)**: L'utilisation de `b` dans le mode d'ouverture (`"wb"`, `"rb"`) est essentielle pour les fichiers binaires. Sur certains systèmes (notamment Windows), cela empêche la traduction des caractères de fin de ligne (`\n` en `\r\n`), garantissant que les données sont lues et écrites octet par octet sans modification.
*   **Gestion des erreurs**: `fopen` retourne `NULL` en cas d'échec. `fwrite` et `fread` retournent le nombre d'éléments lus/écrits. Ces retours sont vérifiés, et `perror()` est utilisé pour afficher des messages d'erreur système pertinents.

### Exercice 4 : Gestion de la Bibliothèque Musicale

*   **Tableau dynamique de `Song` (`Song *library`)**: La bibliothèque est gérée comme un tableau de structures `Song` alloué dynamiquement. `song_count` suit le nombre d'éléments réels, et `library_capacity` la taille allouée.
*   **`realloc`**: Utilisé dans `add_song_to_library` pour augmenter la taille du tableau `library` lorsque la capacité est atteinte. `realloc` est efficace car il tente d'étendre le bloc mémoire existant avant d'en allouer un nouveau et de copier les données.
*   **Sauvegarde (`save_library_to_file`)**:
    1.  Écrit d'abord `song_count` (un `int`) dans le fichier. C'est une information cruciale pour le chargement, car elle indique combien de structures `Song` il faudra lire.
    2.  Écrit ensuite tout le tableau `library` d'un seul coup avec `fwrite(library, sizeof(Song), song_count, file)`. C'est très efficace.
*   **Chargement (`load_library_from_file`)**:
    1.  Lit d'abord le `song_count` depuis le fichier.
    2.  Libère toute `*library` existante pour éviter les fuites de mémoire.
    3.  Alloue un nouveau bloc de mémoire avec `malloc` pour accueillir toutes les chansons à charger.
    4.  Lit toutes les structures `Song` d'un seul coup avec `fread(*library, sizeof(Song), loaded_song_count, file)`.
*   **`fgets` et `strcspn`**: Utilisés pour la saisie utilisateur afin de lire des lignes complètes (y compris les espaces) et d'éviter les débordements de tampon. `strcspn(buffer, "\n")` trouve la position du `\n` et le remplace par `\0` pour une chaîne propre.
*   **`main`**: Gère le menu interactif, initialise la bibliothèque, et assure la libération finale de la mémoire avec `free(my_library)` avant de quitter. Il tente également de charger une bibliothèque existante au démarrage.

---

# Solution 2 : Implémentation Monofichier avec Gestion de Chaînes de Taille Variable (Bonus)

Cette solution regroupe tout le code dans un seul fichier `main.c` et explore le défi bonus de la gestion des chaînes de caractères de taille variable dans le fichier binaire. Cela rend la structure `Song` plus flexible mais la logique de lecture/écriture plus complexe.

## Fichier `main.c`



```c
#include <stdio.h>
#include <stdlib.h> // Pour malloc, realloc, free, EXIT_SUCCESS, EXIT_FAILURE
#include <string.h> // Pour strncpy, strlen, strcspn, strcmp

// Taille initiale et facteur de croissance pour la bibliothèque dynamique
#define INITIAL_CAPACITY 5
#define GROWTH_FACTOR    2

// Taille maximale des chaînes pour la saisie utilisateur (pour fgets)
#define MAX_STRING_INPUT 256

// Définition de la structure Song avec pointeurs pour les chaînes (taille variable)
typedef struct Song {
    char *title;             // Pointeur vers le titre (taille variable)
    char *artist;            // Pointeur vers l'artiste (taille variable)
    int duration_seconds;    // Durée en secondes
    int track_number;        // Numéro de piste
} Song;

// --- Fonctions utilitaires pour la gestion des chaînes (pour la structure Song) ---

/**
 * @brief Alloue et copie une chaîne de caractères.
 * @param src La chaîne source.
 * @return Un pointeur vers la nouvelle chaîne allouée, ou NULL en cas d'échec.
 */
static char* allocate_and_copy_string(const char* src) {
    if (src == NULL) return NULL;
    size_t len = strlen(src);
    char* dest = (char*)malloc(len + 1);
    if (dest == NULL) {
        perror("Erreur d'allocation mémoire pour la chaîne");
        return NULL;
    }
    strcpy(dest, src);
    return dest;
}

/**
 * @brief Libère la mémoire allouée pour les chaînes d'une structure Song.
 * @param song Pointeur vers la structure Song.
 */
static void free_song_strings(Song *song) {
    if (song == NULL) return;
    free(song->title);
    free(song->artist);
    song->title = NULL;
    song->artist = NULL;
}

/**
 * @brief Lit une ligne de l'entrée standard de manière sécurisée.
 * @param buffer Le tampon où stocker la ligne lue.
 * @param size La taille maximale du tampon.
 */
static void read_line(char *buffer, int size) {
    if (fgets(buffer, size, stdin) != NULL) {
        buffer[strcspn(buffer, "\n")] = '\0';
    } else {
        fprintf(stderr, "Erreur de lecture de l'entrée.\n");
        buffer[0] = '\0';
    }
}

// --- Fonctions de gestion de la bibliothèque en mémoire ---

/**
 * @brief Ajoute une nouvelle chanson à la bibliothèque en demandant les informations à l'utilisateur.
 *        Redimensionne la bibliothèque si nécessaire.
 * @param library Pointeur vers le pointeur de la bibliothèque (tableau dynamique de Song).
 * @param song_count Pointeur vers le compteur du nombre de chansons.
 * @param library_capacity Pointeur vers la capacité actuelle de la bibliothèque.
 */
void add_song_to_library(Song **library, int *song_count, int *library_capacity) {
    if (*song_count >= *library_capacity) {
        *library_capacity = (*library_capacity == 0) ? INITIAL_CAPACITY : (*library_capacity * GROWTH_FACTOR);
        Song *temp_library = (Song *)realloc(*library, *library_capacity * sizeof(Song));
        if (temp_library == NULL) {
            perror("Erreur de réallocation mémoire pour la bibliothèque");
            return;
        }
        *library = temp_library;
        printf("Capacité de la bibliothèque augmentée à %d.\n", *library_capacity);
    }

    Song new_song = {NULL, NULL, 0, 0}; // Initialiser les pointeurs à NULL
    char temp_buffer[MAX_STRING_INPUT];

    printf("\n--- Ajout d'une nouvelle chanson ---\n");

    printf("Titre: ");
    read_line(temp_buffer, sizeof(temp_buffer));
    new_song.title = allocate_and_copy_string(temp_buffer);
    if (new_song.title == NULL) {
        free_song_strings(&new_song); // Libérer ce qui a pu être alloué
        return;
    }

    printf("Artiste: ");
    read_line(temp_buffer, sizeof(temp_buffer));
    new_song.artist = allocate_and_copy_string(temp_buffer);
    if (new_song.artist == NULL) {
        free_song_strings(&new_song);
        return;
    }

    printf("Durée (secondes): ");
    if (scanf("%d", &new_song.duration_seconds) != 1) {
        fprintf(stderr, "Entrée invalide pour la durée.\n");
        while (getchar() != '\n');
        free_song_strings(&new_song);
        return;
    }
    while (getchar() != '\n');

    printf("Numéro de piste: ");
    if (scanf("%d", &new_song.track_number) != 1) {
        fprintf(stderr, "Entrée invalide pour le numéro de piste.\n");
        while (getchar() != '\n');
        free_song_strings(&new_song);
        return;
    }
    while (getchar() != '\n');

    (*library)[*song_count] = new_song;
    (*song_count)++;
    printf("Chanson '%s' ajoutée à la bibliothèque.\n", new_song.title);
}

/**
 * @brief Affiche toutes les chansons actuellement dans la bibliothèque.
 * @param library Pointeur constant vers le tableau de Song.
 * @param song_count Le nombre de chansons à afficher.
 */
void display_library(const Song *library, int song_count) {
    printf("\n--- Bibliothèque Musicale (%d chansons) ---\n", song_count);
    if (song_count == 0) {
        printf("La bibliothèque est vide.\n");
        return;
    }

    for (int i = 0; i < song_count; i++) {
        printf("  %d. Titre: %s, Artiste: %s, Durée: %d s, Piste: %d\n",
               i + 1, library[i].title, library[i].artist,
               library[i].duration_seconds, library[i].track_number);
    }
    printf("-----------------------------------------\n");
}

// --- Fonctions de persistance (lecture/écriture binaire avec chaînes de taille variable) ---

/**
 * @brief Écrit une chaîne de caractères de taille variable dans un fichier binaire.
 *        Écrit d'abord la longueur de la chaîne, puis la chaîne elle-même.
 * @param file Le pointeur de fichier.
 * @param str La chaîne à écrire.
 * @return 0 en cas de succès, -1 en cas d'erreur.
 */
static int write_variable_string(FILE *file, const char *str) {
    size_t len = (str == NULL) ? 0 : strlen(str);
    if (fwrite(&len, sizeof(size_t), 1, file) != 1) {
        perror("Erreur lors de l'écriture de la longueur de la chaîne");
        return -1;
    }
    if (len > 0) {
        if (fwrite(str, sizeof(char), len, file) != len) {
            perror("Erreur lors de l'écriture de la chaîne");
            return -1;
        }
    }
    return 0;
}

/**
 * @brief Lit une chaîne de caractères de taille variable depuis un fichier binaire.
 *        Lit d'abord la longueur de la chaîne, puis alloue la mémoire et lit la chaîne.
 * @param file Le pointeur de fichier.
 * @return Un pointeur vers la chaîne lue (allouée dynamiquement), ou NULL en cas d'erreur.
 */
static char* read_variable_string(FILE *file) {
    size_t len;
    if (fread(&len, sizeof(size_t), 1, file) != 1) {
        perror("Erreur lors de la lecture de la longueur de la chaîne");
        return NULL;
    }
    if (len == 0) {
        return allocate_and_copy_string(""); // Retourne une chaîne vide allouée
    }

    char* str = (char*)malloc(len + 1);
    if (str == NULL) {
        perror("Erreur d'allocation mémoire pour la chaîne lue");
        return NULL;
    }
    if (fread(str, sizeof(char), len, file) != len) {
        perror("Erreur lors de la lecture de la chaîne");
        free(str);
        return NULL;
    }
    str[len] = '\0'; // Assurer la null-termination
    return str;
}


/**
 * @brief Sauvegarde l'intégralité de la bibliothèque en mémoire dans un fichier binaire.
 *        Écrit d'abord le nombre de chansons, puis chaque structure Song (avec chaînes variables).
 * @param filename Le nom du fichier binaire.
 * @param library Pointeur constant vers le tableau de Song à sauvegarder.
 * @param song_count Le nombre de chansons à sauvegarder.
 */
void save_library_to_file(const char *filename, const Song *library, int song_count) {
    FILE *file = fopen(filename, "wb");
    if (file == NULL) {
        perror("Erreur lors de l'ouverture du fichier pour la sauvegarde");
        return;
    }

    if (fwrite(&song_count, sizeof(int), 1, file) != 1) {
        perror("Erreur lors de l'écriture du nombre de chansons");
        fclose(file);
        return;
    }

    for (int i = 0; i < song_count; i++) {
        // Écrire les chaînes de taille variable
        if (write_variable_string(file, library[i].title) != 0) { fclose(file); return; }
        if (write_variable_string(file, library[i].artist) != 0) { fclose(file); return; }

        // Écrire les champs int
        if (fwrite(&library[i].duration_seconds, sizeof(int), 1, file) != 1) { perror("Err durée"); fclose(file); return; }
        if (fwrite(&library[i].track_number, sizeof(int), 1, file) != 1) { perror("Err piste"); fclose(file); return; }
    }

    printf("Bibliothèque sauvegardée dans '%s' (%d chansons).\n", filename, song_count);
    fclose(file);
}

/**
 * @brief Charge une bibliothèque depuis un fichier binaire dans la mémoire.
 *        Lit d'abord le nombre de chansons, puis chaque structure Song (avec chaînes variables).
 *        Libère l'ancienne bibliothèque en mémoire et alloue la nouvelle.
 * @param filename Le nom du fichier binaire.
 * @param library Pointeur vers le pointeur de la bibliothèque (sera mis à jour).
 * @param song_count Pointeur vers le compteur du nombre de chansons (sera mis à jour).
 * @param library_capacity Pointeur vers la capacité actuelle de la bibliothèque (sera mis à jour).
 */
void load_library_from_file(const char *filename, Song **library, int *song_count, int *library_capacity) {
    FILE *file = fopen(filename, "rb");
    if (file == NULL) {
        perror("Erreur lors de l'ouverture du fichier pour le chargement");
        printf("Création d'une nouvelle bibliothèque vide.\n");
        if (*library != NULL) {
            for (int i = 0; i < *song_count; i++) {
                free_song_strings(&(*library)[i]);
            }
            free(*library);
            *library = NULL;
        }
        *song_count = 0;
        *library_capacity = 0;
        return;
    }

    int loaded_song_count = 0;
    if (fread(&loaded_song_count, sizeof(int), 1, file) != 1) {
        perror("Erreur lors de la lecture du nombre de chansons");
        fclose(file);
        return;
    }

    // Libérer l'ancienne bibliothèque et ses chaînes
    if (*library != NULL) {
        for (int i = 0; i < *song_count; i++) {
            free_song_strings(&(*library)[i]);
        }
        free(*library);
        *library = NULL;
    }

    *library_capacity = loaded_song_count;
    *library = (Song *)malloc(*library_capacity * sizeof(Song));
    if (*library == NULL) {
        perror("Erreur d'allocation mémoire pour le chargement de la bibliothèque");
        fclose(file);
        *song_count = 0;
        *library_capacity = 0;
        return;
    }

    for (int i = 0; i < loaded_song_count; i++) {
        (*library)[i].title = read_variable_string(file);
        if ((*library)[i].title == NULL) {
            perror("Erreur lecture titre");
            // Nettoyage partiel en cas d'erreur
            for (int j = 0; j < i; j++) free_song_strings(&(*library)[j]);
            free(*library); *library = NULL; *song_count = 0; *library_capacity = 0; fclose(file); return;
        }
        (*library)[i].artist = read_variable_string(file);
        if ((*library)[i].artist == NULL) {
            perror("Erreur lecture artiste");
            for (int j = 0; j <= i; j++) free_song_strings(&(*library)[j]);
            free(*library); *library = NULL; *song_count = 0; *library_capacity = 0; fclose(file); return;
        }

        if (fread(&(*library)[i].duration_seconds, sizeof(int), 1, file) != 1) { perror("Err durée"); /* ... */ fclose(file); return; }
        if (fread(&(*library)[i].track_number, sizeof(int), 1, file) != 1) { perror("Err piste"); /* ... */ fclose(file); return; }
    }

    *song_count = loaded_song_count;
    printf("Bibliothèque chargée depuis '%s' (%d chansons).\n", filename, *song_count);
    fclose(file);
}

// Nom du fichier de sauvegarde par défaut
#define LIBRARY_FILENAME "library_var_str.bin"

int main() {
    Song *my_library = NULL;
    int song_count = 0;
    int library_capacity = 0;

    int choice;

    printf("Tentative de chargement de la bibliothèque au démarrage...\n");
    load_library_from_file(LIBRARY_FILENAME, &my_library, &song_count, &library_capacity);

    do {
        printf("\n--- Menu de la Bibliothèque Musicale (Chaînes Variables) ---\n");
        printf("1. Ajouter une chanson\n");
        printf("2. Afficher la bibliothèque\n");
        printf("3. Sauvegarder la bibliothèque\n");
        printf("4. Charger la bibliothèque\n");
        printf("5. Quitter\n");
        printf("Entrez votre choix: ");

        if (scanf("%d", &choice) != 1) {
            fprintf(stderr, "Entrée invalide. Veuillez entrer un nombre.\n");
            while (getchar() != '\n');
            choice = 0;
            continue;
        }
        while (getchar() != '\n');

        switch (choice) {
            case 1:
                add_song_to_library(&my_library, &song_count, &library_capacity);
                break;
            case 2:
                display_library(my_library, song_count);
                break;
            case 3:
                save_library_to_file(LIBRARY_FILENAME, my_library, song_count);
                break;
            case 4:
                load_library_from_file(LIBRARY_FILENAME, &my_library, &song_count, &library_capacity);
                break;
            case 5:
                printf("Quitter le programme. Au revoir !\n");
                break;
            default:
                printf("Choix invalide. Veuillez réessayer.\n");
                break;
        }
    } while (choice != 5);

    // Libérer la mémoire allouée pour la bibliothèque et ses chaînes
    if (my_library != NULL) {
        for (int i = 0; i < song_count; i++) {
            free_song_strings(&my_library[i]);
        }
        free(my_library);
        my_library = NULL;
    }

    return EXIT_SUCCESS;
}
```



## Compilation

Pour compiler cette solution, utilisez la commande suivante :
`gcc -Wall -Wextra -std=c11 main.c -o music_library_s2`

## Explications des Concepts Clés (Solution 2)

Cette solution est une extension de la première, se concentrant sur la gestion des chaînes de caractères de taille variable, ce qui est un défi plus avancé en C pour la persistance binaire.

### Exercice 1 : Définition de la Structure de Données

*   **`struct Song` avec pointeurs**: Les champs `title` et `artist` sont maintenant des `char *`. Cela signifie que la taille de la structure `Song` elle-même est fixe (taille des pointeurs + taille des `int`), mais les chaînes qu'ils pointent sont allouées dynamiquement et peuvent avoir des longueurs différentes.

### Exercice 2 & 3 : Lecture/Écriture d'une Chanson dans un Fichier Binaire (Logique modifiée)

La lecture et l'écriture d'une `Song` ne peuvent plus se faire d'un seul bloc avec `fwrite(&song, sizeof(Song), 1, file)`. Il faut gérer chaque champ individuellement.

*   **`write_variable_string(FILE *file, const char *str)`**:
    1.  Écrit d'abord la **longueur** de la chaîne (`size_t len`) dans le fichier.
    2.  Puis écrit les `len` caractères de la chaîne elle-même.
    Cette convention permet au lecteur de savoir combien d'octets lire pour la chaîne suivante.
*   **`read_variable_string(FILE *file)`**:
    1.  Lit d'abord la **longueur** de la chaîne (`size_t len`).
    2.  Alloue dynamiquement un tampon de `len + 1` octets (`malloc`).
    3.  Lit les `len` caractères de la chaîne dans ce tampon.
    4.  Ajoute le caractère de fin de chaîne `\0`.
    5.  Retourne le pointeur vers la chaîne allouée.
*   **`allocate_and_copy_string` / `free_song_strings`**: Ces fonctions utilitaires sont essentielles pour gérer l'allocation et la libération des chaînes pointées par `title` et `artist` dans la structure `Song`. Chaque `malloc` pour une chaîne doit être suivi d'un `free` correspondant.

### Exercice 4 : Gestion de la Bibliothèque Musicale (Logique modifiée)

*   **`add_song_to_library`**:
    *   Lit la saisie utilisateur dans un tampon temporaire.
    *   Utilise `allocate_and_copy_string` pour allouer de la mémoire pour `new_song.title` et `new_song.artist` et y copier les chaînes.
    *   La gestion des erreurs est plus complexe : si l'allocation d'une chaîne échoue, il faut libérer les chaînes déjà allouées pour la `new_song` avant de retourner.
*   **`save_library_to_file`**:
    *   Écrit `song_count` comme précédemment.
    *   Pour chaque `Song`, elle appelle `write_variable_string` pour le titre et l'artiste, puis `fwrite` pour les champs `int`.
*   **`load_library_from_file`**:
    *   Lit `song_count`.
    *   **Libération complexe**: Avant d'allouer la nouvelle bibliothèque, elle doit parcourir l'ancienne bibliothèque (si elle existe) et appeler `free_song_strings` pour chaque `Song` afin de libérer les chaînes individuelles, puis `free(*library)` pour le tableau lui-même.
    *   Alloue le tableau `*library`.
    *   Pour chaque `Song` à lire, elle appelle `read_variable_string` pour le titre et l'artiste (qui alloue la mémoire pour les chaînes), puis `fread` pour les champs `int`.
    *   La gestion des erreurs pendant la lecture est encore plus complexe : si une lecture de chaîne ou un `fread` échoue, il faut libérer toutes les chaînes et les structures `Song` déjà allouées pour les éléments précédents.
*   **`main`**: La logique du menu est similaire, mais la phase de libération finale de la mémoire est plus complexe, nécessitant une boucle pour appeler `free_song_strings` sur chaque élément de `my_library` avant de libérer le tableau `my_library` lui-même.

### Avantages et Inconvénients de la gestion des chaînes de taille variable

*   **Avantages**:
    *   **Flexibilité**: Les titres et artistes peuvent avoir n'importe quelle longueur, sans gaspillage d'espace pour les chaînes courtes ni troncature pour les chaînes longues.
    *   **Optimisation de l'espace disque**: Le fichier binaire n'occupe que l'espace nécessaire pour les données réelles.
*   **Inconvénients**:
    *   **Complexité accrue**: La logique de lecture/écriture est beaucoup plus complexe, nécessitant des fonctions auxiliaires et une gestion minutieuse de l'allocation/libération de mémoire pour chaque chaîne.
    *   **Performance**: L'écriture/lecture champ par champ et les allocations/libérations multiples peuvent être moins performantes que l'écriture d'un bloc fixe.
    *   **Risque d'erreurs**: Plus de points d'échec potentiels et une gestion des erreurs plus délicate.

Cette solution démontre une technique plus avancée mais aussi plus fragile en C. Elle est souvent utilisée lorsque la flexibilité de la taille des données est primordiale et que les performances ne sont pas le facteur limitant.
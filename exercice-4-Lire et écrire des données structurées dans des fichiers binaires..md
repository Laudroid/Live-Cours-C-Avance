Bonjour à toutes et à tous !

Nous allons aborder un sujet fondamental et très pratique en C : la lecture et l'écriture de données structurées dans des fichiers binaires. C'est une compétence essentielle pour la persistance des données, que ce soit pour des fichiers de configuration, des sauvegardes de jeux, ou des bases de données embarquées.

---

## TP: Lecture/écriture de données structurées dans un fichier binaire

**Objectif du TP :** Maîtriser la lecture et l'écriture de données structurées (structures C) dans des fichiers binaires, en comprenant les implications de ce format.

**Contexte du mini-projet :**
Pour illustrer ces concepts, nous allons développer un petit programme de gestion de "Bibliothèque Musicale". Ce programme permettra d'ajouter des chansons, de les afficher, et surtout, de sauvegarder et de charger l'intégralité de la bibliothèque dans un fichier binaire.

**Prérequis :**
*   Connaissance des bases du langage C (variables, boucles, conditions, fonctions).
*   Maîtrise des structures (`struct`).
*   Notions de base sur les pointeurs et l'allocation dynamique (`malloc`, `free`, `realloc`).
*   Connaissance des opérations de base sur les fichiers texte (`fopen`, `fclose`, `fprintf`, `fscanf`).

---

### Exercice 1 : Définition de la Structure de Données

Pour notre bibliothèque musicale, chaque chanson sera représentée par une structure.

1.  **Définissez une structure `Song`** qui contiendra les informations suivantes :
    *   `title` (titre de la chanson) : une chaîne de caractères.
    *   `artist` (artiste) : une chaîne de caractères.
    *   `duration_seconds` (durée en secondes) : un entier.
    *   `track_number` (numéro de piste) : un entier.

    **Conseil :** Pour les chaînes de caractères dans une structure destinée à être écrite directement en binaire, il est préférable d'utiliser des tableaux de caractères de taille fixe (ex: `char title[100];`) plutôt que des pointeurs (`char *title`). Cela garantit que la taille de la structure est fixe, ce qui simplifie grandement la lecture/écriture binaire.

### Exercice 2 : Écriture d'une Chanson dans un Fichier Binaire

Nous allons créer une fonction pour écrire une seule chanson dans un fichier binaire.

1.  **Créez une fonction `write_song_to_file(const char *filename, const Song *song)`** :
    *   Elle prend en paramètre le nom du fichier et un pointeur vers la structure `Song` à écrire.
    *   Ouvrez le fichier en mode écriture binaire (`"wb"` ou `"ab"` si vous voulez ajouter à la fin).
    *   Utilisez `fwrite` pour écrire la structure `Song` directement dans le fichier.
    *   N'oubliez pas de vérifier si l'ouverture du fichier a réussi.
    *   Fermez le fichier.

    **Rappel `fwrite` :** `size_t fwrite(const void *ptr, size_t size, size_t nmemb, FILE *stream);`
    *   `ptr` : adresse de la donnée à écrire.
    *   `size` : taille d'un élément (ici, `sizeof(Song)`).
    *   `nmemb` : nombre d'éléments à écrire (ici, `1`).

### Exercice 3 : Lecture d'une Chanson depuis un Fichier Binaire

Maintenant, nous allons faire l'opération inverse : lire une chanson depuis un fichier binaire.

1.  **Créez une fonction `read_song_from_file(const char *filename, Song *song)`** :
    *   Elle prend en paramètre le nom du fichier et un pointeur vers une structure `Song` où stocker les données lues.
    *   Ouvrez le fichier en mode lecture binaire (`"rb"`).
    *   Utilisez `fread` pour lire une structure `Song` depuis le fichier.
    *   Vérifiez si l'ouverture du fichier a réussi et si la lecture a été effective (retour de `fread`).
    *   Fermez le fichier.

    **Rappel `fread` :** `size_t fread(void *ptr, size_t size, size_t nmemb, FILE *stream);`
    *   `ptr` : adresse où stocker la donnée lue.
    *   `size` : taille d'un élément (ici, `sizeof(Song)`).
    *   `nmemb` : nombre d'éléments à lire (ici, `1`).
    *   La fonction retourne le nombre d'éléments lus avec succès.

### Exercice 4 : Gestion de la Bibliothèque Musicale (Mini-Projet)

C'est le cœur de notre mini-projet. Nous allons gérer une collection de chansons en mémoire, puis la sauvegarder et la charger depuis un fichier binaire.

1.  **Structure de la bibliothèque en mémoire :**
    *   Utilisez un tableau dynamique de structures `Song` (un `Song *library`) pour stocker toutes les chansons.
    *   Maintenez un compteur du nombre de chansons (`int song_count`) et une capacité (`int library_capacity`) pour gérer l'allocation dynamique.

2.  **Implémentez les fonctions suivantes :**

    *   `void add_song_to_library(Song **library, int *song_count, int *library_capacity)` :
        *   Demande à l'utilisateur les informations d'une nouvelle chanson.
        *   Alloue ou réalloue de la mémoire pour la bibliothèque si nécessaire (`realloc`).
        *   Ajoute la nouvelle chanson au tableau dynamique.

    *   `void display_library(const Song *library, int song_count)` :
        *   Affiche toutes les chansons de la bibliothèque de manière lisible.

    *   `void save_library_to_file(const char *filename, const Song *library, int song_count)` :
        *   Ouvre le fichier binaire en mode écriture (`"wb"`).
        *   **Important :** Écrivez d'abord le nombre total de chansons (`song_count`) dans le fichier. Cela permettra de savoir combien de chansons lire lors du chargement.
        *   Ensuite, parcourez le tableau `library` et écrivez chaque `Song` une par une dans le fichier en utilisant `fwrite`.
        *   Gérez les erreurs d'ouverture de fichier.
        *   Fermez le fichier.

    *   `void load_library_from_file(const char *filename, Song **library, int *song_count, int *library_capacity)` :
        *   Ouvre le fichier binaire en mode lecture (`"rb"`).
        *   Gérez les erreurs d'ouverture de fichier.
        *   **Important :** Lisez d'abord le nombre total de chansons depuis le fichier.
        *   Libérez l'ancienne bibliothèque en mémoire si elle existe (`free(*library)`).
        *   Allouez la mémoire nécessaire pour la nouvelle bibliothèque (`malloc`) en fonction du nombre de chansons lues.
        *   Parcourez le fichier et lisez chaque `Song` une par une en utilisant `fread`.
        *   Mettez à jour `song_count` et `library_capacity`.
        *   Fermez le fichier.

3.  **Fonction `main` :**
    *   Mettez en place un menu interactif permettant à l'utilisateur de :
        *   Ajouter une chanson.
        *   Afficher la bibliothèque.
        *   Sauvegarder la bibliothèque dans un fichier (ex: `library.bin`).
        *   Charger la bibliothèque depuis un fichier.
        *   Quitter le programme.
    *   N'oubliez pas de libérer la mémoire allouée pour la bibliothèque avant de quitter le programme (`free`).

---

### Consignes Générales et Conseils

*   **Gestion des erreurs :** Pour toutes les opérations de fichier (`fopen`, `fwrite`, `fread`), vérifiez toujours les valeurs de retour pour détecter les erreurs. Utilisez `perror()` pour afficher des messages d'erreur système pertinents.
*   **Fermeture des fichiers :** Assurez-vous de toujours fermer les fichiers avec `fclose()` après les avoir utilisés, même en cas d'erreur.
*   **Allocation dynamique :** Soyez rigoureux avec `malloc`, `realloc` et `free`. Chaque `malloc` ou `realloc` doit avoir son `free` correspondant pour éviter les fuites de mémoire.
*   **Chaînes de caractères :** Lors de la saisie utilisateur, utilisez `fgets` au lieu de `scanf("%s", ...)` pour éviter les débordements de tampon et gérer les espaces dans les titres/artistes. N'oubliez pas de supprimer le caractère `\n` ajouté par `fgets`.
*   **Utilisation de l'IA :** L'IA est un outil puissant. N'hésitez pas à l'utiliser pour explorer des solutions, comprendre des concepts, débloquer des situations ou même générer des squelettes de code. L'objectif est votre apprentissage et votre compréhension, pas de réinventer la roue à chaque fois. Soyez curieux, posez-lui des questions sur les "pourquoi" et les "comment".

---

### Pour aller plus loin (Extensions)

Si vous avez terminé le TP et que vous souhaitez explorer davantage :

1.  **Recherche :** Implémentez une fonction pour rechercher une chanson par titre ou par artiste.
2.  **Suppression :** Implémentez une fonction pour supprimer une chanson de la bibliothèque (cela implique de réorganiser le tableau en mémoire et de reconstruire le fichier binaire lors de la sauvegarde).
3.  **Modification :** Permettez de modifier les informations d'une chanson existante.
4.  **Gestion des chaînes de taille variable :** Si vous vous sentez à l'aise, explorez comment gérer des chaînes de caractères de taille variable dans un fichier binaire. Cela rend la structure plus complexe à écrire/lire mais plus flexible. Une approche courante est d'écrire la longueur de la chaîne avant la chaîne elle-même.
5.  **Interface utilisateur :** Améliorez l'interface utilisateur avec des couleurs, une meilleure mise en page, etc.

Bon courage pour ce TP ! C'est une excellente occasion de consolider vos connaissances en C avancé.
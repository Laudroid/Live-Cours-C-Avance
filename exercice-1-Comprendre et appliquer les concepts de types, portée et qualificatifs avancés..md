Bonjour à toutes et à tous !

Aujourd'hui, nous allons plonger au cœur des mécanismes fondamentaux du C qui, bien que parfois sous-estimés, sont cruciaux pour écrire du code robuste, performant et maintenable, surtout dans des contextes exigeants comme les systèmes embarqués ou les bibliothèques.

Ce TP est conçu comme un mini-projet de simulation, ce qui nous permettra de manipuler concrètement les concepts de types avancés, de portée et de qualificatifs.

---

### **TP C Avancé : Gestion d'un Simulateur de Capteur et Journalisation des Données**

**Objectif du TP :**
Comprendre et appliquer les concepts de types (`typedef`, `enum`, pointeurs de fonctions), de portée (`static`, `extern`) et de qualificatifs (`const`, `volatile`) avancés du langage C, à travers la conception d'un module de simulation de capteur et de son système de journalisation.

**Contexte / Mini-Projet :**
Vous allez développer un petit système composé de trois modules principaux :
1.  **`sensor` :** Simule un capteur (température, humidité, etc.) capable de générer des données. Ce module gérera l'état interne du capteur et sa lecture.
2.  **`logger` :** Un module de journalisation qui stockera les données lues par le capteur.
3.  **`main` :** L'application principale qui orchestrera la lecture du capteur et la journalisation des données.

Ce découpage en modules est intentionnel pour vous permettre de manipuler les concepts de portée et de partage d'informations.

**Prérequis :**
*   Bonne connaissance des bases du langage C (variables, fonctions, boucles, conditions, tableaux, pointeurs simples, structures).
*   Maîtrise de la compilation de programmes C (GCC ou équivalent).
*   Compréhension de la séparation en fichiers `.h` et `.c`.

---

### **Exercices**

**1. Définition des Types et Structures (Fichier `sensor.h`)**

Créez un fichier d'en-tête `sensor.h` qui définira les types de données nécessaires pour notre capteur.

*   Définissez une énumération `SensorType_t` pour représenter différents types de capteurs (par exemple, `TEMPERATURE`, `HUMIDITY`, `PRESSURE`).
*   Définissez une structure `SensorData_t` qui contiendra :
    *   Le type du capteur (`SensorType_t`).
    *   Une valeur entière (`int value`).
    *   Un horodatage (`long timestamp`).
*   Définissez un type de pointeur de fonction `SensorReadCallback_t` qui prendra en paramètre un pointeur vers `SensorData_t` (pour y stocker la lecture) et retournera un `int` (par exemple, 0 en cas de succès, -1 en cas d'erreur). Ce sera la fonction que le module capteur appellera pour "lire" la donnée réelle.

**2. Le Module Capteur (Fichiers `sensor.h` et `sensor.c`)**

Implémentez le module `sensor` dans `sensor.c` et complétez `sensor.h` avec les déclarations nécessaires.

*   **Dans `sensor.c` :**
    *   Déclarez une variable globale de fichier (`static`) de type `const int` pour l'identifiant unique de votre capteur (par exemple, `SENSOR_ID`).
    *   Déclarez une variable globale de fichier (`static volatile unsigned int`) nommée `sensor_status_register`. Cette variable simulera un registre matériel dont la valeur peut changer à tout moment, indépendamment de votre programme.
    *   Déclarez une variable globale de fichier (`static`) de type `SensorData_t` pour stocker la *dernière lecture* effectuée par le capteur.
    *   Déclarez une variable globale de fichier (`static`) de type `SensorReadCallback_t` pour stocker la fonction de rappel de lecture.
    *   Implémentez une fonction `extern void init_sensor(SensorReadCallback_t callback_func, SensorType_t type);` qui initialise le capteur, stocke la fonction de rappel et met à jour le `sensor_status_register`.
    *   Implémentez une fonction `extern int read_sensor(SensorData_t *output_data);` qui :
        *   Vérifie le `sensor_status_register` (simulez une condition d'erreur si sa valeur est 0).
        *   Appelle la fonction de rappel stockée pour obtenir les données.
        *   Met à jour la `last_reading` interne avec les nouvelles données.
        *   Copie les données dans `output_data`.
        *   Retourne 0 en cas de succès, -1 en cas d'erreur.

*   **Dans `sensor.h` :**
    *   Déclarez les prototypes des fonctions `init_sensor` et `read_sensor`.

**3. Le Module Journalisation (Fichiers `logger.h` et `logger.c`)**

Créez un module `logger` pour stocker et afficher les données des capteurs.

*   **Dans `logger.c` :**
    *   Définissez une constante de préprocesseur `#define MAX_LOG_ENTRIES 10`.
    *   Déclarez un tableau global de fichier (`static`) de `SensorData_t` de taille `MAX_LOG_ENTRIES` pour stocker les logs.
    *   Déclarez une variable globale de fichier (`static int`) pour suivre le nombre actuel d'entrées dans le log.
    *   Implémentez une fonction `extern void log_sensor_data(const SensorData_t *data);` qui :
        *   Ajoute la donnée pointée par `data` au tableau de logs.
        *   Utilisez le qualificatif `const` pour le pointeur `data` afin d'indiquer que la fonction ne modifiera pas les données originales.
        *   Gérez le cas où le tableau est plein (par exemple, en écrasant la plus ancienne entrée ou en ignorant la nouvelle).
        *   Utilisez une variable locale `static int call_count = 0;` dans cette fonction pour compter combien de fois elle a été appelée, et affichez ce compte à chaque appel.
    *   Implémentez une fonction `extern void display_all_logs();` qui parcourt le tableau de logs et affiche toutes les entrées formatées.

*   **Dans `logger.h` :**
    *   Déclarez les prototypes des fonctions `log_sensor_data` et `display_all_logs`.

**4. L'Application Principale (Fichier `main.c`)**

Créez le fichier `main.c` pour assembler et tester vos modules.

*   Incluez les fichiers d'en-tête nécessaires (`sensor.h`, `logger.h`, `stdio.h`, `stdlib.h`, `time.h`).
*   Implémentez une fonction `static int simulate_temperature_read(SensorData_t *data)` qui simule la lecture d'un capteur de température :
    *   Elle génère une valeur aléatoire entre 18 et 30.
    *   Elle remplit le champ `value` et `timestamp` de la structure `data`.
    *   Elle retourne 0.
*   Dans la fonction `main` :
    *   Initialisez le générateur de nombres aléatoires (`srand(time(NULL))`).
    *   Appelez `init_sensor` en lui passant `simulate_temperature_read` comme fonction de rappel et `TEMPERATURE` comme type de capteur.
    *   Créez une boucle qui s'exécute 15 fois :
        *   Déclarez une variable `SensorData_t current_reading;`.
        *   Appelez `read_sensor(&current_reading);`.
        *   Si la lecture est réussie, appelez `log_sensor_data(&current_reading);`.
        *   Introduisez un petit délai (par exemple, `sleep(1)` si vous êtes sur Linux/macOS ou une boucle vide si vous voulez rester purement C standard) pour simuler le temps.
    *   Après la boucle, appelez `display_all_logs();`.
    *   Tentez de modifier la `SENSOR_ID` (déclarée `const` dans `sensor.c`) depuis `main.c` et observez l'erreur de compilation. Expliquez pourquoi cette erreur se produit.
    *   Tentez de modifier directement le `sensor_status_register` (déclaré `volatile` dans `sensor.c`) depuis `main.c` (vous devrez ajouter une déclaration `extern volatile unsigned int sensor_status_register;` dans `main.c` ou `sensor.h` pour y accéder). Observez le comportement et expliquez l'intérêt de `volatile` dans un contexte réel.

**Compilation :**
N'oubliez pas de compiler tous vos fichiers `.c` ensemble :
`gcc main.c sensor.c logger.c -o sensor_app`

---

**Rendu :**

*   Les fichiers sources (`.h` et `.c`) de votre projet.
*   Un court rapport (fichier `README.md` ou similaire) expliquant :
    *   Comment vous avez utilisé chaque concept (`typedef`, `enum`, pointeur de fonction, `static`, `extern`, `const`, `volatile`).
    *   Les erreurs de compilation rencontrées et leur résolution (notamment pour `const`).
    *   Votre compréhension de l'intérêt de `volatile` dans un contexte réel (par exemple, interaction avec du matériel).
    *   Comment l'IA vous a aidé dans ce TP.

Amusez-vous, expérimentez, et n'hésitez pas à poser des questions si vous rencontrez des difficultés !

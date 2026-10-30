
## 1. Communication inter-processus (IPC) par Pipes anonymes en C

Dans un système d'exploitation de type UNIX, chaque processus dispose de son propre espace d'adressage virtuel isolé. Par conséquent, un processus fils ne peut pas directement lire ou modifier les variables de son processus père.

Pour faire communiquer deux processus (père et fils), le système d'exploitation fournit des mécanismes de **Communication Inter-Processus (IPC)**. Le plus fondamental d'entre eux est le **tube anonyme** (ou *pipe*).

### Définition d'un pipe anonyme
Un pipe est un canal de communication **unidirectionnel** (half-duplex) géré par le noyau (*kernel*) du système d'exploitation. Les données y transitent selon le principe **FIFO** (*First In, First Out*).

```
                 +-----------------------+
                 |    Noyau (Kernel)     |
                 |                       |
Processus    --->|  [Buffer du Pipe]    |---> Processus
Écrivain         |                       |     Lecteur
(write)          +-----------------------+     (read)
```

---

## 2. Anatomie d'un Pipe et Appels Système

Pour utiliser un pipe en C, nous manipulons des **descripteurs de fichiers** (*file descriptors*).

### 2.1. L'appel système `pipe()`

L'appel système `pipe()` crée un tube en mémoire noyau et renvoie deux nouveaux descripteurs de fichiers.

```c
#include <unistd.h>

int pipe(int pipefd[2]);
```

- **Paramètre :** Un tableau de 2 entiers `int pipefd[2]` passé par adresse.
  - `pipefd[0]` : Descripteur ouvert en **lecture** (*read*).
  - `pipefd[1]` : Descripteur ouvert en **écriture** (*write*).
- **Valeur de retour :** `0` en cas de succès, `-1` en cas d'erreur (et `errno` est renseignée).

---

## 3. Le schéma d'exécution : `pipe()` + `fork()`

Pour faire communiquer un père et son fils, l'ordre des appels système est crucial : **il faut créer le pipe AVANT de faire le `fork()`**.

### Étape 1 : Création du pipe par le père
Le père appelle `pipe(pipefd)`. Le noyau alloue le tube et renvoie les deux descripteurs.

```
PÈRE: pipefd[0] (Lecture)  <---+
                                +--- [ TUBE (Noyau) ]
PÈRE: pipefd[1] (Écriture) <---+
```

### Étape 2 : Duplication via `fork()`
Le père appelle `fork()`. Le fils hérite de la table des descripteurs de fichiers du père. Père et fils possèdent maintenant TOUS LES DEUX un accès en lecture (`pipefd[0]`) et en écriture (`pipefd[1]`).

```
PÈRE: pipefd[0] (Lect)  <--+                      +--> FILS: pipefd[0] (Lect)
                           +--- [ TUBE (Noyau) ] -+
PÈRE: pipefd[1] (Écrit) <--+                      +--> FILS: pipefd[1] (Écrit)
```

### Étape 3 : Fermeture des extrémités inutilisées via `close()`
Un pipe étant unidirectionnel, chaque processus doit **fermer l'extrémité qu'il n'utilise pas**.

Si le **Père écrit** vers le **Fils qui lit** :
1. Le Père ferme la lecture : `close(pipefd[0])`
2. Le Fils ferme l'écriture : `close(pipefd[1])`

```
PÈRE: pipefd[1] (Écrit) ---> [ TUBE (Noyau) ] ---> FILS: pipefd[0] (Lect)
```

---

## 4. Pourquoi la fermeture (`close()`) est-elle CRITIQUE ?

L'omission de `close()` est la source de bogue #1 lors de la manipulation des pipes.

1. **Fin de fichier (EOF) sur `read()` :**
   - L'appel `read()` sur un pipe renvoie `0` (EOF) **uniquement quand TOUS les descripteurs ouverts en écriture sont fermés**.
   - Si le fils attend une fin de fichier mais que le père (ou le fils lui-même) conserve `pipefd[1]` ouvert, `read()` bloquera indéfiniment (*Deadlock*).

2. **Écriture dans un pipe sans lecteur (Signal `SIGPIPE`) :**
   - Si un processus tente d'écrire (`write`) dans un pipe dont TOUS les descripteurs de lecture ont été fermés, le noyau lui envoie le signal `SIGPIPE` (ce qui termine le processus par défaut).

---

## 5. Exemple complet commenté

Le programme ci-dessous illustre l'envoi d'une chaîne de caractères du processus père vers le processus fils.

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>

#define BUFFER_SIZE 128

int main(void) {
    int pipefd[2];
    pid_t pid;
    char message[] = "Bonjour depuis le processus père !";
    char buffer[BUFFER_SIZE];

    // 1. Création du pipe
    if (pipe(pipefd) == -1) {
        perror("Échec de la création du pipe");
        exit(EXIT_FAILURE);
    }

    // 2. Création du processus fils
    pid = fork();

    if (pid == -1) {
        perror("Échec du fork");
        exit(EXIT_FAILURE);
    }

    if (pid == 0) {
        /* ==================== CODE DU FILS (LECTEUR) ==================== */
        
        // Fermeture de l'extrémité d'écriture non utilisée
        close(pipefd[1]);

        printf("[FILS] En attente de message...\n");
        
        // Lecture dans le pipe
        ssize_t bytesRead = read(pipefd[0], buffer, sizeof(buffer) - 1);
        if (bytesRead < 0) {
            perror("[FILS] Erreur de lecture");
            close(pipefd[0]);
            exit(EXIT_FAILURE);
        }

        // Ajout du caractère de fin de chaîne
        buffer[bytesRead] = '\0';
        printf("[FILS] Message reçu : \"%s\"\n", buffer);

        // Fermeture du descripteur de lecture
        close(pipefd[0]);
        exit(EXIT_SUCCESS);

    } else {
        /* ==================== CODE DU PÈRE (ÉCRIVAIN) =================== */

        // Fermeture de l'extrémité de lecture non utilisée
        close(pipefd[0]);

        printf("[PÈRE] Envoi du message au fils...\n");

        // Écriture dans le pipe
        ssize_t bytesWritten = write(pipefd[1], message, strlen(message));
        if (bytesWritten < 0) {
            perror("[PÈRE] Erreur d'écriture");
            close(pipefd[1]);
            exit(EXIT_FAILURE);
        }

        // Fermeture de l'écriture pour envoyer l'EOF au lecteur
        close(pipefd[1]);

        // Attente de la fin du fils pour éviter les processus zombies
        wait(NULL);
        printf("[PÈRE] Le fils a terminé son exécution.\n");
    }

    return 0;
}
```

---

## 6. Synthèse des bonnes pratiques

| Étape | Action |
| :--- | :--- |
| **1. Initialisation** | Toujours exécuter `pipe()` **avant** `fork()`. |
| **2. Gestion d'erreurs** | Vérifier systématiquement les retours de `pipe()`, `fork()`, `read()` et `write()`. |
| **3. Nettoyage immédiat** | Fermer l'extrémité inutile dans chaque processus immédiatement après le `fork()`. |
| **4. Fin de transmission** | Fermer l'extrémité d'écriture dès que la transmission est terminée pour libérer le `read()` du lecteur. |
| **5. Nettoyage des processus** | Utiliser `wait()` ou `waitpid()` dans le père pour éviter la création de processus zombies. |

---

## 7. Exercices d'application (Travaux Pratiques)

1. **Communication bidirectionnelle :** Écrivez un programme où le père envoie un nombre entier au fils, le fils le multiplie par 2, puis renvoie le résultat au père. *(Indice : Combien de pipes sont nécessaires ?)*
2. **Transfert de fichier :** Écrivez un programme où le père lit un fichier texte et transmet son contenu bloc par bloc au fils via un pipe. Le fils affiche le contenu reçu sur le terminal.
# 5-Séance 5 : Programmation Système et Multitâche  
## 1-Appels Système de Base  
### 1-Utilisation des fonctions `fork`, `exec`, `wait`  

---

## Introduction  
Dans les systèmes UNIX/Linux, le multitâche repose sur la création et la gestion des processus. Les fonctions `fork`, `exec` et `wait` sont des appels système fondamentaux permettant respectivement la création d’un nouveau processus, le remplacement de son image par un programme différent, et la synchronisation avec la fin d’un processus enfant.

---

## 1. Fonction `fork` : création d’un nouveau processus  

### 1.1 Description  
`fork()` crée un processus enfant en dupliquant le processus appelant. Le processus parent et le fils s’exécutent alors simultanément.  

### 1.2 Prototype  

```c
pid_t fork(void);
```

- Retourne :
  - Dans le **processus parent** : le PID (identifiant) du fils.  
  - Dans le **processus fils** : 0.  
  - En cas d’erreur : -1.  

### 1.3 Exemple  

```c
pid_t pid = fork();
if (pid < 0) {
    perror("fork échoué");
}
else if (pid == 0) {
    // Code exécuté par le fils
    printf("Fils, PID=%d\n", getpid());
}
else {
    // Code exécuté par le parent
    printf("Parent, PID=%d, PID fils=%d\n", getpid(), pid);
}
```

---

## 2. Fonction `exec` : remplacement du programme courant  

### 2.1 Description  
Les fonctions `exec` remplacent l’image mémoire du processus courant par un nouveau programme. Ce processus conserve son PID, mais la mémoire, le code et la pile sont remplacés.  

### 2.2 Famille des fonctions  

- `execl`, `execv`, `execle`, `execve`, `execlp`, `execvp`, avec variantes selon la manière de passer les arguments et l’environnement.  
- Exemples :
  - `execl("/bin/ls", "ls", "-l", NULL);`
  - `execvp("ls", args);`  

### 2.3 Prototype générique  

```c
int execvp(const char *file, char *const argv[]);
```

- Ne retourne jamais si succès.  
- En cas d’erreur, renvoie -1 et définit `errno`.  

### 2.4 Exemple  

```c
char *args[] = {"ls", "-l", NULL};
execvp("ls", args);
perror("execvp échoué");  // n'est atteint que si execvp échoue
```

---

## 3. Fonction `wait` : attente de la fin d’un processus enfant  

### 3.1 Description  
`wait` suspend le processus parent jusqu'à la terminaison d’un de ses enfants.  

### 3.2 Prototype  

```c
pid_t wait(int *status);
```

- Retourne le PID de l’enfant terminé.  
- `status` est un entier où est stocké le code de fin.  

### 3.3 Analyse du code retourné  

- Macro `WIFEXITED(status)` : vrai si le fils s’est terminé normalement.  
- Macro `WEXITSTATUS(status)` : donne le code de retour du fils.  

### 3.4 Exemple  

```c
int status;
pid_t pid = wait(&status);
if (pid > 0) {
    if (WIFEXITED(status)) {
        printf("Enfant %d terminé avec code %d\n", pid, WEXITSTATUS(status));
    }
}
else {
    perror("wait échoué");
}
```

---

## 4. Exemple complet : création de processus, exécution et attente  

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid = fork();
    if (pid < 0) {
        perror("fork");
        return EXIT_FAILURE;
    }
    else if (pid == 0) {
        // Processus fils: remplacer par un autre programme
        char *args[] = {"ls", "-l", NULL};
        execvp("ls", args);
        perror("execvp"); // si execvp échoue
        exit(EXIT_FAILURE);
    }
    else {
        // Processus parent : attendre la fin du fils
        int status;
        wait(&status);
        if (WIFEXITED(status)) {
            printf("Fils terminé avec code %d\n", WEXITSTATUS(status));
        }
    }
    return EXIT_SUCCESS;
}
```

---

## 5. Diagramme Mermaid : cycle de vie d’un processus avec fork/exec/wait  

```mermaid
flowchart TD
    A[Processus Parent] --> B{fork()}
    B -- Succès --> C[Processus Fils]
    C --> D{exec()}
    D -- Succès --> E[Nouveau programme exécuté dans fils]
    D -- Échec --> F[Erreur et sortie fils]
    B -- Échec --> G[Erreur fork]
    A --> H{wait()}
    H --> I[Parent attend la fin du fils]
    I --> J[Parent récupère statut et continue]
```

---

## 6. Sources utilisées  

- [fork - Linux man page](https://man7.org/linux/man-pages/man2/fork.2.html)  
- [exec - Linux man page](https://man7.org/linux/man-pages/man3/exec.3.html)  
- [wait - Linux man page](https://man7.org/linux/man-pages/man2/wait.2.html)  
- [Advanced Programming in the UNIX Environment - Stevens, Rago]  
- [The Linux Programming Interface - Kerrisk]  

---

Ces fonctions constituent la base pour la gestion des processus en multitâche sous UNIX/Linux. `fork` crée un processus enfant, `exec` charge un nouveau programme dans ce processus, et `wait` permet au parent de gérer la fin des processus enfants, assurant ainsi une synchronisation efficace dans les applications système.
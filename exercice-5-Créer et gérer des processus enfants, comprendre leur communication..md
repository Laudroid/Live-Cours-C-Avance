Bonjour à toutes et à tous,

Ce TP est conçu pour vous immerger dans la gestion des processus et leur communication en C. L'objectif est de manipuler ces concepts de manière concrète à travers un mini-projet.

---

### TP: Création de processus enfants et communication simple

**Objectif du TP :** Créer et gérer des processus enfants, comprendre leur communication via des mécanismes de base.

**Contexte :**
Dans les systèmes d'exploitation modernes, la capacité à exécuter plusieurs tâches simultanément est fondamentale. Cela permet d'exploiter pleinement les architectures multi-cœurs, d'améliorer la réactivité des applications et de mieux gérer les ressources. La création de processus enfants et leur communication sont les briques de base de cette concurrence.

**Le Projet : "Chasse aux Nombres Premiers Distribuée"**

Votre mission est de développer un programme C qui utilise des processus enfants pour trouver des nombres premiers dans une plage donnée.

**Description :**
Le programme principal (le processus parent) recevra un nombre maximum `N` et un nombre de processus enfants `K` en arguments. Il devra ensuite :
1.  Diviser la plage `[2, N]` en `K` sous-plages (approximativement égales).
2.  Créer `K` processus enfants.
3.  Assigner à chaque enfant une sous-plage spécifique.
4.  Chaque processus enfant sera responsable de trouver tous les nombres premiers dans sa sous-plage.
5.  Les enfants devront communiquer les nombres premiers qu'ils ont trouvés au processus parent.
6.  Le processus parent collectera tous les nombres premiers et les affichera dans l'ordre croissant.

**Exemple d'exécution :**
`./prime_finder 100 4`
Le parent divise `[2, 100]` en 4 sous-plages (ex: `[2, 25]`, `[26, 50]`, `[51, 75]`, `[76, 100]`).
Chaque enfant cherche les premiers dans sa plage et les envoie au parent.
Le parent affiche : `2 3 5 7 11 13 17 19 23 29 31 37 41 43 47 53 59 61 67 71 73 79 83 89 97`

**Prérequis :**
*   Connaissance du langage C (compilation, fonctions, pointeurs).
*   Notions de base sur les appels système `fork()`, `wait()`, `pipe()`, `read()`, `write()`, `close()`.
*   Un environnement de développement Linux/Unix (ou WSL sous Windows).

**Consignes Générales :**

L'utilisation d'outils d'IA générative (comme ChatGPT, Copilot, etc.) est non seulement autorisée, mais encouragée. Votre objectif n'est pas de réinventer la roue, mais de comprendre comment elle tourne, de l'adapter et de la maîtriser.

*   **Compréhension avant tout :** Si vous utilisez l'IA pour générer du code, prenez le temps de le lire attentivement. Demandez-lui des explications sur les parties que vous ne comprenez pas.
*   **Débugging et adaptation :** Le code généré par l'IA n'est pas toujours parfait ou adapté à 100% à votre besoin. Vous devez être capable de le débugger, de le modifier et de l'adapter.
*   **Explication :** Soyez prêt à expliquer vos choix, à justifier vos implémentations et à décrire le fonctionnement de votre code.
*   **Gestion des erreurs :** Pensez à gérer les erreurs potentielles des appels système (`fork`, `pipe`, `read`, `write`, etc.) avec `perror()`.
*   **Clarté du code :** Un code bien structuré, commenté et lisible est toujours apprécié.

---

### Étapes du TP :

#### Étape 1 : La fonction `is_prime`

Avant de manipuler les processus, écrivez une fonction `int is_prime(int num)` qui retourne `1` si `num` est premier, et `0` sinon.
Testez-la indépendamment pour vous assurer de son bon fonctionnement.

#### Étape 2 : Création des processus enfants

1.  **Parsing des arguments :** Le processus parent doit lire `N` et `K` depuis la ligne de commande.
2.  **Division des tâches :** Calculez les bornes `start` et `end` pour chaque sous-plage que vous allez attribuer aux enfants.
3.  **Boucle `fork()` :**
    *   Dans une boucle de `K` itérations, utilisez `fork()` pour créer les processus enfants.
    *   Gérez les erreurs de `fork()`.
    *   Dans le code du parent, stockez les PIDs des enfants si nécessaire.
    *   Dans le code de l'enfant, assurez-vous qu'il connaît sa sous-plage (`start`, `end`).

#### Étape 3 : Communication via les tuyaux (pipes)

Pour que les enfants puissent envoyer leurs résultats au parent, vous utiliserez des pipes.

1.  **Création des pipes :** Avant la boucle `fork()`, vous devrez créer `K` paires de pipes (une paire par enfant) ou une approche plus dynamique. Une approche courante est de créer un pipe *avant* chaque `fork` pour la communication enfant -> parent.
    *   `int pipefd[2];`
    *   `pipe(pipefd);`
2.  **Gestion des descripteurs de fichiers :**
    *   **Parent :** Après `fork()`, le parent doit fermer l'extrémité d'écriture (`pipefd[1]`) du pipe qu'il ne va pas utiliser pour lire les résultats de cet enfant. Il conservera l'extrémité de lecture (`pipefd[0]`).
    *   **Enfant :** Après `fork()`, l'enfant doit fermer l'extrémité de lecture (`pipefd[0]`) du pipe qu'il ne va pas utiliser pour écrire ses résultats. Il conservera l'extrémité d'écriture (`pipefd[1]`).
    *   **Important :** Chaque processus doit fermer les descripteurs de fichiers des pipes qu'il n'utilise pas. Ne pas le faire peut entraîner des blocages ou des fuites de ressources.
3.  **Écriture par l'enfant :** Chaque enfant, après avoir trouvé un nombre premier dans sa plage, doit l'écrire dans son pipe (`write(pipefd[1], &prime_num, sizeof(int));`).
4.  **Lecture par le parent :** Le parent, après avoir créé tous les enfants, doit lire les nombres premiers de chaque pipe. Il peut le faire dans une boucle, lisant de chaque pipe jusqu'à ce qu'il reçoive un EOF (fin de fichier), ce qui se produit lorsque l'enfant ferme son extrémité d'écriture et termine.
    *   `read(pipefd[0], &prime_num, sizeof(int));`
5.  **Attente des enfants :** Le parent doit utiliser `wait()` ou `waitpid()` pour attendre la terminaison de tous ses enfants avant de terminer lui-même.

#### Étape 4 : Assemblage et affichage

1.  **Collecte des résultats :** Le parent doit stocker tous les nombres premiers reçus dans une structure de données (par exemple, un tableau dynamique ou une liste chaînée).
2.  **Tri :** Une fois tous les résultats collectés et tous les enfants terminés, triez les nombres premiers (si nécessaire, car ils devraient déjà être dans l'ordre si les plages sont bien gérées et lues séquentiellement).
3.  **Affichage :** Affichez les nombres premiers collectés, séparés par des espaces.

#### Étape 5 : Améliorations et Extensions (Optionnel, pour les plus rapides ou curieux)

*   **Gestion dynamique des pipes :** Au lieu de créer `K` paires de pipes, explorez comment un seul pipe pourrait être utilisé pour la communication de multiples enfants (cela implique des défis de synchronisation ou de multiplexage comme `select`/`poll`).
*   **Performance :** Mesurez le temps d'exécution de votre programme avec différentes valeurs de `N` et `K`. Observez l'impact du nombre de processus sur la performance.
*   **Communication bidirectionnelle :** Modifiez le programme pour que le parent envoie explicitement les bornes de la sous-plage à chaque enfant via un pipe, au lieu que l'enfant les déduise ou les reçoive en arguments.
*   **Robustesse :** Que se passe-t-il si un enfant ne trouve aucun nombre premier ? Comment le parent gère-t-il cela ?

---

N'hésitez pas à poser des questions, à explorer différentes approches et à vous amuser avec ces concepts fondamentaux ! Bon courage !
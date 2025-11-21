**Concepts avancés du langage C** : Notions techniques approfondies du langage C à maîtriser pour des applications robustes et performantes.

**Applications robustes** : Programmes C conçus pour être fiables et résistants aux erreurs.

**Applications performantes** : Programmes C optimisés pour une exécution rapide et efficace.

**Meilleures pratiques de programmation C** : Recommandations pour écrire du code C de haute qualité, maintenable et sécurisé.

**Standards C (C11, C17, C23)** : Évolutions du langage C, incluant les versions C11, C17 et C23, avec leurs principales nouveautés et améliorations.

**Débogage** : Processus de détection et de correction des erreurs dans un programme C.

**Optimisation de code C** : Techniques visant à améliorer l'efficacité et les performances du code C.

**Structures de données dynamiques** : Structures de données dont la taille peut être ajustée durant l'exécution, nécessitant une gestion de la mémoire.

**Gestion de la mémoire** : Ensemble des techniques pour allouer, utiliser et libérer les ressources mémoire en C.

**Programmation système en C** : Développement de programmes interagissant directement avec le système d'exploitation via des appels système.

**Bases** : Notions fondamentales du langage C à consolider.

**Types de données avancés** : Types de données C et leur utilisation complexe ou spécifique, au-delà des fondamentaux.

**Opérateurs avancés** : Opérateurs C au-delà des fondamentaux, utilisés pour des manipulations spécifiques.

**Portée des variables** : Règles définissant la visibilité et la durée de vie des variables (storage classes) dans le code C.

**storage classes** : Qualificatifs C déterminant la portée et la durée de vie des variables.

**Qualificatifs** : Mots-clés C qui fournissent des indications au compilateur sur le comportement des variables, comme `volatile` et `restrict`.

**volatile** : Qualificatif C indiquant qu'une variable peut être modifiée par des moyens externes, empêchant certaines optimisations du compilateur.

**restrict** : Qualificatif C indiquant qu'un pointeur est le seul moyen d'accéder à la zone mémoire qu'il pointe, permettant des optimisations.

**_Generic** : Fonctionnalité introduite avec C11 permettant de créer des expressions génériques basées sur le type de l'argument.

**Threads C11** : Le support pour la programmation multithread introduit dans le standard C11.

**Alignement** : Concept du C11 lié à la manière dont les données sont stockées en mémoire pour optimiser l'accès.

**const C23** : Améliorations ou nouvelles utilisations du qualificatif `const` dans le standard C23.

**Fonctions de bibliothèque (C23)** : Nouvelles fonctions ajoutées à la bibliothèque standard C avec le standard C23.

**Allocation dynamique** : Processus de gestion de la mémoire à l'exécution avec des fonctions comme malloc, calloc, realloc et free.

**Gestion des erreurs mémoire** : Stratégies pour prévenir et gérer les problèmes liés à l'allocation et à la libération de la mémoire.

**malloc** : Fonction d'allocation dynamique de mémoire pour réserver une zone de mémoire brute.

**calloc** : Fonction d'allocation dynamique de mémoire qui initialise la zone allouée à zéro.

**realloc** : Fonction d'allocation dynamique de mémoire pour redimensionner une zone déjà allouée.

**free** : Fonction d'allocation dynamique de mémoire pour libérer une zone de mémoire précédemment allouée.

**Fuites mémoire** : Erreurs où la mémoire allouée dynamiquement n'est pas libérée, entraînant une consommation excessive de ressources.

**Double free** : Erreur où la même zone de mémoire est libérée deux fois, conduisant à un comportement indéfini.

**Stratégies d'allocation** : Méthodes pour gérer l'allocation et la libération de la mémoire de manière efficace et sûre.

**Pointeurs avancés** : Utilisation sophistiquée des pointeurs, incluant les pointeurs de fonctions, génériques, tableaux de pointeurs et pointeurs et structures.

**Arithmétique des pointeurs** : Opérations mathématiques autorisées sur les adresses mémoire pointées par des pointeurs.

**Pointeurs de fonctions** : Pointeur qui stocke l'adresse d'une fonction, permettant de l'appeler indirectement.

**Pointeurs génériques (void*)** : Pointeur capable de pointer vers n'importe quel type de données, nécessitant un cast explicite pour son utilisation.

**Tableaux de pointeurs** : Tableau dont les éléments sont des pointeurs.

**Pointeurs et structures** : Utilisation de pointeurs pour manipuler et accéder aux membres des structures.

**Structures de données complexes** : Implémentation et manipulation de structures de données non triviales telles que listes, piles, files, arbres et graphes.

**Listes chaînées** : Structure de données où les éléments sont liés séquentiellement par des pointeurs.

**Piles** : Structure de données suivant le principe 'dernier entré, premier sorti' (LIFO).

**Files** : Structure de données suivant le principe 'premier entré, premier sorti' (FIFO).

**Doubly linked lists** : Implémentation de liste chaînée où chaque nœud pointe vers le nœud précédent et le nœud suivant.

**Circular buffers** : Implémentation efficace de files utilisant un tableau de taille fixe en boucle.

**Arbres** : Structure de données hiérarchique, incluant les arbres binaires de recherche et les arbres équilibrés.

**Graphes** : Structure de données représentant des relations entre des entités, avec des représentations comme les listes d'adjacence ou les matrices.

**Arbres binaires de recherche** : Type d'arbre où les nœuds sont organisés pour faciliter la recherche.

**Arbres équilibrés** : Type d'arbre qui maintient une hauteur minimale pour optimiser les performances des opérations.

**Représentations de graphes** : Manières de stocker les graphes, comme les listes d'adjacence ou les matrices.

**Listes d'adjacence** : Représentation d'un graphe où chaque nœud est associé à une liste des nœuds connectés.

**Matrices (pour graphes)** : Représentation d'un graphe où une matrice stocke les informations de connexion entre les nœuds.

**Flux de données** : Canaux d'entrée/sortie utilisés pour transférer des données.

**Fichiers binaires** : Fichiers stockant des données dans leur format interne brut, sans interprétation textuelle.

**Fichiers texte** : Fichiers stockant des données sous forme de caractères lisibles, souvent ligne par ligne.

**fopen** : Fonction de la bibliothèque standard C pour ouvrir un fichier.

**fread** : Fonction de la bibliothèque standard C pour lire des données d'un fichier binaire.

**fwrite** : Fonction de la bibliothèque standard C pour écrire des données dans un fichier binaire.

**fseek** : Fonction de la bibliothèque standard C pour déplacer le pointeur de position dans un fichier.

**ftell** : Fonction de la bibliothèque standard C pour obtenir la position actuelle du pointeur de fichier.

**Gestion des erreurs (fichiers)** : Mécanismes pour détecter et gérer les problèmes survenant lors des opérations sur les fichiers.

**Entrées/Sorties Bufférisées** : Opérations d'entrée/sortie qui utilisent des tampons pour minimiser les accès directs au système.

**Entrées/Sorties Non Bufférisées** : Opérations d'entrée/sortie qui interagissent directement avec le système sans utiliser de tampons.

**Tampons (buffers)** : Zones de mémoire temporaire utilisées pour stocker des données lors des opérations d'entrée/sortie.

**setbuf** : Fonction de la bibliothèque standard C pour associer un tampon spécifique à un flux de données.

**fflush** : Fonction de la bibliothèque standard C pour vider le contenu des tampons de sortie vers le fichier physique.

**fsync** : Fonction pour synchroniser les données d'un fichier en mémoire avec le stockage physique.

**Appels système** : Fonctions permettant à un programme C d'interagir directement avec le noyau du système d'exploitation.

**Gestion des processus** : Mécanismes du système d'exploitation pour créer, gérer et terminer les processus.

**Threads** : Unités d'exécution légères au sein d'un même processus, permettant l'exécution concurrente de tâches.

**fork** : Appel système pour créer un nouveau processus enfant, copie du processus parent.

**exec** : Appel système pour remplacer l'image d'un processus par un nouveau programme.

**wait** : Appel système pour qu'un processus parent attende la terminaison d'un processus enfant.

**Gestion des signaux** : Mécanisme permettant aux processus de réagir à des événements asynchrones.

**Pthreads C11** : L'API POSIX Threads intégrée au standard C11 pour la programmation multithread.

**pthread_create** : Fonction Pthreads pour créer un nouveau thread d'exécution.

**Synchronisation des threads** : Mécanismes pour coordonner l'exécution de multiples threads et éviter les problèmes de concurrence.

**Mutex** : Mécanisme de verrouillage utilisé pour protéger une ressource partagée et assurer qu'un seul thread y accède à la fois.

**Sémaphore** : Mécanisme de synchronisation plus général qu'un mutex, utilisé pour contrôler l'accès à un nombre limité de ressources.

**Deadlock** : Situation où deux ou plusieurs threads sont bloqués indéfiniment en attendant chacun une ressource détenue par l'autre.

**Race condition** : Problème de concurrence où le résultat d'une opération dépend de l'ordre non déterministe d'exécution des threads.

**Préprocesseur** : Phase de compilation qui traite les directives (macros, inclusions conditionnelles) avant la compilation du code source.

**Macros avancées** : Utilisation sophistiquée des macros du préprocesseur, incluant les arguments variables, la concaténation de tokens et la stringification.

**Macros avec arguments variables (__VA_ARGS__)** : Macros capables d'accepter un nombre variable d'arguments.

**Concaténation de tokens (##)** : Opérateur du préprocesseur pour combiner deux tokens en un seul lors de l'expansion d'une macro.

**Stringification (#)** : Opérateur du préprocesseur pour convertir un argument de macro en une chaîne de caractères.

**Inclusions conditionnelles** : Directives du préprocesseur (comme #if, #ifdef, #ifndef) pour compiler ou ignorer sélectivement des parties du code.

**#if** : Directive du préprocesseur pour inclure ou exclure du code basé sur une expression constante.

**#ifdef** : Directive du préprocesseur pour inclure ou exclure du code si une macro est définie.

**#ifndef** : Directive du préprocesseur pour inclure ou exclure du code si une macro n'est pas définie.

**#error** : Directive du préprocesseur qui génère un message d'erreur et arrête la compilation.

**#pragma** : Directive du préprocesseur qui fournit des informations spécifiques au compilateur.

**Attributs (C23)** : Éléments de syntaxe introduits en C23 pour fournir des informations supplémentaires au compilateur, comme [[maybe_unused]] et [[fallthrough]].

**[[maybe_unused]]** : Attribut C23 indiquant au compilateur qu'une entité peut être déclarée mais non utilisée sans générer d'avertissement.

**[[fallthrough]]** : Attribut C23 indiquant que l'absence de `break` dans un `switch` est intentionnelle pour éviter un avertissement du compilateur.

**Profiling** : Processus d'analyse des performances d'un programme pour identifier les goulots d'étranglement.

**Optimisation** : Processus d'amélioration des performances, de l'efficacité ou de l'utilisation des ressources d'un programme.

**Outils de débogage** : Logiciels aidant à la détection et à la correction des erreurs dans les programmes C, tels que GDB.

**GDB** : Outil de débogage avancé pour C, permettant notamment les breakpoints conditionnels, les watchpoints et l'utilisation de commandes Python.

**breakpoints conditionnels** : Fonctionnalité de GDB permettant de suspendre l'exécution du programme uniquement si une condition est remplie.

**watchpoints** : Fonctionnalité de GDB permettant de suspendre l'exécution lorsque la valeur d'une variable change.

**commandes Python (GDB)** : Utilisation de scripts Python pour étendre les fonctionnalités de GDB.

**gprof** : Outil de profiling pour analyser le temps d'exécution des fonctions et identifier les parties coûteuses d'un programme C.

**valgrind** : Outil de débogage et de profilage, notamment utilisé pour la détection des fuites mémoire.

**Détection fuites mémoire** : Capacité d'identifier les blocs de mémoire alloués mais jamais libérés, souvent via des outils comme Valgrind.

**Techniques d'optimisation** : Méthodes spécifiques pour améliorer les performances, comme l'optimisation des boucles ou l'utilisation de fonctions inlines.

**fonctions inlines** : Fonctions dont le code est inséré directement à l'appel par le compilateur pour réduire le surcoût des appels de fonctions.

**Conventions de nommage** : Règles standardisées pour nommer les identificateurs afin d'améliorer la lisibilité et la maintenabilité du code.

**Gestion des erreurs (codes de retour, errno)** : Mécanismes pour gérer les erreurs, incluant l'utilisation de codes de retour et de la variable globale `errno`.

**Documentation du code** : Processus d'ajout de commentaires et de descriptions pour expliquer le fonctionnement et l'intention du code.

**Design Patterns en C** : Solutions architecturales réutilisables adaptées au langage C pour résoudre des problèmes de conception courants.

**Singleton** : Design pattern assurant qu'une classe n'a qu'une seule instance et fournissant un point d'accès global à celle-ci.

**Factory** : Design pattern permettant de créer des objets sans exposer la logique d'instanciation au client.

**Sécurité** : Ensemble des mesures et pratiques pour protéger les programmes C contre les vulnérabilités et les attaques.

**Gestion des vulnérabilités** : Processus d'identification, d'analyse et de correction des failles de sécurité potentielles dans le code C.

**Buffer overflow** : Vulnérabilité de sécurité où l'écriture de données dépasse la taille allouée d'un tampon, pouvant corrompre la mémoire adjacente.

**Integer overflow** : Vulnérabilité de sécurité où une opération arithmétique dépasse la capacité maximale d'un type entier, entraînant un comportement inattendu.


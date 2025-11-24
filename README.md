# Cours Langage C avance

## Objectifs de la formation

- Maîtriser les concepts avancés du langage C pour des applications robustes et performantes.
- Appliquer les meilleures pratiques de programmation C, y compris les standards récents (C11, C17, C23).
- Développer des compétences en débogage et optimisation de code C.
- Comprendre et utiliser efficacement les structures de données dynamiques, la gestion de la mémoire, et la programmation système en C.
## Séance 1 : Rappels et Approfondissements

### Objectifs pédagogiques

- Consolider les bases.
- Introduire les particularités des standards C récents.
### Contenus

#### Rappels essentiels

- Types de données et opérateurs avancés.
- Portée des variables (storage classes).
- Qualificatifs (volatile, restrict).
#### Les Standards C (C11, C17, C23)

- Évolution du langage, principales nouveautés (alignement, _Generic, threads C11).
- Améliorations const C23 et nouvelles fonctions de bibliothèque.
## Séance 2 : Gestion Avancée de la Mémoire

### Objectifs pédagogiques

- Maîtriser l'allocation dynamique et la gestion des erreurs mémoire.
### Contenus

#### Allocation Dynamique (malloc, calloc, realloc, free)

- Rappels, erreurs courantes (fuites mémoire, double free).
- Stratégies d'allocation, vérification des retours d'allocation.
#### Pointeurs Avancés et Arithmétique des Pointeurs

- Pointeurs de fonctions, pointeurs génériques (void*).
- Tableaux de pointeurs, pointeurs et structures.
## Séance 3 : Structures de Données Avancées

### Objectifs pédagogiques

- Implémenter et manipuler des structures de données complexes.
### Contenus

#### Listes, Piles, Files

- Rappels sur les listes chaînées, piles et files.
- Implémentations efficaces (doubly linked lists, circular buffers).
#### Arbres et Graphes

- Arbres binaires de recherche, arbres équilibrés (concepts).
- Représentations de graphes (listes d'adjacence, matrices).
## Séance 4 : Manipulation de Fichiers et Entrées/Sorties Avancées

### Objectifs pédagogiques

- Gérer les flux de données et les fichiers binaires.
### Contenus

#### Fichiers Texte et Binaire

- Utilisation des fonctions fopen, fread, fwrite, fseek, ftell.
- Gestion des erreurs lors des opérations sur les fichiers.
#### Entrées/Sorties Bufférisées et Non Bufférisées

- Compréhension des tampons (buffers) et de leur impact.
- Fonctions setbuf, fflush, fsync (concepts).
## Séance 5 : Programmation Système et Multitâche

### Objectifs pédagogiques

- Introduction aux appels système, gestion des processus et threads.
### Contenus

#### Appels Système de Base

- Utilisation des fonctions fork, exec, wait.
- Concepts de gestion des signaux.
#### Introduction aux Threads (Pthreads C11)

- Création de threads avec pthread_create.
- Synchronisation des threads (mutex, semaphore), problèmes courants (deadlock, race condition).
## Séance 6 : Préprocesseur Avancé et Macros

### Objectifs pédagogiques

- Utiliser le préprocesseur pour des codes flexibles et modulaires.
### Contenus

#### Macros Avancées

- Macros avec arguments variables (__VA_ARGS__).
- Concaténation de tokens (##), stringification (#).
#### Inclusions Conditionnelles et Attributs (C23)

- Utilisation de #if, #ifdef, #ifndef, #error, #pragma.
- Introduction aux attributs C23 ([[maybe_unused]], [[fallthrough]]).
## Séance 7 : Debugging, Profiling et Optimisation

### Objectifs pédagogiques

- Débugger, profiler et optimiser le code C de manière efficace.
### Contenus

#### Outils de Débogage

- Utilisation avancée de GDB (breakpoints conditionnels, watchpoints, commandes Python).
#### Profiling et Optimisation

- Introduction à gprof, valgrind (détection fuites mémoire).
- Techniques d'optimisation (boucles, fonctions inlines).
## Séance 8 : Best Practices, Design Patterns et Sécurité

### Objectifs pédagogiques

- Appliquer les meilleures pratiques de programmation C.
- Comprendre les design patterns en C et les aspects de sécurité.
### Contenus

#### Bonnes Pratiques de Programmation C

- Conventions de nommage, gestion des erreurs (codes de retour, errno).
- Importance de la documentation du code.
#### Introduction aux Design Patterns en C et Sécurité

- Singleton, Factory (patterns adaptés au C).
- Gestion des vulnérabilités (buffer overflow, integer overflow).

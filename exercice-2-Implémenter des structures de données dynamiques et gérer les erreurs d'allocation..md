Salut à toutes et à tous !

Ce TP est conçu pour vous faire manipuler concrètement les structures de données dynamiques en C, avec un focus particulier sur les listes chaînées et la gestion robuste de la mémoire. Nous allons construire un petit gestionnaire de contacts, ce qui nous permettra d'illustrer l'utilisation d'un tableau de pointeurs vers des listes chaînées distinctes.

---

## TP: Gestionnaire de Contacts Simplifié avec Listes Chaînées et Tableau de Pointeur

### Objectif du TP

*   Implémenter des listes chaînées dynamiques pour stocker des données.
*   Utiliser un tableau de pointeurs pour organiser et accéder à plusieurs listes chaînées.
*   Maîtriser la gestion des erreurs d'allocation mémoire (`malloc`, `free`).
*   Assurer une libération propre de toute la mémoire allouée dynamiquement.

### Contexte et Scénario

Nous allons développer un gestionnaire de contacts rudimentaire. Chaque contact sera caractérisé par un nom, un prénom et un numéro de téléphone. Pour organiser ces contacts, nous les classerons par catégories (par exemple : "Famille", "Amis", "Travail", "Autre"). Chaque catégorie sera représentée par une liste chaînée de contacts distincte, et l'ensemble de ces catégories sera géré via un tableau de pointeurs.

### Prérequis

*   Maîtrise des pointeurs et de l'arithmétique des pointeurs.
*   Connaissance des structures (`struct`) et des énumérations (`enum`).
*   Compréhension des principes de base de l'allocation dynamique de mémoire (`malloc`, `free`, `sizeof`).
*   Capacité à organiser un projet C en plusieurs fichiers (`.h`, `.c`).

### Partie 1 : Les Structures de Données de Base

Commencez par définir les structures nécessaires :

1.  **`struct Contact`** :
    *   `char nom[50];`
    *   `char prenom[50];`
    *   `char telephone[15];`

2.  **`struct Node`** :
    *   `struct Contact contact;` (Le contact stocké dans ce nœud)
    *   `struct Node *next;` (Pointeur vers le nœud suivant dans la liste)

3.  **`enum Categorie`** :
    *   Définissez une énumération pour vos catégories : `FAMILLE`, `AMIS`, `TRAVAIL`, `AUTRE`.
    *   Ajoutez une valeur `NB_CATEGORIES` à la fin pour connaître le nombre total de catégories, utile pour dimensionner votre tableau.

### Partie 2 : Fonctions de Manipulation d'une Liste Chaînée

Implémentez les fonctions suivantes pour gérer une *seule* liste chaînée. Ces fonctions seront génériques et réutilisables pour chaque catégorie.

1.  **`Node* creerNoeud(struct Contact c)`**
    *   Alloue dynamiquement un nouveau nœud.
    *   Initialise ses champs avec le contact donné.
    *   Retourne un pointeur vers le nouveau nœud, ou `NULL` en cas d'échec d'allocation.

2.  **`Node* ajouterContactEnTete(Node* head, struct Contact c)`**
    *   Ajoute un nouveau contact en tête de la liste.
    *   Utilise `creerNoeud`.
    *   Retourne le nouveau pointeur `head` de la liste (qui peut être `NULL` si l'allocation échoue).

3.  **`void afficherListe(Node* head)`**
    *   Parcourt la liste et affiche les détails de chaque contact.
    *   Gère le cas d'une liste vide.

4.  **`Node* supprimerContact(Node* head, const char* nom, const char* prenom)`**
    *   Recherche et supprime le premier contact correspondant au `nom` et `prenom` donnés.
    *   Gère les cas de suppression en tête, au milieu, ou de contact non trouvé.
    *   Libère la mémoire du nœud supprimé.
    *   Retourne le nouveau pointeur `head` de la liste.

5.  **`void libererListe(Node* head)`**
    *   Parcourt la liste et libère *tous* les nœuds un par un.
    *   Indispensable pour éviter les fuites de mémoire.

### Partie 3 : Le Gestionnaire de Catégories (Tableau de Pointeur)

Maintenant, intégrez les listes chaînées dans un gestionnaire de catégories :

1.  **Déclaration du Gestionnaire** :
    *   Déclarez un tableau de pointeurs de nœuds : `Node* gestionnaireCategories[NB_CATEGORIES];`
    *   Chaque élément de ce tableau sera la tête d'une liste chaînée correspondant à une catégorie.

2.  **`void initialiserGestionnaire(Node* gestionnaire[])`**
    *   Initialise toutes les têtes de liste du tableau à `NULL`.

3.  **`void ajouterContactAGestionnaire(Node* gestionnaire[], enum Categorie cat, struct Contact c)`**
    *   Ajoute un contact à la liste correspondant à la `catégorie` spécifiée.
    *   Utilise la fonction `ajouterContactEnTete` de la Partie 2.
    *   Gère l'échec d'allocation du nouveau nœud.

4.  **`void afficherCategorie(Node* gestionnaire[], enum Categorie cat)`**
    *   Affiche tous les contacts d'une catégorie donnée.
    *   Utilise la fonction `afficherListe` de la Partie 2.

5.  **`void supprimerContactDeCategorie(Node* gestionnaire[], enum Categorie cat, const char* nom, const char* prenom)`**
    *   Supprime un contact d'une catégorie spécifique.
    *   Utilise la fonction `supprimerContact` de la Partie 2.

6.  **`void libererGestionnaire(Node* gestionnaire[])`**
    *   Parcourt toutes les catégories du tableau.
    *   Pour chaque catégorie, appelle `libererListe` pour libérer tous les nœuds de cette liste.
    *   Assure une libération complète de la mémoire du gestionnaire.

### Partie 4 : Robustesse et Gestion des Erreurs

La gestion des erreurs d'allocation est un point central de ce TP :

*   **Vérification de `malloc`** : Chaque appel à `malloc` doit être suivi d'une vérification de la valeur de retour. En cas d'échec (`NULL`), affichez un message d'erreur clair (sur `stderr` de préférence) et gérez la situation :
    *   Pour `creerNoeud`, retournez `NULL`.
    *   Pour les fonctions d'ajout, assurez-vous que la liste reste cohérente même si l'ajout échoue.
*   **Gestion des `NULL`** : Assurez-vous que toutes vos fonctions gèrent correctement les pointeurs `NULL` en entrée (par exemple, une liste vide passée à `afficherListe` ou `supprimerContact`).
*   **Libération complète** : La fonction `libererGestionnaire` doit garantir qu'aucune fuite de mémoire ne subsiste après l'utilisation du gestionnaire.

### Consignes Générales

*   **Modularité** : Organisez votre code en plusieurs fichiers (`.h` pour les déclarations, `.c` pour les implémentations) pour chaque partie logique (ex: `contact.h/.c`, `list.h/.c`, `manager.h/.c`).
*   **Fonction `main`** : Écrivez une fonction `main` qui démontre l'utilisation de toutes les fonctions implémentées :
    *   Initialisation du gestionnaire.
    *   Ajout de plusieurs contacts dans différentes catégories.
    *   Affichage de catégories spécifiques et de toutes les catégories.
    *   Tentatives de suppression de contacts (existants et non existants).
    *   Test des erreurs d'allocation (si vous voulez aller plus loin, vous pouvez simuler un échec de `malloc` pour tester votre gestion d'erreur).
    *   Libération finale de toute la mémoire.
*   **Compilation** : Compilez votre code avec les options `-Wall -Wextra -std=c11` (ou `c99`) pour activer tous les avertissements et vous assurer de la conformité au standard.
*   **Utilisation de l'IA** : N'hésitez pas à utiliser des outils d'IA pour vous aider à comprendre des concepts, débugger votre code, ou même générer des ébauches de fonctions. L'objectif est d'apprendre et de maîtriser le sujet, pas de prouver que vous pouvez tout faire sans aide. Soyez critique envers les suggestions de l'IA et assurez-vous de comprendre *pourquoi* le code fonctionne comme il le fait. C'est votre compréhension et votre capacité à expliquer le code qui comptent.

### Aller plus loin (Bonus)

Si vous avez terminé et que vous souhaitez explorer davantage :

1.  **Recherche Globale** : Implémentez une fonction `void rechercherContact(Node* gestionnaire[], const char* nom, const char* prenom)` qui recherche un contact dans *toutes* les catégories et affiche où il a été trouvé.
2.  **Ajout Trié** : Modifiez `ajouterContactEnTete` (ou créez une nouvelle fonction) pour insérer les contacts de manière triée (par nom, puis prénom) dans chaque liste.
3.  **Modification de Contact** : Ajoutez une fonction `bool modifierContact(Node* gestionnaire[], enum Categorie cat, const char* nomAncien, const char* prenomAncien, struct Contact nouveauContact)` pour modifier les informations d'un contact existant.
4.  **Sauvegarde/Chargement** : Implémentez des fonctions pour sauvegarder l'ensemble du gestionnaire de contacts dans un fichier texte et pour le recharger depuis ce fichier.

---

Amusez-vous bien avec ce TP ! C'est une excellente occasion de solidifier vos compétences en C avancé.
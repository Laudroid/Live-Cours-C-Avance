Voici deux solutions possibles pour le TP "Gestionnaire de Contacts Simplifié avec Listes Chaînées et Tableau de Pointeur", chacune présentée dans un chapitre distinct au format Markdown. Ces solutions sont commentées et expliquées pour faciliter votre compréhension.

---

# Solution 1 : Implémentation Standard avec Recherche Globale

Cette solution suit les instructions du TP en mettant l'accent sur la modularité, la gestion robuste de la mémoire et l'implémentation d'une fonction de recherche globale comme bonus.

## Structure des fichiers

*   `contact.h` / `contact.c` : Définition des structures `Contact` et `Categorie`, et fonction utilitaire.
*   `node.h` : Définition de la structure `Node`.
*   `list.h` / `list.c` : Fonctions de manipulation d'une liste chaînée simple.
*   `manager.h` / `manager.c` : Fonctions de gestion du tableau de catégories.
*   `main.c` : Application principale pour démontrer l'utilisation.

## Fichier `contact.h`


```c
#ifndef CONTACT_H
#define CONTACT_H

// Structure pour un contact
typedef struct Contact {
    char nom[50];
    char prenom[50];
    char telephone[15];
} Contact;

// Énumération pour les catégories de contacts
typedef enum Categorie {
    FAMILLE,
    AMIS,
    TRAVAIL,
    AUTRE,
    NB_CATEGORIES // Doit toujours être la dernière pour le compte total des catégories
} Categorie;

// Fonction utilitaire pour obtenir le nom d'une catégorie sous forme de chaîne de caractères
const char* get_categorie_name(Categorie cat);

#endif // CONTACT_H
```


## Fichier `contact.c`


```c
#include "contact.h"

// Implémentation de la fonction utilitaire pour obtenir le nom d'une catégorie
const char* get_categorie_name(Categorie cat) {
    switch (cat) {
        case FAMILLE: return "Famille";
        case AMIS: return "Amis";
        case TRAVAIL: return "Travail";
        case AUTRE: return "Autre";
        default: return "Inconnu"; // Gère les cas où la catégorie est hors limites
    }
}
```


## Fichier `node.h`


```c
#ifndef NODE_H
#define NODE_H

#include "contact.h" // Nécessaire pour inclure la définition de struct Contact

// Structure pour un nœud de liste chaînée
typedef struct Node {
    Contact contact;      // Le contact stocké dans ce nœud
    struct Node *next;    // Pointeur vers le nœud suivant dans la liste
} Node;

#endif // NODE_H
```


## Fichier `list.h`


```c
#ifndef LIST_H
#define LIST_H

#include "node.h" // Nécessaire pour inclure la définition de struct Node

// Fonctions de manipulation d'une liste chaînée simple

/**
 * @brief Alloue dynamiquement un nouveau nœud et l'initialise avec un contact.
 * @param c Le contact à stocker dans le nouveau nœud.
 * @return Un pointeur vers le nouveau nœud, ou NULL en cas d'échec d'allocation.
 */
Node* creerNoeud(Contact c);

/**
 * @brief Ajoute un nouveau contact en tête de la liste chaînée.
 * @param head La tête actuelle de la liste.
 * @param c Le contact à ajouter.
 * @return La nouvelle tête de la liste (qui peut être NULL si l'allocation échoue).
 */
Node* ajouterContactEnTete(Node* head, Contact c);

/**
 * @brief Parcourt et affiche les détails de tous les contacts dans la liste.
 * @param head La tête de la liste à afficher.
 */
void afficherListe(Node* head);

/**
 * @brief Recherche et supprime le premier contact correspondant au nom et prénom donnés.
 * @param head La tête de la liste.
 * @param nom Le nom du contact à supprimer.
 * @param prenom Le prénom du contact à supprimer.
 * @return La nouvelle tête de la liste après suppression (peut être la même si non trouvé ou supprimé au milieu).
 */
Node* supprimerContact(Node* head, const char* nom, const char* prenom);

/**
 * @brief Libère toute la mémoire allouée pour les nœuds d'une liste chaînée.
 * @param head La tête de la liste à libérer.
 */
void libererListe(Node* head);

#endif // LIST_H
```


## Fichier `list.c`


```c
#include "list.h"
#include <stdio.h>  // Pour printf, fprintf
#include <stdlib.h> // Pour malloc, free
#include <string.h> // Pour strncpy, strcmp

// Implémentation de creerNoeud
Node* creerNoeud(Contact c) {
    Node* nouveauNoeud = (Node*)malloc(sizeof(Node));
    if (nouveauNoeud == NULL) {
        fprintf(stderr, "Erreur d'allocation mémoire pour un nouveau nœud.\n");
        return NULL;
    }
    // Copie sécurisée des données du contact pour éviter les débordements de tampon
    strncpy(nouveauNoeud->contact.nom, c.nom, sizeof(nouveauNoeud->contact.nom) - 1);
    nouveauNoeud->contact.nom[sizeof(nouveauNoeud->contact.nom) - 1] = '\0'; // Assure la null-termination

    strncpy(nouveauNoeud->contact.prenom, c.prenom, sizeof(nouveauNoeud->contact.prenom) - 1);
    nouveauNoeud->contact.prenom[sizeof(nouveauNoeud->contact.prenom) - 1] = '\0';

    strncpy(nouveauNoeud->contact.telephone, c.telephone, sizeof(nouveauNoeud->contact.telephone) - 1);
    nouveauNoeud->contact.telephone[sizeof(nouveauNoeud->contact.telephone) - 1] = '\0';

    nouveauNoeud->next = NULL; // Le nouveau nœud est initialement le dernier
    return nouveauNoeud;
}

// Implémentation d'ajouterContactEnTete
Node* ajouterContactEnTete(Node* head, Contact c) {
    Node* nouveauNoeud = creerNoeud(c);
    if (nouveauNoeud == NULL) {
        return head; // Retourne la tête inchangée si l'allocation échoue
    }
    nouveauNoeud->next = head; // Le nouveau nœud pointe vers l'ancienne tête
    return nouveauNoeud;       // Le nouveau nœud devient la nouvelle tête
}

// Implémentation d'afficherListe
void afficherListe(Node* head) {
    if (head == NULL) {
        printf("  (Liste vide)\n");
        return;
    }
    Node* current = head;
    while (current != NULL) {
        printf("  - Nom: %s, Prénom: %s, Tél: %s\n",
               current->contact.nom, current->contact.prenom, current->contact.telephone);
        current = current->next;
    }
}

// Implémentation de supprimerContact
Node* supprimerContact(Node* head, const char* nom, const char* prenom) {
    if (head == NULL) {
        return NULL; // La liste est vide, rien à supprimer
    }

    Node* current = head;
    Node* prev = NULL;

    // Parcourt la liste pour trouver le contact à supprimer
    while (current != NULL && (strcmp(current->contact.nom, nom) != 0 || strcmp(current->contact.prenom, prenom) != 0)) {
        prev = current;
        current = current->next;
    }

    if (current == NULL) {
        // Contact non trouvé dans la liste
        printf("  Contact '%s %s' non trouvé.\n", nom, prenom);
        return head;
    }

    // Contact trouvé, procède à la suppression
    if (prev == NULL) {
        // Le contact à supprimer est la tête de la liste
        head = current->next;
    } else {
        // Le contact à supprimer est au milieu ou à la fin de la liste
        prev->next = current->next;
    }
    free(current); // Libère la mémoire du nœud supprimé
    printf("  Contact '%s %s' supprimé avec succès.\n", nom, prenom);
    return head; // Retourne la nouvelle tête (ou l'ancienne si la tête n'a pas changé)
}

// Implémentation de libererListe
void libererListe(Node* head) {
    Node* current = head;
    Node* next_node;
    while (current != NULL) {
        next_node = current->next; // Sauvegarde le pointeur vers le nœud suivant
        free(current);             // Libère la mémoire du nœud actuel
        current = next_node;       // Passe au nœud suivant
    }
}
```


## Fichier `manager.h`


```c
#ifndef MANAGER_H
#define MANAGER_H

#include "node.h"    // Nécessaire pour struct Node
#include "contact.h" // Nécessaire pour enum Categorie

// Déclaration du gestionnaire de catégories (tableau de pointeurs de têtes de liste)
// 'extern' indique que la variable est définie dans un autre fichier (.c)
extern Node* gestionnaireCategories[NB_CATEGORIES];

// Fonctions de manipulation du gestionnaire de catégories

/**
 * @brief Initialise toutes les têtes de liste du gestionnaire à NULL.
 */
void initialiserGestionnaire();

/**
 * @brief Ajoute un contact à la liste correspondant à la catégorie spécifiée.
 * @param cat La catégorie à laquelle ajouter le contact.
 * @param c Le contact à ajouter.
 */
void ajouterContactAGestionnaire(Categorie cat, Contact c);

/**
 * @brief Affiche tous les contacts d'une catégorie donnée.
 * @param cat La catégorie à afficher.
 */
void afficherCategorie(Categorie cat);

/**
 * @brief Supprime un contact d'une catégorie spécifique.
 * @param cat La catégorie d'où supprimer le contact.
 * @param nom Le nom du contact à supprimer.
 * @param prenom Le prénom du contact à supprimer.
 */
void supprimerContactDeCategorie(Categorie cat, const char* nom, const char* prenom);

/**
 * @brief Libère toute la mémoire allouée par toutes les listes du gestionnaire.
 */
void libererGestionnaire();

// --- Bonus : Recherche Globale ---
/**
 * @brief Recherche un contact par nom et prénom dans toutes les catégories.
 * @param nom Le nom du contact à rechercher.
 * @param prenom Le prénom du contact à rechercher.
 */
void rechercherContact(const char* nom, const char* prenom);

#endif // MANAGER_H
```


## Fichier `manager.c`


```c
#include "manager.h"
#include "list.h" // Nécessaire pour utiliser les fonctions de manipulation de liste
#include <stdio.h>

// Définition (allocation) du gestionnaire de catégories
Node* gestionnaireCategories[NB_CATEGORIES];

// Implémentation d'initialiserGestionnaire
void initialiserGestionnaire() {
    for (int i = 0; i < NB_CATEGORIES; i++) {
        gestionnaireCategories[i] = NULL; // Chaque tête de liste est initialisée à NULL (liste vide)
    }
    printf("Gestionnaire de contacts initialisé.\n");
}

// Implémentation d'ajouterContactAGestionnaire
void ajouterContactAGestionnaire(Categorie cat, Contact c) {
    if (cat < 0 || cat >= NB_CATEGORIES) {
        fprintf(stderr, "Erreur: Catégorie invalide (%d) pour l'ajout de contact.\n", cat);
        return;
    }
    // Appelle la fonction d'ajout en tête de liste pour la catégorie spécifiée
    gestionnaireCategories[cat] = ajouterContactEnTete(gestionnaireCategories[cat], c);
    if (gestionnaireCategories[cat] == NULL) {
        // Si l'allocation a échoué et la liste était vide, la tête est toujours NULL
        fprintf(stderr, "Échec d'ajout du contact %s %s à la catégorie %s (erreur mémoire).\n",
                c.nom, c.prenom, get_categorie_name(cat));
    } else {
        printf("Contact '%s %s' ajouté à la catégorie %s.\n",
               c.nom, c.prenom, get_categorie_name(cat));
    }
}

// Implémentation d'afficherCategorie
void afficherCategorie(Categorie cat) {
    if (cat < 0 || cat >= NB_CATEGORIES) {
        fprintf(stderr, "Erreur: Catégorie invalide (%d) pour l'affichage.\n", cat);
        return;
    }
    printf("\n--- Contacts de la catégorie %s ---\n", get_categorie_name(cat));
    afficherListe(gestionnaireCategories[cat]); // Utilise la fonction d'affichage de liste
}

// Implémentation de supprimerContactDeCategorie
void supprimerContactDeCategorie(Categorie cat, const char* nom, const char* prenom) {
    if (cat < 0 || cat >= NB_CATEGORIES) {
        fprintf(stderr, "Erreur: Catégorie invalide (%d) pour la suppression de contact.\n", cat);
        return;
    }
    printf("Tentative de suppression de '%s %s' de la catégorie %s...\n",
           nom, prenom, get_categorie_name(cat));
    // Appelle la fonction de suppression de liste pour la catégorie spécifiée
    gestionnaireCategories[cat] = supprimerContact(gestionnaireCategories[cat], nom, prenom);
}

// Implémentation de libererGestionnaire
void libererGestionnaire() {
    printf("\n--- Libération de toute la mémoire du gestionnaire ---\n");
    for (int i = 0; i < NB_CATEGORIES; i++) {
        printf("  Libération de la catégorie %s...\n", get_categorie_name((Categorie)i));
        libererListe(gestionnaireCategories[i]); // Libère chaque liste chaînée
        gestionnaireCategories[i] = NULL;        // S'assurer que le pointeur est NULL après libération
    }
    printf("Toute la mémoire a été libérée.\n");
}

// --- Bonus : Implémentation de rechercherContact ---
void rechercherContact(const char* nom, const char* prenom) {
    printf("\n--- Recherche de '%s %s' dans toutes les catégories ---\n", nom, prenom);
    int found_count = 0;
    for (int i = 0; i < NB_CATEGORIES; i++) {
        Node* current = gestionnaireCategories[i];
        while (current != NULL) {
            if (strcmp(current->contact.nom, nom) == 0 && strcmp(current->contact.prenom, prenom) == 0) {
                printf("  Trouvé dans la catégorie %s: Nom: %s, Prénom: %s, Tél: %s\n",
                       get_categorie_name((Categorie)i), current->contact.nom, current->contact.prenom, current->contact.telephone);
                found_count++;
            }
            current = current->next;
        }
    }
    if (found_count == 0) {
        printf("  Contact '%s %s' non trouvé dans aucune catégorie.\n", nom, prenom);
    }
}
```


## Fichier `main.c`


```c
#include <stdio.h>
#include <string.h> // Pour strncpy si besoin de créer des contacts directement

#include "contact.h"  // Inclut les définitions de Contact et Categorie
#include "manager.h"  // Inclut les déclarations des fonctions du gestionnaire

int main() {
    // Initialisation du gestionnaire de contacts
    initialiserGestionnaire();

    // Création de quelques contacts
    Contact c1 = {"Dupont", "Jean", "0611223344"};
    Contact c2 = {"Martin", "Sophie", "0755667788"};
    Contact c3 = {"Durand", "Pierre", "0123456789"};
    Contact c4 = {"Lefevre", "Marie", "0699887766"};
    Contact c5 = {"Dupont", "Jean", "0600000000"}; // Un contact avec le même nom/prénom

    // Ajout de contacts dans différentes catégories
    printf("\n--- Ajout de contacts dans le gestionnaire ---\n");
    ajouterContactAGestionnaire(FAMILLE, c1);
    ajouterContactAGestionnaire(AMIS, c2);
    ajouterContactAGestionnaire(TRAVAIL, c3);
    ajouterContactAGestionnaire(FAMILLE, c4);
    ajouterContactAGestionnaire(AUTRE, c5); // Ajout du duplicata dans une autre catégorie

    // Affichage de catégories spécifiques
    printf("\n--- Affichage des catégories ---\n");
    afficherCategorie(FAMILLE);
    afficherCategorie(AMIS);
    afficherCategorie(TRAVAIL);
    afficherCategorie(AUTRE);
    afficherCategorie((Categorie)99); // Test d'une catégorie invalide

    // Test de suppression de contacts
    printf("\n--- Suppression de contacts ---\n");
    supprimerContactDeCategorie(FAMILLE, "Dupont", "Jean"); // Supprime c1
    supprimerContactDeCategorie(AMIS, "NonExistant", "Contact"); // Tentative de suppression d'un contact non existant
    supprimerContactDeCategorie(TRAVAIL, "Durand", "Pierre"); // Supprime c3

    // Affichage des catégories après suppression
    printf("\n--- Affichage des catégories après suppression ---\n");
    afficherCategorie(FAMILLE);
    afficherCategorie(AMIS);
    afficherCategorie(TRAVAIL);
    afficherCategorie(AUTRE);

    // Test de la recherche globale (Bonus)
    rechercherContact("Lefevre", "Marie");
    rechercherContact("Dupont", "Jean"); // Devrait trouver le "Dupont Jean" restant dans AUTRE
    rechercherContact("Inconnu", "Personne"); // Contact non existant

    // Libération de toute la mémoire à la fin du programme
    libererGestionnaire();

    return 0;
}
```


## Compilation

Pour compiler cette solution, utilisez une commande similaire à celle-ci :
`gcc -Wall -Wextra -std=c11 contact.c list.c manager.c main.c -o contact_manager_s1`

## Explications des Concepts Clés (Solution 1)

*   **Structures de données (`Contact`, `Node`)** : `Contact` encapsule les informations d'un contact. `Node` est la brique fondamentale de la liste chaînée, contenant un `Contact` et un pointeur vers le `next` nœud.
*   **Énumération (`Categorie`)** : Fournit des noms symboliques pour les catégories, rendant le code plus lisible et moins sujet aux erreurs que l'utilisation de simples entiers. `NB_CATEGORIES` est une astuce courante pour obtenir le nombre d'éléments dans l'énumération.
*   **Allocation dynamique (`malloc`, `free`)** : Chaque nœud de la liste est alloué sur le tas avec `malloc` et doit être explicitement libéré avec `free` une fois qu'il n'est plus nécessaire.
*   **Gestion des erreurs d'allocation** : Chaque appel à `malloc` est suivi d'une vérification de sa valeur de retour. Si `malloc` retourne `NULL`, cela indique un échec d'allocation mémoire. Le programme affiche un message d'erreur et gère la situation (par exemple, en retournant la tête de liste inchangée pour les fonctions d'ajout).
*   **Pointeurs et listes chaînées** : Les fonctions de `list.c` manipulent les pointeurs `next` pour insérer, parcourir et supprimer des nœuds. La tête de liste (`head`) est un pointeur crucial qui indique le début de la liste.
*   **`strncpy`** : Utilisé pour copier les chaînes de caractères (`nom`, `prenom`, `telephone`) de manière sécurisée, en spécifiant la taille maximale du tampon de destination pour éviter les débordements. La null-termination est assurée manuellement.
*   **Tableau de pointeurs (`gestionnaireCategories`)** : Ce tableau, déclaré globalement dans `manager.c` et `extern` dans `manager.h`, est le cœur du gestionnaire de catégories. Chaque élément du tableau est un pointeur vers la tête d'une liste chaînée distincte, permettant d'avoir une liste de contacts par catégorie.
*   **Modularité** : Le code est divisé en plusieurs fichiers `.h` et `.c` pour organiser les responsabilités : `contact` pour les définitions de base, `list` pour la logique de liste chaînée, et `manager` pour la logique de gestion des catégories. Cela améliore la lisibilité, la maintenabilité et la réutilisabilité du code.
*   **Libération complète de la mémoire (`libererListe`, `libererGestionnaire`)** : La fonction `libererListe` parcourt une liste et libère chaque nœud. `libererGestionnaire` itère sur toutes les catégories du tableau et appelle `libererListe` pour chacune, garantissant qu'aucune fuite de mémoire ne subsiste à la fin du programme.
*   **Bonus : Recherche Globale** : La fonction `rechercherContact` démontre comment parcourir toutes les listes du gestionnaire pour trouver un contact, illustrant l'accès aux données organisées par le tableau de pointeurs.

---

# Solution 2 : Implémentation avec Ajout en Queue et Modification de Contact

Cette solution propose une alternative à la première, en incluant une fonction d'ajout en queue (`ajouterContactEnQueue`) et en implémentant la modification de contact comme bonus.

## Structure des fichiers

*   `contact.h` / `contact.c` : Définition des structures `Contact` et `Categorie`, et fonction utilitaire.
*   `node.h` : Définition de la structure `Node`.
*   `list.h` / `list.c` : Fonctions de manipulation d'une liste chaînée simple (avec ajout en queue).
*   `manager.h` / `manager.c` : Fonctions de gestion du tableau de catégories.
*   `main.c` : Application principale pour démontrer l'utilisation.

## Fichier `contact.h`


```c
#ifndef CONTACT_H
#define CONTACT_H

// Structure pour un contact
typedef struct Contact {
    char nom[50];
    char prenom[50];
    char telephone[15];
} Contact;

// Énumération pour les catégories de contacts
typedef enum Categorie {
    FAMILLE,
    AMIS,
    TRAVAIL,
    AUTRE,
    NB_CATEGORIES // Doit toujours être la dernière pour le compte total des catégories
} Categorie;

// Fonction utilitaire pour obtenir le nom d'une catégorie sous forme de chaîne de caractères
const char* get_categorie_name(Categorie cat);

#endif // CONTACT_H
```


## Fichier `contact.c`


```c
#include "contact.h"

// Implémentation de la fonction utilitaire pour obtenir le nom d'une catégorie
const char* get_categorie_name(Categorie cat) {
    switch (cat) {
        case FAMILLE: return "Famille";
        case AMIS: return "Amis";
        case TRAVAIL: return "Travail";
        case AUTRE: return "Autre";
        default: return "Inconnu"; // Gère les cas où la catégorie est hors limites
    }
}
```


## Fichier `node.h`


```c
#ifndef NODE_H
#define NODE_H

#include "contact.h" // Nécessaire pour inclure la définition de struct Contact

// Structure pour un nœud de liste chaînée
typedef struct Node {
    Contact contact;      // Le contact stocké dans ce nœud
    struct Node *next;    // Pointeur vers le nœud suivant dans la liste
} Node;

#endif // NODE_H
```


## Fichier `list.h`


```c
#ifndef LIST_H
#define LIST_H

#include "node.h"    // Nécessaire pour inclure la définition de struct Node
#include <stdbool.h> // Nécessaire pour le type bool (pour le bonus)

// Fonctions de manipulation d'une liste chaînée simple

/**
 * @brief Alloue dynamiquement un nouveau nœud et l'initialise avec un contact.
 * @param c Le contact à stocker dans le nouveau nœud.
 * @return Un pointeur vers le nouveau nœud, ou NULL en cas d'échec d'allocation.
 */
Node* creerNoeud(Contact c);

/**
 * @brief Ajoute un nouveau contact en tête de la liste chaînée.
 * @param head La tête actuelle de la liste.
 * @param c Le contact à ajouter.
 * @return La nouvelle tête de la liste (qui peut être NULL si l'allocation échoue).
 */
Node* ajouterContactEnTete(Node* head, Contact c);

/**
 * @brief Ajoute un nouveau contact en queue de la liste chaînée.
 * @param head La tête actuelle de la liste.
 * @param c Le contact à ajouter.
 * @return La tête de la liste (inchangée si la liste n'était pas vide, ou le nouveau nœud si la liste était vide).
 */
Node* ajouterContactEnQueue(Node* head, Contact c);

/**
 * @brief Parcourt et affiche les détails de tous les contacts dans la liste.
 * @param head La tête de la liste à afficher.
 */
void afficherListe(Node* head);

/**
 * @brief Recherche et supprime le premier contact correspondant au nom et prénom donnés.
 * @param head La tête de la liste.
 * @param nom Le nom du contact à supprimer.
 * @param prenom Le prénom du contact à supprimer.
 * @return La nouvelle tête de la liste après suppression (peut être la même si non trouvé ou supprimé au milieu).
 */
Node* supprimerContact(Node* head, const char* nom, const char* prenom);

/**
 * @brief Libère toute la mémoire allouée pour les nœuds d'une liste chaînée.
 * @param head La tête de la liste à libérer.
 */
void libererListe(Node* head);

// --- Bonus : Modification de Contact ---
/**
 * @brief Modifie les informations d'un contact existant dans la liste.
 * @param head La tête de la liste.
 * @param nomAncien Le nom actuel du contact à modifier.
 * @param prenomAncien Le prénom actuel du contact à modifier.
 * @param nouveauContact La nouvelle structure Contact avec les informations mises à jour.
 * @return true si le contact a été trouvé et modifié, false sinon.
 */
bool modifierContactDansListe(Node* head, const char* nomAncien, const char* prenomAncien, Contact nouveauContact);

#endif // LIST_H
```


## Fichier `list.c`


```c
#include "list.h"
#include <stdio.h>  // Pour printf, fprintf
#include <stdlib.h> // Pour malloc, free
#include <string.h> // Pour strncpy, strcmp

// Implémentation de creerNoeud
Node* creerNoeud(Contact c) {
    Node* nouveauNoeud = (Node*)malloc(sizeof(Node));
    if (nouveauNoeud == NULL) {
        fprintf(stderr, "Erreur d'allocation mémoire pour un nouveau nœud.\n");
        return NULL;
    }
    // Copie sécurisée des données du contact
    strncpy(nouveauNoeud->contact.nom, c.nom, sizeof(nouveauNoeud->contact.nom) - 1);
    nouveauNoeud->contact.nom[sizeof(nouveauNoeud->contact.nom) - 1] = '\0';

    strncpy(nouveauNoeud->contact.prenom, c.prenom, sizeof(nouveauNoeud->contact.prenom) - 1);
    nouveauNoeud->contact.prenom[sizeof(nouveauNoeud->contact.prenom) - 1] = '\0';

    strncpy(nouveauNoeud->contact.telephone, c.telephone, sizeof(nouveauNoeud->contact.telephone) - 1);
    nouveauNoeud->contact.telephone[sizeof(nouveauNoeud->contact.telephone) - 1] = '\0';

    nouveauNoeud->next = NULL;
    return nouveauNoeud;
}

// Implémentation d'ajouterContactEnTete
Node* ajouterContactEnTete(Node* head, Contact c) {
    Node* nouveauNoeud = creerNoeud(c);
    if (nouveauNoeud == NULL) {
        return head; // Retourne la tête inchangée si l'allocation échoue
    }
    nouveauNoeud->next = head;
    return nouveauNoeud;
}

// Implémentation d'ajouterContactEnQueue (nouvelle fonction)
Node* ajouterContactEnQueue(Node* head, Contact c) {
    Node* nouveauNoeud = creerNoeud(c);
    if (nouveauNoeud == NULL) {
        return head; // Retourne la tête inchangée si l'allocation échoue
    }

    if (head == NULL) {
        return nouveauNoeud; // Si la liste est vide, le nouveau nœud est la tête
    }

    Node* current = head;
    while (current->next != NULL) {
        current = current->next; // Parcourt jusqu'au dernier nœud
    }
    current->next = nouveauNoeud; // Ajoute le nouveau nœud à la fin
    return head;
}

// Implémentation d'afficherListe
void afficherListe(Node* head) {
    if (head == NULL) {
        printf("  (Liste vide)\n");
        return;
    }
    Node* current = head;
    while (current != NULL) {
        printf("  - Nom: %s, Prénom: %s, Tél: %s\n",
               current->contact.nom, current->contact.prenom, current->contact.telephone);
        current = current->next;
    }
}

// Implémentation de supprimerContact
Node* supprimerContact(Node* head, const char* nom, const char* prenom) {
    if (head == NULL) {
        return NULL; // La liste est vide
    }

    Node* current = head;
    Node* prev = NULL;

    // Parcourt la liste pour trouver le contact
    while (current != NULL && (strcmp(current->contact.nom, nom) != 0 || strcmp(current->contact.prenom, prenom) != 0)) {
        prev = current;
        current = current->next;
    }

    if (current == NULL) {
        printf("  Contact '%s %s' non trouvé.\n", nom, prenom);
        return head; // Contact non trouvé
    }

    // Contact trouvé, procède à la suppression
    if (prev == NULL) { // Suppression en tête
        head = current->next;
    } else { // Suppression au milieu ou à la fin
        prev->next = current->next;
    }
    free(current);
    printf("  Contact '%s %s' supprimé avec succès.\n", nom, prenom);
    return head;
}

// Implémentation de libererListe
void libererListe(Node* head) {
    Node* current = head;
    Node* next_node;
    while (current != NULL) {
        next_node = current->next;
        free(current);
        current = next_node;
    }
}

// --- Bonus : Implémentation de modifierContactDansListe ---
bool modifierContactDansListe(Node* head, const char* nomAncien, const char* prenomAncien, Contact nouveauContact) {
    Node* current = head;
    while (current != NULL) {
        if (strcmp(current->contact.nom, nomAncien) == 0 && strcmp(current->contact.prenom, prenomAncien) == 0) {
            // Contact trouvé, met à jour ses informations
            strncpy(current->contact.nom, nouveauContact.nom, sizeof(current->contact.nom) - 1);
            current->contact.nom[sizeof(current->contact.nom) - 1] = '\0';

            strncpy(current->contact.prenom, nouveauContact.prenom, sizeof(current->contact.prenom) - 1);
            current->contact.prenom[sizeof(current->contact.prenom) - 1] = '\0';

            strncpy(current->contact.telephone, nouveauContact.telephone, sizeof(current->contact.telephone) - 1);
            current->contact.telephone[sizeof(current->contact.telephone) - 1] = '\0';

            printf("  Contact '%s %s' modifié en '%s %s'.\n", nomAncien, prenomAncien, nouveauContact.nom, nouveauContact.prenom);
            return true;
        }
        current = current->next;
    }
    printf("  Contact '%s %s' non trouvé pour modification.\n", nomAncien, prenomAncien);
    return false; // Contact non trouvé
}
```


## Fichier `manager.h`


```c
#ifndef MANAGER_H
#define MANAGER_H

#include "node.h"    // Nécessaire pour struct Node
#include "contact.h" // Nécessaire pour enum Categorie
#include <stdbool.h> // Nécessaire pour le type bool (pour le bonus)

// Déclaration du gestionnaire de catégories (tableau de pointeurs de têtes de liste)
extern Node* gestionnaireCategories[NB_CATEGORIES];

// Fonctions de manipulation du gestionnaire de catégories

/**
 * @brief Initialise toutes les têtes de liste du gestionnaire à NULL.
 */
void initialiserGestionnaire();

/**
 * @brief Ajoute un contact à la liste correspondant à la catégorie spécifiée.
 * @param cat La catégorie à laquelle ajouter le contact.
 * @param c Le contact à ajouter.
 */
void ajouterContactAGestionnaire(Categorie cat, Contact c);

/**
 * @brief Affiche tous les contacts d'une catégorie donnée.
 * @param cat La catégorie à afficher.
 */
void afficherCategorie(Categorie cat);

/**
 * @brief Supprime un contact d'une catégorie spécifique.
 * @param cat La catégorie d'où supprimer le contact.
 * @param nom Le nom du contact à supprimer.
 * @param prenom Le prénom du contact à supprimer.
 */
void supprimerContactDeCategorie(Categorie cat, const char* nom, const char* prenom);

/**
 * @brief Libère toute la mémoire allouée par toutes les listes du gestionnaire.
 */
void libererGestionnaire();

// --- Bonus : Modification de Contact ---
/**
 * @brief Modifie les informations d'un contact existant dans une catégorie spécifique.
 * @param cat La catégorie où rechercher le contact.
 * @param nomAncien Le nom actuel du contact à modifier.
 * @param prenomAncien Le prénom actuel du contact à modifier.
 * @param nouveauContact La nouvelle structure Contact avec les informations mises à jour.
 * @return true si le contact a été trouvé et modifié, false sinon.
 */
bool modifierContact(Categorie cat, const char* nomAncien, const char* prenomAncien, Contact nouveauContact);

#endif // MANAGER_H
```


## Fichier `manager.c`


```c
#include "manager.h"
#include "list.h" // Nécessaire pour utiliser les fonctions de manipulation de liste
#include <stdio.h>

// Définition (allocation) du gestionnaire de catégories
Node* gestionnaireCategories[NB_CATEGORIES];

// Implémentation d'initialiserGestionnaire
void initialiserGestionnaire() {
    for (int i = 0; i < NB_CATEGORIES; i++) {
        gestionnaireCategories[i] = NULL;
    }
    printf("Gestionnaire de contacts initialisé.\n");
}

// Implémentation d'ajouterContactAGestionnaire (utilise ajouterContactEnQueue)
void ajouterContactAGestionnaire(Categorie cat, Contact c) {
    if (cat < 0 || cat >= NB_CATEGORIES) {
        fprintf(stderr, "Erreur: Catégorie invalide (%d) pour l'ajout de contact.\n", cat);
        return;
    }
    // Utilise ajouterContactEnQueue pour varier l'insertion
    gestionnaireCategories[cat] = ajouterContactEnQueue(gestionnaireCategories[cat], c);
    if (gestionnaireCategories[cat] == NULL) {
        fprintf(stderr, "Échec d'ajout du contact %s %s à la catégorie %s (erreur mémoire).\n",
                c.nom, c.prenom, get_categorie_name(cat));
    } else {
        printf("Contact '%s %s' ajouté à la catégorie %s.\n",
               c.nom, c.prenom, get_categorie_name(cat));
    }
}

// Implémentation d'afficherCategorie
void afficherCategorie(Categorie cat) {
    if (cat < 0 || cat >= NB_CATEGORIES) {
        fprintf(stderr, "Erreur: Catégorie invalide (%d) pour l'affichage.\n", cat);
        return;
    }
    printf("\n--- Contacts de la catégorie %s ---\n", get_categorie_name(cat));
    afficherListe(gestionnaireCategories[cat]);
}

// Implémentation de supprimerContactDeCategorie
void supprimerContactDeCategorie(Categorie cat, const char* nom, const char* prenom) {
    if (cat < 0 || cat >= NB_CATEGORIES) {
        fprintf(stderr, "Erreur: Catégorie invalide (%d) pour la suppression de contact.\n", cat);
        return;
    }
    printf("Tentative de suppression de '%s %s' de la catégorie %s...\n",
           nom, prenom, get_categorie_name(cat));
    gestionnaireCategories[cat] = supprimerContact(gestionnaireCategories[cat], nom, prenom);
}

// Implémentation de libererGestionnaire
void libererGestionnaire() {
    printf("\n--- Libération de toute la mémoire du gestionnaire ---\n");
    for (int i = 0; i < NB_CATEGORIES; i++) {
        printf("  Libération de la catégorie %s...\n", get_categorie_name((Categorie)i));
        libererListe(gestionnaireCategories[i]);
        gestionnaireCategories[i] = NULL;
    }
    printf("Toute la mémoire a été libérée.\n");
}

// --- Bonus : Implémentation de modifierContact ---
bool modifierContact(Categorie cat, const char* nomAncien, const char* prenomAncien, Contact nouveauContact) {
    if (cat < 0 || cat >= NB_CATEGORIES) {
        fprintf(stderr, "Erreur: Catégorie invalide (%d) pour la modification de contact.\n", cat);
        return false;
    }
    printf("Tentative de modification de '%s %s' dans la catégorie %s...\n",
           nomAncien, prenomAncien, get_categorie_name(cat));
    return modifierContactDansListe(gestionnaireCategories[cat], nomAncien, prenomAncien, nouveauContact);
}
```


## Fichier `main.c`


```c
#include <stdio.h>
#include <string.h> // Pour strncpy si besoin de créer des contacts directement

#include "contact.h"  // Inclut les définitions de Contact et Categorie
#include "manager.h"  // Inclut les déclarations des fonctions du gestionnaire

int main() {
    initialiserGestionnaire();

    // Création de quelques contacts
    Contact c1 = {"Smith", "Alice", "0611111111"};
    Contact c2 = {"Jones", "Bob", "0722222222"};
    Contact c3 = {"Williams", "Charlie", "0133333333"};
    Contact c4 = {"Brown", "Diana", "0644444444"};
    Contact c5 = {"Miller", "Eve", "0755555555"};

    // Ajout de contacts dans différentes catégories (utilise ajouterContactEnQueue via manager)
    printf("\n--- Ajout de contacts dans le gestionnaire ---\n");
    ajouterContactAGestionnaire(AMIS, c1);
    ajouterContactAGestionnaire(FAMILLE, c2);
    ajouterContactAGestionnaire(TRAVAIL, c3);
    ajouterContactAGestionnaire(AMIS, c4);
    ajouterContactAGestionnaire(AUTRE, c5);

    // Affichage de catégories spécifiques
    printf("\n--- Affichage des catégories ---\n");
    afficherCategorie(AMIS);
    afficherCategorie(FAMILLE);
    afficherCategorie(TRAVAIL);
    afficherCategorie(AUTRE);

    // Test de modification de contact (Bonus)
    printf("\n--- Modification de contacts ---\n");
    Contact c1_modifie = {"Smith", "Alicia", "0699999999"};
    modifierContact(AMIS, "Smith", "Alice", c1_modifie); // Modifie c1
    modifierContact(FAMILLE, "NonExistant", "Personne", c1_modifie); // Tentative de modification d'un contact non existant

    // Affichage des catégories après modification
    printf("\n--- Affichage des catégories après modification ---\n");
    afficherCategorie(AMIS);
    afficherCategorie(FAMILLE);

    // Test de suppression de contacts
    printf("\n--- Suppression de contacts ---\n");
    supprimerContactDeCategorie(AMIS, "Smith", "Alicia"); // Supprime le contact modifié
    supprimerContactDeCategorie(TRAVAIL, "Williams", "Charlie"); // Supprime c3
    supprimerContactDeCategorie(AUTRE, "NonExistant", "Contact"); // Tentative de suppression d'un contact non existant

    // Affichage des catégories après suppression
    printf("\n--- Affichage des catégories après suppression ---\n");
    afficherCategorie(AMIS);
    afficherCategorie(FAMILLE);
    afficherCategorie(TRAVAIL);
    afficherCategorie(AUTRE);

    // Libération de toute la mémoire à la fin du programme
    libererGestionnaire();

    return 0;
}
```


## Compilation

Pour compiler cette solution, utilisez une commande similaire à celle-ci :
`gcc -Wall -Wextra -std=c11 contact.c list.c manager.c main.c -o contact_manager_s2`

## Explications des Concepts Clés (Solution 2)

*   **Structures de données (`Contact`, `Node`) et Énumération (`Categorie`)** : Identiques à la Solution 1, elles fournissent la base pour organiser les informations et les listes.
*   **Allocation dynamique (`malloc`, `free`) et Gestion des erreurs** : Le même principe de gestion robuste de la mémoire est appliqué, avec des vérifications de `malloc` et des retours `NULL` en cas d'échec.
*   **`strncpy`** : Toujours utilisé pour des copies de chaînes sécurisées.
*   **`ajouterContactEnQueue` (Nouveau)** : Cette fonction ajoute un nouveau nœud à la fin de la liste. Contrairement à `ajouterContactEnTete`, elle nécessite de parcourir la liste jusqu'au dernier élément, ce qui est moins performant pour de très longues listes mais peut être souhaitable pour maintenir un ordre d'insertion.
*   **Tableau de pointeurs (`gestionnaireCategories`)** : Le mécanisme central reste le même : un tableau où chaque élément pointe vers la tête d'une liste chaînée de contacts pour une catégorie donnée.
*   **Modularité** : La structure en fichiers séparés (`.h` et `.c`) est maintenue pour une organisation claire du code.
*   **Libération complète de la mémoire** : Les fonctions `libererListe` et `libererGestionnaire` garantissent une désallocation propre de toute la mémoire allouée dynamiquement.
*   **Bonus : Modification de Contact (`modifierContactDansListe`, `modifierContact`)** :
    *   `modifierContactDansListe` parcourt une liste spécifique pour trouver un contact par son ancien nom et prénom. Si trouvé, elle met à jour les champs `nom`, `prenom` et `telephone` avec les nouvelles informations.
    *   `modifierContact` agit comme un wrapper pour le gestionnaire, dirigeant l'appel vers la bonne liste chaînée via le tableau de catégories. Cette fonctionnalité est essentielle pour un gestionnaire de données complet.
*   **`bool` (via `<stdbool.h>`)** : Utilisé pour les fonctions de modification afin de retourner un indicateur clair de succès ou d'échec, améliorant la lisibilité et la gestion des conditions.
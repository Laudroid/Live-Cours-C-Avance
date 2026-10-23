Voici deux solutions complètes pour le TP "Implémentation d'un Arbre Binaire de Recherche (ABR) Simple", chacune présentée dans un chapitre Markdown distinct. Chaque solution est contenue dans un seul fichier `.c`, comme demandé, avec des commentaires détaillés et des explications.

---

# Solution 1 : Implémentation Récursive Complète

Cette solution propose une implémentation entièrement récursive des opérations de l'ABR, y compris les fonctions bonus de suppression, de libération et de calcul de hauteur. L'accent est mis sur la clarté de la logique récursive.

## Fichier `abr_solution1.c`


```c
#include <stdio.h>  // Pour printf, scanf
#include <stdlib.h> // Pour malloc, free, EXIT_SUCCESS, EXIT_FAILURE

// --- Partie 1: La Structure d'un Nœud d'ABR ---

/**
 * @brief Définition de la structure d'un nœud d'Arbre Binaire de Recherche (ABR).
 */
typedef struct NoeudABR {
    int valeur;             // La valeur entière stockée dans le nœud
    struct NoeudABR* gauche;  // Pointeur vers le sous-arbre gauche
    struct NoeudABR* droit;   // Pointeur vers le sous-arbre droit
} NoeudABR;

// --- Partie 2: Les Opérations Fondamentales de l'ABR ---

/**
 * @brief Crée et initialise un nouveau nœud ABR.
 * @param valeur La valeur à stocker dans le nouveau nœud.
 * @return Un pointeur vers le nouveau nœud, ou NULL en cas d'échec d'allocation.
 */
NoeudABR* creerNoeud(int valeur) {
    NoeudABR* nouveauNoeud = (NoeudABR*)malloc(sizeof(NoeudABR));
    if (nouveauNoeud == NULL) {
        fprintf(stderr, "Erreur: Échec d'allocation mémoire pour un nouveau nœud.\n");
        return NULL;
    }
    nouveauNoeud->valeur = valeur;
    nouveauNoeud->gauche = NULL;
    nouveauNoeud->droit = NULL;
    return nouveauNoeud;
}

/**
 * @brief Insère une nouvelle valeur dans l'ABR de manière récursive.
 *        Si la valeur existe déjà, elle n'est pas insérée.
 * @param racine La racine actuelle du sous-arbre.
 * @param valeur La valeur à insérer.
 * @return La nouvelle racine du sous-arbre après insertion.
 */
NoeudABR* inserer(NoeudABR* racine, int valeur) {
    // Cas de base: l'arbre est vide, créer un nouveau nœud et le retourner comme racine.
    if (racine == NULL) {
        return creerNoeud(valeur);
    }

    // Si la valeur est inférieure, insérer dans le sous-arbre gauche.
    if (valeur < racine->valeur) {
        racine->gauche = inserer(racine->gauche, valeur);
    }
    // Si la valeur est supérieure, insérer dans le sous-arbre droit.
    else if (valeur > racine->valeur) {
        racine->droit = inserer(racine->droit, valeur);
    }
    // Si la valeur est égale, elle existe déjà, ne rien faire.
    else {
        printf("La valeur %d existe déjà dans l'arbre.\n", valeur);
    }

    // Retourner la racine (inchangée ou modifiée si un enfant a été ajouté).
    return racine;
}

/**
 * @brief Recherche une valeur spécifique dans l'ABR de manière récursive.
 * @param racine La racine actuelle du sous-arbre.
 * @param valeur La valeur à rechercher.
 * @return Un pointeur vers le nœud contenant la valeur si trouvée, ou NULL sinon.
 */
NoeudABR* rechercher(NoeudABR* racine, int valeur) {
    // Cas de base: l'arbre est vide ou la valeur est trouvée à la racine.
    if (racine == NULL || racine->valeur == valeur) {
        return racine;
    }

    // Si la valeur est inférieure, rechercher dans le sous-arbre gauche.
    if (valeur < racine->valeur) {
        return rechercher(racine->gauche, valeur);
    }
    // Si la valeur est supérieure, rechercher dans le sous-arbre droit.
    else {
        return rechercher(racine->droit, valeur);
    }
}

/**
 * @brief Parcourt l'ABR en ordre infixe (gauche, racine, droite) et affiche les valeurs.
 *        Affiche les éléments dans l'ordre croissant.
 * @param racine La racine actuelle du sous-arbre.
 */
void afficherInfixe(NoeudABR* racine) {
    if (racine != NULL) {
        afficherInfixe(racine->gauche);  // Parcourir le sous-arbre gauche
        printf("%d ", racine->valeur);   // Afficher la valeur du nœud courant
        afficherInfixe(racine->droit);   // Parcourir le sous-arbre droit
    }
}

// --- Partie 4: Pour aller plus loin (Défis Optionnels) ---

/**
 * @brief Trouve le nœud avec la valeur minimale dans un sous-arbre donné.
 * @param racine La racine du sous-arbre.
 * @return Un pointeur vers le nœud contenant la valeur minimale.
 */
NoeudABR* trouverMin(NoeudABR* racine) {
    if (racine == NULL) {
        return NULL;
    }
    // Le minimum est le nœud le plus à gauche.
    while (racine->gauche != NULL) {
        racine = racine->gauche;
    }
    return racine;
}

/**
 * @brief Supprime une valeur de l'ABR de manière récursive.
 * @param racine La racine actuelle du sous-arbre.
 * @param valeur La valeur à supprimer.
 * @return La nouvelle racine du sous-arbre après suppression.
 */
NoeudABR* supprimer(NoeudABR* racine, int valeur) {
    // Cas de base: l'arbre est vide ou la valeur n'est pas trouvée.
    if (racine == NULL) {
        return NULL;
    }

    // Parcourir l'arbre pour trouver le nœud à supprimer.
    if (valeur < racine->valeur) {
        racine->gauche = supprimer(racine->gauche, valeur);
    } else if (valeur > racine->valeur) {
        racine->droit = supprimer(racine->droit, valeur);
    } else { // La valeur est trouvée (racine est le nœud à supprimer).
        // Cas 1: Nœud sans enfant ou avec un seul enfant.
        if (racine->gauche == NULL) {
            NoeudABR* temp = racine->droit;
            free(racine);
            return temp;
        } else if (racine->droit == NULL) {
            NoeudABR* temp = racine->gauche;
            free(racine);
            return temp;
        }
        // Cas 2: Nœud avec deux enfants.
        // Trouver le successeur in-ordre (le plus petit nœud du sous-arbre droit).
        NoeudABR* temp = trouverMin(racine->droit);
        // Copier la valeur du successeur in-ordre dans ce nœud.
        racine->valeur = temp->valeur;
        // Supprimer le successeur in-ordre du sous-arbre droit.
        racine->droit = supprimer(racine->droit, temp->valeur);
    }
    return racine;
}

/**
 * @brief Libère toute la mémoire allouée pour l'arbre de manière post-ordre.
 * @param racine La racine de l'arbre à libérer.
 */
void libererArbre(NoeudABR* racine) {
    if (racine != NULL) {
        libererArbre(racine->gauche);  // Libérer le sous-arbre gauche
        libererArbre(racine->droit);   // Libérer le sous-arbre droit
        free(racine);                  // Libérer le nœud courant
    }
}

/**
 * @brief Calcule la hauteur de l'arbre de manière récursive.
 *        La hauteur d'un arbre vide est -1. La hauteur d'un nœud est 0.
 * @param racine La racine de l'arbre.
 * @return La hauteur de l'arbre.
 */
int hauteur(NoeudABR* racine) {
    if (racine == NULL) {
        return -1; // Hauteur d'un arbre vide
    } else {
        // Calculer la hauteur des sous-arbres gauche et droit
        int hauteurGauche = hauteur(racine->gauche);
        int hauteurDroit = hauteur(racine->droit);

        // La hauteur de l'arbre est 1 + la hauteur maximale des sous-arbres
        return (hauteurGauche > hauteurDroit ? hauteurGauche : hauteurDroit) + 1;
    }
}

// --- Partie 3: Mini-Projet : Une Application de Gestion d'Entiers ---

int main() {
    NoeudABR* racine = NULL; // Initialisation d'un arbre vide
    int choix, valeur;
    NoeudABR* resultatRecherche;

    printf("--- Gestionnaire d'Entiers avec Arbre Binaire de Recherche ---\n");

    do {
        printf("\nMenu:\n");
        printf("1. Insérer une valeur\n");
        printf("2. Rechercher une valeur\n");
        printf("3. Afficher l'arbre (infixe)\n");
        printf("4. Supprimer une valeur\n"); // Option bonus
        printf("5. Afficher la hauteur de l'arbre\n"); // Option bonus
        printf("6. Quitter\n");
        printf("Entrez votre choix: ");
        if (scanf("%d", &choix) != 1) {
            printf("Entrée invalide. Veuillez entrer un nombre.\n");
            // Nettoyer le buffer d'entrée pour éviter une boucle infinie
            while (getchar() != '\n');
            continue;
        }

        switch (choix) {
            case 1:
                printf("Entrez la valeur à insérer: ");
                if (scanf("%d", &valeur) == 1) {
                    racine = inserer(racine, valeur);
                    if (racine != NULL) { // Vérifier si l'insertion a réussi (pas d'échec malloc)
                        printf("Valeur %d insérée.\n", valeur);
                    }
                } else {
                    printf("Entrée invalide.\n");
                    while (getchar() != '\n');
                }
                break;
            case 2:
                printf("Entrez la valeur à rechercher: ");
                if (scanf("%d", &valeur) == 1) {
                    resultatRecherche = rechercher(racine, valeur);
                    if (resultatRecherche != NULL) {
                        printf("Valeur %d trouvée dans l'arbre.\n", valeur);
                    } else {
                        printf("Valeur %d non trouvée dans l'arbre.\n", valeur);
                    }
                } else {
                    printf("Entrée invalide.\n");
                    while (getchar() != '\n');
                }
                break;
            case 3:
                printf("Contenu de l'arbre (ordre infixe): ");
                if (racine == NULL) {
                    printf("L'arbre est vide.\n");
                } else {
                    afficherInfixe(racine);
                    printf("\n");
                }
                break;
            case 4: // Option bonus: Supprimer
                printf("Entrez la valeur à supprimer: ");
                if (scanf("%d", &valeur) == 1) {
                    NoeudABR* ancienRacine = racine;
                    racine = supprimer(racine, valeur);
                    if (ancienRacine != racine || rechercher(racine, valeur) == NULL) { // Vérifier si la suppression a eu lieu
                         printf("Valeur %d supprimée (si elle existait).\n", valeur);
                    } else {
                         printf("Valeur %d non trouvée ou suppression échouée.\n", valeur);
                    }
                } else {
                    printf("Entrée invalide.\n");
                    while (getchar() != '\n');
                }
                break;
            case 5: // Option bonus: Hauteur
                printf("Hauteur de l'arbre: %d\n", hauteur(racine));
                break;
            case 6:
                printf("Quitter le programme.\n");
                break;
            default:
                printf("Choix invalide. Veuillez réessayer.\n");
                break;
        }
    } while (choix != 6);

    // Libération de toute la mémoire avant de quitter
    printf("\nLibération de la mémoire de l'arbre...\n");
    libererArbre(racine);
    printf("Mémoire libérée. Au revoir !\n");

    return EXIT_SUCCESS;
}
```


## Explications des Concepts Clés (Solution 1)

*   **`struct NoeudABR` et `typedef`**: La structure définit la composition de chaque nœud : une `valeur` entière et deux pointeurs (`gauche`, `droit`) vers d'autres nœuds. Le `typedef` simplifie l'écriture de `struct NoeudABR` en `NoeudABR`.
*   **`creerNoeud(int valeur)`**: Cette fonction est la "fabrique" de nœuds. Elle alloue dynamiquement de la mémoire pour un nouveau nœud, initialise sa valeur et s'assure que ses enfants sont `NULL` (un nœud fraîchement créé est toujours une feuille). La vérification de `malloc` est cruciale pour la robustesse.
*   **`inserer(NoeudABR* racine, int valeur)` (Récursif)**:
    *   Le cas de base pour la récursion est lorsque la fonction atteint un pointeur `NULL`, signifiant qu'elle a trouvé l'emplacement où insérer le nouveau nœud.
    *   La logique de l'ABR est appliquée : si la valeur est plus petite, on va à gauche ; si elle est plus grande, on va à droite.
    *   La fonction retourne toujours la racine du sous-arbre (qui peut être un nouveau nœud ou le même nœud si aucun changement n'a eu lieu), ce qui permet de "reconnecter" les sous-arbres après une insertion.
*   **`rechercher(NoeudABR* racine, int valeur)` (Récursif)**:
    *   Le cas de base est soit la valeur trouvée à la racine actuelle, soit la fin d'un chemin (`NULL`) sans avoir trouvé la valeur.
    *   La recherche est dirigée vers la gauche ou la droite en fonction de la comparaison avec la valeur du nœud courant, tirant parti de la propriété de l'ABR.
*   **`afficherInfixe(NoeudABR* racine)` (Récursif)**:
    *   Ce parcours est fondamental pour les ABR car il affiche les éléments dans l'ordre croissant.
    *   La séquence est : parcourir le sous-arbre `gauche`, visiter le nœud courant (afficher sa valeur), puis parcourir le sous-arbre `droit`.
*   **`trouverMin(NoeudABR* racine)` (Itératif)**: Cette fonction trouve le nœud le plus à gauche dans un sous-arbre, qui est par définition le nœud avec la plus petite valeur. Elle est implémentée de manière itérative pour varier, mais une version récursive est également possible.
*   **`supprimer(NoeudABR* racine, int valeur)` (Récursif)**: C'est l'opération la plus complexe.
    *   Elle localise d'abord le nœud à supprimer.
    *   **Cas 1 (0 ou 1 enfant)**: Le nœud est simplement remplacé par son unique enfant (ou `NULL` s'il n'en a pas), et le nœud supprimé est libéré.
    *   **Cas 2 (2 enfants)**: Pour maintenir la propriété de l'ABR, le nœud supprimé est remplacé par son *successeur in-ordre* (le plus petit nœud de son sous-arbre droit). La valeur du successeur est copiée dans le nœud à supprimer, puis le successeur est supprimé de son emplacement d'origine (ce qui retombe dans le Cas 1 ou 2).
*   **`libererArbre(NoeudABR* racine)` (Récursif - Post-ordre)**: Pour éviter les fuites de mémoire, tous les nœuds alloués doivent être libérés. Un parcours post-ordre est idéal : on libère d'abord les enfants, puis le nœud parent.
*   **`hauteur(NoeudABR* racine)` (Récursif)**: Calcule la hauteur de l'arbre en trouvant la longueur du plus long chemin de la racine à une feuille. La hauteur d'un arbre vide est définie comme -1.
*   **`main`**: Le programme principal fournit une interface utilisateur interactive pour tester toutes les fonctions de l'ABR, y compris les options bonus. Il gère les entrées utilisateur et assure la libération finale de la mémoire.

---

# Solution 2 : Implémentation Récursive avec Prédécesseur In-ordre pour Suppression

Cette solution est similaire à la première, mais elle utilise le *prédécesseur in-ordre* (le plus grand nœud du sous-arbre gauche) pour la suppression des nœuds ayant deux enfants, offrant une perspective alternative à l'algorithme de suppression.

## Fichier `abr_solution2.c`


```c
#include <stdio.h>  // Pour printf, scanf
#include <stdlib.h> // Pour malloc, free, EXIT_SUCCESS, EXIT_FAILURE

// --- Partie 1: La Structure d'un Nœud d'ABR ---

/**
 * @brief Définition de la structure d'un nœud d'Arbre Binaire de Recherche (ABR).
 */
typedef struct NoeudABR {
    int cle;                // La clé (valeur) entière stockée dans le nœud
    struct NoeudABR* enfantGauche; // Pointeur vers le sous-arbre gauche
    struct NoeudABR* enfantDroit;  // Pointeur vers le sous-arbre droit
} NoeudABR;

// --- Partie 2: Les Opérations Fondamentales de l'ABR ---

/**
 * @brief Crée et initialise un nouveau nœud ABR.
 * @param cle La clé à stocker dans le nouveau nœud.
 * @return Un pointeur vers le nouveau nœud, ou NULL en cas d'échec d'allocation.
 */
NoeudABR* creerNouveauNoeud(int cle) {
    NoeudABR* nouveau = (NoeudABR*)malloc(sizeof(NoeudABR));
    if (nouveau == NULL) {
        fprintf(stderr, "Erreur: Échec d'allocation mémoire pour un nouveau nœud.\n");
        return NULL;
    }
    nouveau->cle = cle;
    nouveau->enfantGauche = NULL;
    nouveau->enfantDroit = NULL;
    return nouveau;
}

/**
 * @brief Insère une nouvelle clé dans l'ABR de manière récursive.
 *        Si la clé existe déjà, elle n'est pas insérée.
 * @param racine L'actuelle racine du sous-arbre.
 * @param cle La clé à insérer.
 * @return La nouvelle racine du sous-arbre après insertion.
 */
NoeudABR* insererCle(NoeudABR* racine, int cle) {
    if (racine == NULL) {
        return creerNouveauNoeud(cle);
    }

    if (cle < racine->cle) {
        racine->enfantGauche = insererCle(racine->enfantGauche, cle);
    } else if (cle > racine->cle) {
        racine->enfantDroit = insererCle(racine->enfantDroit, cle);
    } else {
        printf("La clé %d existe déjà dans l'arbre.\n", cle);
    }
    return racine;
}

/**
 * @brief Recherche une clé spécifique dans l'ABR de manière récursive.
 * @param racine L'actuelle racine du sous-arbre.
 * @param cle La clé à rechercher.
 * @return Un pointeur vers le nœud contenant la clé si trouvée, ou NULL sinon.
 */
NoeudABR* rechercherCle(NoeudABR* racine, int cle) {
    if (racine == NULL || racine->cle == cle) {
        return racine;
    }

    if (cle < racine->cle) {
        return rechercherCle(racine->enfantGauche, cle);
    } else {
        return rechercherCle(racine->enfantDroit, cle);
    }
}

/**
 * @brief Parcourt l'ABR en ordre infixe (gauche, racine, droite) et affiche les clés.
 *        Affiche les éléments dans l'ordre croissant.
 * @param racine L'actuelle racine du sous-arbre.
 */
void afficherOrdreInfixe(NoeudABR* racine) {
    if (racine != NULL) {
        afficherOrdreInfixe(racine->enfantGauche);
        printf("%d ", racine->cle);
        afficherOrdreInfixe(racine->enfantDroit);
    }
}

// --- Partie 4: Pour aller plus loin (Défis Optionnels) ---

/**
 * @brief Trouve le nœud avec la clé maximale dans un sous-arbre donné (prédécesseur in-ordre).
 * @param noeud Le nœud de départ du sous-arbre.
 * @return Un pointeur vers le nœud contenant la clé maximale.
 */
NoeudABR* trouverMax(NoeudABR* noeud) {
    if (noeud == NULL) {
        return NULL;
    }
    // Le maximum est le nœud le plus à droite.
    while (noeud->enfantDroit != NULL) {
        noeud = noeud->enfantDroit;
    }
    return noeud;
}

/**
 * @brief Supprime une clé de l'ABR de manière récursive.
 *        Utilise le prédécesseur in-ordre pour les nœuds à deux enfants.
 * @param racine L'actuelle racine du sous-arbre.
 * @param cle La clé à supprimer.
 * @return La nouvelle racine du sous-arbre après suppression.
 */
NoeudABR* supprimerCle(NoeudABR* racine, int cle) {
    if (racine == NULL) {
        return NULL;
    }

    if (cle < racine->cle) {
        racine->enfantGauche = supprimerCle(racine->enfantGauche, cle);
    } else if (cle > racine->cle) {
        racine->enfantDroit = supprimerCle(racine->enfantDroit, cle);
    } else { // La clé est trouvée (racine est le nœud à supprimer).
        // Cas 1: Nœud sans enfant ou avec un seul enfant.
        if (racine->enfantGauche == NULL) {
            NoeudABR* temp = racine->enfantDroit;
            free(racine);
            return temp;
        } else if (racine->enfantDroit == NULL) {
            NoeudABR* temp = racine->enfantGauche;
            free(racine);
            return temp;
        }
        // Cas 2: Nœud avec deux enfants.
        // Trouver le prédécesseur in-ordre (le plus grand nœud du sous-arbre gauche).
        NoeudABR* temp = trouverMax(racine->enfantGauche);
        // Copier la clé du prédécesseur in-ordre dans ce nœud.
        racine->cle = temp->cle;
        // Supprimer le prédécesseur in-ordre du sous-arbre gauche.
        racine->enfantGauche = supprimerCle(racine->enfantGauche, temp->cle);
    }
    return racine;
}

/**
 * @brief Libère toute la mémoire allouée pour l'arbre de manière post-ordre.
 * @param racine La racine de l'arbre à libérer.
 */
void libererToutArbre(NoeudABR* racine) {
    if (racine != NULL) {
        libererToutArbre(racine->enfantGauche);
        libererToutArbre(racine->enfantDroit);
        free(racine);
    }
}

/**
 * @brief Calcule la hauteur de l'arbre de manière récursive.
 *        La hauteur d'un arbre vide est -1.
 * @param racine La racine de l'arbre.
 * @return La hauteur de l'arbre.
 */
int calculerHauteur(NoeudABR* racine) {
    if (racine == NULL) {
        return -1;
    } else {
        int hauteurGauche = calculerHauteur(racine->enfantGauche);
        int hauteurDroit = calculerHauteur(racine->enfantDroit);
        return (hauteurGauche > hauteurDroit ? hauteurGauche : hauteurDroit) + 1;
    }
}

// --- Partie 3: Mini-Projet : Une Application de Gestion d'Entiers ---

int main() {
    NoeudABR* racineArbre = NULL; // Initialisation d'un arbre vide
    int choixUtilisateur, valeurEntree;
    NoeudABR* resultatRecherche;

    printf("--- Gestionnaire de Clés avec Arbre Binaire de Recherche (Solution 2) ---\n");

    do {
        printf("\nOptions:\n");
        printf("1. Insérer une clé\n");
        printf("2. Rechercher une clé\n");
        printf("3. Afficher l'arbre (ordre infixe)\n");
        printf("4. Supprimer une clé\n");
        printf("5. Calculer la hauteur de l'arbre\n");
        printf("6. Quitter l'application\n");
        printf("Votre choix: ");
        if (scanf("%d", &choixUtilisateur) != 1) {
            printf("Saisie invalide. Veuillez entrer un nombre.\n");
            while (getchar() != '\n'); // Nettoyer le buffer
            continue;
        }

        switch (choixUtilisateur) {
            case 1:
                printf("Entrez la clé à insérer: ");
                if (scanf("%d", &valeurEntree) == 1) {
                    racineArbre = insererCle(racineArbre, valeurEntree);
                    if (racineArbre != NULL) {
                        printf("Clé %d insérée avec succès.\n", valeurEntree);
                    }
                } else {
                    printf("Saisie invalide.\n");
                    while (getchar() != '\n');
                }
                break;
            case 2:
                printf("Entrez la clé à rechercher: ");
                if (scanf("%d", &valeurEntree) == 1) {
                    resultatRecherche = rechercherCle(racineArbre, valeurEntree);
                    if (resultatRecherche != NULL) {
                        printf("Clé %d trouvée dans l'arbre.\n", valeurEntree);
                    } else {
                        printf("Clé %d non trouvée.\n", valeurEntree);
                    }
                } else {
                    printf("Saisie invalide.\n");
                    while (getchar() != '\n');
                }
                break;
            case 3:
                printf("Contenu de l'arbre (ordre croissant): ");
                if (racineArbre == NULL) {
                    printf("L'arbre est vide.\n");
                } else {
                    afficherOrdreInfixe(racineArbre);
                    printf("\n");
                }
                break;
            case 4:
                printf("Entrez la clé à supprimer: ");
                if (scanf("%d", &valeurEntree) == 1) {
                    NoeudABR* ancienneRacine = racineArbre;
                    racineArbre = supprimerCle(racineArbre, valeurEntree);
                    if (ancienneRacine != racineArbre || rechercherCle(racineArbre, valeurEntree) == NULL) {
                         printf("Clé %d supprimée (si elle existait).\n", valeurEntree);
                    } else {
                         printf("Clé %d non trouvée ou suppression échouée.\n", valeurEntree);
                    }
                } else {
                    printf("Saisie invalide.\n");
                    while (getchar() != '\n');
                }
                break;
            case 5:
                printf("Hauteur de l'arbre: %d\n", calculerHauteur(racineArbre));
                break;
            case 6:
                printf("Fermeture de l'application.\n");
                break;
            default:
                printf("Choix non reconnu. Réessayez.\n");
                break;
        }
    } while (choixUtilisateur != 6);

    printf("\nLibération de la mémoire de l'arbre...\n");
    libererToutArbre(racineArbre);
    printf("Mémoire libérée. À bientôt !\n");

    return EXIT_SUCCESS;
}
```


## Explications des Concepts Clés (Solution 2)

Cette solution partage la même structure et les mêmes principes fondamentaux que la Solution 1, mais avec quelques variations pour illustrer des alternatives courantes.

*   **`struct NoeudABR` et `typedef`**: Les noms des champs ont été légèrement modifiés (`cle`, `enfantGauche`, `enfantDroit`) pour varier, mais la structure logique reste identique.
*   **`creerNouveauNoeud(int cle)`**: Fonctionnellement identique à `creerNoeud`, avec des noms de variables différents. Elle alloue un nouveau nœud et l'initialise.
*   **`insererCle(NoeudABR* racine, int cle)` (Récursif)**: La logique d'insertion est la même que dans la Solution 1, respectant la propriété de l'ABR.
*   **`rechercherCle(NoeudABR* racine, int cle)` (Récursif)**: La logique de recherche est identique, exploitant la structure ordonnée de l'ABR.
*   **`afficherOrdreInfixe(NoeudABR* racine)` (Récursif)**: Le parcours infixe est le même, garantissant un affichage des clés dans l'ordre croissant.
*   **`trouverMax(NoeudABR* noeud)` (Itératif)**: Cette fonction est l'équivalent de `trouverMin` mais pour le côté droit. Elle trouve le nœud avec la plus grande clé dans un sous-arbre, qui est le nœud le plus à droite. Elle est utilisée pour la suppression avec deux enfants.
*   **`supprimerCle(NoeudABR* racine, int cle)` (Récursif)**: C'est la principale différence avec la Solution 1.
    *   Au lieu d'utiliser le *successeur in-ordre* (minimum du sous-arbre droit) pour remplacer un nœud à deux enfants, cette implémentation utilise le *prédécesseur in-ordre* (maximum du sous-arbre gauche).
    *   La valeur du prédécesseur est copiée dans le nœud à supprimer, puis le prédécesseur est supprimé de son emplacement d'origine dans le sous-arbre gauche. Les trois cas de suppression (0, 1 ou 2 enfants) sont gérés de manière similaire.
*   **`libererToutArbre(NoeudABR* racine)` (Récursif - Post-ordre)**: La logique de libération de la mémoire est identique à la Solution 1, utilisant un parcours post-ordre pour s'assurer que les enfants sont libérés avant leur parent.
*   **`calculerHauteur(NoeudABR* racine)` (Récursif)**: Le calcul de la hauteur est le même, déterminant la longueur du plus long chemin de la racine à une feuille.
*   **`main`**: Le programme principal offre une interface utilisateur similaire, testant toutes les fonctionnalités de l'ABR, y compris les options bonus, et assure la libération finale de la mémoire. Les messages ont été légèrement reformulés pour varier.

---
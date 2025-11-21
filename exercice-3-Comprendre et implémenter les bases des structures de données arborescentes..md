Bonjour à toutes et à tous,

Ce TP est conçu pour vous guider dans l'implémentation d'une structure de données fondamentale : l'arbre binaire de recherche. C'est un excellent point de départ pour appréhender les concepts liés aux arbres.

---

### TP: Implémentation d'un Arbre Binaire de Recherche (ABR) Simple

**Objectif du TP :**
Ce TP vise à vous familiariser avec les structures de données arborescentes, en particulier l'Arbre Binaire de Recherche (ABR), et à implémenter ses opérations fondamentales en langage C.

**Contexte & Prérequis :**
Une bonne maîtrise des pointeurs, des structures (`struct`), et des concepts de récursion en C est nécessaire pour aborder ce TP.

**Introduction :**
Les arbres binaires de recherche sont des structures de données fondamentales pour organiser des données de manière à permettre des recherches, insertions et suppressions efficaces. Leur principe est simple : pour chaque nœud, toutes les valeurs de son sous-arbre gauche sont inférieures à sa propre valeur, et toutes les valeurs de son sous-arbre droit lui sont supérieures.

---

#### Partie 1: La Structure d'un Nœud d'ABR

Définissez la structure `NoeudABR` qui représentera un nœud de votre arbre. Chaque nœud devra contenir :
*   Une valeur entière (`int valeur`).
*   Un pointeur vers son enfant gauche (`struct NoeudABR* gauche`).
*   Un pointeur vers son enfant droit (`struct NoeudABR* droit`).

N'oubliez pas le `typedef` pour simplifier l'écriture (ex: `typedef struct NoeudABR NoeudABR;`).

---

#### Partie 2: Les Opérations Fondamentales de l'ABR

Implémentez les fonctions suivantes :

**2.1 `NoeudABR* creerNoeud(int valeur)`**
*   Crée et initialise un nouveau nœud avec la valeur donnée. Ses pointeurs `gauche` et `droit` devront être initialisés à `NULL`. N'oubliez pas l'allocation mémoire (`malloc`).

**2.2 `NoeudABR* inserer(NoeudABR* racine, int valeur)`**
*   Insère une nouvelle valeur dans l'arbre. Cette fonction doit être récursive.
*   Si l'arbre est vide (`racine == NULL`), le nouveau nœud devient la racine.
*   Sinon, la valeur est insérée récursivement dans le sous-arbre gauche ou droit selon sa comparaison avec la valeur du nœud courant.
*   La fonction doit retourner la nouvelle racine de l'arbre (utile pour l'insertion initiale ou si la racine change).

**2.3 `NoeudABR* rechercher(NoeudABR* racine, int valeur)`**
*   Recherche une valeur spécifique dans l'arbre.
*   La fonction doit retourner un pointeur vers le nœud contenant la valeur si elle est trouvée, ou `NULL` sinon.
*   Implémentez-la de manière récursive.

**2.4 `void afficherInfixe(NoeudABR* racine)`**
*   Parcourt l'arbre en ordre infixe (gauche, racine, droite) et affiche les valeurs des nœuds.
*   Ce parcours permet d'afficher les éléments de l'ABR dans l'ordre croissant.

---

#### Partie 3: Mini-Projet : Une Application de Gestion d'Entiers

Développez un programme `main` qui utilise les fonctions implémentées ci-dessus pour simuler une petite application de gestion d'entiers.

Le programme devra :
*   Initialiser un arbre vide (un pointeur `NoeudABR*` à `NULL`).
*   Proposer un menu à l'utilisateur avec les options suivantes :
    1.  Insérer une valeur.
    2.  Rechercher une valeur.
    3.  Afficher l'arbre (parcours infixe).
    4.  Quitter.
*   Gérer les entrées utilisateur et appeler les fonctions appropriées.
*   Afficher des messages clairs à l'utilisateur (ex: "Valeur insérée.", "Valeur trouvée / non trouvée.").

Testez votre implémentation avec différentes séquences d'insertions et de recherches pour vérifier son bon fonctionnement.

---

#### Partie 4: Pour aller plus loin (Défis Optionnels)

Si vous avez terminé les parties précédentes et souhaitez approfondir, voici quelques défis :

**4.1 `NoeudABR* trouverMin(NoeudABR* racine)`**
*   Trouve le nœud avec la valeur minimale dans un sous-arbre donné. Cette fonction est souvent utile pour l'implémentation de la suppression.

**4.2 `NoeudABR* supprimer(NoeudABR* racine, int valeur)`**
*   Implémentez la suppression d'un nœud. C'est l'opération la plus complexe pour un ABR. Pensez aux trois cas principaux :
    *   Le nœud à supprimer n'a pas d'enfant.
    *   Le nœud à supprimer a un seul enfant.
    *   Le nœud à supprimer a deux enfants (nécessite de trouver le successeur in-ordre ou le prédécesseur in-ordre).
*   La fonction doit retourner la nouvelle racine du sous-arbre modifié.

**4.3 `void libererArbre(NoeudABR* racine)`**
*   Libère toute la mémoire allouée pour l'arbre afin d'éviter les fuites mémoire. Un parcours post-ordre est souvent approprié ici.

**4.4 `int hauteur(NoeudABR* racine)`**
*   Calcule la hauteur de l'arbre (la longueur du plus long chemin de la racine à une feuille).

---

#### Conseils pour l'utilisation de l'IA (ChatGPT, Copilot, etc.) :

L'utilisation d'outils d'IA est encouragée pour ce TP. Ils peuvent vous aider à :
*   Comprendre des concepts que vous trouvez flous.
*   Débugger votre code en identifiant des erreurs ou en proposant des corrections.
*   Générer des exemples de code pour des fonctions spécifiques (ex: `creerNoeud`).
*   Explorer des implémentations alternatives ou des optimisations.

Cependant, l'objectif reste votre *compréhension*. Ne vous contentez pas de copier-coller. Analysez le code généré, expliquez-le avec vos mots, et assurez-vous de pouvoir le reproduire ou le modifier. Utilisez l'IA comme un assistant intelligent, pas comme un substitut à votre apprentissage.

---

**Rendu :**
Un fichier `.c` contenant l'intégralité de votre code source. Assurez-vous que votre code est bien commenté et lisible.

Bon courage !
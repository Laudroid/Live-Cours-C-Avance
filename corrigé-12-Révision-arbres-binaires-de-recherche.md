
# Corrigé Exercice Arbre Binaire de Recherche (ABR)

## Structure d’un nœud

```id="abr1"
Structure Noeud
    valeur : entier
    gauche : ^Noeud
    droite : ^Noeud
FinStructure
```

* `gauche` → sous-arbre gauche
* `droite` → sous-arbre droit

---

## Algorithme – Insertion récursive

```id="abr2"
Fonction Inserer(racine : ^Noeud, valeur : entier) : ^Noeud
Début
    Si racine = NULL Alors
        Allouer nouveau
        nouveau.valeur ← valeur
        nouveau.gauche ← NULL
        nouveau.droite ← NULL
        Retourner nouveau
    Sinon
        Si valeur < racine.valeur Alors
            racine.gauche ← Inserer(racine.gauche, valeur)
        Sinon
            racine.droite ← Inserer(racine.droite, valeur)
        FinSi
        Retourner racine
    FinSi
FinFonction
```

---

## Algorithme – Recherche récursive

```id="abr3"
Fonction Rechercher(racine : ^Noeud, valeur : entier) : booléen
Début
    Si racine = NULL Alors
        Retourner Faux
    SinonSi racine.valeur = valeur Alors
        Retourner Vrai
    SinonSi valeur < racine.valeur Alors
        Retourner Rechercher(racine.gauche, valeur)
    Sinon
        Retourner Rechercher(racine.droite, valeur)
    FinSi
FinFonction
```

---

## Algorithme – Parcours infixe

```id="abr4"
Procédure ParcoursInfixe(racine : ^Noeud)
Début
    Si racine ≠ NULL Alors
        ParcoursInfixe(racine.gauche)
        Afficher racine.valeur
        ParcoursInfixe(racine.droite)
    FinSi
FinProcédure
```

---

## Explication pédagogique

1. **Insertion récursive** :

   * Cas de base → nœud vide (`NULL`)
   * Appel récursif → gauche ou droite selon la valeur

2. **Recherche** :

   * Même logique → comparer la valeur et descendre à gauche ou droite

3. **Parcours infixe** :

   * Affiche les valeurs **dans l’ordre croissant** pour un ABR

---

# Corrigé Exercice Arbre Équilibré

## Algorithme – Calcul de la hauteur

```id="eq1"
Fonction Hauteur(racine : ^Noeud) : entier
Début
    Si racine = NULL Alors
        Retourner 0
    Sinon
        gaucheHauteur ← Hauteur(racine.gauche)
        droiteHauteur ← Hauteur(racine.droite)
        Retourner 1 + Max(gaucheHauteur, droiteHauteur)
    FinSi
FinFonction
```

---

## Algorithme – Vérification d’équilibre

```id="eq2"
Fonction EstEquilibre(racine : ^Noeud) : booléen
Début
    Si racine = NULL Alors
        Retourner Vrai
    Sinon
        gaucheHauteur ← Hauteur(racine.gauche)
        droiteHauteur ← Hauteur(racine.droite)

        Si |gaucheHauteur - droiteHauteur| > 1 Alors
            Retourner Faux
        Sinon
            Retourner EstEquilibre(racine.gauche) ET EstEquilibre(racine.droite)
        FinSi
    FinSi
FinFonction
```

---

## Algorithme – Programme principal

```id="eq3"
Début
    racine ← NULL

    // Exemple d’insertion
    racine ← Inserer(racine, 10)
    racine ← Inserer(racine, 20)
    racine ← Inserer(racine, 5)

    Si EstEquilibre(racine) Alors
        Afficher "Arbre équilibré"
    Sinon
        Afficher "Arbre non équilibré"
    FinSi
Fin
```

---

## Explication pédagogique

1. **Hauteur d’un nœud** :

   * `1 + max(hauteur sous-arbre gauche, hauteur sous-arbre droite)`

2. **Équilibrage** :

   * Vérifie que la différence de hauteur ≤ 1 pour chaque nœud
   * Applique la vérification récursivement sur tous les sous-arbres

3. **Complexité** :

   * Hauteur : O(n)
   * Vérification d’équilibre : O(n²) dans ce cas naïf (B1, pas d’AVL ici)


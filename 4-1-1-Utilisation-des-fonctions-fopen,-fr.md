# 4-Séance 4 : Manipulation de Fichiers et Entrées/Sorties Avancées  
## 1-Fichiers Texte et Binaire  
### 1-Utilisation des fonctions `fopen`, `fread`, `fwrite`, `fseek`, `ftell`  

---

## Introduction  
La gestion des fichiers en C repose sur un ensemble de fonctions standards qui permettent d’ouvrir, lire, écrire et manipuler des fichiers texte ou binaires de manière fine et contrôlée. Ces fonctions offrent la flexibilité nécessaire pour traverser le fichier, lire ou écrire des blocs de données, et contrôler la position du curseur dans le fichier.

---

## 1. Fonction `fopen` : ouvrir un fichier  

### 1.1 Syntaxe  

```c
FILE *fopen(const char *filename, const char *mode);
```

- `filename` : chemin du fichier  
- `mode` : mode d’ouverture parmi les principaux :
  - `"r"` : lecture  
  - `"w"` : écriture (fichier écrasé ou créé)  
  - `"a"` : ajout (append)  
  - `"rb"`, `"wb"`, `"ab"` : même modes en binaire  
  - `"r+"`, `"w+"`, `"a+"` : lecture/écriture  

### 1.2 Exemple  

```c
FILE *file = fopen("data.bin", "rb");
if (file == NULL) {
    perror("Erreur ouverture fichier");
    return 1;
}
```

---

## 2. Fonctions `fread` et `fwrite` : lecture/écriture binaire  

### 2.1 Syntaxe  

```c
size_t fread(void *ptr, size_t size, size_t nmemb, FILE *stream);
size_t fwrite(const void *ptr, size_t size, size_t nmemb, FILE *stream);
```

- `ptr` : zone mémoire pour stocker ou lire les données  
- `size` : taille en octets d’un élément  
- `nmemb` : nombre d’éléments  
- `stream` : pointeur vers fichier ouvert

Renvoient le nombre d’éléments effectivement lus ou écrits.

### 2.2 Exemple d’écriture de tableau d’entiers  

```c
int tab[5] = {10, 20, 30, 40, 50};
FILE *f = fopen("entiers.bin", "wb");
if (f == NULL) return 1;
fwrite(tab, sizeof(int), 5, f);
fclose(f);
```

### 2.3 Exemple de lecture correspondante  

```c
int tab[5];
FILE *f = fopen("entiers.bin", "rb");
if (f == NULL) return 1;
fread(tab, sizeof(int), 5, f);
fclose(f);
for (int i=0; i<5; i++) {
    printf("%d ", tab[i]);
}
```

---

## 3. Fonctions `fseek` et `ftell` : déplacement dans un fichier  

### 3.1 `fseek`  

Permet de positionner le curseur de lecture/écriture dans le fichier.  

```c
int fseek(FILE *stream, long offset, int whence);
```

- `offset` : déplacement (en octets) relatif à `whence`.  
- `whence` : point de référence :  
  - `SEEK_SET` : début du fichier  
  - `SEEK_CUR` : position actuelle  
  - `SEEK_END` : fin du fichier  

Retourne 0 si succès, sinon une erreur.

### 3.2 `ftell`  

Retourne la position actuelle du curseur dans le fichier.  

```c
long ftell(FILE *stream);
```

### 3.3 Exemple : lire un entier au milieu du fichier  

```c
FILE *f = fopen("entiers.bin", "rb");
if (!f) return 1;
fseek(f, 2 * sizeof(int), SEEK_SET);  // aller au 3e int
int val;
fread(&val, sizeof(int), 1, f);
printf("3e entier = %d\n", val);
fclose(f);
```

---

## 4. Diagramme Mermaid : cycle d’utilisation classique des fichiers  

```mermaid
flowchart TD
    A[Début] --> B[Ouvrir fichier (fopen)]
    B --> C{Fichier ouvert ?}
    C -- Oui --> D[Lire ou écrire (fread/fwrite)]
    D --> E[Positionner curseur (fseek) ?]
    E -- Oui --> F[Déplacer curseur]
    E -- Non --> G[Continuer lecture/écriture]
    F --> G
    G --> H[Fermeture fichier (fclose)]
    C -- Non --> I[Erreur gestion]
```

---

## 5. Conseils pratiques  

- Toujours vérifier que le fichier s’est ouvert (`fopen` ne retourne pas NULL).  
- Bien gérer les erreurs lors des lectures/écritures (`fread` ou `fwrite` ne retournent pas obligatoirement le nombre d’éléments attendu).  
- Toujours fermer les fichiers avec `fclose` pour libérer les ressources.  
- Pour les fichiers binaires, utiliser mode `"rb"`/`"wb"`. Pour les fichiers textes, utiliser `"r"`/`"w"`.  
- Utiliser `fseek` et `ftell` pour manipuler efficacement les fichiers volumineux ou accéder à des zones précises.  

---

## 6. Sources utilisées  

- [C Standard I/O Library - cppreference](https://en.cppreference.com/w/c/io)  
- [fopen - GNU C Library documentation](https://www.gnu.org/software/libc/manual/html_node/Opening-Streams.html)  
- [Fread and fwrite in C - GeeksforGeeks](https://www.geeksforgeeks.org/fread-fwrite-functions-c/)  
- [fseek and ftell - Tutorialspoint](https://www.tutorialspoint.com/c_standard_library/c_function_fseek.htm)  
- [Wikipedia : stdio.h](https://en.wikipedia.org/wiki/Stdio.h)  

---

Ce cours explique simplement comment ouvrir, lire, écrire et naviguer dans des fichiers en C avec les fonctions standard, permettant un contrôle fin de l'accès aux données texte ou binaires.
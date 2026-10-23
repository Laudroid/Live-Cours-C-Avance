# 3-Séance 3 : Structures de Données Avancées  
## 1-Listes, Piles, Files  
### 2-Implémentations efficaces (Listes doublement chaînées, buffers circulaires)  

---

## Introduction  
Les listes doublement chaînées et les buffers circulaires sont des structures de données optimisées pour des opérations fréquentes d’insertion, suppression et gestion mémoire circulaire. Elles améliorent la flexibilité par rapport aux listes simplement chaînées et aux files classiques en offrant une meilleure gestion des accès et des performances.

---

## 1. Listes doublement chaînées (Doubly Linked Lists)  

### 1.1 Concept  
Une liste doublement chaînée est une séquence de nœuds où chaque nœud possède deux pointeurs : un vers l’élément suivant et un vers l’élément précédent. Cela permet un parcours bidirectionnel et simplifie certaines opérations comme la suppression d’un nœud donné.

### 1.2 Structure de nœud  

```c
typedef struct Node {
    int value;
    struct Node *prev;
    struct Node *next;
} Node;
```

### 1.3 Avantages  

- Parcours en avant et en arrière possible.  
- Suppression et insertion en O(1) sans nécessité de parcourir la liste pour trouver le prédécesseur.  
- Utile pour les piles, files, et autres structures complexes (ex: deque).

### 1.4 Exemple d’insertion en début  

```c
Node* insert_head(Node *head, int val) {
    Node *new_node = malloc(sizeof(Node));
    if (!new_node) return head;
    new_node->value = val;
    new_node->prev = NULL;
    new_node->next = head;
    if (head) head->prev = new_node;
    return new_node;
}
```

### 1.5 Suppression d’un nœud  

```c
Node* delete_node(Node *head, Node *node) {
    if (!head || !node) return head;
    if (node->prev) node->prev->next = node->next;
    else head = node->next;
    if (node->next) node->next->prev = node->prev;
    free(node);
    return head;
}
```

---

## 2. Buffers circulaires (Circular Buffers)  

### 2.1 Concept  
Un buffer circulaire est une structure de données en mémoire contiguë utilisée comme une file circulaire. Une fois la fin du buffer atteinte, l’écriture ou la lecture reprend au début, évitant ainsi une opération coûteuse de décalage des valeurs.

### 2.2 Caractéristique  

- Taille fixe (statique ou dynamique).  
- Deux indices : `head` (point d’écriture), `tail` (point de lecture).  
- Gestion des cas de buffer plein ou vide par des contrôles spécifiques.

### 2.3 Structure typique  

```c
typedef struct {
    int *buffer;
    size_t head;
    size_t tail;
    size_t max;    // capacité du buffer
    int full;      // indicateur buffer plein
} CircularBuffer;
```

### 2.4 Exemple d’insertion (enqueue)  

```c
void cb_enqueue(CircularBuffer *cb, int val) {
    if (cb->full) {
        // Optionnel : écraser le plus ancien ou gestion d’erreur
        cb->tail = (cb->tail + 1) % cb->max;
    }
    cb->buffer[cb->head] = val;
    cb->head = (cb->head + 1) % cb->max;
    cb->full = (cb->head == cb->tail);
}
```

### 2.5 Exemple de lecture (dequeue)  

```c
int cb_dequeue(CircularBuffer *cb, int *val) {
    if (cb->head == cb->tail && !cb->full) return 0; // buffer vide
    *val = cb->buffer[cb->tail];
    cb->tail = (cb->tail + 1) % cb->max;
    cb->full = 0;
    return 1;
}
```

---

## 3. Diagramme Mermaid : Comparaison des structures  

```mermaid
graph TD
    A[Listes doublement chaînées] -->|Bidirectionnel| B[Prev et Next]
    A -->|Insertion/Suppression| C[O(1) local]
    D[Buffer circulaire] -->|Mémoire contiguë| E[Tableau]
    D -->|Indices head/tail| F[Utilisation circulaire]
    D -->|Pas de décalage| G[Performance]
```

---

## 4. Résumé des usages  

| Structure              | Avantages                                        | Usages typiques                                      |
|------------------------|-------------------------------------------------|-----------------------------------------------------|
| Listes doublement chaînées | Parcours bidirectionnel, suppression rapide     | Implémentation de déques, gestion d’historique      |
| Buffers circulaires     | Utilisation fixe de mémoire, performances optimales | Communication en temps réel, buffers audio/vidéo    |

---

## 5. Sources utilisées  

- [Doubly Linked List - GeeksforGeeks](https://www.geeksforgeeks.org/doubly-linked-list/)  
- [Circular Buffer - Embedded Artistry](https://embeddedartistry.com/blog/2017/4/6/circular-buffers-in-cc)  
- [Circular Queue Implementation - GeeksforGeeks](https://www.geeksforgeeks.org/circular-queue-set-1-introduction-array-implementation/)  
- [Data Structures and Algorithms in C, Adam Drozdek]  
- [Coding Labs - Circular Buffer in C](https://www.codinglabs.net/article_c_circular_buffer.aspx)

---

Ce cours présente deux structures performantes permettant un contrôle efficace des collections grâce à une gestion mémoire optimisée et adaptée aux besoins d’accès rapide et modifiable.
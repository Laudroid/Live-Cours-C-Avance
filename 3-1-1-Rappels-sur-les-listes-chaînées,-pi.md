# 3-Séance 3 : Structures de Données Avancées  
## 1-Listes, Piles, Files  
### 1-Rappels sur les listes chaînées, piles et files  

---

## Introduction  
Les structures de données fondamentales telles que les listes chaînées, piles et files permettent une gestion dynamique des collections d’éléments en mémoire. Ce rappel synthétique illustre leurs caractéristiques, fonctionnements et exemples d’implémentation simple en C.

---

## 1. Listes chaînées  

### 1.1 Définition  
Une liste chaînée est une structure de données composée d’éléments (nœuds) dans lesquels chaque nœud contient une donnée et un pointeur vers le nœud suivant. Contrairement aux tableaux, la liste chaînée est dynamique et permet des insertions/suppressions efficaces n’importe où dans la liste.

### 1.2 Structure type  

```c
typedef struct Node {
    int value;
    struct Node *next;
} Node;
```

### 1.3 Exemple d’insertion en tête  

```c
Node *inserer_en_tete(Node *head, int val) {
    Node *new_node = malloc(sizeof(Node));
    if (new_node == NULL) return head;
    new_node->value = val;
    new_node->next = head;
    return new_node;
}
```

### 1.4 Parcours d’une liste  

```c
void afficher_liste(Node *head) {
    Node *current = head;
    while (current != NULL) {
        printf("%d -> ", current->value);
        current = current->next;
    }
    printf("NULL\n");
}
```

---

## 2. Piles (Stack)  

### 2.1 Définition  
Une pile suit une gestion Last In, First Out (LIFO) : le dernier élément inséré est le premier à sortir. Les opérations principales sont `push` (empiler) et `pop` (dépiler).

### 2.2 Implémentation avec liste chaînée  

```c
typedef Node Stack;

Stack *push(Stack *s, int val) {
    return inserer_en_tete(s, val);
}

Stack *pop(Stack *s, int *val) {
    if (s == NULL) return NULL;
    *val = s->value;
    Node *temp = s->next;
    free(s);
    return temp;
}
```

---

## 3. Files (Queue)  

### 3.1 Définition  
Une file respecte la gestion First In, First Out (FIFO) : le premier élément inséré est le premier à être extrait. Les opérations types sont `enqueue` (ajouter à la fin) et `dequeue` (retirer au début).

### 3.2 Implémentation simple avec liste chaînée  

```c
typedef struct Queue {
    Node *front;
    Node *rear;
} Queue;

void enqueue(Queue *q, int val) {
    Node *new_node = malloc(sizeof(Node));
    if (new_node == NULL) return;
    new_node->value = val;
    new_node->next = NULL;
    if (q->rear == NULL) {
        q->front = q->rear = new_node;
        return;
    }
    q->rear->next = new_node;
    q->rear = new_node;
}

int dequeue(Queue *q, int *val) {
    if (q->front == NULL) return 0; 
    Node *temp = q->front;
    *val = temp->value;
    q->front = q->front->next;
    if (q->front == NULL) q->rear = NULL;
    free(temp);
    return 1;
}
```

---

## 4. Diagramme Mermaid : relations entre listes, piles et files  

```mermaid
graph TD
    Listes[Listes chaînées]
    Piles[Piles (LIFO)]
    Files[Files (FIFO)]
    Listes -->|Implémentation| Piles
    Listes -->|Implémentation| Files
```

---

## 5. Remarques et conseils  

- Les listes chaînées facilitent les insertions/suppressions en temps constant (O(1)) en tête.  
- Piles et files sont abstraites mais souvent implémentées via listes chaînées ou tableaux.  
- Gérer correctement la libération de mémoire dans les structures dynamiques.  
- Utiliser ces structures selon le besoin spécifique d’ordre d’accès (LIFO vs FIFO).  

---

## 6. Sources utilisées  

- [Linked List in C - GeeksforGeeks](https://www.geeksforgeeks.org/data-structures/linked-list/)  
- [Stack Data Structure in C - Programiz](https://www.programiz.com/dsa/stack-data-structure)  
- [Queue Data Structure in C - GeeksforGeeks](https://www.geeksforgeeks.org/queue-data-structure/)  
- [CS50 Lecture - Data Structures](https://cs50.harvard.edu/x/2022/notes/3/)  
- [Tutorialspoint - Linked List](https://www.tutorialspoint.com/data_structures_algorithms/linked_list_algorithms.htm)  

---

Ce rappel couvre les fondements des listes chaînées, piles et files, en mettant l’accent sur la clarté des concepts et la simplicité d’implémentation en C.
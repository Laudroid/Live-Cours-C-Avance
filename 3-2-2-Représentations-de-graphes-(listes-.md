# 3-Séance 3 : Structures de Données Avancées  
## 2-Arbres et Graphes  
### 2-Représentations de graphes (listes d'adjacence, matrices)  

---

## Introduction  
Un graphe est une structure composée de sommets (ou nœuds) et d’arêtes (ou arcs) reliant certains sommets. Pour exploiter un graphe en mémoire, il faut choisir une représentation adaptée : listes d’adjacence ou matrice d’adjacence sont les plus courantes. Chaque représentation présente des avantages et inconvénients selon la densité du graphe et les opérations ciblées.

---

## 1. Matrice d’adjacence  

### 1.1 Description  
La matrice d’adjacence est une matrice carrée (de taille N×N où N est le nombre de sommets) où une entrée `[i][j]` indique la présence (et parfois le poids) d’une arête du sommet `i` vers le sommet `j`.  

- Pour un graphe non orienté, la matrice est symétrique.  
- Pour un graphe orienté, les deux sens sont distincts.  

### 1.2 Avantages  
- Accès direct et rapide à l’existence d’une arête (O(1)).  
- Représentation simple et intuitive.  

### 1.3 Inconvénients  
- Occupation mémoire importante (O(N²)) même pour des graphes peu denses.  
- Itération sur les voisins d’un sommet coûte O(N).  

### 1.4 Exemple  

```c
#define N 4
int matrice_adj[N][N] = {
    {0, 1, 0, 0},
    {1, 0, 1, 1},
    {0, 1, 0, 0},
    {0, 1, 0, 0}
};
```

Ici, le sommet 0 est connecté au sommet 1, le sommet 1 est connecté aux sommets 0, 2, 3, etc.

---

## 2. Listes d’adjacence  

### 2.1 Description  
Chaque sommet possède une liste chaînée ou tableau dynamique enumerant ses voisins directs. C’est une représentation plus économe en mémoire, particulièrement adaptée aux graphes clairsemés.  

### 2.2 Avantages  
- Utilisation mémoire proportionnelle au nombre d’arêtes, souvent bien inférieure à N².  
- Itération rapide sur les voisins d’un sommet (coût proportionnel au degré du sommet).  

### 2.3 Inconvénients  
- Accès à l’existence d’une arête potentiellement O(k), avec k degré du sommet (peut nécessiter parcours de liste).  
- Implémentation un peu plus complexe.  

### 2.4 Exemple  

```c
typedef struct AdjNode {
    int vertex;
    struct AdjNode* next;
} AdjNode;

typedef struct Graph {
    int numVertices;
    AdjNode** adjLists;
} Graph;

AdjNode* createNode(int v) {
    AdjNode* newNode = malloc(sizeof(AdjNode));
    newNode->vertex = v;
    newNode->next = NULL;
    return newNode;
}

Graph* createGraph(int vertices) {
    Graph* graph = malloc(sizeof(Graph));
    graph->numVertices = vertices;
    graph->adjLists = malloc(vertices * sizeof(AdjNode*));
    for (int i = 0; i < vertices; i++)
        graph->adjLists[i] = NULL;
    return graph;
}

void addEdge(Graph* graph, int src, int dest) {
    AdjNode* newNode = createNode(dest);
    newNode->next = graph->adjLists[src];
    graph->adjLists[src] = newNode;

    // Si graphe non orienté, ajouter dans les deux sens
    newNode = createNode(src);
    newNode->next = graph->adjLists[dest];
    graph->adjLists[dest] = newNode;
}
```

---

## 3. Diagramme Mermaid : comparatif des représentations  

```mermaid
flowchart LR
    A[Représentations de graphe]
    A --> B[Matrice d'adjacence]
    A --> C[Liste d'adjacence]

    B --> B1[Complexité mémoire O(N²)]
    B --> B2[Accès direct O(1)]
    B --> B3[Mieux pour graphes denses]

    C --> C1[Complexité mémoire O(N + E)]
    C --> C2[Itération efficace sur voisins]
    C --> C3[Mieux pour graphes clairsemés]
```

---

## 4. Comparaison synthétique  

| Critère           | Matrice d’adjacence                | Liste d’adjacence                  |
|-------------------|----------------------------------|----------------------------------|
| Espace mémoire    | O(N²) (fixe)                     | O(N + E) (variable)               |
| Recherche arête   | O(1)                            | O(k) (k = degré sommet)           |
| Parcours voisins  | O(N)                            | O(k)                             |
| Facilité d’implémentation | Simple                        | Plus élaborée                    |
| Adapté pour       | Graphes denses                  | Graphes clairs                   |

---

## 5. Sources utilisées  

- [Graph Representation - GeeksforGeeks](https://www.geeksforgeeks.org/graph-and-its-representations/)  
- [Graph data structure - Wikipedia](https://en.wikipedia.org/wiki/Graph_(abstract_data_type)#Representation)  
- [C implementation of adjacency list - Programiz](https://www.programiz.com/dsa/graph-adjacency-list)  
- [Introduction to Algorithms (Cormen et al.), chapitre sur graphes]  

---

### Synthèse  
Le choix entre matrice et liste d’adjacence dépend de la densité du graphe et des opérations prioritaires. La matrice permet un accès rapide au statut d’une arête mais consomme beaucoup de mémoire sur des graphes vastes et peu denses. Les listes d’adjacence offrent une plus grande économie mémoire et une efficacité dans la traversal des voisins, idéale pour la majorité des graphes réels clairsemés.
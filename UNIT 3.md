# UNIT 3: Non-Linear Data Structures (Graphs)

---

## 1. Graphs: Concept and Real-World Context

### Definition

A **graph** is a non-linear data structure consisting of:

* **Vertices (Nodes)**: Fundamental units or points.
* **Edges**: Connections between pairs of vertices. These may be directed or undirected and weighted or unweighted.

### Applications

* **Social networks**: Users as vertices, connections (friends/followers) as edges.
* **Maps and routing systems**: Cities as vertices, roads as edges (with weights representing distance, time, or cost).
* **Dependency graphs**: Tasks or modules as vertices, dependencies as directed edges.
* **Telecommunication networks**: Routers or devices as nodes, cables or signals as edges.

---

## 2. Graph Representations

### A. Adjacency Matrix

An adjacency matrix is a two-dimensional array `adj[N][N]` for a graph with `N` vertices, where:

* `adj[i][j] = 1` or weight if an edge exists between vertex `i` and vertex `j`.
* `adj[i][j] = 0` if no edge exists.

#### Characteristics

* Space complexity: O(V²), where V is the number of vertices.
* Time complexity for edge check: O(1).
* Inefficient for sparse graphs.

#### C Example

```c
#define MAX 100
int adj[MAX][MAX];

void addEdge(int u, int v, int weight) {
    adj[u][v] = weight;
    adj[v][u] = weight; // For undirected graph
}
```

### B. Adjacency List

Uses an array of pointers, where each index stores a linked list of connected vertices.

#### Characteristics

* Space complexity: O(V + E), more efficient for sparse graphs.
* Time complexity for edge check: O(k), where k is the number of adjacent vertices.
* Preferred for most graph algorithms due to efficient storage and traversal.

#### C Example

```c
typedef struct Node {
    int vertex;
    struct Node* next;
} Node;

Node* adjList[MAX];

void addEdge(int u, int v) {
    Node* node = malloc(sizeof(Node));
    node->vertex = v;
    node->next = adjList[u];
    adjList[u] = node;

    node = malloc(sizeof(Node));
    node->vertex = u;
    node->next = adjList[v];
    adjList[v] = node;
}
```

---

## 3. Graph Traversals

### A. Breadth-First Search (BFS)

#### Description

BFS explores vertices in layers, visiting all neighbors before moving deeper. It uses a queue for implementation and is useful for finding the shortest path in unweighted graphs.

#### Time Complexity

* O(V + E) with adjacency list.
* O(V²) with adjacency matrix.

#### C Example

```c
#include <stdio.h>
#include <stdlib.h>

int visited[MAX];
int queue[MAX], front = 0, rear = -1;

void enqueue(int value) {
    queue[++rear] = value;
}

int dequeue() {
    return queue[front++];
}

void bfs(int start, Node* adjList[]) {
    visited[start] = 1;
    enqueue(start);

    while (front <= rear) {
        int current = dequeue();
        printf("%d ", current);

        for (Node* temp = adjList[current]; temp != NULL; temp = temp->next) {
            if (!visited[temp->vertex]) {
                visited[temp->vertex] = 1;
                enqueue(temp->vertex);
            }
        }
    }
}
```

### B. Depth-First Search (DFS)

#### Description

DFS dives as deep as possible along a branch before backtracking. Implemented using recursion or a stack.

#### Time Complexity

* O(V + E)

#### C Example (Recursive)

```c
void dfs(int vertex, Node* adjList[], int visited[]) {
    visited[vertex] = 1;
    printf("%d ", vertex);

    for (Node* temp = adjList[vertex]; temp != NULL; temp = temp->next) {
        if (!visited[temp->vertex]) {
            dfs(temp->vertex, adjList, visited);
        }
    }
}
```

---

## 4. Connected Components

### Description

A connected component is a subset of vertices in which every pair is connected via some path.

### Application

Used to determine isolated groups in networks.

### C Example

Use DFS from each unvisited vertex and increment component count.

---

## 5. Minimum Spanning Tree (MST)

### A. Kruskal’s Algorithm

#### Description

* Sort edges by weight.
* Add to MST if it doesn’t form a cycle (Union-Find).

#### Time Complexity

* O(E log E)

### B. Prim’s Algorithm

#### Description

* Start from a vertex, grow MST by adding the smallest weight edge connecting visited and unvisited vertices.

#### Time Complexity

* O(E log V) with priority queue.

#### Application

* Efficient for dense graphs.

---

## 6. Shortest Path Algorithms

### A. Dijkstra’s Algorithm

#### Description

Computes the shortest path from a source to all vertices in a weighted graph with non-negative edges.

#### Time Complexity

* O((V + E) log V) using a min-heap.

#### Limitations

* Fails with negative weights.

### B. Floyd-Warshall Algorithm

#### Description

All-pairs shortest paths using dynamic programming.

#### Time Complexity

* O(V³)

#### Application

* Suitable for dense graphs or where multiple shortest paths are needed.

---

## 7. Topological Sort

### Description

Linear ordering of vertices in a DAG such that for every edge (u, v), u appears before v.

### Algorithms

* DFS-based topological sort.
* Kahn’s algorithm using in-degree and BFS.

### Applications

* Course scheduling
* Task ordering in build systems

---

## 8. Case Studies

### A. Dynamic Graphs

Graphs that allow insertion and deletion of vertices or edges. Used in:

* Navigation systems (e.g., traffic-based rerouting)
* Real-time strategy games

### B. Social Network Graphs

* Huge graphs with millions of vertices and edges.
* Used to detect communities, recommend friends, measure influence.

### C. Sparse Graphs

* Fewer edges relative to vertices.
* Efficiently represented using adjacency lists.
* Examples include flight networks, citation networks.

---

## Summary Comparison

| Representation / Algorithm | Time Complexity    | Space Complexity | Suitable For               | Strengths                                 |
| -------------------------- | ------------------ | ---------------- | -------------------------- | ----------------------------------------- |
| Adjacency Matrix           | O(1) edge lookup   | O(V²)            | Dense graphs               | Fast edge checks                          |
| Adjacency List             | O(V + E) traversal | O(V + E)         | Sparse graphs              | Memory-efficient and scalable             |
| BFS                        | O(V + E)           | O(V)             | Shortest path (unweighted) | Layer-wise exploration                    |
| DFS                        | O(V + E)           | O(V)             | Connectivity, components   | Simple recursion-based implementation     |
| Kruskal                    | O(E log E)         | O(E)             | Sparse MSTs                | Greedy, uses Union-Find                   |
| Prim                       | O(E log V)         | O(V)             | Dense MSTs                 | Expands from any starting vertex          |
| Dijkstra                   | O((V + E) log V)   | O(V)             | SSSP, no negative weights  | Efficient for real-time navigation        |
| Floyd-Warshall             | O(V³)              | O(V²)            | All-pairs shortest paths   | Simple and systematic                     |
| Topological Sort           | O(V + E)           | O(V)             | DAGs                       | Crucial in dependency resolution problems |


# UNIT 2

## 1. Trees: Foundation and Terminology

### What is a Tree?
A tree is a hierarchical, non-linear data structure that consists of nodes connected by edges, forming a structure that resembles an inverted tree. Unlike linear data structures (arrays, linked lists), trees allow for branching paths and hierarchical relationships.

**Real-World Analogy**: Think of a company's organizational chart. The CEO is at the top (root), with VPs below (children), then directors under VPs (grandchildren), and so on. Each person reports to exactly one superior, but can manage multiple subordinates.

### Essential Terminology

**Node**: The fundamental unit containing data and references to other nodes
- **Root Node**: The topmost node with no parent (CEO in our analogy)
- **Leaf Node**: Nodes with no children (entry-level employees)
- **Internal Node**: Nodes with at least one child (managers)

**Relationships**:
- **Parent**: A node that has children
- **Child**: A node connected to another node when moving away from root
- **Sibling**: Nodes sharing the same parent
- **Ancestor**: Any node on the path from root to a given node
- **Descendant**: Any node reachable by following child pointers

**Structural Properties**:
- **Height**: Longest path from root to any leaf (levels in company hierarchy)
- **Depth**: Distance from root to a specific node
- **Level**: All nodes at the same depth
- **Degree**: Number of children a node has

### Why Trees Matter in Software Systems

Trees solve fundamental problems in computer science:

1. **Hierarchical Organization**: File systems, XML/HTML parsing, organization charts
2. **Efficient Searching**: Binary search trees provide O(log n) search time
3. **Decision Making**: Decision trees in AI/ML, game trees in chess engines
4. **Memory Management**: B-trees in databases, tries in autocomplete systems

## 2. Binary Trees: The Foundation

### Internal Structure and Implementation

A binary tree is a specialized tree where each node has at most two children, typically called "left" and "right" children.

```c
struct TreeNode {
    int data;
    struct TreeNode* left;
    struct TreeNode* right;
};

struct TreeNode* createTreeNode(int val) {
    struct TreeNode* node = (struct TreeNode*)malloc(sizeof(struct TreeNode));
    node->data = val;
    node->left = NULL;
    node->right = NULL;
    return node;
}
```

**Real-World Example**: Think of a decision-making process. At each step, you have two choices (left/right), and each choice leads to further binary decisions until you reach a conclusion (leaf).

### Types of Binary Trees

**Full Binary Tree**: Every node has either 0 or 2 children
- **Use Case**: Expression trees in compilers where operators have exactly two operands

**Complete Binary Tree**: All levels filled except possibly the last, which is filled left-to-right
- **Use Case**: Heap implementation for priority queues

**Perfect Binary Tree**: All internal nodes have two children, all leaves at same level
- **Use Case**: Optimal for cache-friendly data structures

**Balanced Binary Tree**: Height difference between left and right subtrees ≤ 1
- **Use Case**: Maintaining search efficiency in dynamic datasets

### Properties and Complexity Analysis

**Space Complexity**: O(n) where n is number of nodes
**Height Relationships**:
- Minimum height: ⌊log₂(n)⌋ (perfect binary tree)
- Maximum height: n-1 (skewed tree, essentially a linked list)

**Performance Impact**: Height directly affects operation efficiency. A balanced tree with 1 million nodes has height ~20, while a skewed tree has height 999,999.

## 3. Binary Search Trees (BST): Ordered Efficiency

### The BST Property

For every node in a BST:
- All values in left subtree < node's value
- All values in right subtree > node's value
- Both subtrees are also BSTs

**Real-World Analogy**: Imagine organizing a library where books are arranged such that at any shelf, all books to the left have smaller call numbers, and all books to the right have larger call numbers. Finding any book becomes much faster.

### Implementation and Operations

```c
struct TreeNode* insert(struct TreeNode* node, int val) {
    if (!node) return createTreeNode(val);
    if (val < node->data)
        node->left = insert(node->left, val);
    else if (val > node->data)
        node->right = insert(node->right, val);
    return node;
}

struct TreeNode* search(struct TreeNode* node, int val) {
    if (!node || node->data == val) return node;
    if (val < node->data)
        return search(node->left, val);
    return search(node->right, val);
}

struct TreeNode* findMin(struct TreeNode* node) {
    while (node && node->left)
        node = node->left;
    return node;
}

struct TreeNode* deleteNode(struct TreeNode* node, int val) {
    if (!node) return node;
    if (val < node->data)
        node->left = deleteNode(node->left, val);
    else if (val > node->data)
        node->right = deleteNode(node->right, val);
    else {
        if (!node->left) {
            struct TreeNode* temp = node->right;
            free(node);
            return temp;
        }
        if (!node->right) {
            struct TreeNode* temp = node->left;
            free(node);
            return temp;
        }
        struct TreeNode* temp = findMin(node->right);
        node->data = temp->data;
        node->right = deleteNode(node->right, temp->data);
    }
    return node;
}
```

### Complexity Analysis

**Best/Average Case** (balanced tree):
- Search: O(log n)
- Insert: O(log n) 
- Delete: O(log n)

**Worst Case** (skewed tree):
- All operations: O(n)

**Real-World Application**: Database indexing systems use BST variants to maintain sorted data for fast queries.

## 4. Tree Traversals: Systematic Exploration

Tree traversals are methods to visit every node in a tree exactly once. Each serves different purposes.

### Depth-First Traversals

**1. Inorder (Left, Root, Right)**
```c
void inorderRecursive(struct TreeNode* node) {
    if (node) {
        inorderRecursive(node->left);
        printf("%d ", node->data);
        inorderRecursive(node->right);
    }
}

// Iterative using stack
#include <stdio.h>
#include <stdlib.h>
#define MAX_STACK_SIZE 1000

struct TreeNode* stack[MAX_STACK_SIZE];
int top = -1;

void push(struct TreeNode* node) { stack[++top] = node; }
struct TreeNode* pop() { return stack[top--]; }
int isEmpty() { return top == -1; }

void inorderIterative(struct TreeNode* root) {
    struct TreeNode* current = root;
    while (current || !isEmpty()) {
        while (current) {
            push(current);
            current = current->left;
        }
        current = pop();
        printf("%d ", current->data);
        current = current->right;
    }
}
```

**Use Case**: For BST, inorder traversal gives sorted sequence. Essential for range queries in databases.

**2. Preorder (Root, Left, Right)**
```c
void preorderRecursive(struct TreeNode* node) {
    if (node) {
        printf("%d ", node->data);
        preorderRecursive(node->left);
        preorderRecursive(node->right);
    }
}

void preorderIterative(struct TreeNode* root) {
    if (!root) return;
    
    struct TreeNode* stack[1000];
    int top = -1;
    
    stack[++top] = root;
    
    while (top != -1) {
        struct TreeNode* node = stack[top--];
        printf("%d ", node->data);
        
        if (node->right) stack[++top] = node->right;
        if (node->left) stack[++top] = node->left;
    }
}
```

**Use Case**: Tree serialization, expression tree evaluation, copying trees.

**3. Postorder (Left, Right, Root)**
```c
void postorderRecursive(struct TreeNode* node) {
    if (node) {
        postorderRecursive(node->left);
        postorderRecursive(node->right);
        printf("%d ", node->data);
    }
}

void postorderIterative(struct TreeNode* root) {
    if (!root) return;
    
    struct TreeNode* stack1[1000];
    struct TreeNode* stack2[1000];
    int top1 = -1;
    int top2 = -1;
    
    stack1[++top1] = root;
    
    while (top1 != -1) {
        struct TreeNode* node = stack1[top1--];
        stack2[++top2] = node;
        
        if (node->left) stack1[++top1] = node->left;
        if (node->right) stack1[++top1] = node->right;
    }
    
    while (top2 != -1) {
        printf("%d ", stack2[top2--]->data);
    }
}
```

**Use Case**: Tree deletion, calculating directory sizes, expression evaluation.

### Breadth-First Traversal (Level Order)

```c
void levelOrder(struct TreeNode* root) {
    if (!root) return;
    
    struct TreeNode* queue[1000];
    int front = 0;
    int rear = 0;
    
    queue[rear++] = root;
    
    while (front < rear) {
        struct TreeNode* node = queue[front++];
        printf("%d ", node->data);
        
        if (node->left) queue[rear++] = node->left;
        if (node->right) queue[rear++] = node->right;
    }
}
```

**Use Case**: Finding shortest path in unweighted graphs, printing tree level by level.

### Performance Comparison

**Recursive Traversals**:
- **Time**: O(n) for all
- **Space**: O(h) where h is height (call stack)
- **Pros**: Clean, intuitive code
- **Cons**: Stack overflow risk for deep trees

**Iterative Traversals**:
- **Time**: O(n) for all
- **Space**: O(h) for explicit stack
- **Pros**: No recursion overhead, better for deep trees
- **Cons**: More complex implementation

## 5. Threaded Binary Trees: Optimizing Traversal

### The Problem with Traditional Trees

In a standard binary tree, approximately half the pointers are null (leaf nodes have two null pointers, internal nodes may have one). Threaded binary trees utilize these null pointers to store "threads" pointing to inorder predecessor/successor.

**Real-World Analogy**: Imagine a museum where each exhibit has directions to the next exhibit in a logical sequence, eliminating the need to backtrack to find your path.

### Types of Threaded Trees

**1. Single Threaded**: Either left or right null pointers are threaded
**2. Double Threaded**: Both null pointers are threaded

### Implementation

```c
struct ThreadedNode {
    int data;
    struct ThreadedNode* left;
    struct ThreadedNode* right;
    int leftThread;
    int rightThread;
};

struct ThreadedNode* inorderSuccessor(struct ThreadedNode* node) {
    if (node->rightThread)
        return node->right;
    
    node = node->right;
    while (!node->leftThread)
        node = node->left;
    
    return node;
}

struct ThreadedNode* leftmostNode(struct ThreadedNode* node) {
    while (!node->leftThread)
        node = node->left;
    return node;
}

void inorderTraversal(struct ThreadedNode* root) {
    if (!root) return;
    
    struct ThreadedNode* current = leftmostNode(root);
    
    while (current) {
        printf("%d ", current->data);
        current = inorderSuccessor(current);
    }
}
```

### Advantages and Trade-offs

**Advantages**:
- **Faster Traversal**: O(1) to find inorder successor/predecessor
- **No Stack/Recursion**: Memory efficient traversal
- **Space Utilization**: Uses otherwise wasted null pointers

**Disadvantages**:
- **Complex Implementation**: Insertion/deletion more intricate
- **Additional Memory**: Boolean flags for each pointer
- **Maintenance Overhead**: Thread management during modifications

**Use Cases**: 
- Frequently traversed trees with infrequent modifications
- Memory-constrained environments
- Real-time systems requiring predictable traversal times

## 6. Tree Sort: Leveraging BST Properties

Tree sort uses the BST property where inorder traversal yields sorted sequence.

### Algorithm Implementation

```c
void treeSort(int arr[], int size) {
    struct TreeNode* tree = NULL;
    
    // Insert all elements into BST
    for (int i = 0; i < size; i++) {
        tree = insert(tree, arr[i]);
    }
    
    // Inorder traversal gives sorted sequence
    inorderRecursive(tree);
}
```

### Complexity Analysis

**Time Complexity**:
- **Best/Average**: O(n log n) - balanced tree
- **Worst**: O(n²) - skewed tree (sorted input)

**Space Complexity**: O(n) for tree structure

### Comparison with Other Sorting Algorithms

| Algorithm | Best Case | Average Case | Worst Case | Space | Stable |
|-----------|-----------|--------------|------------|-------|--------|
| Tree Sort | O(n log n) | O(n log n) | O(n²) | O(n) | No |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(log n) | No |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes |
| Heap Sort | O(n log n) | O(n log n) | O(n log n) | O(1) | No |

**When to Use Tree Sort**:
- When you need both sorting and subsequent search operations
- Building an index structure while sorting
- Educational purposes to understand BST properties

## 7. Tries (Prefix Trees): String Processing Powerhouse

### What is a Trie?

A trie is a tree-like data structure used to store strings where each path from root to leaf represents a complete string. Each node represents a character, and paths represent prefixes.

**Real-World Analogy**: Think of a dictionary where words are organized by their prefixes. All words starting with 'A' are in one section, words starting with 'AB' in a subsection, and so on.

### Implementation

```c
struct TrieNode {
    struct TrieNode* children[26];  // For lowercase letters
    int isEndOfWord;
};

struct TrieNode* createTrieNode() {
    struct TrieNode* node = (struct TrieNode*)malloc(sizeof(struct TrieNode));
    for (int i = 0; i < 26; i++)
        node->children[i] = NULL;
    node->isEndOfWord = 0;
    return node;
}

void insert(struct TrieNode* root, char* word) {
    struct TrieNode* current = root;
    
    for (int i = 0; word[i] != '\0'; i++) {
        int index = word[i] - 'a';
        if (!current->children[index])
            current->children[index] = createTrieNode();
        current = current->children[index];
    }
    current->isEndOfWord = 1;
}

int search(struct TrieNode* root, char* word) {
    struct TrieNode* current = root;
    
    for (int i = 0; word[i] != '\0'; i++) {
        int index = word[i] - 'a';
        if (!current->children[index])
            return 0;
        current = current->children[index];
    }
    return current->isEndOfWord;
}

int startsWith(struct TrieNode* root, char* prefix) {
    struct TrieNode* current = root;
    
    for (int i = 0; prefix[i] != '\0'; i++) {
        int index = prefix[i] - 'a';
        if (!current->children[index])
            return 0;
        current = current->children[index];
    }
    return 1;
}

void dfsCollect(struct TrieNode* node, char* currentWord, struct TrieNode* results[]) {
    if (node->isEndOfWord) {
        results[results[0]++] = node;
    }
    
    for (int i = 0; i < 26; i++) {
        if (node->children[i]) {
            char nextChar = 'a' + i;
            dfsCollect(node->children[i], currentWord, results);
        }
    }
}

void autoComplete(struct TrieNode* root, char* prefix, struct TrieNode* results[]) {
    struct TrieNode* prefixNode = root;
    
    // Navigate to prefix
    for (int i = 0; prefix[i] != '\0'; i++) {
        int index = prefix[i] - 'a';
        if (!prefixNode->children[index])
            return;
        prefixNode = prefixNode->children[index];
    }
    
    // DFS to find all words with this prefix
    dfsCollect(prefixNode, prefix, results);
}
```

### Complexity Analysis

**Time Complexity**:
- **Insert**: O(m) where m is string length
- **Search**: O(m)
- **Prefix Search**: O(p) where p is prefix length
- **AutoComplete**: O(p + n) where n is number of matching words

**Space Complexity**: O(ALPHABET_SIZE × N × M) in worst case
- Can be optimized using compressed tries or hash maps

### Real-World Applications

**1. Autocomplete Systems**
```c
// Google Search, IDE autocomplete
struct AutocompleteSystem {
    struct TrieNode* trie;
    
    void addWord(char* word) {
        insert(trie, word);
    }
    
    struct TrieNode* getSuggestions(char* prefix) {
        struct TrieNode* results[100];
        autoComplete(trie, prefix, results);
        return results;
    }
};
```

**2. Spell Checkers**
```c
int isValidWord(struct TrieNode* root, char* word) {
    return search(root, word);
}

struct TrieNode* getSuggestions(struct TrieNode* root, char* misspelled) {
    // Implementation for edit distance based suggestions
    // using trie for efficient prefix matching
}
```

**3. IP Routing Tables**
```c
// Network routers use tries for longest prefix matching
struct IPTrieNode {
    struct IPTrieNode* children[2];  // 0 and 1 for binary IP
    char* nextHop;
    int isRoute;
};
```

### Trie vs Other Data Structures

| Operation | Trie | Hash Table | BST |
|-----------|------|------------|-----|
| Insert | O(m) | O(1) avg | O(log n) |
| Search | O(m) | O(1) avg | O(log n) |
| Prefix Search | O(p) | O(n) | O(n) |
| Space | High | Medium | Medium |
| Ordering | Lexicographic | None | Sorted |

**Choose Trie When**:
- Frequent prefix-based operations
- Autocomplete functionality needed
- String matching with wildcards
- Dictionary implementations

## 8. AVL Trees: Self-Balancing Excellence

### The Balance Problem

Regular BSTs can degenerate into linked lists with O(n) operations. AVL trees maintain balance automatically, ensuring O(log n) performance.

**Real-World Analogy**: Think of a well-managed team where workload is evenly distributed. If one person gets overloaded, tasks are redistributed to maintain efficiency.

### AVL Property

For every node in an AVL tree:
- Heights of left and right subtrees differ by at most 1
- Both subtrees are AVL trees

### Balance Factor and Rotations

```c
struct AVLNode {
    int data;
    struct AVLNode* left;
    struct AVLNode* right;
    int height;
};

int getHeight(struct AVLNode* node) {
    return node ? node->height : 0;
}

int getBalance(struct AVLNode* node) {
    return node ? getHeight(node->left) - getHeight(node->right) : 0;
}

void updateHeight(struct AVLNode* node) {
    if (node)
        node->height = 1 + max(getHeight(node->left), getHeight(node->right));
}

// Right rotation
struct AVLNode* rotateRight(struct AVLNode* y) {
    struct AVLNode* x = y->left;
    struct AVLNode* T2 = x->right;
    
    // Perform rotation
    x->right = y;
    y->left = T2;
    
    // Update heights
    updateHeight(y);
    updateHeight(x);
    
    return x;  // New root
}

// Left rotation
struct AVLNode* rotateLeft(struct AVLNode* x) {
    struct AVLNode* y = x->right;
    struct AVLNode* T2 = y->left;
    
    // Perform rotation
    y->left = x;
    x->right = T2;
    
    // Update heights
    updateHeight(x);
    updateHeight(y);
    
    return y;  // New root
}

struct AVLNode* insert(struct AVLNode* node, int val) {
    // 1. Perform normal BST insertion
    if (!node) return createTreeNode(val);
    
    if (val < node->data)
        node->left = insert(node->left, val);
    else if (val > node->data)
        node->right = insert(node->right, val);
    else
        return node;  // Duplicates not allowed
    
    // 2. Update height
    updateHeight(node);
    
    // 3. Get balance factor
    int balance = getBalance(node);
    
    // 4. Perform rotations if unbalanced
    
    // Left Left Case
    if (balance > 1 && val < node->left->data)
        return rotateRight(node);
    
    // Right Right Case
    if (balance < -1 && val > node->right->data)
        return rotateLeft(node);
    
    // Left Right Case
    if (balance > 1 && val > node->left->data) {
        node->left = rotateLeft(node->left);
        return rotateRight(node);
    }
    
    // Right Left Case
    if (balance < -1 && val < node->right->data) {
        node->right = rotateRight(node->right);
        return rotateLeft(node);
    }
    
    return node;
}

struct AVLNode* deleteNode(struct AVLNode* node, int val) {
    // 1. Perform standard BST deletion
    if (!node) return node;
    
    if (val < node->data)
        node->left = deleteNode(node->left, val);
    else if (val > node->data)
        node->right = deleteNode(node->right, val);
    else {
        if (!node->left || !node->right) {
            struct AVLNode* temp = node->left ? node->left : node->right;
            if (!temp) {
                temp = node;
                node = NULL;
            } else {
                *node = *temp;
            }
            free(temp);
        } else {
            struct AVLNode* temp = getMin(node->right);
            node->data = temp->data;
            node->right = deleteNode(node->right, temp->data);
        }
    }
    
    if (!node) return node;
    
    // 2. Update height
    updateHeight(node);
    
    // 3. Get balance factor
    int balance = getBalance(node);
    
    // 4. Perform rotations if needed
    // Left Left Case
    if (balance > 1 && getBalance(node->left) >= 0)
        return rotateRight(node);
    
    // Left Right Case
    if (balance > 1 && getBalance(node->left) < 0) {
        node->left = rotateLeft(node->left);
        return rotateRight(node);
    }
    
    // Right Right Case
    if (balance < -1 && getBalance(node->right) <= 0)
        return rotateLeft(node);
    
    // Right Left Case
    if (balance < -1 && getBalance(node->right) > 0) {
        node->right = rotateRight(node->right);
        return rotateLeft(node);
    }
    
    return node;
}
```

### Performance Guarantees

**Time Complexity**: O(log n) for all operations (search, insert, delete)
**Space Complexity**: O(n)
**Height Guarantee**: Always between log₂(n) and 1.44 × log₂(n)

### AVL vs Red-Black Trees

| Feature | AVL Trees | Red-Black Trees |
|---------|-----------|-----------------|
| Balance | Stricter | Relaxed |
| Rotations | More on insert/delete | Fewer rotations |
| Search | Slightly faster | Slightly slower |
| Insert/Delete | Slower due to rotations | Faster |
| Use Case | Read-heavy workloads | Write-heavy workloads |

**Real-World Applications**:
- **Database Indexes**: When frequent lookups with occasional updates
- **Memory-mapped databases**: SQLite uses modified B-trees (similar principles)
- **Game Development**: Spatial indexing for collision detection

## 9. M-Way Trees and B-Trees: Database Powerhouses

### Why M-Way Trees?

Binary trees become inefficient for disk-based storage due to:
- **High I/O Cost**: Each node access requires disk read
- **Poor Cache Utilization**: Small nodes don't fill disk blocks efficiently
- **Deep Trees**: More levels mean more disk accesses

**Real-World Analogy**: Instead of a narrow, tall filing cabinet (binary tree), use a wide, shallow one (M-way tree) where each drawer holds multiple folders, reducing the number of drawers you need to open.

### M-Way Tree Structure

```c
struct MWayNode {
    int keys[2 * m - 1];
    struct MWayNode* children[2 * m];
    int isLeaf;
    int degree;  // Maximum number of children
};
```

### B-Trees: The Database Standard

B-trees are self-balancing M-way trees with specific properties ensuring optimal disk performance.

**B-Tree Properties**:
1. All leaves at the same level
2. Every node has between ⌈m/2⌉ and m children (except root)
3. Root has at least 2 children (unless it's a leaf)
4. Keys within nodes are sorted
5. For node with k keys, there are k+1 children

### B-Tree Implementation

```c
struct BTreeNode {
    int keys[2 * m - 1];
    struct BTreeNode* children[2 * m];
    int isLeaf;
    int minDegree;
};

void insertNonFull(struct BTreeNode* node, int key) {
    int i = node->keys[0] - 1;
    
    if (node->isLeaf) {
        while (i >= 0 && key < node->keys[i]) {
            node->keys[i + 1] = node->keys[i];
            i--;
        }
        node->keys[i + 1] = key;
    } else {
        while (i >= 0 && key < node->keys[i])
            i--;
        
        if (node->children[i + 1]->keys[0] == 2 * minDegree - 1) {
            splitChild(i + 1, node->children[i + 1], node);
            if (key > node->keys[i + 1])
                i++;
        }
        insertNonFull(node->children[i + 1], key);
    }
}

void splitChild(int index, struct BTreeNode* fullChild, struct BTreeNode* node) {
    struct BTreeNode* newChild = (struct BTreeNode*)malloc(sizeof(struct BTreeNode));
    
    // Move half the keys to new node
    for (int i = 0; i < minDegree - 1; i++) {
        newChild->keys[i] = fullChild->keys[i + minDegree];
    }
    
    if (!fullChild->isLeaf) {
        for (int i = 0; i < minDegree; i++) {
            newChild->children[i] = fullChild->children[i + minDegree];
        }
    }
    
    // Reduce size of original node
    for (int i = node->keys[0] - 1; i >= index; i--) {
        node->keys[i + 1] = node->keys[i];
    }
    node->keys[index] = fullChild->keys[minDegree - 1];
    
    // Insert new child into current node
    for (int i = node->keys[0]; i >= index + 1; i--) {
        node->children[i + 1] = node->children[i];
    }
    node->children[index + 1] = newChild;
    
    // Update keys and children
    node->keys[0]++;
    newChild->keys[0] = minDegree - 1;
    newChild->isLeaf = fullChild->isLeaf;
}

int search(struct BTreeNode* node, int key) {
    int i = 0;
    while (i < node->keys[0] && key > node->keys[i])
        i++;
    
    if (i < node->keys[0] && key == node->keys[i])
        return 1;
    
    if (node->isLeaf)
        return 0;
    
    return search(node->children[i], key);
}
```

### B+ Trees: Enhanced for Databases

B+ trees improve upon B-trees for database applications:

**Key Differences**:
1. **Data only in leaves**: Internal nodes store only keys for navigation
2. **Linked leaves**: Leaves form linked list for range queries
3. **More keys per internal node**: Better fanout, shorter trees

```c
struct BPlusNode {
    int keys[2 * m - 1];
    struct BPlusNode* children[2 * m];
    struct BPlusNode* next;  // For leaf linking
    int isLeaf;
    
    // In real implementation, leaf nodes would store data records
    struct Record* records[2 * m - 1];  // Only in leaf nodes
};
```

**Advantages for Databases**:
- **Range Queries**: Follow leaf pointers for efficient range scans
- **Better Caching**: Internal nodes cache better (no data overhead)
- **Sequential Access**: Linked leaves enable efficient sequential processing

### B* Trees: Space Optimization

B* trees further optimize B+ trees by maintaining higher minimum occupancy (2/3 instead of 1/2).

**Key Feature**: Delay splits by redistributing keys among siblings first.

### Performance Analysis and Comparisons

| Tree Type | Search | Insert | Delete | Range Query | Disk I/O |
|-----------|--------|--------|--------|-------------|----------|
| BST | O(log n) | O(log n) | O(log n) | O(k + log n) | High |
| B-Tree | O(log n) | O(log n) | O(log n) | O(k + log n) | Low |
| B+ Tree | O(log n) | O(log n) | O(log n) | O(k + log n) | Low |
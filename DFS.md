# Complete Data Structures and Algorithms Guide
## Deep Dive into Linear and Non-Linear Data Structures


# UNIT 1: Linear Data Structures

## Chapter 1: Arrays - The Foundation of Data Storage

### Understanding Arrays at the Core

Arrays are the most fundamental data structure, representing a collection of elements stored in contiguous memory locations. Think of an array like a row of mailboxes in an apartment building - each mailbox has a specific address (index), and you can directly access any mailbox if you know its number.

### Internal Memory Representation

When you declare an array in C like `int arr[5]`, the computer allocates a continuous block of memory. If each integer takes 4 bytes and the array starts at memory address 1000, the layout looks like:

```
Index:    0     1     2     3     4
Address: 1000  1004  1008  1012  1016
Value:   [10]  [20]  [30]  [40]  [50]
```

The beauty of arrays lies in their **address calculation formula**:
```
Address of arr[i] = Base Address + (i × size of each element)
```

This formula enables **constant time O(1) access** - a fundamental advantage that makes arrays incredibly efficient for random access operations.

### Single Dimension Arrays: Deep Implementation

```c
#include <stdio.h>
#include <stdlib.h>

// Static array declaration
int staticArray[100];

// Dynamic array allocation
int* createDynamicArray(int size) {
    int* arr = (int*)malloc(size * sizeof(int));
    if (arr == NULL) {
        printf("Memory allocation failed!\n");
        return NULL;
    }
    return arr;
}

// Array operations with error handling
void arrayOperations() {
    int arr[5] = {10, 20, 30, 40, 50};
    
    // Traversal - O(n) time complexity
    printf("Array elements: ");
    for (int i = 0; i < 5; i++) {
        printf("%d ", arr[i]);
    }
    
    // Insertion at specific position
    // Requires shifting elements - O(n) worst case
    void insertElement(int arr[], int* size, int pos, int value) {
        for (int i = *size; i > pos; i--) {
            arr[i] = arr[i-1];
        }
        arr[pos] = value;
        (*size)++;
    }
    
    // Deletion at specific position
    // Requires shifting elements - O(n) worst case
    void deleteElement(int arr[], int* size, int pos) {
        for (int i = pos; i < *size - 1; i++) {
            arr[i] = arr[i+1];
        }
        (*size)--;
    }
}
```

### Multi-Dimensional Arrays: Matrix Operations

Multi-dimensional arrays are like spreadsheets or tables. A 2D array `int matrix[3][4]` can be visualized as a table with 3 rows and 4 columns.

#### Memory Layout: Row-Major vs Column-Major

**Row-Major Order (used in C/C++):**
```
Matrix[2][3] = {{1,2,3}, {4,5,6}}
Memory: [1][2][3][4][5][6]
```

**Address calculation for 2D array:**
```
Address of arr[i][j] = Base + ((i × number_of_columns + j) × size_of_element)
```

```c
// 2D Array Implementation
#define ROWS 3
#define COLS 4

void matrixOperations() {
    int matrix[ROWS][COLS];
    
    // Initialize matrix
    int value = 1;
    for (int i = 0; i < ROWS; i++) {
        for (int j = 0; j < COLS; j++) {
            matrix[i][j] = value++;
        }
    }
    
    // Matrix multiplication example
    void multiplyMatrices(int A[][COLS], int B[][COLS], int result[][COLS]) {
        for (int i = 0; i < ROWS; i++) {
            for (int j = 0; j < COLS; j++) {
                result[i][j] = 0;
                for (int k = 0; k < COLS; k++) {
                    result[i][j] += A[i][k] * B[k][j];
                }
            }
        }
        // Time Complexity: O(n³) for n×n matrices
    }
}
```

### Sparse Matrices: Optimizing Memory Usage

A sparse matrix is like a huge parking lot where most spaces are empty. Instead of storing every empty space, we only record the occupied ones.

**Real-world example:** Google's search index is essentially a massive sparse matrix where rows are web pages, columns are words, and values indicate relevance scores. Most pages don't contain most words, making it highly sparse.

#### Types of Sparse Matrix Representation:

**1. Triplet Representation (COO - Coordinate Format):**
```c
typedef struct {
    int row, col, value;
} Element;

typedef struct {
    int rows, cols, nonZeroCount;
    Element* elements;
} SparseMatrix;

// Convert regular matrix to sparse
SparseMatrix* createSparseMatrix(int matrix[][COLS], int rows, int cols) {
    SparseMatrix* sparse = malloc(sizeof(SparseMatrix));
    sparse->rows = rows;
    sparse->cols = cols;
    
    // Count non-zero elements
    int count = 0;
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (matrix[i][j] != 0) count++;
        }
    }
    
    sparse->nonZeroCount = count;
    sparse->elements = malloc(count * sizeof(Element));
    
    // Store non-zero elements
    int index = 0;
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (matrix[i][j] != 0) {
                sparse->elements[index].row = i;
                sparse->elements[index].col = j;
                sparse->elements[index].value = matrix[i][j];
                index++;
            }
        }
    }
    return sparse;
}
```

**2. Compressed Sparse Row (CSR) Format:**
Used in high-performance computing and machine learning libraries like SciPy.

```c
typedef struct {
    int* values;      // Non-zero values
    int* colIndices;  // Column indices
    int* rowPointers; // Pointers to start of each row
    int rows, cols, nonZeros;
} CSRMatrix;
```

### Searching Algorithms: Finding Needles in Haystacks

#### Linear Search: The Methodical Approach
Like looking for a book by checking every shelf one by one.

```c
int linearSearch(int arr[], int size, int target) {
    for (int i = 0; i < size; i++) {
        if (arr[i] == target) {
            return i; // Found at index i
        }
    }
    return -1; // Not found
}
// Time Complexity: O(n) - worst case checks every element
// Space Complexity: O(1) - only uses a few variables
// Best for: Unsorted arrays, small datasets
```

#### Binary Search: The Divide and Conquer Champion
Like looking up a word in a dictionary - you don't start from page 1, you open to the middle and decide which half to search.

```c
// Iterative Binary Search
int binarySearch(int arr[], int size, int target) {
    int left = 0, right = size - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2; // Prevents overflow
        
        if (arr[mid] == target) {
            return mid;
        } else if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return -1;
}

// Recursive Binary Search
int binarySearchRecursive(int arr[], int left, int right, int target) {
    if (left > right) return -1;
    
    int mid = left + (right - left) / 2;
    
    if (arr[mid] == target) return mid;
    else if (arr[mid] < target) 
        return binarySearchRecursive(arr, mid + 1, right, target);
    else 
        return binarySearchRecursive(arr, left, mid - 1, target);
}
// Time Complexity: O(log n) - eliminates half the search space each iteration
// Space Complexity: O(1) iterative, O(log n) recursive due to call stack
// Prerequisite: Array must be sorted
```

### Sorting Algorithms: Organizing Chaos

#### Selection Sort: The Perfectionist's Choice
Like organizing a deck of cards by repeatedly finding the smallest card and placing it in the correct position.

```c
void selectionSort(int arr[], int size) {
    for (int i = 0; i < size - 1; i++) {
        int minIndex = i;
        
        // Find the minimum element
        for (int j = i + 1; j < size; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j;
            }
        }
        
        // Swap minimum with current position
        if (minIndex != i) {
            int temp = arr[i];
            arr[i] = arr[minIndex];
            arr[minIndex] = temp;
        }
    }
}
// Time Complexity: O(n²) in all cases
// Space Complexity: O(1)
// Characteristics: Not stable, minimum swaps (n-1)
// Best for: Small datasets, when memory writes are expensive
```

#### Bubble Sort: The Social Butterfly
Like bubbles rising to the surface - larger elements "bubble up" to their correct positions.

```c
void bubbleSort(int arr[], int size) {
    for (int i = 0; i < size - 1; i++) {
        bool swapped = false;
        
        for (int j = 0; j < size - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                // Swap adjacent elements
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
                swapped = true;
            }
        }
        
        // If no swapping occurred, array is sorted
        if (!swapped) break;
    }
}
// Time Complexity: O(n²) worst case, O(n) best case (already sorted)
// Space Complexity: O(1)
// Characteristics: Stable, adaptive
// Best for: Educational purposes, nearly sorted data
```

#### Insertion Sort: The Card Player's Method
Like sorting playing cards in your hand - pick up each card and insert it in the correct position among the already sorted cards.

```c
void insertionSort(int arr[], int size) {
    for (int i = 1; i < size; i++) {
        int key = arr[i];
        int j = i - 1;
        
        // Move elements greater than key one position ahead
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;
    }
}
// Time Complexity: O(n²) worst case, O(n) best case
// Space Complexity: O(1)
// Characteristics: Stable, adaptive, online
// Best for: Small datasets, nearly sorted data, real-time sorting
```

#### Merge Sort: The Divide and Conquer Master
Like organizing two pre-sorted piles of papers into one sorted pile.

```c
void merge(int arr[], int left, int mid, int right) {
    int leftSize = mid - left + 1;
    int rightSize = right - mid;
    
    // Create temporary arrays
    int* leftArr = malloc(leftSize * sizeof(int));
    int* rightArr = malloc(rightSize * sizeof(int));
    
    // Copy data to temporary arrays
    for (int i = 0; i < leftSize; i++)
        leftArr[i] = arr[left + i];
    for (int j = 0; j < rightSize; j++)
        rightArr[j] = arr[mid + 1 + j];
    
    // Merge the temporary arrays back
    int i = 0, j = 0, k = left;
    
    while (i < leftSize && j < rightSize) {
        if (leftArr[i] <= rightArr[j]) {
            arr[k] = leftArr[i];
            i++;
        } else {
            arr[k] = rightArr[j];
            j++;
        }
        k++;
    }
    
    // Copy remaining elements
    while (i < leftSize) {
        arr[k] = leftArr[i];
        i++;
        k++;
    }
    while (j < rightSize) {
        arr[k] = rightArr[j];
        j++;
        k++;
    }
    
    free(leftArr);
    free(rightArr);
}

void mergeSort(int arr[], int left, int right) {
    if (left < right) {
        int mid = left + (right - left) / 2;
        
        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }
}
// Time Complexity: O(n log n) in all cases
// Space Complexity: O(n)
// Characteristics: Stable, not adaptive
// Best for: Large datasets, when stability is required, external sorting
```

#### Shell Sort: The Gap-Reduction Genius
An improvement over insertion sort that compares elements separated by a gap, gradually reducing the gap.

```c
void shellSort(int arr[], int size) {
    // Start with a big gap, then reduce
    for (int gap = size / 2; gap > 0; gap /= 2) {
        // Perform gapped insertion sort
        for (int i = gap; i < size; i++) {
            int temp = arr[i];
            int j;
            
            for (j = i; j >= gap && arr[j - gap] > temp; j -= gap) {
                arr[j] = arr[j - gap];
            }
            arr[j] = temp;
        }
    }
}
// Time Complexity: O(n²) worst case, O(n log n) average
// Space Complexity: O(1)
// Characteristics: Not stable, adaptive
// Best for: Medium-sized datasets, when space is limited
```

#### Radix Sort: The Digital Organizer
Like sorting mail by zip code - first by the last digit, then second-to-last, and so on.

```c
int getMax(int arr[], int size) {
    int max = arr[0];
    for (int i = 1; i < size; i++) {
        if (arr[i] > max) max = arr[i];
    }
    return max;
}

void countingSort(int arr[], int size, int exp) {
    int output[size];
    int count[10] = {0};
    
    // Count occurrences of each digit
    for (int i = 0; i < size; i++) {
        count[(arr[i] / exp) % 10]++;
    }
    
    // Change count[i] to actual position
    for (int i = 1; i < 10; i++) {
        count[i] += count[i - 1];
    }
    
    // Build the output array
    for (int i = size - 1; i >= 0; i--) {
        output[count[(arr[i] / exp) % 10] - 1] = arr[i];
        count[(arr[i] / exp) % 10]--;
    }
    
    // Copy output array to arr
    for (int i = 0; i < size; i++) {
        arr[i] = output[i];
    }
}

void radixSort(int arr[], int size) {
    int max = getMax(arr, size);
    
    // Do counting sort for every digit
    for (int exp = 1; max / exp > 0; exp *= 10) {
        countingSort(arr, size, exp);
    }
}
// Time Complexity: O(d × (n + k)) where d is digits, k is range
// Space Complexity: O(n + k)
// Characteristics: Stable, not comparison-based
// Best for: Integers with limited range, strings of fixed length
```

## Chapter 2: Linked Lists - Dynamic Data Structures

### Understanding Dynamic vs Static Memory Allocation

**Static Memory Allocation** is like buying a house - you get a fixed amount of space that you own for the program's lifetime. The size is determined at compile time.

```c
int staticArray[100]; // Fixed size, allocated at compile time
```

**Dynamic Memory Allocation** is like renting hotel rooms - you can request rooms (memory) as needed during runtime and return them when done.

```c
int* dynamicArray = malloc(size * sizeof(int)); // Size determined at runtime
free(dynamicArray); // Must explicitly return memory
```

### Singly Linked List: The Chain of Connections

Think of a singly linked list like a treasure hunt where each clue leads to the next location, and the final clue says "treasure found" (NULL).

```c
typedef struct Node {
    int data;
    struct Node* next;
} Node;

typedef struct {
    Node* head;
    int size;
} LinkedList;

// Create a new node
Node* createNode(int data) {
    Node* newNode = malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Memory allocation failed!\n");
        return NULL;
    }
    newNode->data = data;
    newNode->next = NULL;
    return newNode;
}

// Initialize linked list
LinkedList* createLinkedList() {
    LinkedList* list = malloc(sizeof(LinkedList));
    list->head = NULL;
    list->size = 0;
    return list;
}

// Insert at beginning - O(1)
void insertAtBeginning(LinkedList* list, int data) {
    Node* newNode = createNode(data);
    if (newNode == NULL) return;
    
    newNode->next = list->head;
    list->head = newNode;
    list->size++;
}

// Insert at end - O(n)
void insertAtEnd(LinkedList* list, int data) {
    Node* newNode = createNode(data);
    if (newNode == NULL) return;
    
    if (list->head == NULL) {
        list->head = newNode;
    } else {
        Node* current = list->head;
        while (current->next != NULL) {
            current = current->next;
        }
        current->next = newNode;
    }
    list->size++;
}

// Insert at specific position - O(n)
void insertAtPosition(LinkedList* list, int data, int position) {
    if (position < 0 || position > list->size) {
        printf("Invalid position!\n");
        return;
    }
    
    if (position == 0) {
        insertAtBeginning(list, data);
        return;
    }
    
    Node* newNode = createNode(data);
    if (newNode == NULL) return;
    
    Node* current = list->head;
    for (int i = 0; i < position - 1; i++) {
        current = current->next;
    }
    
    newNode->next = current->next;
    current->next = newNode;
    list->size++;
}

// Delete node with specific value - O(n)
void deleteValue(LinkedList* list, int data) {
    if (list->head == NULL) return;
    
    // If head needs to be deleted
    if (list->head->data == data) {
        Node* temp = list->head;
        list->head = list->head->next;
        free(temp);
        list->size--;
        return;
    }
    
    Node* current = list->head;
    while (current->next != NULL && current->next->data != data) {
        current = current->next;
    }
    
    if (current->next != NULL) {
        Node* temp = current->next;
        current->next = current->next->next;
        free(temp);
        list->size--;
    }
}

// Search for a value - O(n)
int search(LinkedList* list, int data) {
    Node* current = list->head;
    int position = 0;
    
    while (current != NULL) {
        if (current->data == data) {
            return position;
        }
        current = current->next;
        position++;
    }
    return -1; // Not found
}

// Display the list - O(n)
void display(LinkedList* list) {
    Node* current = list->head;
    printf("List: ");
    while (current != NULL) {
        printf("%d -> ", current->data);
        current = current->next;
    }
    printf("NULL\n");
}
```

### Doubly Linked List: The Two-Way Street

Like a train where you can walk both towards the engine and towards the caboose.

```c
typedef struct DNode {
    int data;
    struct DNode* next;
    struct DNode* prev;
} DNode;

typedef struct {
    DNode* head;
    DNode* tail;
    int size;
} DoublyLinkedList;

// Create new node for doubly linked list
DNode* createDNode(int data) {
    DNode* newNode = malloc(sizeof(DNode));
    if (newNode == NULL) return NULL;
    
    newNode->data = data;
    newNode->next = NULL;
    newNode->prev = NULL;
    return newNode;
}

// Insert at beginning - O(1)
void insertAtBeginningDLL(DoublyLinkedList* list, int data) {
    DNode* newNode = createDNode(data);
    if (newNode == NULL) return;
    
    if (list->head == NULL) {
        list->head = list->tail = newNode;
    } else {
        newNode->next = list->head;
        list->head->prev = newNode;
        list->head = newNode;
    }
    list->size++;
}

// Insert at end - O(1) due to tail pointer
void insertAtEndDLL(DoublyLinkedList* list, int data) {
    DNode* newNode = createDNode(data);
    if (newNode == NULL) return;
    
    if (list->tail == NULL) {
        list->head = list->tail = newNode;
    } else {
        list->tail->next = newNode;
        newNode->prev = list->tail;
        list->tail = newNode;
    }
    list->size++;
}

// Delete a node - O(1) if node pointer is given
void deleteDNode(DoublyLinkedList* list, DNode* nodeToDelete) {
    if (nodeToDelete == NULL) return;
    
    // Update previous node
    if (nodeToDelete->prev != NULL) {
        nodeToDelete->prev->next = nodeToDelete->next;
    } else {
        list->head = nodeToDelete->next;
    }
    
    // Update next node
    if (nodeToDelete->next != NULL) {
        nodeToDelete->next->prev = nodeToDelete->prev;
    } else {
        list->tail = nodeToDelete->prev;
    }
    
    free(nodeToDelete);
    list->size--;
}
```

### Circular Linked List: The Endless Loop

Like a race track where after the finish line, you're back at the starting line.

```c
typedef struct CNode {
    int data;
    struct CNode* next;
} CNode;

typedef struct {
    CNode* last; // Points to the last node
    int size;
} CircularLinkedList;

// Insert at beginning - O(1)
void insertAtBeginningCLL(CircularLinkedList* list, int data) {
    CNode* newNode = createCNode(data);
    if (newNode == NULL) return;
    
    if (list->last == NULL) {
        newNode->next = newNode; // Points to itself
        list->last = newNode;
    } else {
        newNode->next = list->last->next; // New node points to first node
        list->last->next = newNode; // Last node points to new node
    }
    list->size++;
}

// Traverse circular list
void traverseCircular(CircularLinkedList* list) {
    if (list->last == NULL) return;
    
    CNode* current = list->last->next; // Start from first node
    do {
        printf("%d -> ", current->data);
        current = current->next;
    } while (current != list->last->next);
    printf("(back to start)\n");
}
```

### Header Linked List: The List with a Guide

A header node is like having a table of contents at the beginning of a book - it provides metadata about the list.

```c
typedef struct HeaderNode {
    int count;           // Number of elements
    struct Node* first;  // Pointer to first data node
} HeaderNode;

// The header node contains metadata and doesn't store actual data
HeaderNode* createHeaderList() {
    HeaderNode* header = malloc(sizeof(HeaderNode));
    header->count = 0;
    header->first = NULL;
    return header;
}
```

### Polynomial Arithmetic: Real-World Application

Polynomials like `3x³ + 2x² + 5x + 1` can be efficiently represented using linked lists.

```c
typedef struct PolyNode {
    int coefficient;
    int exponent;
    struct PolyNode* next;
} PolyNode;

typedef struct {
    PolyNode* head;
} Polynomial;

// Add two polynomials
Polynomial* addPolynomials(Polynomial* poly1, Polynomial* poly2) {
    Polynomial* result = createPolynomial();
    PolyNode* p1 = poly1->head;
    PolyNode* p2 = poly2->head;
    
    while (p1 != NULL && p2 != NULL) {
        if (p1->exponent > p2->exponent) {
            insertTerm(result, p1->coefficient, p1->exponent);
            p1 = p1->next;
        } else if (p1->exponent < p2->exponent) {
            insertTerm(result, p2->coefficient, p2->exponent);
            p2 = p2->next;
        } else { // Same exponent
            int sumCoeff = p1->coefficient + p2->coefficient;
            if (sumCoeff != 0) {
                insertTerm(result, sumCoeff, p1->exponent);
            }
            p1 = p1->next;
            p2 = p2->next;
        }
    }
    
    // Add remaining terms
    while (p1 != NULL) {
        insertTerm(result, p1->coefficient, p1->exponent);
        p1 = p1->next;
    }
    while (p2 != NULL) {
        insertTerm(result, p2->coefficient, p2->exponent);
        p2 = p2->next;
    }
    
    return result;
}
```

## Chapter 3: Stacks and Queues - Ordered Access Structures

### Stacks: Last In, First Out (LIFO)

Think of a stack like a pile of plates in a cafeteria - you can only add or remove plates from the top.

```c
#define MAX_SIZE 100

typedef struct {
    int items[MAX_SIZE];
    int top;
} ArrayStack;

// Array-based stack implementation
ArrayStack* createArrayStack() {
    ArrayStack* stack = malloc(sizeof(ArrayStack));
    stack->top = -1;
    return stack;
}

// Push operation - O(1)
void push(ArrayStack* stack, int data) {
    if (stack->top == MAX_SIZE - 1) {
        printf("Stack overflow!\n");
        return;
    }
    stack->items[++stack->top] = data;
}

// Pop operation - O(1)
int pop(ArrayStack* stack) {
    if (stack->top == -1) {
        printf("Stack underflow!\n");
        return -1;
    }
    return stack->items[stack->top--];
}

// Peek operation - O(1)
int peek(ArrayStack* stack) {
    if (stack->top == -1) {
        printf("Stack is empty!\n");
        return -1;
    }
    return stack->items[stack->top];
}

// Linked list-based stack (dynamic size)
typedef struct StackNode {
    int data;
    struct StackNode* next;
} StackNode;

typedef struct {
    StackNode* top;
    int size;
} LinkedStack;

void pushLinked(LinkedStack* stack, int data) {
    StackNode* newNode = malloc(sizeof(StackNode));
    if (newNode == NULL) {
        printf("Memory allocation failed!\n");
        return;
    }
    newNode->data = data;
    newNode->next = stack->top;
    stack->top = newNode;
    stack->size++;
}

int popLinked(LinkedStack* stack) {
    if (stack->top == NULL) {
        printf("Stack underflow!\n");
        return -1;
    }
    StackNode* temp = stack->top;
    int data = temp->data;
    stack->top = stack->top->next;
    free(temp);
    stack->size--;
    return data;
}
```

### Stack Applications: Real-World Problem Solving

#### 1. Expression Evaluation and Conversion

**Infix to Postfix Conversion:**
Converting `A + B * C` to `A B C * +`

```c
int precedence(char op) {
    switch (op) {
        case '+':
        case '-':
            return 1;
        case '*':
        case '/':
            return 2;
        case '^':
            return 3;
        default:
            return 0;
    }
}

void infixToPostfix(char* infix, char* postfix) {
    ArrayStack* stack = createArrayStack();
    int i = 0, j = 0;
    
    while (infix[i] != '\0') {
        char current = infix[i];
        
        if (isalnum(current)) {
            // Operand: add to output
            postfix[j++] = current;
        } else if (current == '(') {
            // Left parenthesis: push to stack
            push(stack, current);
        } else if (current == ')') {
            // Right parenthesis: pop until left parenthesis
            while (stack->top != -1 && peek(stack) != '(') {
                postfix[j++] = pop(stack);
            }
            pop(stack); // Remove left parenthesis
        } else {
            // Operator: pop operators with higher or equal precedence
            while (stack->top != -1 && 
                   precedence(peek(stack)) >= precedence(current)) {
                postfix[j++] = pop(stack);
            }
            push(stack, current);
        }
        i++;
    }
    
    // Pop remaining operators
    while (stack->top != -1) {
        postfix[j++] = pop(stack);
    }
    postfix[j] = '\0';
}

// Evaluate postfix expression
int evaluatePostfix(char* postfix) {
    ArrayStack* stack = createArrayStack();
    int i = 0;
    
    while (postfix[i] != '\0') {
        char current = postfix[i];
        
        if (isdigit(current)) {
            // Convert character to integer and push
            push(stack, current - '0');
        } else {
            // Operator: pop two operands
            int operand2 = pop(stack);
            int operand1 = pop(stack);
            int result;
            
            switch (current) {
                case '+': result = operand1 + operand2; break;
                case '-': result = operand1 - operand2; break;
                case '*': result = operand1 * operand2; break;
                case '/': result = operand1 / operand2; break;
                case '^': result = pow(operand1, operand2); break;
            }
            push(stack, result);
        }
        i++;
    }
    
    return pop(stack); // Final result
}
```

#### 2. Function Call Management

Every programming language uses a stack to manage function calls - this is called the **call stack**.

```c
// Example: Factorial calculation showing stack frames
int factorial(int n) {
    // Base case
    if (n <= 1) return 1;
    
    // Recursive case - creates new stack frame
    return n * factorial(n - 1);
}

/*
Call Stack for factorial(4):
+-------------------+
| factorial(1) = 1  | <- Top
+-------------------+
| factorial(2) = 2  |
+-------------------+
| factorial(3) = 6  |
+-------------------+
| factorial(4) = 24 | <- Bottom
+-------------------+
*/
```

#### 3. Balanced Parentheses Checking

```c
bool isBalanced(char* expression) {
    ArrayStack* stack = createArrayStack();
    
    for (int i = 0; expression[i] != '\0'; i++) {
        char current = expression[i];
        
        // Push opening brackets
        if (current == '(' || current == '[' || current == '{') {
            push(stack, current);
        }
        // Check closing brackets
        else if (current == ')' || current == ']' || current == '}') {
            if (stack->top == -1) return false; // No matching opening bracket
            
            char top = pop(stack);
            if ((current == ')' && top != '(') ||
                (current == ']' && top != '[') ||
                (current == '}' && top != '{')) {
                return false;
            }
        }
    }
    
    return stack->top == -1; // Stack should be empty for balanced expression
}
```

### Queues: First In, First Out (FIFO)

Think of a queue like a line at a coffee shop - the first person in line is the first to be served.

```c
#define MAX_QUEUE_SIZE 100

// Array-based circular queue
typedef struct {
    int items[MAX_QUEUE_SIZE];
    int front, rear;
    int size;
} CircularQueue;

CircularQueue* createQueue() {
    CircularQueue* queue = malloc(sizeof(CircularQueue));
    queue->front = 0;
    queue->rear = -1;
    queue->size = 0;
    return queue;
}

// Enqueue operation - O(1)
void enqueue(CircularQueue* queue, int data) {
    if (queue->size == MAX_QUEUE_SIZE) {
        printf("Queue overflow!\n");
        return;
    }
    
    queue->rear = (queue->rear + 1) % MAX_QUEUE_SIZE;
    queue->items[queue->rear] = data;
    queue->size++;
}

// Dequeue operation - O(1)
int dequeue(CircularQueue* queue) {
    if (queue->size == 0) {
        printf("Queue underflow!\n");
        return -1;
    }
    
    int data = queue->items[queue->front];
    queue->front = (queue->front + 1) % MAX_QUEUE_SIZE;
    queue->size--;
    return data;
}

// Linked list-based queue (unlimited size)
typedef struct QueueNode {
    int data;
    struct QueueNode* next;
} QueueNode;

typedef struct {
    QueueNode* front;
    QueueNode* rear;
    int size;
} LinkedQueue;

void enqueueLinked(LinkedQueue* queue, int data) {
    QueueNode* newNode = malloc(sizeof(QueueNode));
    if (newNode == NULL) return;
    
    newNode->data = data;
    newNode->next = NULL;
    
    if (queue->rear == NULL) {
        queue->front = queue->rear = newNode;
    } else {
        queue->rear->next = newNode;
        queue->rear = newNode;
    }
    queue->size++;
}

int dequeueLinked(LinkedQueue* queue) {
    if (queue->front == NULL) {
        printf("Queue is empty!\n");
        return -1;
    }
    
    QueueNode* temp = queue->front;
    int data = temp->data;
    queue->front = queue->front->next;
    
    if (queue->front == NULL) {
        queue->rear = NULL; // Queue became empty
    }
    
    free(temp);
    queue->size--;
    return data;
}
```

### Types of Queues and Applications

#### 1. Priority Queue
Like an emergency room - patients with more severe conditions are treated first, regardless of arrival time.

```c
typedef struct PriorityElement {
    int data;
    int priority;
} PriorityElement;

typedef struct {
    PriorityElement items[MAX_QUEUE_SIZE];
    int size;
} PriorityQueue;

void enqueuePriority(PriorityQueue* pq, int data, int priority) {
    if (pq->size == MAX_QUEUE_SIZE) {
        printf("Priority queue overflow!\n");
        return;
    }
    
    // Find correct position to insert
    int i = pq->size - 1;
    while (i >= 0 && pq->items[i].priority < priority) {
        pq->items[i + 1] = pq->items[i];
        i--;
    }
    
    pq->items[i + 1].data = data;
    pq->items[i + 1].priority = priority;
    pq->size++;
}

int dequeuePriority(PriorityQueue* pq) {
    if (pq->size == 0) {
        printf("Priority queue is empty!\n");
        return -1;
    }
    
    int data = pq->items[0].data;
    
    // Shift all elements left
    for (int i = 0; i < pq->size - 1; i++) {
        pq->items[i] = pq->items[i + 1];
    }
    pq->size--;
    
    return data;
}
```

#### 2. Deque (Double-Ended Queue)
Like a train where you can board from either the front or rear car.

```c
typedef struct DequeNode {
    int data;
    struct DequeNode* next;
    struct DequeNode* prev;
} DequeNode;

typedef struct {
    DequeNode* front;
    DequeNode* rear;
    int size;
} Deque;

void insertFront(Deque* deque, int data) {
    DequeNode* newNode = malloc(sizeof(DequeNode));
    newNode->data = data;
    newNode->prev = NULL;
    newNode->next = deque->front;
    
    if (deque->front != NULL) {
        deque->front->prev = newNode;
    } else {
        deque->rear = newNode;
    }
    
    deque->front = newNode;
    deque->size++;
}

void insertRear(Deque* deque, int data) {
    DequeNode* newNode = malloc(sizeof(DequeNode));
    newNode->data = data;
    newNode->next = NULL;
    newNode->prev = deque->rear;
    
    if (deque->rear != NULL) {
        deque->rear->next = newNode;
    } else {
        deque->front = newNode;
    }
    
    deque->rear = newNode;
    deque->size++;
}
```

### Multi-Stacks and Multi-Queues

#### Multi-Stack Implementation
Imagine dividing a single array into multiple stacks - like having multiple piles of plates on one large tray.

```c
typedef struct {
    int* array;
    int* tops;
    int* sizes;
    int numStacks;
    int totalSize;
} MultiStack;

MultiStack* createMultiStack(int numStacks, int totalSize) {
    MultiStack* ms = malloc(sizeof(MultiStack));
    ms->array = malloc(totalSize * sizeof(int));
    ms->tops = malloc(numStacks * sizeof(int));
    ms->sizes = malloc(numStacks * sizeof(int));
    ms->numStacks = numStacks;
    ms->totalSize = totalSize;
    
    int stackSize = totalSize / numStacks;
    for (int i = 0; i < numStacks; i++) {
        ms->tops[i] = i * stackSize - 1; // Initialize tops
        ms->sizes[i] = stackSize;
    }
    
    return ms;
}

void multiPush(MultiStack* ms, int stackNum, int data) {
    if (ms->tops[stackNum] >= (stackNum + 1) * ms->sizes[stackNum] - 1) {
        printf("Stack %d overflow!\n", stackNum);
        return;
    }
    
    ms->array[++ms->tops[stackNum]] = data;
}

int multiPop(MultiStack* ms, int stackNum) {
    if (ms->tops[stackNum] < stackNum * ms->sizes[stackNum]) {
        printf("Stack %d underflow!\n", stackNum);
        return -1;
    }
    
    return ms->array[ms->tops[stackNum]--];
}
```

### Quick Sort: The Divide and Conquer Algorithm

Quick Sort uses a stack-like approach through recursion, making it a perfect example of stack applications.

```c
int partition(int arr[], int low, int high) {
    int pivot = arr[high]; // Choose last element as pivot
    int i = low - 1; // Index of smaller element
    
    for (int j = low; j < high; j++) {
        if (arr[j] < pivot) {
            i++;
            // Swap arr[i] and arr[j]
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
        }
    }
    
    // Place pivot in correct position
    int temp = arr[i + 1];
    arr[i + 1] = arr[high];
    arr[high] = temp;
    
    return i + 1;
}

void quickSort(int arr[], int low, int high) {
    if (low < high) {
        int pivotIndex = partition(arr, low, high);
        
        // Recursively sort elements before and after partition
        quickSort(arr, low, pivotIndex - 1);
        quickSort(arr, pivotIndex + 1, high);
    }
}

// Iterative version using explicit stack
void quickSortIterative(int arr[], int size) {
    ArrayStack* stack = createArrayStack();
    
    push(stack, 0);      // Starting index
    push(stack, size - 1); // Ending index
    
    while (stack->top != -1) {
        int high = pop(stack);
        int low = pop(stack);
        
        if (low < high) {
            int pivotIndex = partition(arr, low, high);
            
            // Push left subarray bounds
            push(stack, low);
            push(stack, pivotIndex - 1);
            
            // Push right subarray bounds
            push(stack, pivotIndex + 1);
            push(stack, high);
        }
    }
}
// Time Complexity: O(n log n) average, O(n²) worst case
// Space Complexity: O(log n) average, O(n) worst case
// Best for: Large datasets, when average performance is acceptable
```

### Understanding Recursion: The Self-Calling Function

Recursion is like Russian nesting dolls - each doll contains a smaller version of itself.

```c
// Classic examples demonstrating recursion principles

// 1. Factorial: n! = n × (n-1)!
int factorial(int n) {
    // Base case: stops the recursion
    if (n <= 1) return 1;
    
    // Recursive case: function calls itself with smaller input
    return n * factorial(n - 1);
}

// 2. Fibonacci: F(n) = F(n-1) + F(n-2)
int fibonacci(int n) {
    if (n <= 1) return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
}

// Optimized Fibonacci using memoization
int fibonacciMemo(int n, int* memo) {
    if (n <= 1) return n;
    if (memo[n] != -1) return memo[n];
    
    memo[n] = fibonacciMemo(n - 1, memo) + fibonacciMemo(n - 2, memo);
    return memo[n];
}

// 3. Tower of Hanoi: Classic recursion problem
void towerOfHanoi(int n, char source, char destination, char auxiliary) {
    if (n == 1) {
        printf("Move disk 1 from %c to %c\n", source, destination);
        return;
    }
    
    // Move n-1 disks from source to auxiliary
    towerOfHanoi(n - 1, source, auxiliary, destination);
    
    // Move the largest disk from source to destination
    printf("Move disk %d from %c to %c\n", n, source, destination);
    
    // Move n-1 disks from auxiliary to destination
    towerOfHanoi(n - 1, auxiliary, destination, source);
}

// 4. Binary Search (Recursive)
int binarySearchRecursive(int arr[], int left, int right, int target) {
    if (left > right) return -1;
    
    int mid = left + (right - left) / 2;
    
    if (arr[mid] == target) return mid;
    else if (arr[mid] > target) 
        return binarySearchRecursive(arr, left, mid - 1, target);
    else 
        return binarySearchRecursive(arr, mid + 1, right, target);
}
```

---

# UNIT 2: Non-Linear Data Structures - Trees

## Chapter 4: Trees - Hierarchical Data Organization

### Understanding Tree Terminology

A tree is like a family tree or organizational chart - it has a hierarchical structure with clear parent-child relationships.

**Key Terminology:**
- **Root**: The topmost node (like the CEO in a company)
- **Parent**: A node that has children (like a manager)
- **Child**: A node that has a parent (like an employee)
- **Leaf**: A node with no children (like entry-level employees)
- **Height**: Maximum distance from root to any leaf
- **Depth**: Distance from root to a specific node
- **Degree**: Number of children a node has

### Binary Trees: The Foundation

A binary tree is like a decision tree where each person can make at most two choices.

```c
typedef struct TreeNode {
    int data;
    struct TreeNode* left;
    struct TreeNode* right;
} TreeNode;

// Create a new tree node
TreeNode* createTreeNode(int data) {
    TreeNode* newNode = malloc(sizeof(TreeNode));
    if (newNode == NULL) return NULL;
    
    newNode->data = data;
    newNode->left = NULL;
    newNode->right = NULL;
    return newNode;
}

// Tree properties calculations
int getHeight(TreeNode* root) {
    if (root == NULL) return -1;
    
    int leftHeight = getHeight(root->left);
    int rightHeight = getHeight(root->right);
    
    return 1 + (leftHeight > rightHeight ? leftHeight : rightHeight);
}

int countNodes(TreeNode* root) {
    if (root == NULL) return 0;
    return 1 + countNodes(root->left) + countNodes(root->right);
}

int countLeaves(TreeNode* root) {
    if (root == NULL) return 0;
    if (root->left == NULL && root->right == NULL) return 1;
    return countLeaves(root->left) + countLeaves(root->right);
}
```

### Tree Traversals: Visiting Every Node

Tree traversal is like exploring a museum - there are different systematic ways to visit every room.

#### Recursive Traversals

```c
// Preorder: Root -> Left -> Right
// Like reading a book's table of contents
void preorderTraversal(TreeNode* root) {
    if (root != NULL) {
        printf("%d ", root->data);        // Visit root
        preorderTraversal(root->left);    // Traverse left subtree
        preorderTraversal(root->right);   // Traverse right subtree
    }
}

// Inorder: Left -> Root -> Right
// For BST, this gives sorted order
void inorderTraversal(TreeNode* root) {
    if (root != NULL) {
        inorderTraversal(root->left);     // Traverse left subtree
        printf("%d ", root->data);        // Visit root
        inorderTraversal(root->right);    // Traverse right subtree
    }
}

// Postorder: Left -> Right -> Root
// Like calculating directory sizes (need children first)
void postorderTraversal(TreeNode* root) {
    if (root != NULL) {
        postorderTraversal(root->left);   // Traverse left subtree
        postorderTraversal(root->right);  // Traverse right subtree
        printf("%d ", root->data);        // Visit root
    }
}
```

#### Non-Recursive Traversals Using Stacks

```c
// Non-recursive preorder traversal
void preorderNonRecursive(TreeNode* root) {
    if (root == NULL) return;
    
    ArrayStack* stack = createArrayStack();
    push(stack, (int)root);
    
    while (stack->top != -1) {
        TreeNode* current = (TreeNode*)pop(stack);
        printf("%d ", current->data);
        
        // Push right child first (so left is processed first)
        if (current->right) push(stack, (int)current->right);
        if (current->left) push(stack, (int)current->left);
    }
}

// Non-recursive inorder traversal
void inorderNonRecursive(TreeNode* root) {
    ArrayStack* stack = createArrayStack();
    TreeNode* current = root;
    
    while (current != NULL || stack->top != -1) {
        // Go to the leftmost node
        while (current != NULL) {
            push(stack, (int)current);
            current = current->left;
        }
        
        // Current is NULL, so pop from stack
        current = (TreeNode*)pop(stack);
        printf("%d ", current->data);
        
        // Visit right subtree
        current = current->right;
    }
}

// Level-order traversal (Breadth-First)
void levelOrderTraversal(TreeNode* root) {
    if (root == NULL) return;
    
    LinkedQueue* queue = createLinkedQueue();
    enqueueLinked(queue, (int)root);
    
    while (queue->size > 0) {
        TreeNode* current = (TreeNode*)dequeueLinked(queue);
        printf("%d ", current->data);
        
        if (current->left) enqueueLinked(queue, (int)current->left);
        if (current->right) enqueueLinked(queue, (int)current->right);
    }
}
```

### Binary Search Trees (BST): Ordered Trees

A BST is like a well-organized library where books are arranged systematically - smaller call numbers on the left, larger on the right.

**BST Property**: For every node:
- All nodes in left subtree < node's value
- All nodes in right subtree > node's value

```c
// Insert into BST
TreeNode* insertBST(TreeNode* root, int data) {
    // Base case: create new node
    if (root == NULL) {
        return createTreeNode(data);
    }
    
    // Recursive case: choose left or right subtree
    if (data < root->data) {
        root->left = insertBST(root->left, data);
    } else if (data > root->data) {
        root->right = insertBST(root->right, data);
    }
    // If data equals root->data, do nothing (no duplicates)
    
    return root;
}

// Search in BST - O(log n) average, O(n) worst case
TreeNode* searchBST(TreeNode* root, int data) {
    if (root == NULL || root->data == data) {
        return root;
    }
    
    if (data < root->data) {
        return searchBST(root->left, data);
    }
    return searchBST(root->right, data);
}

// Find minimum value node (leftmost node)
TreeNode* findMin(TreeNode* root) {
    while (root && root->left) {
        root = root->left;
    }
    return root;
}

// Delete from BST - most complex operation
TreeNode* deleteBST(TreeNode* root, int data) {
    if (root == NULL) return root;
    
    if (data < root->data) {
        root->left = deleteBST(root->left, data);
    } else if (data > root->data) {
        root->right = deleteBST(root->right, data);
    } else {
        // Node to be deleted found
        
        // Case 1: Node with only right child or no child
        if (root->left == NULL) {
            TreeNode* temp = root->right;
            free(root);
            return temp;
        }
        // Case 2: Node with only left child
        else if (root->right == NULL) {
            TreeNode* temp = root->left;
            free(root);
            return temp;
        }
        
        // Case 3: Node with two children
        TreeNode* temp = findMin(root->right); // Inorder successor
        root->data = temp->data; // Copy successor's data
        root->right = deleteBST(root->right, temp->data); // Delete successor
    }
    
    return root;
}
```

### Threaded Binary Trees: Optimizing Traversal

In a normal binary tree, many pointers are NULL (wasted space). Threaded binary trees use these NULL pointers to store threads (links) to inorder predecessor or successor.

```c
typedef struct ThreadedNode {
    int data;
    struct ThreadedNode* left;
    struct ThreadedNode* right;
    bool leftThread;  // true if left pointer is a thread
    bool rightThread; // true if right pointer is a thread
} ThreadedNode;

// Inorder traversal without recursion or stack
void inorderThreaded(ThreadedNode* root) {
    ThreadedNode* current = root;
    
    // Find leftmost node
    while (current->left != NULL && !current->leftThread) {
        current = current->left;
    }
    
    while (current != NULL) {
        printf("%d ", current->data);
        
        // If right is a thread, follow it
        if (current->rightThread) {
            current = current->right;
        } else {
            // Move to rightmost node of right subtree
            current = current->right;
            while (current != NULL && !current->leftThread) {
                current = current->left;
            }
        }
    }
}
```

### Tree Sort: Sorting Using BST

Tree sort builds a BST from the input array, then performs inorder traversal to get sorted elements.

```c
void treeSort(int arr[], int size) {
    TreeNode* root = NULL;
    
    // Build BST
    for (int i = 0; i < size; i++) {
        root = insertBST(root, arr[i]);
    }
    
    // Inorder traversal gives sorted order
    int index = 0;
    void storeSorted(TreeNode* node, int arr[], int* index) {
        if (node != NULL) {
            storeSorted(node->left, arr, index);
            arr[(*index)++] = node->data;
            storeSorted(node->right, arr, index);
        }
    }
    
    storeSorted(root, arr, &index);
}
// Time Complexity: O(n log n) average, O(n²) worst case
// Space Complexity: O(n)
// Advantage: In-place if we modify the BST structure
```

### Tries: Efficient String Storage

A Trie (prefix tree) is like a word dictionary where each path from root to a node represents a word prefix.

```c
#define ALPHABET_SIZE 26

typedef struct TrieNode {
    struct TrieNode* children[ALPHABET_SIZE];
    bool isEndOfWord;
} TrieNode;

TrieNode* createTrieNode() {
    TrieNode* node = malloc(sizeof(TrieNode));
    node->isEndOfWord = false;
    
    for (int i = 0; i < ALPHABET_SIZE; i++) {
        node->children[i] = NULL;
    }
    return node;
}

// Insert word into trie
void insertTrie(TrieNode* root, char* word) {
    TrieNode* current = root;
    
    for (int i = 0; word[i] != '\0'; i++) {
        int index = word[i] - 'a'; // Convert to index (0-25)
        
        if (current->children[index] == NULL) {
            current->children[index] = createTrieNode();
        }
        current = current->children[index];
    }
    current->isEndOfWord = true;
}

// Search word in trie
bool searchTrie(TrieNode* root, char* word) {
    TrieNode* current = root;
    
    for (int i = 0; word[i] != '\0'; i++) {
        int index = word[i] - 'a';
        
        if (current->children[index] == NULL) {
            return false;
        }
        current = current->children[index];
    }
    return current->isEndOfWord;
}

// Check if any word starts with given prefix
bool startsWith(TrieNode* root, char* prefix) {
    TrieNode* current = root;
    
    for (int i = 0; prefix[i] != '\0'; i++) {
        int index = prefix[i] - 'a';
        
        if (current->children[index] == NULL) {
            return false;
        }
        current = current->children[index];
    }
    return true;
}
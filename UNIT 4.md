# UNIT 4: Hashing and File Structures

## Chapter 1: Hashing - Organizing Data for Instant Access

### What is Hashing?

Hashing is like organizing a library such that each book is directly placed in a specific location based on its title, without needing to search shelf-by-shelf.

Hashing uses a **hash function** to convert keys (e.g., strings or numbers) into array indices in a **hash table**.

---

### Hash Tables - The Core Data Structure

A hash table is an array where each index can store one or more data elements. Think of it like a hotel where rooms (indexes) are assigned based on a guest’s name (key).

```c
#define TABLE_SIZE 10

typedef struct Item {
    int key;
    int value;
    struct Item* next; // For chaining
} Item;

Item* hashTable[TABLE_SIZE];
```

---

### Hash Functions - Mapping Keys to Indices

A good hash function should:

* Be fast
* Distribute keys uniformly
* Minimize collisions

**Example Hash Function:**

```c
int hash(int key) {
    return key % TABLE_SIZE;
}
```

---

### Collision Resolution Techniques

#### 1. Chaining (Separate Chaining)

Multiple items at the same index are stored using a linked list.

```c
void insert(int key, int value) {
    int index = hash(key);
    Item* newItem = malloc(sizeof(Item));
    newItem->key = key;
    newItem->value = value;
    newItem->next = hashTable[index];
    hashTable[index] = newItem;
}
```

**Use Case Analogy:** Like stacking files for different people with the same last name into the same cabinet drawer.

#### 2. Open Addressing

All elements are stored in the table directly. If the spot is taken, probe for the next one.

* **Linear Probing:**

```c
int linearProbe(int key) {
    int index = hash(key);
    while (table[index] != -1) {
        index = (index + 1) % TABLE_SIZE;
    }
    return index;
}
```

* **Quadratic Probing** and **Double Hashing** can reduce clustering.

**Real-World Analogy:** Like finding the next empty seat in a row when your preferred seat is taken.

---

### Performance Comparison

| Technique       | Time Complexity (Search/Insert) | Space       | Strengths                | Weaknesses        |
| --------------- | ------------------------------- | ----------- | ------------------------ | ----------------- |
| Chaining        | O(1) avg, O(n) worst            | Extra space | Handles collisions well  | Requires pointers |
| Open Addressing | O(1) avg, O(n) worst            | In-place    | Better cache performance | Clustering issues |

---

## Chapter 2: File Organization Techniques

### 1. Sequential File Organization

Records are stored one after another in order.

```c
struct Record {
    int id;
    char name[50];
};

void createSequentialFile() {
    FILE* fp = fopen("data.dat", "wb");
    struct Record r = {1, "Alice"};
    fwrite(&r, sizeof(struct Record), 1, fp);
    fclose(fp);
}
```

**Best Use:** When records are read in bulk or sorted.

### 2. Index Sequential

Maintains an index for fast access + sequential file for storage.

**Real-World Analogy:** Like an address book with an index tab for each letter.

### 3. Relative File Organization

Records are stored at positions calculated by a hash function.

```c
long getPosition(int key) {
    return key * sizeof(struct Record);
}
```

### File Operations in C

* `fopen`, `fread`, `fwrite`, `fseek`, `ftell`

```c
fseek(fp, getPosition(key), SEEK_SET);
fread(&r, sizeof(r), 1, fp);
```

---

## Chapter 3: External Sorting

When data is too large for RAM, external sorting is used.

### 1. Merging Files

#### Ordered Merge

Used to merge two or more sorted files into a new sorted output.

**Analogy:** Like merging two pre-sorted lines at a buffet into one line while keeping the order intact.

```c
void mergeOrderedFiles(FILE* f1, FILE* f2, FILE* fout) {
    int a, b;
    int read1 = fread(&a, sizeof(int), 1, f1);
    int read2 = fread(&b, sizeof(int), 1, f2);

    while (read1 && read2) {
        if (a <= b) {
            fwrite(&a, sizeof(int), 1, fout);
            read1 = fread(&a, sizeof(int), 1, f1);
        } else {
            fwrite(&b, sizeof(int), 1, fout);
            read2 = fread(&b, sizeof(int), 1, f2);
        }
    }
    while (read1) {
        fwrite(&a, sizeof(int), 1, fout);
        read1 = fread(&a, sizeof(int), 1, f1);
    }
    while (read2) {
        fwrite(&b, sizeof(int), 1, fout);
        read2 = fread(&b, sizeof(int), 1, f2);
    }
}
```

#### Unordered Merge

First sort individual files using an internal sort, then perform ordered merge.

### 2. Natural Merge Sort

This method identifies already sorted "runs" in data and merges them, reducing the number of passes needed.

**Analogy:** Like a partially sorted deck of cards that’s easier to merge.

### 3. Balanced Merge

Splits data equally into multiple auxiliary files and repeatedly merges them in pairs, maintaining balance.

**Use Case:** Useful when the number of runs is a power of 2.

### 4. K-Way Merge

Merges more than 2 files simultaneously using a priority queue to pick the smallest element across files.

**Analogy:** Like monitoring multiple queues and always serving the person with the smallest number ticket.

### 5. Polyphase Merge

Minimizes the number of temporary files and dummy runs using Fibonacci sequence distribution of data.

**Efficiency:** Reduces overhead compared to balanced merge when the number of runs isn’t a power of 2.

---

## Summary Table

| Concept               | Best For                     | Real Example              |
| --------------------- | ---------------------------- | ------------------------- |
| Hash Table (Chaining) | Fast lookups with collisions | Dictionaries              |
| Open Addressing       | Space-efficient hash tables  | CPU register lookup       |
| Sequential Files      | Reading in bulk              | Transaction logs          |
| Index Sequential      | Mixed access patterns        | Phone directory           |
| Relative Files        | Key-based access             | Bank account databases    |
| External Sorting      | Large-scale data sorting     | Sorting terabytes of logs |

---

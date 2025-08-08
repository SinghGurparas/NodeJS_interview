# DSA

## INDEX

- [DSA](#dsa)
  - [INDEX](#index)
    - [1. Arrays](#1-arrays)
    - [2. Linked Lists](#2-linked-lists)
    - [3. Stacks](#3-stacks)
    - [4. Queues](#4-queues)
    - [5. Hash Tables (Hash Maps)](#5-hash-tables-hash-maps)
    - [6. Trees](#6-trees)
    - [7. Graphs](#7-graphs)
    - [8. Heaps](#8-heaps)
    - [9. Tries (Prefix Trees)](#9-tries-prefix-trees)
    - [10. Self-Balancing Binary Search Trees](#10-self-balancing-binary-search-trees)
    - [11. Hash Sets](#11-hash-sets)
  
### 1. Arrays

An array is a collection of elements, all of the same data type, stored in contiguous memory locations. It's great for quick access to any element via its index.

- **Phonebook Example:** Storing a list of names. To find 'Charlie', you'd access `names[2]`.
- **Visualization:** `['Alice', 'Bob', 'Charlie', 'Diana']`
- **Key Operations:**
  - **Access:** Very fast (O(1)).
  - **Add/Delete:** Can be slow (O(n)) as other elements may need to shift.

---

### 2. Linked Lists

A linked list is a sequence of nodes where each node contains data and a pointer to the next node. Nodes are not stored in contiguous memory.

- **Phonebook Example:** A list of names where you frequently add or remove contacts.
- **Visualization:** `['Alice'] -> ['Bob'] -> ['Charlie'] -> ['Diana']`
- **Key Operations:**
  - **Access:** Slow (O(n)) as you must traverse from the start.
  - **Add/Delete:** Very fast (O(1)) as you only need to update a few pointers.

---

### 3. Stacks

A stack is a linear data structure that follows the **Last-In, First-Out (LIFO)** principle. Think of a stack of plates.

- **Phonebook Example:** A call history or "undo" function. The last call you made is the first one you'd see in the history.
- **Visualization:** `[bottom]`...`['Alice']`...`['Bob']`...`['Charlie'] [top]`
- **Key Operations:**
  - **Push:** Add an item to the top.
  - **Pop:** Remove the item from the top.
  - **Peek:** Look at the top item without removing it.

---

### 4. Queues

A queue is a linear data structure that follows the **First-In, First-Out (FIFO)** principle. It's like a line of people waiting.

- **Phonebook Example:** A call center's incoming calls. The first call to arrive is the first one to be answered.
- **Visualization:** `[front] 'Alice' -> 'Bob' -> 'Charlie' [back]`
- **Key Operations:**
  - **Enqueue:** Add an item to the back.
  - **Dequeue:** Remove the item from the front.
  - **Front:** Look at the front item without removing it.

---

### 5. Hash Tables (Hash Maps)

A hash table stores data as key-value pairs using a **hash function** to quickly find the value associated with a key.

- **Phonebook Example:** The most efficient way to store and look up a person's number by their name. The name is the key, and the number is the value.
- **Visualization:** `{ 'Alice': '123', 'Bob': '456' }`
- **Key Operations:**
  - **Search/Insert/Delete:** Extremely fast on average (O(1)).

---

### 6. Trees

A tree is a non-linear, hierarchical data structure with a root and child nodes.

- **Phonebook Example:** Organizing contacts by company, department, and then name.
- **Key Operations:**
  - **Search:** Can be efficient in a **Binary Search Tree** (O(log n)).
  - **Traversal:** Visiting every node in a specific order.

---

### 7. Graphs

A graph is a collection of nodes (vertices) and edges connecting them. It models networks and relationships.

- **Phonebook Example:** Your social network, where each contact is a node, and an edge represents a friendship. You could use it to find mutual friends or the shortest connection path between two people.
- **Key Operations:**
  - **Traversal:** Algorithms like BFS or DFS to explore relationships.
  - **Pathfinding:** Finding the shortest path between two nodes.

---

### 8. Heaps

A heap is a specialized tree that follows the heap property (parent is always greater or smaller than its children). It's used to implement **priority queues**.

- **Phonebook Example:** Prioritizing calls. A min-heap could be used to prioritize contacts with the shortest average call time.
- **Key Operations:**
  - **Insert/Extract:** Very efficient (O(log n)).

---

### 9. Tries (Prefix Trees)

A trie is a tree-like structure used for storing strings. Each node represents a character.

- **Phonebook Example:** Implementing auto-completion in a search bar. As you type 'Cha', the trie quickly finds and suggests 'Charlie'.
- **Key Operations:**
  - **Search for Prefix:** Extremely fast (O(k), where k is the length of the prefix).

---

### 10. Self-Balancing Binary Search Trees

These are binary search trees that automatically maintain balance, ensuring efficient lookups, insertions, and deletions.

- **Phonebook Example:** Storing a contact list that is always sorted, guaranteeing that all operations remain efficient (O(log n)) even with a large number of contacts.

---

### 11. Hash Sets

A hash set stores unique elements and provides very fast checks for the existence of an element. It's similar to a hash table but without values.

- **Phonebook Example:** Storing a list of unique phone numbers to quickly check if a new number already exists in the phonebook.
- **Key Operations:**
  - **Add/Remove/Check for Existence:** Very fast on average (O(1)).

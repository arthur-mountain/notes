# Link List

A linked list is a `linear data structure` where each element, known as a node, points to the next or previous node in the sequence.

Each node contains two parts: data and a reference (or link) to the next or previous node in the sequence.

Nodes allow for dynamic memory allocation at runtime.

Benefits:

1. **Dynamic Memory Allocation**: Linked lists are used in dynamic memory allocation algorithms, allowing for efficient memory management and utilization.

2. **Efficient Insertions/Deletions**: Insertions and deletions can be more efficient at beginning or end compared to arrays.

Disadvantages:

1. **More memory usage**: It needs to store pointers that reference other nodes.

## Single Link List

The each node `points to the next node in the sequence`.

The first node is called the head, and the last node is called the tail, which points to null.

Singly linked lists allow for efficient `insertion` and `deletion` of elements `at the beginning`.

### Big O

|                 |                Access                 |               Search                |          Insert(at beginning)          |                      Insert(at middle or end)                       |        Deletion(at beginning)         |                     Deletion(at middle or end)                     |
| :-------------: | :-----------------------------------: | :---------------------------------: | :------------------------------------: | :-----------------------------------------------------------------: | :-----------------------------------: | :----------------------------------------------------------------: |
|   Description   | Accessing an element using its index. | Finding an element (linear search). | Inserting an element at the beginning. | Inserting an element at the end or middle needs iterating the list. | Deleting an element at the beginning. | Deleting an element at the middle or end needs iterating the list. |
| Time complexity |                 O(n)                  |                O(n)                 |               **_O(1)_**               |                                O(n)                                 |              **_O(1)_**               |                                O(n)                                |

---

## Double Link List

The each node `points to both the next and the previous node`.

The first node is called the head, and the last node is called the tail, which points to null.

Doubly linked lists allow for efficient `insertion` and `deletion` of elements `at both the beginning and the end`.

### Big O

|                 |                Access                 |               Search                |          Insert(at beginning or end)          |                      Insert(at middle)                       |        Deletion(at beginning or end)         |                     Deletion(at middle)                     |
| :-------------: | :-----------------------------------: | :---------------------------------: | :-------------------------------------------: | :----------------------------------------------------------: | :------------------------------------------: | :---------------------------------------------------------: |
|   Description   | Accessing an element using its index. | Finding an element (linear search). | Inserting an element at the beginning or end. | Inserting an element at the middle needs iterating the list. | Deleting an element at the beginning or end. | Deleting an element at the middle needs iterating the list. |
| Time complexity |                 O(n)                  |                O(n)                 |                  **_O(1)_**                   |                             O(n)                             |                  **_O(1)_**                  |                            O(n)                             |

---

## Cycle Link List

A cycle linked list is a variation of a linked list where `the last node points to another node` in the list, forming a loop or cycle.

In a cycle linked list, no node points to null.

Instead, the last node references another node (most commonly the head node) in the list, creating a cycle.

### Big O

Similar to single and double linked lists, but with some differences depending on whether the node is part of a cycle.

# Stack

A stack is a `Last In, First Out (LIFO)` data structure.

This means that the `last element added` to the stack will `be the first one to be removed`.

There are two common implementations for stack: array-based and linked-list-based.

## Basic Operations

- **Push**: Adds an element to the top of the stack.

- **Pop**: Removes and returns the top element of the stack.

- **Peek**: Returns the top element of the stack without removing it, known as `Top`.

- **IsEmpty**: Checks if the stack is empty.

- **Size**: Returns the number of elements in the stack.

## Applications

- **Function Call Management**: Used to save and restore function states in programming languages.

- **Expression Evaluation**: Used for evaluating postfix or prefix expressions.

- **Parenthesis Matching**: Used to check if parentheses are correctly paired.

- **Backtracking**: Used in algorithms like depth-first search.

- **Undo Mechanism**: Implementing undo functionality in applications (e.g., text editors/history stack).

---

## Implementations

### Array-Based

**Uses a fixed-size array**.

**Efficient for small, fixed maximum size stacks**.

**May require resizing for dynamic stacks**.

### Big O

|                 |               Push               |               Pop               |
| :-------------: | :------------------------------: | :-----------------------------: |
|   Description   | Inserting an element at the end. | Deleting an element at the end. |
| Time complexity |               O(1)               |              O(1)               |

---

### Linked-List-Based

**Uses a singly linked list**.

**Dynamically sized**.

**Efficient for memory usage without resizing**.

### Big O

|                 |                    Push                    |                    Pop                    |
| :-------------: | :----------------------------------------: | :---------------------------------------: |
|   Description   | Inserting an element at the **beginning**. | Deleting an element at the **beginning**. |
| Time complexity |                    O(1)                    |                   O(1)                    |

> Always push/pop at the head node to maintain O(1) time complexity for push/pop operations.

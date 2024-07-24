# Queue

A queue is a `First In, First Out (FIFO)` data structure.

This means that `the first element added` to the queue will `be the first one to be removed`.

There are two main implementations for queue: array-based and linked-list-based.

## Basic Operations

- **Enqueue**: Adds an element to the end of the queue.

- **Dequeue**: Removes and returns the front element of the queue.

- **Front**: Returns the front element of the queue without removing it, also known as "Peek".

- **IsEmpty**: Checks if the queue is empty.

- **Size**: Returns the number of elements in the queue.

## Applications

- **Queue Systems**: Simulate real-world queuing, used in scenarios like line management and task scheduling.

- **Breadth-First Search**: Used for traversing graphs and trees.

- **Resource Management**: Used for managing limited resources like printers or CPU scheduling.

- **Data Stream**: Used for processing continuous data streams like multimedia streaming.

- **Buffers**: Data buffering (e.g., I/O buffers, keyboard buffers).

---

## Implementations

### Array-Based

**Uses a fixed-size array**.

**Requires a circular buffer to efficiently utilize space**.

**May require resizing for dynamic queues**.

### Big O

|                 |         Enqueue         |        Dequeue         |
| :-------------: | :---------------------: | :--------------------: |
|   Description   | Inserting an element at | Deleting an element at |
|                 |        the end.         |       the front.       |
| Time complexity |          O(1)           |      O(1) or O(n)      |

> If resizing is required, it may occasionally lead to O(n) time complexity.
>
> In an efficient array-based circular queue implementation,
> both enqueue and dequeue operations are O(1),
> that with two pointer front and rear without resizing,
> but they are limited by capacity.

---

### Linked-List-Based

**Uses a singly or doubly linked list**.

**Dynamically sized**.

**Efficient for memory usage without resizing**.

### Big O

|                 |         Enqueue         |        Dequeue         |
| :-------------: | :---------------------: | :--------------------: |
|   Description   | Inserting an element at | Deleting an element at |
|                 |     the end (tail).     | the beginning (head).  |
| Time complexity |          O(1)           |          O(1)          |

> Using a doubly linked list for queue implementation,
> makes the time complexity of enqueue and dequeue operations O(1),
> because both the head and tail can be accessed directly.

# Circular Queue

A circular queue is a `First In, First Out (FIFO)` data structure implemented with a `fixed-size array` that `connects the end of the queue back to the beginning`, forming a circular buffer.

This means that `the first element added` to the circular queue will `be the first one to be removed`.

When the end of the array is reached, the next element will be added to the beginning of the array if there is space (i.e., it is not full).

## Basic Operations

- **Enqueue**: Adds an element to the end of the queue, wrapping around to the beginning if necessary.

- **Dequeue**: Removes and returns the front element of the queue, wrapping around to the beginning if necessary.

- **Front**: Returns the front element of the queue without removing it, also known as "Peek"

- **IsEmpty**: Checks if the queue is empty.

- **IsFull**: Checks if the queue is full.

- **Size**: Returns the number of elements in the queue.

## Applications

- **Fixed-Size Buffers**: Used in scenarios where a fixed-size buffer is needed, such as multimedia streaming and data buffering.

- **Resource Management**: Used for managing limited resources like printer queues, task scheduling, and CPU scheduling in operating systems.

- **Network Data Packet Management**: Used in network routers and switches to manage data packets efficiently.

- **Producer-Consumer Problem**: Useful in multi-threaded applications for handling producer-consumer scenarios.

## Implementations

### Array-Based

- **Uses a fixed-size array with two pointers: front and rear.**.

- **Efficiently utilizes space with circular wrapping**.

- **May require size check to prevent overflow**.

### Big O

|                 |           Enqueue            |           Dequeue            |
| :-------------: | :--------------------------: | :--------------------------: |
|   Description   |   Inserting an element at    |    Deleting an element at    |
|                 |           the end,           |          the front,          |
|                 | wrapping around if necessary | wrapping around if necessary |
| Time Complexity |             O(1)             |             O(1)             |

Space complexity: O(n), where n is the size of the fixed array.

> In a circular queue, both enqueue and dequeue operations are O(1),
> Cause they only require `adjusting the front and rear pointers without shifting elements`.
> This design allows for efficient space utilization, as empty spots at the beginning of the array can be reused.

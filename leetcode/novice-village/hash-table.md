# Hash Table

A hash table is a data structure that stores key-value pairs.
It uses a hash function to map keys to their associated values.

The "Key" of the data is processed by a "Hash Function," which generates a "Hashed Text" (or hash code).
This hash code serves as an index, and the corresponding "Value" is stored in a "Bucket" associated with that index.
The table storing these values is called a "Hash Table."

To look up a value, we use the key to find the corresponding value.
Ideally, the time complexity for this operation is O(1) if there is no hash collision.

If a collision occurs, the time complexity can degrade to O(n),
where n is the number of values that have the same hash code.

A hash collision happens when multiple keys are mapped to the same hash code.

When collisions occur, they must be handled using one of several strategies:

1. **Chaining**: Use a linked list to store all values that hash to the same index.

2. **Open Addressing**: Find the next available index using a probing sequence until an empty bucket is found.

3. **Double Hashing**: Use a second hash function to determine the step size for finding the next available index.

## Basic Operations

- **Insertion**: To insert a key-value pair.

- **Search**: To look up a value by key

- **Deletion**: To delete a key-value pair by key.

## Applications

- **Databases**: For indexing and fast data retrieval.

- **Caches**: For fast lookup of frequently accessed data.

- **Symbol Tables**: In compilers and interpreters to store variable bindings.

- **Sets**: To implement sets for fast membership testing.

## Big O

|                 |          Insert           |      Search      |   Deletion   |
| :-------------: | :-----------------------: | :--------------: | :----------: |
|   Description   | Insert a key with a value | Search for a key | Delete a key |
| Time Complexity |           O(1)            |       O(1)       |     O(1)     |

> The average time complexity is O(1) if there are no collisions;
> otherwise, it can degrade to O(n) in the worst case.

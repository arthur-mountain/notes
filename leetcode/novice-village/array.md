# Array

An array is a `collection of elements`, each `identified by an index or key`, stored in `contiguous memory locations`.

## Big O

|                 |                Access                 |               Search                |                        Insert(at beginning or middle)                         |          Insert(at end)          |                       Deletion(at beginning or middle)                       |        Deletion(at end)         |
| :-------------: | :-----------------------------------: | :---------------------------------: | :---------------------------------------------------------------------------: | :------------------------------: | :--------------------------------------------------------------------------: | :-----------------------------: |
|   Description   | Accessing an element using its index. | Finding an element (linear search). | Inserting an element at the beginning or middle (requires shifting elements). | Inserting an element at the end. | Deleting an element at the beginning or middle (requires shifting elements). | Deleting an element at the end. |
| Time complexity |                 O(1)                  |                O(n)                 |                                     O(n)                                      |               O(1)               |                                     O(n)                                     |              O(1)               |

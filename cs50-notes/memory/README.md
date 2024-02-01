# 短篇小文

有一天被問，ㄟ arthur, string 裡面是不是有個 split 方法，可以讓字串變 array，

me: 是

對方又問: 那有沒有更漂亮的寫法

me 想了想: 有，直接用 deconstructing

# 學習 CS50 的心得

會讓我想寫這篇的原因主要是，最近在看 CS50，

對於 C 來說，其實 string 就是一個 array 的 pointer，

所以我才想說那 JS 用的 string ，是不是也可以用 deconstructing，

畢竟 JS 也只是跑在 V8 引擎上，而 V8 引擎其實又是用 C++ 寫得，C++ 又是從 基於 C 演化出來的

試了一下，不出所料可以，

![Screenshot 2024-01-23 at 11.24.21 AM.png](asset-1.png)

P.S. 關於，對於 C 來說，其實 string 就是一個 array 的 pointer

string 是由一個連續的 char array 組成，每一個 char 會是 1byte，

而 string 只是一個 pointer，它會指向 第一個 char 的 address(記憶體位置)，

而一個 array 在 memory 的最後一個符號，一定是 \0(別名：NULL)，在 [ascii table](https://simple.m.wikipedia.org/wiki/File:ASCII-Table-wide.svg) 裡的第一個值

![Screenshot 2024-01-23 at 11.30.06 AM.png](asset-2.png)

# Refs:

[https://cs50.harvard.edu/college/2023/fall/notes/4/](https://cs50.harvard.edu/college/2023/fall/notes/4/)

[https://simple.m.wikipedia.org/wiki/File:ASCII-Table-wide.svg](https://simple.m.wikipedia.org/wiki/File:ASCII-Table-wide.svg)
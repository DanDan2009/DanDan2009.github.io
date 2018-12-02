---
layout:     post
title:       数据结构3--语言支持
subtitle:   "Data Structures — Language Support (Part 3)"
date:       2018-11-28 12:00:00
author:     "Dan"
header-img: "img/data-structures -language-support.jpg"
tags:
    - 算法
    - 数据结构
---


# Data Structures — Language Support (Part 3) 数据结构3--语言支持

## arrays 数组

Support for N-th dimensional arrays is generally built directly into the core language itself. They exist in most of languages if not all of them.
对n维数组的支持通常直接构建在核心语言本身中。它们存在于大多数语言中，如果不是全部的话。

>Some languages index from zero while others index from one.有些语言从0开始索引，而有些语言从1开始索引。

## Dynamic Arrays 动态数组

### java

Java offers a couple of classes, the best known being the ArrayList class.
Java提供了两个类，最著名的是ArrayList 类。

### python

In Python, list is a data type, just like strings and numbers. It’s an implementation of dynamic arrays.
在Python中，list 是一种数据类型，就像字符串和数字一样。它是动态数组的实现。

### C++ 

C++’s std::vector is an implementation of dynamic arrays.
c++的std::vector是一个动态数组的实现。

C# also offers ArrayListclass as in Java.
c#也像Java一样提供ArrayListclass。

## linked list 链表

### java

In Java, Listis an interface, where LinkedListclass implements the Listinterface. It’s implemented as doubly-linked list.
在Java中，Listis是一个接口，LinkedListclass在其中实现了Listinterface。它实现为双链表。

### python 

Python does not have a built-in linked list implementation. Python lists are not linked lists, they are dynamic arrays.
Python没有内置的链表实现。Python列表不是链表，它们是动态数组。

### C++
The list  container in the standard temple library is a doubly-linked list implementation.
标准temple库中的列表容器是一个双链表实现。

###  C#
In C#, there is a LinkedListclass as in Java. It’s also implemented as doubly-linked list.
在c#中，有一个LinkedListclass，就像在Java中一样。它还实现为双链表。

## stacks 栈

### java 
In Java, it has a Stack class.
在Java中，它有一个堆栈类。

### python

Python doesn’t have an explicit stack class, but there is a section in the Python documentation of using lists as stacks.
Python没有显式的堆栈类，但是Python文档中有一节介绍如何使用列表作为堆栈。

### C++
In C++, stack is part of the standard template library.
在c++中，stack是标准模板库的一部分。

### C#
C# also offers Stack class as in Java.
c#还提供Java中的堆栈类。

## queues 队列

### java
Queueis an interface just as List was. There are multiple concrete classes that do support queue behavior. The LinkedListclass actually supports a simple queue behavior.
Queueis是一个与List相同的接口。有多个具体类支持队列行为。LinkedListclass实际上支持一个简单的队列行为。

### python 

The queue module offers a queue class. It is especially useful in when working with threading which is very common with queues. It’s also possible to use list as queues, however, lists are not efficient for this purpose.
队列模块提供了一个队列类。它在处理线程时特别有用，线程在队列中非常常见。也可以将list用作队列，但是，list在此用途上并不有效。 

>collections.deque is an alternative implementation of unbounded queues with fast atomic append() and popleft() operations that do not require locking. deque是无界队列的另一种实现，具有快速的原子append()和popleft()操作，这些操作不需要锁定。

### C++

In C++, there is a queuecontainer in the standard template library.
在c++中，标准模板库中有一个queuecontainer。

### C#


In C#, there is a Queueclass.
在c#中，有一个Queueclass。


## Deque
### java 

Java also has aDequeinterface just as Queue was. The LinkedListclass also implements that Dequeinterface.
Java和Queue一样也有足够的接口。LinkedListclass还实现了那个Dequeinterface。

> Notice that LinkedList is turning out to be a very flexible class in Java. It can behave like a linked list and it can behave like a queue, and it can behave like a double ended queue; deque.  请注意，LinkedList在Java中是一个非常灵活的类。它可以像一个链表，可以像一个队列，可以像一个双端队列;双端队列。

### python 

Python also has a deque class for this. Even though you could use a Python list, the deque is optimized for this kind of usage; appends and pops on either end.  Python还为此提供了一个dequeclass。即使您可以使用Python列表，deque也为这种用法进行了优化;在任意一端追加和弹出。

### C++ 

In C++, there is a dequecontainer.
在c++中，有一个dequecontainer。

### C#
It doesn’t have a built in explicit deque, but, we can provide equivalent behavior with a linked list or even a dynamic array. Just be conscious as shifting elements around in the array is not an efficient task.
它没有内建的显式deque，但是，我们可以用链表甚至动态数组提供等价的行为。只要保持清醒，因为在数组中移动元素并不是一项有效的任务。

## Priority Queues 优先级队列

Just as a list can be implemented with a linked list or an array, a priority queue can be implemented with a heap or a variety of other methods.
正如列表可以用链表或数组实现一样，优先级队列也可以用堆或各种其他方法实现。

### Java
Java has a PriorityQueueclass, which is based on a priority heap.
Java有一个基于优先级堆的PriorityQueueclass。

### Python
The queue module offers a PriorityQueueclass, which uses heapq module under the hood to prioritize the queue entries. The heapq module uses the heap data structure.   队列模块提供了一个PriorityQueueclass，它使用底层的heapq模块对队列条目进行优先排序。heapq模块使用堆数据结构。

>Queue.PriorityQueue is a thread-safe class, while the heapq module makes no thread-safety guarantees.  队列中。PriorityQueue是一个线程安全类，而heapq模块不提供线程安全保证。

### C++ 

C++ has priority_queuecontainer, which is also implemented as a heap data structure.c++具有priority_queuecontainer，它也是作为堆数据结构实现的。

### C#
C# doesn’t contain a priority queue class. But, there are several implementations for priority queues available on the internet.
c#不包含优先级队列类。但是，internet上有几种优先级队列的实现。

## Associative Arrays 关联数组

Most associate arrays, whether they are called dictionaries or maps or hash, are implemented using something called a hash table, and a hash table itself is a very important and useful data structure.
大多数关联数组，无论它们被称为字典、映射或哈希，都是使用称为哈希表的东西实现的，而哈希表本身就是一个非常重要和有用的数据结构。

Other languages use binary search tree (probably a red black tree; a type of self balancing binary search tree) where each node has a key and associated value. So, if you want to keep your keys in a sorted order, hash tables aren’t really good at that, but, binary search trees are.
其他语言使用二叉搜索树(可能是红黑树;自平衡二叉搜索树的一种类型)，其中每个节点都有一个键和关联的值。所以，如果你想让你的键保持有序，哈希表并不擅长这个，但是二叉搜索树擅长。

### java
In Java, It has Map interface just as List was. It defines the operations for associative arrays, while HashMap and HashTable classes are implementations of these operations based on the hash table data structure.
在Java中，它具有与List相同的映射接口。它定义关联数组的操作，而HashMap和HashTable类是基于哈希表数据结构的这些操作的实现。

>HashTable is better when working with multi-threaded applications, where you have different threads accessing and changing this hash table, but it will add a performance cost.  哈希表在处理多线程应用程序时更好，在这些应用程序中，有不同的线程访问和更改这个哈希表，但这会增加性能成本。
>
ConcurrentHashMapclass is a replacement for the older HashTable.  ConcurrentHashMapclass是旧哈希表的替代。

There is also LinkedHashMap, which use linked list to iterate over the items in the same way they were inserted.还有LinkedHashMap，它使用链表以插入条目的相同方式迭代条目。

While TreeMap class also implements the Map interface, it’s actually a RedBlackTree, which is a self-balancing binary search tree.虽然TreeMap类也实现了映射接口，但它实际上是一个RedBlackTree，这是一个自平衡的二叉搜索树。

## Python
In Python, they are called dictionaries. They are implemented as a data type called dict, just like strings and numbers.在Python中，它们被称为字典。它们是作为一种名为dict的数据类型实现的，就像字符串和数字一样。


## C++ 

C++ offers std::unordered_mapcontainer in the standard template library, while std::mapcontainer is the sorted version which is typically implemented as binary search tree.
c++在标准模板库中提供std::unordered_mapcontainer，而std::mapcontainer是排序后的版本，通常实现为二叉搜索树。

### C# 

In C#, they’re available in Dictionary<TKey,TValue>, Hashtableand StringDictionaryclasses, whileSortedDictionaryclass sorts the key-value pairs that’s implemented as a binary search tree.
在c#中，它们在Dictionary<TKey、TValue>、Hashtableand StringDictionaryclasses中可用，而lesorteddictionaryclass则对实现为二进制搜索树的键-值对进行排序。

## sets 集

The idea of a set; having a big container where you can put a bunch of items into it, is usually implemented using hash tables, or binary search trees.
集合的概念;通常使用哈希表或二叉搜索树实现一个大容器，在这个容器中可以放入许多项。

### java 

In Java, It has Set interface just as List and Queue, whileHashSet and TreeSetclasses implement the Setinterface.
在Java中，它有Set接口，就像List和Queue一样，而lehashset和TreeSetclasses实现了Setinterface。

HashSet is internally implemented using an HashMap, while TreeSet is internally implemented using TreeMap.
HashSet是使用HashMap在内部实现的，而TreeSet是使用TreeMap在内部实现的。

### python 

Python supports set and frozensetdata types. frozenset is a way of making the set immutable after it has been created.
Python支持set和frozensetdata类型。frozenset是一种在创建集合后使其不可变的方法。

### C++ 

As with maps, C++ also offers std::unordered_setcontainer in the standard template library, while std::setcontainer is the sorted version which is typically implemented as binary search tree.
与映射一样，c++也在标准模板库中提供了std::unordered_setcontainer，而std::setcontainer是排序后的版本，通常实现为二叉搜索树。

### C# 

C# also offers HashSet class which uses the hash table as in Java, while SortedSet is the sorted version.
c#还提供了HashSet类，它像在Java中一样使用哈希表，而SortedSet是排序后的版本。

## graphs 图

There is no direct support for graph data structure in languages(same as trees). Because the implementation of any graph is always going to be more specific.
在语言中不直接支持图形数据结构(与树相同)。因为任何图形的实现都是更具体的。

For example, A linked list is a kind of graph, a tree is a kind of graph, a heap is a kind of graph. A single linked list would be considered a directed graph, whereas a doubly linked list is a kind of undirected graph. They are all graphs with intentional constraints.
例如，链表是一种图，树是一种图，堆是一种图。单链表被认为是有向图，而双链表是一种无向图。它们都是有意图约束的图。

![](/img/15432304891264.jpg)

Summary for Data Structures Language Support
数据结构的摘要语言支持



来源： https://medium.com/omarelgabrys-blog/data-structures-language-support-5f70f8312e84


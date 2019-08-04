JAVA_UML_CODE
====================
`JAVA_UML_CODE` 记录了 Java 笔记的 UML 模型图的相关代码。

## 01.Collection 容器

<div align="center"> <img src="images/01.collection.png" width="520px"> </div><br>

```text
@startuml
@startuml
skinparam classAttributeIconSize 0
interface Iterator {
}

interface Collection {
}
Iterator <-- Collection

interface List {
}
interface Queue {
}
interface Set {
}
Collection <|-- List
Collection <|-- Queue
Collection <|-- Set

class ArrayList {
}
List <|.. ArrayList

class Vector {
}
List <|.. Vector

class Stack {
}
Vector <|-- Stack

class LinkedList {
}
List <|.. LinkedList
Queue <|.. LinkedList

interface Deque {
}
Queue <|-- Deque

class ArrayDeque {
}
Deque <|.. ArrayDeque

class HashSet {
}
Set <|.. HashSet

class LinkedHashSet {
}
Set <|.. LinkedHashSet

interface SortedSet {
}
Set <|-- SortedSet

interface NavigableSet {
}
SortedSet <|-- NavigableSet
class TreeSet {
}
NavigableSet <|.. TreeSet

@enduml
```

## 02.Map 容器

<div align="center"> <img src="images/02.map.png" width="480px"> </div><br>

```text
@startuml
skinparam classAttributeIconSize 0
interface Map {
}

interface SortedMap {
}
Map <|-- SortedMap

interface NavigableMap {
}
SortedMap <|-- NavigableMap

class TreeMap {
}
NavigableMap <|.. TreeMap

class HashMap {
}
Map <|.. HashMap

class LinkedHashMap {
}
Map <|-- LinkedHashMap

class HashTable {
}
Map <|.. HashTable

@enduml
```



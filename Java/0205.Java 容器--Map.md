Java容器--HashMap 深入解析
====================

# 1 Map 简介

Java为数据结构中的映射定义了一个接口 java.util.Map，此接口主要有四个常用的实现类，分别是 HashMap、Hashtable、LinkedHashMap 和 TreeMap，类继承关系如下图所示：

<img src=".\images\020501.jpg" style="zoom:80%;" />

实现类说明如下：

- **HashMap**：根据键的 hashCode 值存储数据，大多数情况下可以直接定位到它的值，因而具有很快的访问速度，但遍历顺序却是不确定的。HashMap 最多只允许一条记录的键为 null，允许多条记录的值为 null。HashMap 非线程安全，即任一时刻可以有多个线程同时写HashMap，可能会导致数据的不一致。如果需要满足线程安全，可以用 Collections#synchronizedMap() 或 ConcurrentHashMap 使 HashMap 具有线程安全性。
- **LinkedHashMap**：是 HashMap 的一个子类，保存了记录的插入顺序，用 Iterator 遍历 LinkedHashMap时，先得到的记录肯定是先插入的，也可以在构造时带参数，按照访问次序排序。
- **Hashtable**：遗留类，常用功能与 HashMap 类似，不同的是它承自 Dictionary 类，并且是线程安全的，任一时间只有一个线程能写 Hashtable，并发性不如 ConcurrentHashMap，因为 ConcurrentHashMap引入了分段锁。
- **TreeMap**：实现了 SortedMap 接口，能够把它保存的记录根据键排序，默认是按键值的升序排序，也可以指定排序的比较器，当用 Iterator 遍历 TreeMap 时，得到的记录是排过序的。在使用 TreeMap 时，key 必须实现 Comparable 接口或者在构造 TreeMap 传入自定义的 Comparator，否则会在运行时抛出java.lang.ClassCastException 类型的异常。

通过上面的比较，我们知道了HashMap是Java的Map家族中一个普通成员，鉴于它可以满足大多数场景的使用条件，所以是使用频度最高的一个。下文我们主要结合源码，从存储结构、常用方法分析、扩容以及安全性等方面深入讲解HashMap的工作原理。

# 2 HashMap 简介

JDK1.7中的 HashMap 是由**数组+链表**组成的，而 JDK1.8 中的 HashMap 是由**数组+链表+红黑树组成**。数组的默认长度（`DEFAULT_INITIAL_CAPACITY`）为**16**（可以自动变长，也可指定长度），加载因子（`DEFAULT_LOAD_FACTOR`）为**0.75**。

- HashMap 的默认长度为 16 和规定数组长度为 2 的幂，是为了降低哈希碰撞的几率。
- HashMap 中使用链表主要为了解决哈希冲突，链表出现越少或者长度越小，性能越好。

> **数组**：具有遍历快，增删慢的特点。数组在堆中是一块连续的存储空间，遍历时数组的首地址是知道的，所以遍历快，遍历的时间复杂度为`O(1)` ；当在中间插入或删除元素时，会造成该元素后面所有元素地址的改变，所以增删慢，时间复杂度为`O(n)`。

> **链表**：链表具有增删快，遍历慢的特点。链表中各元素的内存空间是不连续的，一个节点至少包含节点数据与后继节点的引用，所以在插入删除时，只需修改该位置的前驱节点与后继节点即可，链表在插入删除时的时间复杂度为`O(1)`。但是在遍历时，get(n)元素时，需要从第一个开始，依次拿到后面元素的地址，进行遍历，直到遍历到第n个元素，遍历的时间复杂度为`O(n)` ，效率极低。

## 哈希冲突

指两个元素通过 hash 函数计算出的值是一样的，表示这两个元素存储的是同一个地址。当后面的元素要插入到这个地址时，发现已经被占用了，这时候就产生了哈希冲突。

**常用的哈希冲突的解决办法**：

- **开放定址法**：当发生哈希冲突时，查询产生冲突的地址的下一个地址是否被占用，直到寻找到空的地址。
- **链地址法**：当发生哈希冲突时，在冲突的地址上生成一个链表，将冲突的元素的key通过equals进行比较，相同即覆盖，不同则添加到链表上。

`HashMap`使用的是链地址法。在JDK1.7中，如果链表过长，效率就会大大降低，查找和添加操作的时间复杂度都为`O(n)`；在JDK1.8中，如果链表长度大于8，链表就会转化为红黑树，时间复杂度也就将为`O(logn)`，性能得到了很大的提升。

# 3 JDK1.8源码分析

JDK1.8中的HashMap是由**数组+链表+红黑树**组成的，组成链表结点的是`Node`包含三个元素：`key`、`value`和指向下一个Node的`next`。

<img src=".\images\020502.jpg" style="zoom: 67%;" />

## 3.1 Node<k,v> 源码

HashMap 中有一个非常重要的字段 Node[] table（哈希桶数组），Node<K,V> 源码如下： 

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash; //用来定位数组索引位置
    final K key;
    V value;
    Node<K,V> next; //链表的下一个node

    Node(int hash, K key, V value, Node<K,V> next) { }

    public final K getKey()        {  }
    public final V getValue()      {  }
    public final String toString() {  }
    public final int hashCode() { }
    public final V setValue(V newValue) { }
    public final boolean equals(Object o) { }
}
```

Node 是 HashMap 的一个内部类，实现了 Map.Entry 接口，本质是一个映射(键值对)。上图中的每个黑色圆点就是一个 Node 对象。

## 3.2 根据key获取哈希桶数组索引位置

不管增加、删除、查找键值对，定位到哈希桶数组的位置都是很关键的第一步。

```java
// 方法一：
static final int hash(Object key) { //jdk1.8 & jdk1.7
     int h;
     // h = key.hashCode() 为第一步 取hashCode值
     // h ^ (h >>> 16) 为第二步 高位参与运算
     return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
// 方法二：
static int indexFor(int h, int length) { //jdk1.7的源码，jdk1.8没有这个方法，但是实现原理一样的
 return h & (length-1); //第三步 取模运算
}
```

这里的Hash算法本质上就是三步：**取key的hashCode值、高位运算、取模运算**。

对于任意给定的key，只要它的hashCode()返回值相同，那么他hash值总是相同的。可以通过**把hash值对数组长度取模运算**来保证元素的均匀分布；但模运算开销比较大，HashMap 中 调用 indexFor 方法，来计算该对象应该保存在table数组的哪个索引处。

 indexFor 方法通过 `h & (length-1)`来得到该对象的保存位，而 HashMap 底层数组的长度总是 2 的 n 次方；当 length 总是 2 的 n 次方时，h& (length-1) 运算等价于 h%length，但是&比%效率更高。这是 HashMap 在速度上的优化。

在 JDK1.8 的实现中，优化了高位运算的算法，通过 hashCode() 的高16位异或低16位实现的：(h = k.hashCode()) ^ (h >>> 16)，主要是从速度、功效、质量来考虑的，这么做可以在数组table的length比较小的时候，也能保证考虑到高低Bit都参与到Hash的计算中，同时不会有太大的开销。

下面举例说明下，n为table的长度：

<img src=".\images\020505.jpg" style="zoom: 80%;" />

## 3.3 put() 源码

```java
public V put(K key, V value) {
     // 对key做hash运算得到hashCode
     return putVal(hash(key), key, value, false, true);
 }
 final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                boolean evict) {
     Node<K,V>[] tab; Node<K,V> p; int n, i;
     // 步骤①：tab为空则创建。
     if ((tab = table) == null || (n = tab.length) == 0)
         n = (tab = resize()).length;
     // 步骤②：计算index。
     if ((p = tab[i = (n - 1) & hash]) == null) 
         tab[i] = newNode(hash, key, value, null);
     else {
         Node<K,V> e; K k;
         // 步骤③：节点key存在，直接覆盖value。
         if (p.hash == hash &&
             ((k = p.key) == key || (key != null && key.equals(k))))
             e = p;
         // 步骤④：判断该链是否为红黑树。
         else if (p instanceof TreeNode)
             e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
         // 步骤⑤：链表
         else {
             for (int binCount = 0; ; ++binCount) {
                 if ((e = p.next) == null) {
                     p.next = newNode(hash, key,value,null);
                      // 判断链表长度是否大于8，如果链表长度大于8转换为红黑树进行处理。
                     if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st  
                         treeifyBin(tab, hash);
                     break;
                 }
                  // 如果key已经存在并且equals相等，则直接覆盖value。
                 if (e.hash == hash &&
                     ((k = e.key) == key || (key != null && key.equals(k)))) 
							break;
                 p = e;
             }
         }
         if (e != null) { // existing mapping for key
             V oldValue = e.value;
             if (!onlyIfAbsent || oldValue == null)
                 e.value = value;
             afterNodeAccess(e);
             return oldValue;
         }
     }     ++modCount;
     // 步骤⑥：超过最大容量就扩容。
     if (++size > threshold)
         resize();
     afterNodeInsertion(evict);
     return null;
 }
```

具体步骤为：

- 1).判断键值对数组 table[i] 是否为空或为 null，如果为空则创建 Node；

- 2).根据键值 key 计算 hash 值得到插入的数组索引 i，如果`table[i]==null`，直接新建节点添加，转向⑥，如果 table[i] 不为空，则转向③；

- 3).判断 table[i] 的首个元素是否和 key 一样，如果相同直接覆盖 value，否则转向 ④，这里的相同指的是`hashCode` 以及 `equals`；

- 4).判断 table[i] 是否为 `treeNode`红黑树，如果是红黑树，则直接在树中插入键值对，否则转向⑤；

- 5).遍历 table[i]，判断链表长度是否大于8，**如果链表长度大于8的话把链表转换为红黑树**，在红黑树中执行插入操作，否则进行链表的插入操作；遍历过程中若发现key已经存在直接覆盖value即可；

- 6).插入成功后，判断实际存在的键值对数量size是否超多了最大容量`threshold`，如果超过，进行`resize()`扩容。

流程如下：

<img src=".\images\020504.jpg" style="zoom: 67%;" />

## 3.4 resize() 扩容机制

如果HashMap的大小超过了负载因子（默认为0.75）定义的容量，也就是说，**当一个Map填满了75%的Bucket时候，将会创建原来HashMap大小的两倍的Bucket数组**，来重新调整Map的大小，并将原来的对象放入新的Bucket数组中。

> 阈值 = 数组默认的长度 x 负载因子（阈值 = 16 x 0.75 = 12）

负载因子默认的 0.75 是对空间和时间效率的一个平衡选择，建议不要修改；除非在时间和空间比较特殊的情况下，如果内存空间很多而又对时间效率要求很高，可以降低负载因子Load factor的值；相反，如果内存空间紧张而对时间效率要求不高，可以增加负载因子loadFactor的值，这个值可以大于1。

鉴于JDK1.8融入了红黑树，较复杂，为了便于理解我们仍然使用JDK1.7的代码，好理解一些，本质上区别不大。

```java
void resize(int newCapacity) { //传入新的容量
	Entry[] oldTable = table; //引用扩容前的Entry数组
	int oldCapacity = oldTable.length; 
	if (oldCapacity == MAXIMUM_CAPACITY) { //扩容前的数组大小如果已经达到最大(2^30)了
		threshold = Integer.MAX_VALUE; //修改阈值为int的最大值(2^31-1)，这样以后就不会扩容了
		return;
	}
	Entry[] newTable = new Entry[newCapacity]; //初始化一个新的Entry数组
	transfer(newTable); //！！将数据转移到新的Entry数组里
	table = newTable; //HashMap的table属性引用新的Entry数组
	threshold = (int)(newCapacity * loadFactor);//修改阈值
}

void transfer(Entry[] newTable) {
	Entry[] src = table; //src引用了旧的Entry数组
	int newCapacity = newTable.length;
	for (int j = 0; j < src.length; j++) { //遍历旧的Entry数组
		Entry<K,V> e = src[j]; //取得旧Entry数组的每个元素
		if (e != null) {
			src[j] = null;//释放旧Entry数组的对象引用（for循环后，旧的Entry数组不再引用任何对象）
			do {
				Entry<K,V> next = e.next;
				int i = indexFor(e.hash, newCapacity); //！！重新计算每个元素在数组中的位置
				e.next = newTable[i]; //标记[1]
				newTable[i] = e; //将元素放在数组上
				e = next; //访问下一个Entry链上的元素
			} while (e != null);
		}
	}
}
```

newTable[i] 的引用赋给了e.next，也就是使用了单链表的头插入方式，同一位置上新元素总会被放在链表的头部位置；这样先放在一个索引上的元素终会被放到Entry链的尾部(如果发生了hash冲突的话），这一点和Jdk1.8有区别。

## 3.4 get() 源码

```java
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}

final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    // Node不为空 && 计算索引位置并且索引处有值
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        // 判断key的hashCode是否相等 
        // && 判断索引处第一个key与传入key是否相等 
        // && 判断key的equals是否相等。
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        // 判断链表是否是红黑树，如果是红黑树，就从树中获取值。
        if ((e = first.next) != null) {
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            // 如果不是红黑树，遍历链表。
            do {
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```

- 1).判断表是否为空，并且计算索引位置，并对索引位置的值进行判空校验。
  - 如果表为空 && 索引位置没有值，直接返回null；
  - 如果表不为空 && 索引位置有值，执行步骤②。

- 2).判断入参key与索引处第一个key的hashCode是否相等、 key是否相等或者equals是否相等。
  - 如果步骤②条件满足，则直接返回；否则执行步骤③；

- 3).判断链接是否为红黑树。
  - 如果链表是红黑树，则按照红黑树二叉查找法获取值；
  - 如果不是红黑树（链表长度小于8为普通链表），则遍历链表，直到找到与入参key的hashCode相等、equals相等的key，并获取该key的值。

# 参考：

- [JDK 8 api](https://docs.oracle.com/javase/8/docs/api/)
- [Java 集合系列18之 Iterator和Enumeration比较](https://www.cnblogs.com/skywang12345/p/3311275.html)
- [Java HashMap工作原理及实现](https://yikun.github.io/2015/04/01/Java-HashMap%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E5%8F%8A%E5%AE%9E%E7%8E%B0/
)
- [Java提高篇（二七）-----TreeMap](https://blog.csdn.net/chenssy/article/details/26668941)
- [通过分析 JDK 源代码研究 TreeMap 红黑树算法实现](https://www.ibm.com/developerworks/cn/java/j-lo-tree/index.html)
- [HashMap实现详解](https://juejin.im/post/6844904077462077448#heading-8)
- [Java中常用的容器类笔记](https://juejin.im/post/5b025843f265da0b7156912d#heading-19)





<h1 align="center">CPT 102</h1>

<h6>下文使用的 E, T, K, V 等指代泛型，为约定俗成的表示，分别代表 Element, Type, Key, Value 等。使用时替换为具体实现了 Comparable 接口的类型。</h6>

## Comparable & Comparator 比较器

`Comparable`通常由实现了该接口的类自身来实现，用于定义该类的natural ordering（通常为字典序，升序）。 在自定义类中实现`Comparable`接口需要重写`int compareTo(T object)`方法，返回值为负数、零、正数分别表示当前对象小于、等于、大于参数对象。最终通过`Arrays.sort()`或`Collections.sort()`方法进行升序排序。

`Comparator`则是一个单独的比较器类，用于定义类的其他排序方式。类中可以不事先实现`Comparable`接口，而是在排序时传入一个`Comparator`对象。在自定义类中实现`Comparator`接口需要重写`int compare(T o1, T o2)`方法，返回值为负数、零、正数分别表示o1小于、等于、大于o2。最终通过`Arrays.sort()`或`Collections.sort()`方法传入`Comparator`对象进行排序。

<table>
    <tr>
        <th>Comparable</th>
        <th>Comparator</th>
    </tr>
    <tr>
        <td>
            <pre>
class MyClass implements Comparable<T> {
    @Override
    public int compareTo(T o) {
        // 自定义排序规则
    }
}
            </pre>
        </td>
        <td>
            <pre>
Comparator<T> myComparator = new Comparator<T>() {
    @Override
    public int compare(T o1, T o2) {
        // 自定义排序规则
    }
};
            </pre>
        </td>
    </tr>
    <tr>
        <td>
            <pre>
Arrays.sort(myArray);
Collections.sort(myList);
            </pre>
        </td>
        <td>
            <pre>
Arrays.sort(myArray, myComparator);
Collections.sort(myList, myComparator);
            </pre>
        </td>
    </tr>
</table>

## Iterable & Iterator 迭代器

`Iterable`接口是集合框架的根接口，实现了该接口的类可以使用`foreach`循环。在自定义类中实现`Iterable`接口需要重写`Iterator<T> iterator()`方法，返回一个`Iterator`对象。

`Iterator`接口是一个迭代器接口，用于遍历集合中的元素。在自定义类中实现`Iterator`接口需要重写`boolean hasNext()`和`T next()`方法，分别用于判断是否还有下一个元素和获取下一个元素。

```java
class MyCollection<T> implements Iterable<T> {
    private T[] elements;
    private int size;
    @Override
    public Iterator<T> iterator() { return new MyIterator(); }
    private class MyIterator implements Iterator<T> {
        private int index = 0;
        @Override
        public boolean hasNext() { return index < size; }
        @Override
        public T next() { return elements[index++]; }
    }
}

MyCollection<T> myCollection = new MyCollection<>();
for (T element : myCollection) { /* do something */ }

Iterator<T> iterator = myCollection.iterator();
while (iterator.hasNext()) {
    T element = iterator.next();
    // do something
}
```

# Quick Sort
<h6>考虑到 INT 102 没有涉及快排但这边有用到，稍微解释一下</h6>

快速排序是一种分治算法，其基本思想是在需要排序的元素中任意选择一个基准元素，将小于基准元素的元素放在基准元素的左边，大于基准元素的元素放在基准元素的右边，然后对左右两部分分别递归地重复该过程。
```lua
step 1: 选择基准元素pivot    step 2: 分区操作，将小于pivot的元素放在左边，大于pivot的元素放在右边
3 6 2 8 4 7 1 5            2 1 3 6 8 4 7 5
^                                 ^

step 3: 递归地对左右两部分进行快速排序
2 1 3 6 8 4 7 5            1 2 3 4 5 6 8 7            1 2 3 4 5 6 7 8
^   ^ ^                      ^ ^     ^ ^                ^ ^     ^   ^
```

快速排序的时间复杂度为$O(nlogn)$（最坏情况下为$O(n^2)$），空间复杂度为$O(logn)$。

# List 

## ArrayList

ArrayList的本质是一个数组，只不过这个数组是动态的，可以根据需要自动扩展或缩减。对于该扩容操作，我们仅需知道其默认数组长度为10，每次扩容时，新数组的长度为原数组长度的1.5倍即可。

其常用方法和对应的时间复杂度如下：
| 方法 | `add()` | `get()` | `set()` | `remove()` | `size()` | `isEmpty()` |
| --- | --- | --- | --- | --- | --- | --- |
| 时间复杂度 | $O(n)$ | $O(1)$ | $O(1)$ | $O(n)$ | $O(1)$ | $O(1)$ |
- `add()` 操作涉及到可能的数组扩容，以及指定元素插入位置时所有后续元素的移动，因此时间复杂度为$O(n)$。
- `remove()` 操作涉及到指定元素删除后所有后续元素的移动，因此时间复杂度为$O(n)$。

## LinkedList

LinkedList的本质是一个双向链表，每一个加入的元素都会被存储为单独的节点对象(Node)，该对象除了存储元素本身外，还存储了指向前一个节点和后一个节点的引用（指针）。

其常用方法和对应的时间复杂度如下：
| 方法 | `add()` | `get()` | `set()` | `remove()` | `size()` | `isEmpty()` |
| --- | --- | --- | --- | --- | --- | --- |
| 时间复杂度 | $O(1)$ | $O(n)$ | $O(n)$ | $O(n)$ | $O(1)$ | $O(1)$ |
- `get()` 和 `set()` 操作涉及到从头节点开始遍历，直到找到指定位置的节点，因此时间复杂度为$O(n)$。
- `add()` 操作本质上仅仅是修改涉及到的节点的指针，时间复杂度为$O(1)$。
- `remove()` 操作涉及到从头节点开始遍历，直到找到指定位置的节点，然后修改涉及到的节点的指针，时间复杂度为$O(n)$。对于`removeFirst()`和`removeLast()`方法，由于直接操作头节点和尾节点，因此时间复杂度为$O(1)$。被删除(unlink)的节点会由垃圾回收器回收以释放内存。

# Tree

## Binary Tree

二叉树是一种树形结构，其每个节点最多有两个子节点，分别为左子节点和右子节点。二叉树的每个节点都包含一个值，以及指向左右子节点的指针。

二叉树的遍历方式有三种：前序遍历、中序遍历和后序遍历。

前序遍历：根节点 -> 左子树 -> 右子树

中序遍历：左子树 -> 根节点 -> 右子树

后序遍历：左子树 -> 右子树 -> 根节点

## Binary Search Tree

二叉搜索树（BST）是一种特殊的二叉树，其每个节点的左子树中的所有节点的值都小于该节点的值，右子树中的所有节点的值都大于该节点的值。

二叉搜索树的中序遍历结果是一个有序数组。

二叉搜索树的查询、插入、删除操作的时间复杂度均为$O(logn)$。

- 缺点：当二叉搜索树退化为链表时，查询、插入、删除操作的时间复杂度会退化为$O(n)$。

## Tree Rotation

对于一些特殊的二叉树，我们可以通过**左旋**和**右旋**操作来调整树的结构，使其保持平衡。
- 左旋：将当前节点的右子节点替换为当前节点，当前节点成为右子节点的左子节点。
- 右旋：将当前节点的左子节点替换为当前节点，当前节点成为左子节点的右子节点。

## Balanced Binary Tree

平衡二叉树是一种特殊的二叉树，通过旋转操作使得树的左右子树的高度差不超过1。

## AVL Tree

AVL树是一种自平衡二叉搜索树，其每个节点的左子树和右子树的高度差不超过1。

AVL树的查询、插入、删除操作的时间复杂度均为$O(logn)$。

- 缺点：由于维护高度平衡所付出的代价可能大于能获得的效率收益，因此只有在插入不频繁且查找要求较高情况下使用。

## Red-Black Tree

红黑树是一种弱平衡二叉搜索树，其每个节点都有一个颜色属性，可以是红色或黑色。

红黑树的每个节点都满足以下性质：
1. 节点是红色或黑色。
2. 根节点是黑色。
3. 每个叶子节点（NIL节点）是黑色。
4. 每个红色节点的两个子节点都是黑色。
5. 从任一节点到其每个叶子节点的所有路径都包含相同数目的黑色节点。

红黑树的查询、插入、删除操作的时间复杂度均为$O(logn)$。

## Huffman Tree

哈夫曼树是一种特殊的二叉树，用于数据压缩。其构建过程是通过贪心算法，每次选择两个权值最小的节点合并为一个新节点，直到所有节点合并为一个根节点。

例如，对于数组`[1, 2, 3, 4, 5]`，其构建过程如下：
```lua
   3  3 4 5  ->  6  4 5  ->  6      9     ->    15
  / \           / \         / \    / \        /    \
 1   2         3   3       3   3  4   5      6      9
              / \         / \               / \    / \
             1   2       1   2             3   3  4   5
                                          / \
                                         1   2
```  

### Huffman Encoding

哈夫曼编码是一种变长编码，通过哈夫曼树的构建过程，我们可以得到每个节点的编码，从而得到原始数据的编码。

对于一颗哈夫曼树，其左子节点的编码为`0`，右子节点的编码为`1`。从根节点到叶子节点的路径上的编码即为该叶子节点的编码。

例如，对于上述哈夫曼树，其编码如下：
```lua
  1: 000   2: 001   3: 01   4: 10   5: 11
```

# Map & Set

<h6>由于Set的本质是Value为一个固定Object对象的Map，因此在这里我们只讨论Map，Set酌情参考。</h6>

对于每一个键 Key，Map 都会维护一个对应的值 Value。Map 中的键是唯一的，即同一个键不能对应多个值。这样的数据结构可以用来存储键值对，以便我们能够通过键快速查找到对应的值。

## HashMap

HashMap的本质是一个`Node<K, V>`数组，每个`Node<K, V>`对象存储了键值对，以及指向下一个节点的引用（指针），其默认初始长度为16，负载因子为0.75（即当HashMap中的元素个数超过数组长度的0.75倍时，会进行扩容操作）。

当我们向HashMap中添加元素时，会根据键的哈希值计算出该元素在数组中的位置（下标），如果该位置已经有元素存在，则遍历该位置的链表，直到找到键值对的键与当前键相等的节点，然后替换其值；如果该位置没有元素存在，则直接添加到该位置的链表中。这种方法被称为**拉链法（Separate Chaining）**。

在Java 8之后，当链表长度超过8时，会将链表转换为**红黑树** (Treeify)，以提高查找效率。相对地，当链表长度小于6时，会将红黑树转换为链表，以节省内存。

在极端情况下，若所有元素的Key的哈希值（数组下标）都相同，那么HashMap查询等涉及遍历链表的时间复杂度会退化为$O(n)$(Java < 8)或$O(logn)$(Java >= 8)。

但是通常情况下，我们可以认为HashMap的查询、插入、删除操作的时间复杂度为$O(1)$。

## TreeMap

TreeMap的本质是一个红黑树，其内部元素是有序的，默认情况下每个节点的左子树的所有节点的键值都小于该节点的键值，右子树的所有节点的键值都大于该节点的键值（可以在创建TreeMap对象时传入一个`Comparator`对象来自定义排序规则）。

TreeMap的查询、插入、删除操作的时间复杂度均为$O(logn)$。

## ArraySet
<h6>Java本身并没有这么个玩意，Apache的collections4里也没有。但是既然Guan讲了...好吧...</h6>

本质是一个ArrayList（因为存在扩容机制），每次进行增删改查操作时都需要遍历整个数组，这一步遍历的时间复杂度为$O(n)$，其余操作的时间复杂度与ArrayList相同。

但是我们可以从一开始就尝试维护一个有序的数组，这样在插入时可以使用二分查找来找到插入位置，从而将遍历需要的时间复杂度降低到$O(logn)$。

如果想设计一个ArrayMap，可以使用两个ArrayList通过下标一一对应来实现。

# Queue & Stack

Queue是一种先进先出（FIFO）的数据结构，其常用方法有`offer()`（添加元素到队尾）、`poll()`（移除并返回队首元素）、`peek()`（返回队首元素但不移除）等。

Stack是一种后进先出（LIFO）的数据结构，其常用方法有`push()`（添加元素到栈顶）、`pop()`（移除并返回栈顶元素）、`peek()`（返回栈顶元素但不移除）等。

# Heap

堆是一种特殊的树形数据结构，其每个节点的值都大于等于（或小于等于）其子节点的值。堆分为最大堆和最小堆，最大堆的根节点的值最大，最小堆的根节点的值最小。

堆常用于实现优先队列(Priority Queue)，其插入和删除操作的时间复杂度均为$O(logn)$。

具体实现时，我们通常使用数组来存储堆，根节点的下标为0，其左子节点的下标为$2i+1$，右子节点的下标为$2i+2$（其中$i$为根（父）节点的下标）。

在Java中，由于没有现成的堆数据结构，我们可以使用`PriorityQueue`类和`Comparator`接口来实现堆。此外，由于Java中的`PriorityQueue`默认使用`Comparator.naturalOrder()`得到最小堆，我们可以通过`Collections.reverseOrder()`来实现最大堆。

```java
PriorityQueue<E> maxHeap = new PriorityQueue<>(Collections.reverseOrder()); // 最大堆
PriorityQueue<E> minHeap = new PriorityQueue<>(); // 最小堆
```

## 堆的插入操作

1. 将新元素插入到堆的末尾
2. 若为最大堆，将新元素与其父节点比较，若新元素大于父节点，则交换两者的位置，重复该过程直到新元素不再大于父节点或到达根节点。最小堆同理。

## 堆的删除操作

1. 将堆顶元素（根节点）删除
2. 将堆的最后一个元素移动到堆顶
3. 若为最大堆，将新的堆顶元素与其左右子节点中较大的节点比较，若新元素小于子节点，则交换两者的位置，重复该过程直到新元素不再小于子节点或到达叶子节点。最小堆同理。

# Bag
<h6>来源： org.apache.commons.collections4 
虽然但是，这里面并没有Guan的Slide上那么多方法...如果真考到了建议顾名思义</h6>

Bag是一种无序的集合，类似于Set，但允许重复元素。Bag接口定义了一些方法，如`add()`（添加元素）、`remove()`（移除元素）、`contains()`（判断是否包含某元素）、`size()`（返回元素个数）等。

在现有的Java类库中，实现Bag的一个方式是使用`Map`，其中键为元素，值为元素的个数。方式很多，这里仅供参考。

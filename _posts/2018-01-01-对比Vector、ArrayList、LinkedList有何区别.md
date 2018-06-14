---
layout:     post
title:      对比Vector、ArrayList、LinkedList有何区别
subtitle:   list不同实现的差异
date:       2018-01-01
author:     BY
header-img: img/post-bg-keybord.jpg
catalog: true
tags:
    - java
---


##引言
这三者都是实现集合框架中的List，也就是所谓的有序集合，因此具体功能也比较近似，比如都提供按照位置进行定位、添加或者删除的操作，都提供迭代器以遍历其内容等。但因为具体的设计区别，在行为、性能、线程安全等方面，表现又有很大不同，接下来就来探讨一下其中的差异。
##实现方式不同
###Vector
Verctor 是 Java 早期提供的线程安全的动态数组，如果不需要线程安全，并不建议选择，毕竟同步是有额外开销的。Vector内部是使用对象数组来保存数据，可以根据需要自动的增加容量，当数组已满时，会创建新的数组，并拷贝原有数组数据。
```java

//如果不指定扩容增量则数组扩容一倍
private int newCapacity(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + ((capacityIncrement > 0) ? capacityIncrement : oldCapacity);
    if (newCapacity - minCapacity <= 0) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return minCapacity;
    }
    return (newCapacity - MAX_ARRAY_SIZE <= 0) ? newCapacity : hugeCapacity(minCapacity);
}

//查找元素
public synchronized int indexOf(Object o, int index) {
    if (o == null) {
        for (int i = index ; i < elementCount ; i++)
            if (elementData[i]==null)
                return i;
    } else {
        for (int i = index ; i < elementCount ; i++)
            if (o.equals(elementData[i]))
                return i;
    }
    return -1;
}


```
###ArrayList
ArrayList 是应用更加广泛的动态数组实现，它本身不是线程安全的，所以性能要好很多。与Vector近似，ArrayList 也是可以根据需要调整容量，不过两者的调整逻辑有所区别，Vector在扩容时会提高1倍，而ArrayList则是增加 50%。ArrayList在执行插入元素是超过当前数组预定义的最大值时，数组需要扩容，扩容过程需要调用底层System.arraycopy()方法进行大量的数组复制操作；在删除元素时并不会减少数组的容量（如果需要缩小数组容量,可以调用trimToSize()方法）
```java
//被用于空实例的共享空数组实例
private static final Object[] EMPTY_ELEMENTDATA = {};

//被用于默认大小的空实例的共享数组实例。其与EMPTY_ELEMENTDATA的区别是：当我们向数组中添加第一个元素时，知道数组该扩充多少。
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

//如果不指定扩容增量则数组扩容50%
 private int newCapacity(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity <= 0) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return minCapacity;
    }
    return (newCapacity - MAX_ARRAY_SIZE <= 0) ? newCapacit : hugeCapacity(minCapacity);
}

//查找元素
 public int indexOf(Object o) {
    if (o == null) {
        for (int i = 0; i < size; i++)
            if (elementData[i]==null)
                return i;
    } else {
        for (int i = 0; i < size; i++)
            if (o.equals(elementData[i]))
                return i;
    }
    return -1;
}

```
###LinkedList 
LinkedList 顾名思义是 Java提供的双向链表，所以它不需要像上面两种那样调整容量，它也不是线程安全的,LinkedList在插入元素时，须建一个新的Entry对象，并更新相应元素的前后元素的引用；在查找元素时，需遍历链表；在删除元素时，要遍历链表，找到要删除的元素，然后从链表上将此元素删除即可
```java
//添加元素
public boolean add(E e) {
    linkLast(e);
    return true;
}
void linkLast(E e) {
    final Node<E> l = last;
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;
    if (l == null)
        first = newNode;
    else
        l.next = newNode;
    size++;
    modCount++;
}
//查找元素
 public int indexOf(Object o) {
    int index = 0;
    if (o == null) {
        for (Node<E> x = first; x != null; x = x.next) {
            if (x.item == null)
                return index;
            index++;
        }
    } else {
        for (Node<E> x = first; x != null; x = x.next) {
            if (o.equals(x.item))
                return index;
            index++;
        }
    }
    return -1;
}

```
##适用场景不同
>* Vector 和 ArrayList 作为动态数组，其内部元素以数组形式顺序存储的，所以非常适合随机访问的场合。除了尾部插入和删除元素，往往性能会相对较差，比如我们在中间位置插入一个元素，需要移动后续所有元素。
>* LinkedList由于基于链表方式存放数据，增加和删除元素的速度较快，但是检索速度较慢。

所以，在应用开发中，如果事先可以估计到，应用操作是偏向于插入、删除，还是随机访问较多，就可以针对性的进行选择。这也是面试最常见的一个考察角度，给定一个场景，选择适合的数据结构。
##扩展
###集合框架结构图
![collection-structure](https://github.com/cropwatchman/cropwatchman.github.io/blob/master/img/2018-01-01-Collection.png)
![collection-structure](https://github.com/cropwatchman/cropwatchman.github.io/blob/master/img/2018-01-01-map.png)
我们可以看到 Java 的集合框架，Collection 接口是所有集合的根，然后扩展开提供了三大类集合，分别是：
>* List，也就是我们前面介绍最多的有序集合，它提供了方便的访问、插入、删除等操作。
>* Set，Set 是不允许重复元素的，这是和 List 最明显的区别，也就是不存在两个对象 equals返回 true。我们在日常开发中有很多需要保证元素唯一性的场合。
>* Queue/Deque，则是 Java 提供的标准队列结构的实现，除了集合的基本功能，它还支持类
似先入先出（FIFO， First-in-First-Out）或者后入先出（LIFO，Last-In-First-Out）等特
定行为。这里不包括 BlockingQueue，因为通常是并发编程场合，所以被放置在并发包里。

每种集合的通用逻辑，都被抽象到相应的抽象类之中，比如AbstractList就集中了各种List操作的通用部分。这些集合不是完全孤立的，比如，LinkedList 本身，既是 List，也是 Deque

如果阅读过更多源码，你会发现，其实，TreeSet 代码里实际默认是利用 TreeMap 实现的，Java 类库创建了一个 Dummy 对象“PRESENT”作为 value，然后所有插入的元素其实是以键的形式放入了 TreeMap 里面；同理，HashSet 其实也是以 HashMap 为基础实现的，原来他们只是 Map 类的马甲！
```java
 private static final Object PRESENT = new Object();
 
 //添加元素
  public boolean add(E e) {
    return map.put(e, PRESENT)==null;
  }
  
  public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
  
  }
```

就像前面提到过的，我们需要对各种具体集合实现，至少了解基本特征和典型使用场景，以 Set的几个实现为例：
TreeSet 支持自然顺序访问，但是添加、删除、包含等操作要相对低效（log(n)时间）。HashSet则是利用哈希算法，理想情况下，如果哈希散列正常，可以提供常数时间的添加、删除、包含等操作，但是它不保证有序。LinkedHashSet，内部构建了一个记录插入顺序的双向链表，因此提供了按照插入顺序遍历的能力，与此同时，也保证了常数时间的添加、删除、包含等操作，这些操作性能略低于HashSet，因为需要维护链表的开销。在遍历元素时，HashSet 性能受自身容量影响，所以初始化时，除非有必要，不然不要将其背后的HashMap容量设置过大。而对于LinkedHashSet，由于其内部链表提供的方便，遍历性能只和元素多少有关系。我今天介绍的这些集合类，都不是线程安全的，对于 java.util.concurrent里面的线程安全容器，我在专栏后面会去介绍。但是，并不代表这些集合完全不能支持并发编程的场景，在Collections 工具类中，提供了一系列的 synchronized 方法，比如
```java
static <T> List<T> synchronizedList(List<T> list)
```

我们完全可以利用类似方法来实现基本的线程安全集合：
```java
List list = Collections.synchronizedList(new ArrayList());
```

它的实现，基本就是将每个基本方法，比如get、set、add之类，都通过synchronizd添加基本的同步支持，非常单粗暴，但也非常实用。注意这些方法创建的线程安全集合，都符合迭代时fail-fast行为，当发生意外的并发尽早抛出ConcurrentModificationException 异常，以避免不可预计的行为。

###其它
在 Java 9 中，Java 标准类库提供了一系列的静态工厂方法，比如，List.of()、Set.of()，大大简化了构建小器实例的代码量。根据业界实践经验，我们发现相当一部分集合实例都是容量非常有限的，而且在生命周期中并不会进行修改。但是，在原有的 Java 类库中，我们可能不得不写成：
```java
ArrayList<String> list = new ArrayList<>();
list.add("Hello");
list.add("World");
```

而利用新的容器静态工厂方法，一句代码就够了，并且保证了不可变性。
```java
List<String> simpleList = List.of("Hello","world");
```

通过各种 of 静态工厂方法创建的实例，还应用了一些我们所谓的最佳实践，比如，它是不可变的，符合我们对线程安全的需求；它因为不需要考虑扩容，所以空间上更加紧凑等。
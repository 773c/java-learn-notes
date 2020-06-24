![image-20200515091722519](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200515091722519.png)

#### Map集合

##### 一、HashMap

##### （1）JDK7

> HashMap是采用`数组`+`链表`来完成存储的。数组是用来存储数据，链表则是为了解决哈希冲突而存在的（`哈希冲突是当两个key的hashCode方法计算的hash值相同时导致索引值相同所产生的冲突`）。名为拉链法。

**字段：**

1. static final int DEFAULT_INITIAL_CAPACITY = 1 << 4（`默认容量大小16`）
2. static final float DEFAULT_LOAD_FACTOR = 0.75f（`默认加载因子0.75`）
3. transient int size（`有效数据`）
4. int threshold（`临界值`）
5. final float loadFactor（`加载因子`）

````java
//获取map字节码
Map<String, String> map = new HashMap<>();
map.put("hollis1", "hollischuang");
map.put("hollis2", "hollischuang");
map.put("hollis3", "hollischuang");
map.put("hollis4", "hollischuang");
map.put("hollis5", "hollischuang");
map.put("hollis6", "hollischuang");
map.put("hollis7", "hollischuang");
map.put("hollis8", "hollischuang");
map.put("hollis9", "hollischuang");
map.put("hollis10", "hollischuang");
map.put("hollis11", "hollischuang");
map.put("hollis12", "hollischuang");
Class<?> mapType = map.getClass();

Method capacity = mapType.getDeclaredMethod("capacity");
capacity.setAccessible(true);
System.out.println("capacity : " + capacity.invoke(map));

Field size = mapType.getDeclaredField("size");
size.setAccessible(true);
System.out.println("size : " + size.get(map));

Field threshold = mapType.getDeclaredField("threshold");
threshold.setAccessible(true);
System.out.println("threshold : " + threshold.get(map));

Field loadFactor = mapType.getDeclaredField("loadFactor");
loadFactor.setAccessible(true);
System.out.println("loadFactor : " + loadFactor.get(map));

map.put("hollis13", "hollischuang");
Method capacity = mapType.getDeclaredMethod("capacity");
capacity.setAccessible(true);
System.out.println("capacity : " + capacity.invoke(map));

Field size = mapType.getDeclaredField("size");
size.setAccessible(true);
System.out.println("size : " + size.get(map));

Field threshold = mapType.getDeclaredField("threshold");
threshold.setAccessible(true);
System.out.println("threshold : " + threshold.get(map));

Field loadFactor = mapType.getDeclaredField("loadFactor");
loadFactor.setAccessible(true);
System.out.println("loadFactor : " + loadFactor.get(map));
````

##### （1-1）`put`方法源码如下：

````java
    public V put(K key, V value) {
        /**
         * 1-1-1
         */
        if (table == EMPTY_TABLE) {
            inflateTable(threshold);  //threshold:阈值（边界值），默认为12
        }
        
        if (key == null)
            return putForNullKey(value);
        /**
         * 1-1-2
         */
        int hash = hash(key);	//获取key的hash值
        int i = indexFor(hash, table.length);	//获取数组下标
        /**
         * 1-1-3
         */
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }

        modCount++;
        /**
         * 1-1-4
         */
        addEntry(hash, key, value, i);
        return null;
    }
````

**（1-1-1）`inflateTable(threshold)`的作用**

> 就是为了获取一个大于等于`数组大小（capacity）`的`2的幂次方`的数字去初始化`Entry`数组
>
> `threshold（边界值）=capacity（容量）* loadFactor（加载因子）`

**`put`方法源码如下：**

````java
        if (table == EMPTY_TABLE) {
            inflateTable(threshold);  //threshold:阈值（边界值），默认为12
        }
````

**`inflateTable`源码如下：**

```java
private void inflateTable(int toSize) {	
    // Find a power of 2 >= toSize
    int capacity = roundUpToPowerOf2(toSize); //获取一个大于等于toSize的2的幂次方数

    threshold = (int) Math.min(capacity * loadFactor, MAXIMUM_CAPACITY + 1);
    table = new Entry[capacity];
    initHashSeedAsNeeded(capacity);
}
```
**为什么capacity必须是2的幂次方数？**

> 为了数据的均匀分配，尽量减少`哈希碰撞`导致链表越来越长，可能出现其他位置没有存储元素，性能下降。

**`roundUpToPowerOf2`方法详解**：

> 返回一个大于等于toSize的2的幂次方的数，例如 7 则返回 8，10 则返回 16，JDK7是在第一次put的时候才进行此操作

**源码如下：**

````java
    private static int roundUpToPowerOf2(int number) {
        // assert number >= 0 : "number must be non-negative";
        return number >= MAXIMUM_CAPACITY
                ? MAXIMUM_CAPACITY
                : (number > 1) ? Integer.highestOneBit((number - 1) << 1) : 1;
    }
````

**`highestOneBit`方法详解**：

> 返回一个小于等于 i 的2的幂次方的数，例如 15 则返回 8。

`当一个数为2的幂次方数时，该数的2进制只会有一个1，例如 8 => 0000 1000`

**源码如下：**

````java
    public static int highestOneBit(int i) {
        // HD, Figure 3-1
        i |= (i >>  1);
        i |= (i >>  2);
        i |= (i >>  4);
        i |= (i >>  8);
        i |= (i >> 16);
        return i - (i >>> 1);
    }
````

**以`10`为例子解析上例代码：**

|（或运算）：有1则为1

》（右移运算）：向右移 n 个单位，正数左边加n个0 ，右边去掉，负数左边加1，右边去掉。

10转为2进制为：`0000 1010`

 i  |  =  ( i  >>  1 )：

​				  i  >>  1：`0000 0101`

  i  =  i  |  i  >>  1：`0000 1111`

后面的2-16的2进制都为：`0000 1111`（因为i 后面为1111，而2-16都是1往后移）

最后i  -  (  i  >>>  1  )：

​									`0000 1111`

​							一  `0000 0111`

​									`0000 1000`



**（1-1-2）数组需要给定下标值去存储数据时如何实现的呢？**

* 首先，会将存储的`key`通过`hashCode方法`获取一个`hash值`，这个hash值可能大于数组的内存大小，所以会将它进行`与（&）运算`，然后再将数据存储到数组中。所以`key`就相当于`下标值`。

**`put`方法源码如下：**

````java
	int i = indexFor(hash, table.length);	//获取数组下标
````

**`indexFor`方法源码如下：**

````java
    static int indexFor(int h, int length) {
        // assert Integer.bitCount(length) == 1 : "length must be a non-zero power of 2";
        return h & (length-1);	//h：hash值
    }
````

1. 首先，length的长度是`2的幂次方`
2. 因为`2的幂次方`只会在2进制上的某个bit位上，所以length-1是为了保证当前length这个2进制bit位上的数字为`0`，这样与（&）运算就能够保证该位上的结果为`0`，这样就能保证数组下标的值在`0 - 2的幂次方-1`这个范围之间
3.  `h & (length-1)`前提是length必须是2的幂次方数。

**当两个对象的hashCode相等时会怎么样？**

> 会产生`哈希碰撞`，则会通过`==`比较key的地址值是否相等`或`者通过`equal()`比较key的内容是否相等，如果相等，则替换key的value值，否则，继续添加。

**为什么是用位运算（&）来获取索引值？**

> 因为效率比较高，如果不考虑效率问题，直接`取余`即可，就不用考虑容量是`2的幂次方`了。即
>
> `h & (length-1)` == `h % (length-1)`

**（1-1-4）为什么haspMap需要扩容？**

> 因为数组大小是固定的，当数据越来越多都存在链表中，遍历的速度会下降

**`put`方法源码如下：**

````java
addEntry(hash, key, value, i);
````

**`addEntry`方法源码如下：**

````java
    void addEntry(int hash, K key, V value, int bucketIndex) {
        //当有效数据大于等于阈值（数组的容量大小）并且数组上的数据不为null
        if ((size >= threshold) && (null != table[bucketIndex])) {
            resize(2 * table.length);
            hash = (null != key) ? hash(key) : 0;
            bucketIndex = indexFor(hash, table.length);
        }

        createEntry(hash, key, value, bucketIndex);
    }
````

**`resize`方法源码如下：**

````java
    void resize(int newCapacity) {
        Entry[] oldTable = table;
        int oldCapacity = oldTable.length;
        if (oldCapacity == MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return;
        }

        Entry[] newTable = new Entry[newCapacity];
        transfer(newTable, initHashSeedAsNeeded(newCapacity));
        table = newTable;
        threshold = (int)Math.min(newCapacity * loadFactor, MAXIMUM_CAPACITY + 1);
    }
````

**`transfer`方法源码如下：**

````java
    void transfer(Entry[] newTable, boolean rehash) {
        int newCapacity = newTable.length;
        for (Entry<K,V> e : table) {
            while(null != e) {
                Entry<K,V> next = e.next;
                if (rehash) {
                    e.hash = null == e.key ? 0 : hash(e.key);
                }
                int i = indexFor(e.hash, newCapacity);
                e.next = newTable[i];
                newTable[i] = e;
                e = next;
            }
        }
    }
````



##### （2）JDK8

> HashMap则是采用`数组`+（`链表`or`红黑树`）来完成存储的。为了更好的解决哈希冲突所产生的红黑树（当链表`阈值`大于8并且数组长度大于64时，索引位置上的数据则改为`红黑树`来存储）

![image-20200507104704648](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200507104704648.png)

**JDK7和JDK8在初始化`容量（capacity）`的区别**

> JDK7之前是在第一次put的时候才进行容量的一个2的幂次方数的操作，而JDK8之后是在创建HashMap对象时就会进行容量的一个操作。

如下为JDK7的源码（`inflateTable(threshold)`）

````java
    public V put(K key, V value) {
        if (table == EMPTY_TABLE) {
            //获取大于等于threshold的第一个2的幂次方数
            inflateTable(threshold);
        }
        if (key == null)
            return putForNullKey(value);
        int hash = hash(key);
        int i = indexFor(hash, table.length);
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }

        modCount++;
        addEntry(hash, key, value, i);
        return null;
    }
````

如下为JDK8的源码（`this.threshold = tableSizeFor(initialCapacity)`）

````java
    public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);
        this.loadFactor = loadFactor;
        //获取大于等于initialCapacity的第一个2的幂次方数
        this.threshold = tableSizeFor(initialCapacity);	
    }

    static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }
````

**为什么JDK7是`threshold`来进行一个2的幂次方的操作，而JDK8是`initialCapacity`?**

> 它将initialCapacity赋值给了threshold，而此时的initialCapacity会变为0

如下为JDK7的构造函数

````java
    public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);

        this.loadFactor = loadFactor;
        threshold = initialCapacity;
        init();
    }
````

**JDK7和JDK8在初始化`临界值（threshold）`的区别**

> 两者都是在第一次put的时候进行初始化threshold，所以在只创建了HahsMap对象的时候`threshold`就等于`capacity`

##### 二、concurrentHashMap


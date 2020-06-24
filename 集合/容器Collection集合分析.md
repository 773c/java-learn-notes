![image-20200515091451969](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200515091451969.png)

#### Collection集合

##### 一、List

>有先后顺序，数值不唯一，可重复。

##### （1）ArrayList

* 核心为`数组`实现了List接口，是线程不安全的
* 由于基于数组实现的，所以擅长`顺序访问`和`随机下标访问`，速度较快，时间复杂度是`O(1)`
* 会自动扩增容量的Array（默认将会创建大小为10的数组）
* `数组扩容`是对ArrayList效率影响比较大的一个因素。每当添加元素时，都会检查内部数组的容量是否不够了，如果不够则以当前容量的`两倍`来重新构建一个数组，将旧元素添加到新数组中，然后再丢弃旧数组
* 增、删会导致元素大量移动，非常耗时，时间复杂度是`O(N)`

##### （2）LinkedList

* 核心为`链表`实现了List接口，是线程不安全的

- 基于头尾相连的双向链表实现，只能顺序或逆序访问，但是可以快速地在链表中间`插入`和`删除元素`，时间复杂度是`O(1)`                                                     
- 随机访问速度较慢，时间复杂度是`O(N)`
- 用作栈、队列和双向队列

##### ArrayList和LinkedList遍历的对比

````java
  // 先创建一个100w元素的ArrayList
        for (int i=0; i < 1000000; i++){
            arrayList.add(i);
        }

        long start, end;

        // foreach循环
        start = System.currentTimeMillis();
        for (Integer item : arrayList) {
            //System.out.println(item);
        }
        end = System.currentTimeMillis();
        System.out.println("foreach cost time = " + (end-start) + "ms");

        // 普通for循环，通过get函数获取元素
        start = System.currentTimeMillis();
        for (int i=0; i < arrayList.size(); i++) {
            arrayList.get(i);
        }
        end = System.currentTimeMillis();
        System.out.println("for cost time = " + (end-start) + "ms");

        // 迭代器循环
        start = System.currentTimeMillis();
        Iterator<Integer> it = arrayList.iterator();
        while (it.hasNext()) {
            it.next();
        }
        end = System.currentTimeMillis();
        System.out.println("Iterator cost time = " + (end-start) + "ms");
````

1. foreach cost time = 15ms
2. for cost time = 7ms
3. Iterator cost time = 29ms

> `ArrayList`推荐用`for`循环get元素遍历最快，不仅是连续内存读取，而且还有内联优化节省函数调用开销。
>
> `LinkedList`推荐用`foreach`遍历元素最快，因为内部实现是迭代器。
>
> 相同的数据量，ArrayList最快的遍历比LinkedList最快遍历快一倍。

##### 二、Set

> 无先后顺序，唯一元素，不可以重复

##### （1）HashSet

* 在`jdk1.8`中，它是由`HashMap`实现的，是线程不安全的
* HashSet不是同步的，如果多个线程同时访问一个HashSet，则必须通过代码来保证其同步
* 集合元素值可以是null

**补充：**

1. HashSet的实质是一个`HashMap`。HashSet的所有集合元素，构成了HashMap的`key`，其`value`为一个静态Object对象。因此HashSet的所有性质，HashMap的key所构成的集合都具备。可以参考后续文章中HashMap的相关内容进行比对。
2. HashSet判断两个元素相等的标准：**两个对象通过equals()方法比较相等，并且两个对象的hashCode()方法返回值也相等。**

##### （2）LinkedHashSet

* LinkedHashSet是HashSet的子类，也是线程不安全的
* 也是根据元素的hash值来决定存储的位置，同时使用链表来决定元素的次序

##### （3）TreeSet

* 


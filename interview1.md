## （一）、概览

 容器主要分为Collection和Map两种，Collection主要用于存储对象的集合，而Map主要存储着键值对的映射表
### -&nbsp;&nbsp;&nbsp;&nbsp;**Collection**
![08cdec4609aa91aaf32a36a3e9b888f9.png](en-resource://database/1093:1)



#### 1.Set

 - TreeSet:基于红黑树实现，支持有序性操作，例如根据一个范围查找元素的操作。但是查找效率不如HashSet，HashSet查找的时间复杂度为O（1），TreeSet则为O（logN）。
 - HashSet：基于哈希表实现，支持快速查找，但不支持有序性操作。并且失去了元素的插入顺序信息，也就是说使用Iterator遍历HashSet得到的结果是不确定的
 - LinkedHashSet：具有HashSet的查找效率，且内部使用双向链表维护元素的插入顺序
 #### 2.List

 - ArrayList：基于动态数组实现，支持随机访问。
 - Vector：和ArrayList类似，但它是线程安全的。
 - LinkedList：基于双向链表实现，只能顺序访问，但是可以快速地在链表中间插入和删除元素。不仅如此，LinkedList还可以用作栈，队列和双向队列。
 #### 3.Queue

 - PriorityQueue：基于堆结构实现，可以用它来实现优先队列
 - LinkedList：可以用它来实现双向队列。
 ### -&nbsp;&nbsp;&nbsp;&nbsp;**Map**
![d8a0c7826c5fb1df274b9a60b86d2c8a.png](en-resource://database/1095:1)


 - TreeMap：基于红黑树实现
 - HashMap：基于哈希表实现
 - 	HashTable：和HashMap类似，但它是线程安全的，这意味着同一个时刻多线程可以同时写入HashTable并且不会导致数据不一致。它是遗留类，不应该去使用它。现在可以使用ConcurrentHashMap来支持线程安全
 - LinkedHashMap：使用双向链表来维护元素的顺序，顺序为插入顺序或者最近最少使用（LRU）顺序。
## （二）、容器中的设计模式
### -&nbsp;&nbsp;&nbsp;&nbsp;**迭代器模式**
![f770c19101e0b51dd87353ed7962604b.png](en-resource://database/1097:1)


 - Collection继承了Iterable的接口，Iterable中的Iterator（）方法能够返回一个Iterator对象，这个对象可以实现元素的迭代遍历。
 - 因为Collection继承了Iterable接口，也就可以使用增强for循环
 

```java
Collection<String> str = new ArrayList<>();
        str.add("aa");
        str.add("bb");
        for(String temp: str) {
            System.out.println(temp);
        }
```
 - Java中的增强for循环的实现原理与坑可以参见推文：[Java中的增强for循环的底层实现原理与坑](https://blog.csdn.net/weixin_41799019/article/details/98112586)
### -&nbsp;&nbsp;&nbsp;&nbsp;**适配器模式**
 - 举其中一个例子，Arrays.asList()方法就使用了适配器模式
 - 使用Arrays.asList()方法可以很方便地将数据转换为集合，但是转换后的集合不能使用修改的相关操作（add，remove，clear）等
 - 如果使用修改的方法，程序会抛出UnsupportedOperationException 异常。
 - 具体实现来看ArrayList（）的相关源码：
 1. 首先来看Arrays.asList()方法,这里直接创建了一个ArrayList
```java
	@SafeVarargs
    @SuppressWarnings("varargs")
    public static <T> List<T> asList(T... a) {
        return new ArrayList<>(a);
    }
```
2. 继续深一步看源码，可以看到，他只是简单判断了一下数组是否为null，如果不为null，就直接将array的地址赋值给a

```java
ArrayList(E[] array) {
     a = Objects.requireNonNull(array);
}
```
3. 因此如果更改子列表的值，原数组的数值也会发生变化
4. 但是，后面对原列表的增加或删除，均会导致子列表的增加删除产生 ConcurrentModificationException异常

```java
  public static void main(String[] args) {
        String[] array = {"aa","bb"};
        List<String> str = Arrays.asList(array);
        str.add("cc");      //抛出java.lang.UnsupportedOperationException
        array[0] = "bb";
        System.out.println(str.get(0));     //修改原数组的值也会印象集合中的值
    }
```

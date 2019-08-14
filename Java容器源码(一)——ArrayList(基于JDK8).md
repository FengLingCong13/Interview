## （一）、ArrayList概述
## （二）、类名

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```
分析：ArrayList是继承自AbstractList，同时实现了List接口、RandomAccess接口，Clonable接口、Seriealzable接口。

 -  ==AbstractList==：同样也继承了List接口，并且实现了List接口中的方法。
   1. 可能大家会有一个疑问就是为什么ArrayList不直接去实现List接口，而是要通过AbstractList实现List接口之后，再去继承。这里主要是涉及了设计模式中的知识。当AbstractList已经实现了List接口之后，就已经实现了大部分需要的功能了。ArrayList只需要完成一些想要的拓展功能即可。减少了代码的重复。
   2.  还有一点比较奇怪的就是。为什么AbstractList已经实现了List接口，ArrayList还要再次实现一次List接口。据ArrayList的作者说，这主要是当时自己的错误造成的，原本以为是为了以后拓展功能做铺垫，实际并没有什么作用。
```java
//AbstractList的源码
public abstract class AbstractList<E> extends AbstractCollection<E> implements List<E>
```
 -  ==RandomAccess==：RandomAccss实际是一个空接口，只是起到标识作用。
```java
//RandomAcces接口的源码
public interface RandomAccess {
}
```
1.  主要起到标识的作用。只要实现了这个接口就支持随机访问。我们可以看一下Collections类中的binarySearch方法，方法中判断了List是否实现了RandomAccess方法，如果实现了，那么就使用indexedBinarySearch（普通for循环进行遍历）。反之没有实现RandomAccess接口的话，则使用iteratorBinarySearch（迭代器进行遍历）
2. 如果有兴趣的朋友可以更进一步查看两种遍历的源码，并进行测试。会发现，ArrayList使用普通for循环进行遍历效率更高，LinkedList使用迭代器进行遍历效率更高。想更深入了解的朋友可以看一下这篇文章：[ArrayList集合实现RandomAccess接口有何作用？为何LinkedList集合却没实现这接口？](https://blog.csdn.net/weixin_39148512/article/details/79234817)

```java
#Collections类中的binarySearch方法
public static <T>
    int binarySearch(List<? extends Comparable<? super T>> list, T key) {
    	#判断是否实现了RandomAccess
        if (list instanceof RandomAccess || list.size()<BINARYSEARCH_THRESHOLD)
            return Collections.indexedBinarySearch(list, key);
        else
            return Collections.iteratorBinarySearch(list, key);
    }
```
-  ==Cloneable==：实现了Cloneable接口，就可以实现克隆功能
-  ==Serializable==：实现了Serializable接口，就可以实现序列化和反序列化功能
## （三）、属性

```java
//序列化版本号
 private static final long serialVersionUID = 8683452581122892189L;

    /**
     * 默认的初始化容量
     */
    private static final int DEFAULT_CAPACITY = 10;

    /**
     * 当设置ArrayList长度为0时的空数组
     */
    private static final Object[] EMPTY_ELEMENTDATA = {};

    /**
    * 初始化时未指定ArrayList的长度时的空数组
    */
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

    /**
    * transient代表不能被序列化。这个数组用于保存数据
    */
    transient Object[] elementData; // non-private to simplify nested class access

    /**
     * ArrayList中实际的数组长度
     *
     * @serial
     */
    private int size;
```

 1. DEFAULT_CAPACITY：默认的初始化容量为10。
 2. EMPTY_ELEMENTDATA：当设置ArrayList长度为0时的空数组。
 3. DEFAULTCAPACITY_EMPTY_ELEMENTDATA：初始化时未指定ArrayList的长度时的空数组。
 4. elementData：transient代表不能被序列化。这个数组用于保存数据。
 5. size：数组的实际长度。
 ## （四）、构造方法
 

```java
   /**
     * 一个参数的构造方法，指定初始化容量
     */
    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
        	//如果指定的容量大于0，则创建一个指定长度的数组
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
        	//如果指定的容量为0，就创建一个空的数组
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
        	//如果指定的容量小于0，则抛出异常
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }

    /**
     * 无参构造方法，创建一个空数组，自动初始化容量为10
     */
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }

    /**
     * 以Collection作为参数的构造方法，主要是实现复制功能
     */
    public ArrayList(Collection<? extends E> c) {
    	//首先将Collection转换为数组并赋值
        elementData = c.toArray();
        //判断数组的长度是否等于0
        if ((size = elementData.length) != 0) {
            // 判断是否为Object类型，如果不是的话，就是进行转换
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // 长度等于0，则赋值为空数组
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }
```

 1. ArrayList()：无参构造方法，创建一个空数组，自动初始化容量为10
 2. ArrayList(int initialCapacity)：一个参数的构造方法，指定初始化容量
 3. ArrayList(Collection<? extends E> c)：以Collection作为参数的构造方法，主要是实现复制功能
## （五）、Add方法（扩容操作）

```java
	/**
     * 在ArrayList的末尾添加上元素
     */
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }

    /**
     * 在指定的位置添加上元素
     */
    public void add(int index, E element) {
        rangeCheckForAdd(index);

        ensureCapacityInternal(size + 1);  // Increments modCount!!
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
        elementData[index] = element;
        size++;
    }
    /**
    * 将另一个Collection对象添加到当前ArrayList中
    */
     public boolean addAll(Collection<? extends E> c) {
        Object[] a = c.toArray();
        int numNew = a.length;
        ensureCapacityInternal(size + numNew);  // Increments modCount
        System.arraycopy(a, 0, elementData, size, numNew);
        size += numNew;
        return numNew != 0;
    }

    /**
     * 在指定位置，将另一个Collection对象添加到当前ArrayList中
     */
    public boolean addAll(int index, Collection<? extends E> c) {
        rangeCheckForAdd(index);

        Object[] a = c.toArray();
        int numNew = a.length;
        ensureCapacityInternal(size + numNew);  // Increments modCount

        int numMoved = size - index;
        if (numMoved > 0)
            System.arraycopy(elementData, index, elementData, index + numNew,
                             numMoved);

        System.arraycopy(a, 0, elementData, index, numNew);
        size += numNew;
        return numNew != 0;
    }
```

 -  ==Add（）方法主要涉及的是扩容操作，需要涉及很多个成员方法，但是基本操作大致上都一样，看懂一个，其他三个方法也能很容易明白。这里就用add(int index, E element)方法进行举例。==
  1. 我们先来看add(int index, E element)的源码，这个方法主要的功能是在指定位置插入元素。
  

```java
	public void add(int index, E element) {
		//检查要插入的位置是否合法
        rangeCheckForAdd(index);
		//确保数组的容量够，如果不够，进行扩容操作
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        //将index+1位置到末尾的元素全部往后移动一位
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
        //在index位置上插入元素
        elementData[index] = element;
        //ArrayList的实际容量增加1
        size++;
    }
```
2. 我们进入 rangeCheckForAdd(index)方法中，方法的实现很简单，判断index是否合法，如果不合法就抛出异常。

```java
	/**
     * 查看插入位置是否合法
     */
    private void rangeCheckForAdd(int index) {
        if (index > size || index < 0)
        	//如果位置不合法就抛出异常
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
```
3. 接着我们就回到add()方法中，然后进入到ensureCapacityInternal(size + 1)方法中，因为是插入一个元素，所以就是最少需要的容量=实际容量size+1。进入方法后，首先要判断是否是空数组，如果是空数组的话，要设置初始化容量为10。然后调用ensureExplicitCapacity(minCapacity);

```java
	/**
     * 判断是否是空数组，如果是空数组的话，要设置初始化容量为10
     */
	private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        	//判断是否是空数组，如果是空数组的话，要设置初始化容量为10
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }
		//调用方法。
        ensureExplicitCapacity(minCapacity);
    }
```
4. 然后我们进入到ensureExplicitCapacity(int minCapacity)方法中。modCount要先加1，这个属性是在AbstractList中的，主要用来记录修改操作，用来识别快速失败(fast-fail机制)，有兴趣可以了解一下。然后判断最小容量是否大于当前数组的长度，如果大于就调用grow()方法进行扩容。

```java
	private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // 判断最小容量是否大于当前数组的长度，如果大于就调用grow()方法进行扩容
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
```
5. 在grow()方法中，首先将原数组的容量赋值给oldCapacity。然后将oldCapacity容量扩大为1.5倍赋值给newCapacity。如果新的容量newCapacity还是小于最小容量，那么就把最小容量赋值给newCapacity。如果newCapacity超过了数组的最大容量就要调用hugeCapacity()方法进行扩容。最后就是利用Arrays.copyOf(elementData, newCapacity)创建一个新数组赋值给elementData，完成扩容。

```java
	private void grow(int minCapacity) {
        // 将原数组的容量赋值给oldCapacity
        int oldCapacity = elementData.length;
        //进行移位运算，将oldCapacity容量扩大为1.5倍赋值给newCapacity
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        //如果新的容量newCapacity还是小于最小容量，那么就把最小容量赋值给newCapacity
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        //如果newCapacity超过了数组的最大容量就要调用hugeCapacity()方法进行扩容
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // 利用Arrays.copyOf(elementData, newCapacity)创建一个新数组赋值给elementData，完成扩容
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```
6. 上一步中调用了hugeCapacity(minCapacity)方法，这个方法使用了三元表达式，如果最小容量大于最大数组长度，就返回整数的最大值，否则返回数组最大长度。

```java
	private static int hugeCapacity(int minCapacity) {
		//范围不合法
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        //使用了三元表达式，如果最小容量大于最大数组长度，就返回整数的最大值，否则返回数组最大长度    
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
```
## （六）、Remove方法（删除元素）

```java
	/**
     * 删除指定位置上的元素
     */
	public E remove(int index) {
		//首先判断范围是否合法
        rangeCheck(index);
		//修改次数+1，前面已经提到过了
        modCount++;
        //获取index位置上的旧值
        E oldValue = elementData(index);
		//计算删除该位置的元素，需要移动元素的个数
        int numMoved = size - index - 1;
        if (numMoved > 0)
        	//将index+1后面的元素全部向前移动1位
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        //将size-1位置的值设置为null，便于垃圾收集
        elementData[--size] = null; // clear to let GC do its work
		//返回被删除的元素
        return oldValue;
    }
    /**
     * 删除指定对象的元素
     */
    public boolean remove(Object o) {
    	//如果对象为null对象
        if (o == null) {
        	//遍历数组
            for (int index = 0; index < size; index++)
            	//如果发现有值为null，调用fastRemove()方法删除元素
                if (elementData[index] == null) {
                    fastRemove(index);
                    return true;
                }
        } else {
        	//遍历数组
            for (int index = 0; index < size; index++)
            	//如果发现有值为o，调用fastRemove()方法删除元素
                if (o.equals(elementData[index])) {
                    fastRemove(index);
                    return true;
                }
        }
        //没有该元素，返回false
        return false;
    }
```

 - remove(int index)方法：删除指定位置上的元素，步骤如下：
1. 首先判断范围是否合法
2. 修改次数+1，前面已经提到过了
3. 获取index位置上的旧值
4. 计算删除该位置的元素，需要移动元素的个数
5. 将index+1后面的元素全部向前移动1位
6. 将size-1位置的值设置为null，便于垃圾收集
7. 返回被删除的元素
 - remove(Object o)方法：删除指定位置上的元素，步骤如下：
1. 判断对象是否为null对象
2. 如果为null，遍历数组，进行删除
3. 如果不为null，也遍历数组，进行删除
 

```java
	private void fastRemove(int index) {
		//修改次数+1
        modCount++;
        //计算移动元素的个数
        int numMoved = size - index - 1;
        if (numMoved > 0)
        	//向前移动一位
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        //最后一位设置位null，利于垃圾收集
        elementData[--size] = null; // clear to let GC do its work
    }
```
删除过程中调用了fastRemove(int index)方法，这个方法其实就是将index位置后的元素全部向前移动1位，实现元素删除。
## （七）、序列化
ArrayList中与序列化相关的方法主要有两个， 分别是readObject()以及writeObject()

```java
	private void writeObject(java.io.ObjectOutputStream s)
        throws java.io.IOException{
        // Write out element count, and any hidden stuff
        int expectedModCount = modCount;
        s.defaultWriteObject();

        // Write out size as capacity for behavioural compatibility with clone()
        s.writeInt(size);

        // Write out all elements in the proper order.
        for (int i=0; i<size; i++) {
            s.writeObject(elementData[i]);
        }

        if (modCount != expectedModCount) {
            throw new ConcurrentModificationException();
        }
    }
```
1. writeObject()主要完成的是序列化操作
2. 首先要判断期望的ModCount和实际的ModCount是否相等，如果不相等说明发生了fast-fail（快速失败）
3. 利用for循环逐一将元素写入到输出流中

```java
	private void readObject(java.io.ObjectInputStream s)
        throws java.io.IOException, ClassNotFoundException {
        elementData = EMPTY_ELEMENTDATA;

        // Read in size, and any hidden stuff
        s.defaultReadObject();

        // Read in capacity
        s.readInt(); // ignored

        if (size > 0) {
            // be like clone(), allocate array based upon size not capacity
            ensureCapacityInternal(size);

            Object[] a = elementData;
            // Read in all elements in the proper order.
            for (int i=0; i<size; i++) {
                a[i] = s.readObject();
            }
        }
    }
```
1. readObject()主要完成的是反序列化操作
2. 首先要判断实际容量是否大于0，如果大于0，调整容量，根据实际大小来分配空间，而不是根据容量来分配空间。
3. 利用for循环从流中写入数据
## （八）、trimToSize()方法

```java
	/**
     * 将ArrayList的容量缩容为实际大小
     */
    public void trimToSize() {
    	//修改次数+1
        modCount++;
        //如果实际大小小于容量
        if (size < elementData.length) {
        	//三元表达式进行缩容
            elementData = (size == 0)
              ? EMPTY_ELEMENTDATA
              : Arrays.copyOf(elementData, size);
        }
    }
```
因为ArrayList的元素数目可能不等于数组的容量，所以有时为了节省空间，会对ArrayList进行缩容操作，将实际大小=数组容量。
## （九）、indexOf()方法

```java
	/**
	* 获取某个元素的位置
	*/
	public int indexOf(Object o) {
		//如果为null
        if (o == null) {
        	//for循环进遍历，如果存在就返回该位置
            for (int i = 0; i < size; i++)
                if (elementData[i]==null)
                    return i;
        } else {
        	//不为null，for循环进遍历，如果存在就返回该位置
            for (int i = 0; i < size; i++)
                if (o.equals(elementData[i]))
                    return i;
        }
        //不存在返回-1
        return -1;
    }

    /**
     * 逆序获得指定元素的位置
     */
    public int lastIndexOf(Object o) {
    	//如果为null
        if (o == null) {
        //for循环进遍历，如果存在就返回该位置
            for (int i = size-1; i >= 0; i--)
                if (elementData[i]==null)
                    return i;
        } else {
        	//不为null，for循环进遍历，如果存在就返回该位置
            for (int i = size-1; i >= 0; i--)
                if (o.equals(elementData[i]))
                    return i;
        }
        //不存在返回-1
        return -1;
    }
```

 - indexOf()和lastIndexOf()分别返回指定元素在列表中正序和逆序的位置，实现较为简单，代码中有详细注释，很容易理解。
## （十）、toArray()方法

```java
	/**
     * 直接调用Arrays.copyOf(elementData, size)返回一个新数组
     */
    public Object[] toArray() {
        return Arrays.copyOf(elementData, size);
    }

    /**
     * 这个是一个类型转换的方法，将数组转换为指定类型
     */
    @SuppressWarnings("unchecked")
    public <T> T[] toArray(T[] a) {
        if (a.length < size)
            // 返回一个T类型的新数组
            return (T[]) Arrays.copyOf(elementData, size, a.getClass());
        //复制元素
        System.arraycopy(elementData, 0, a, 0, size);
        //标识作用
        if (a.length > size)
            a[size] = null;
        return a;
    }
```

 - toArray()方法主要用于列表的转换，实现也较为容易，代码中有详细注释
 - toArray(T[] a)其中存在一些坑，详细可见这篇文章：[集合转数组的toArray()和toArray(T[] a)方法](https://www.cnblogs.com/goloving/p/7693388.html)
## （十一）、batchRemove()方法（批量删除）

```java
	/**
	* 用于批量删除
	*/
	private boolean batchRemove(Collection<?> c, boolean complement) {
		//获取原数组中的元素
        final Object[] elementData = this.elementData;
        //设置两个指针，初始值为0
        int r = 0, w = 0;
        //设置标志值modified，初始值为false
        boolean modified = false;
        try {
        	//删除操作
            for (; r < size; r++)
                if (c.contains(elementData[r]) == complement)
                    elementData[w++] = elementData[r];
        } finally {
            //如果删除完成后，还有剩的，就补齐到原数组中
            if (r != size) {
                System.arraycopy(elementData, r,
                                 elementData, w,
                                 size - r);
                w += size - r;
            }
            //如果删除后，新数组的长度小于原数组的长度，将后面的值置位null，便于垃圾收集
            if (w != size) {
                // clear to let GC do its work
                for (int i = w; i < size; i++)
                    elementData[i] = null;
                modCount += size - w;
                size = w;
                modified = true;
            }
        }
        return modified;
    }
```

 1. batchRemove(Collection<?> c, boolean complement)用于批量删除，complement参数取true的时候就不删除c中的元素，如果为false就删除c中的元素
 2. 如果删除完成后，还有剩的，就补齐到原数组中
 3. 如果删除后，新数组的长度小于原数组的长度，将后面的值置位null，便于垃圾收集

## （十二）、其他一些简单方法

```java
	/**
     * 返回实际大小
     */
    public int size() {
        return size;
    }

    /**
     * 判断是否为空
     */
    public boolean isEmpty() {
        return size == 0;
    }

    /**
     * 判断是否包含该元素
     */
    public boolean contains(Object o) {
        return indexOf(o) >= 0;
    }
     /**
     * 重写Object的克隆方法
     */
    public Object clone() {
        try {
        	//调用父类的克隆方法
            ArrayList<?> v = (ArrayList<?>) super.clone();
            //复制元素
            v.elementData = Arrays.copyOf(elementData, size);
            //mouCount重置
            v.modCount = 0;
            return v;
        } catch (CloneNotSupportedException e) {
            // this shouldn't happen, since we are Cloneable
            throw new InternalError(e);
        }
    }
    /**
     * 获取指定位置的元素
     */
    public E get(int index) {
        rangeCheck(index);

        return elementData(index);
    }

    /**
     * 设置指定位置的元素值
     */
    public E set(int index, E element) {
        rangeCheck(index);

        E oldValue = elementData(index);
        elementData[index] = element;
        return oldValue;
    }
    /**
     * 清空ArrayList()
     */
    public void clear() {
        modCount++;

        // clear to let GC do its work
        for (int i = 0; i < size; i++)
            elementData[i] = null;

        size = 0;
    }
```

 1. size()：返回实际大小
 2. isEmpty()：判断是否为空
 3. contains(Object o)：判断是否包含该元素
 4. clone()：重写Object的克隆方法
 5. get(int index)：获取指定位置的元素
 6. set(int index, E element)：设置指定位置的元素值
 7. clear()：清空ArrayList()
 ## （十三）、System.arraycopy() 和 Arrays.copyOf()之间的对比
 

```java
public static native void arraycopy(Object src,  int  srcPos,
                                        Object dest, int destPos,
                                        int length);
```

 - 首先来看System.arraycopy()方法，这个方法标为native，我们是看不到源码的，说明不是使用java语言实现的。主要功能就是System.arraycopy() 仅拷贝数组的内容，不会创建新数组。
 

```java
	public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
        @SuppressWarnings("unchecked")
        T[] copy = ((Object)newType == (Object)Object[].class)
            ? (T[]) new Object[newLength]
            : (T[]) Array.newInstance(newType.getComponentType(), newLength);
        System.arraycopy(original, 0, copy, 0,
                         Math.min(original.length, newLength));
        return copy;
    }
```
 - 然后是Arrays.copyOf()方法，我们可以看到他首先是根据newLength创建一个新的Object数组，然后同样调用System.arraycopy()，复制到新数组中，并返回新数组。
 ## （十四）、ModCount字段的作用
 
 - ModCount字段是主要是为了防止在多线程环境中，使用迭代器过程中，多个线程对集合进行修改操作，造成错误。
 - 具体迭代器造成的这个坑，可以参见这篇文章：[Java中的增强for循环的底层实现原理与坑](https://blog.csdn.net/weixin_41799019/article/details/98112586)

 
 







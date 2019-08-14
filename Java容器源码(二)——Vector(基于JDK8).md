
## （一）、Vector概述

 - Vecotr是基于数组实现的，是一个动态数组，数组容量可以自动增长
 - Vector和ArrayList是十分类似的，不过Vector在每一个方法中都加入了Synchronized进行修饰，保证线程的安全（其实有时候也是不安全的，需要手动保证线程安全）
 - 因为在ArrayList中较为详细的分析了源码，而Vector又几乎一样，所以还没看过ArrayList源码的朋友建议可以先看下这篇文章：[Java容器源码(一)——ArrayList(基于JDK8)](https://blog.csdn.net/weixin_41799019/article/details/99468782)。读完这篇文章之后再回来看Vector的源码就会特别轻松。
## （二）、Vector源码解析

```java
package java.util;

import java.util.function.Consumer;
import java.util.function.Predicate;
import java.util.function.UnaryOperator;

/**
*	Vector同样也是继承了AbstractList
*	实现了List接口，只是起到标识作用，AbstractList中已经实现了List接口
*	RandomAccess接口标识支持随机访问
*	Cloneable接口标识可以进行克隆
*	Serializable代表可序列化
*/
public class Vector<E>
    extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    /**
    *  	Object类型的数组，用于存放数据
     */
    protected Object[] elementData;

   
     /*
     *	数组的实际长度
     */
    protected int elementCount;

    /**
     * 数组扩容的时候的增长率
     */
    protected int capacityIncrement;

    /** 序列化的版本号 */
    private static final long serialVersionUID = -2767605614048989439L;

    /**
     * 带有两个参数的构造方法Vector(int initialCapacity, int capacityIncrement)
     */
    public Vector(int initialCapacity, int capacityIncrement) {
        super();
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        //根据initialCapacity初始化数组
        this.elementData = new Object[initialCapacity];
        //设定增长率
        this.capacityIncrement = capacityIncrement;
    }

    /**
     * 带有一个参数的构造方法Vector(int initialCapacity)
     */
    public Vector(int initialCapacity) {
    	//调用上面那个带有两个参数的构造方法，增长率设置为0
        this(initialCapacity, 0);
    }

    /**
     * 无参构造方法Vector()
     */
    public Vector() {
    	//默认设置的初始容量为10，调用一个参数的构造方法
        this(10);
    }

    /**
     * 将容器转换为Vector的构造方法Vector(Collection<? extends E> c)
     */
    public Vector(Collection<? extends E> c) {
    	//将容器转换为数组，并赋值给elementData
        elementData = c.toArray();
        //设置数组的实际长度
        elementCount = elementData.length;
        // 如果c不是存放Object类型的数据，就转换为Object类型
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, elementCount, Object[].class);
    }

    /**
     * 将Vector的数据都拷贝到传入的数组anArray中
     */
    public synchronized void copyInto(Object[] anArray) {
        System.arraycopy(elementData, 0, anArray, 0, elementCount);
    }

    /**
     * 将数组的容量缩容为实际大小
     */
    public synchronized void trimToSize() {
    	//修改次数+1
        modCount++;
        int oldCapacity = elementData.length;
        //如果实际大小小于容量，就进行缩容
        if (elementCount < oldCapacity) {
            elementData = Arrays.copyOf(elementData, elementCount);
        }
    }

    /**
     * 用于确保数组的容量足够
     */
    public synchronized void ensureCapacity(int minCapacity) {
        //如果需要的容量大于0
        if (minCapacity > 0) {
            //修改次数+1
            modCount++;
            //尝试进行扩容
            ensureCapacityHelper(minCapacity);
        }
    }

    /**
     * 检查是否需要进行扩容
     */
    private void ensureCapacityHelper(int minCapacity) {
        // 如果需要的容量大于实际容量
        if (minCapacity - elementData.length > 0)
            //进行扩容
            grow(minCapacity);
    }

    /**
     * 进行扩容操作
     */
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        //将oldCapacity扩容为oldCapacity+capacityIncrement
        int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                         capacityIncrement : oldCapacity);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        //如果新的容量大于数组的最大容量，就尝试调用更大的容量
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        //创建一个新的数组，并实现复制
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

    /**
    *	尝试获得更大的容量
    */
    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }

    /**
     * 设置Vector的实际大小
     */
    public synchronized void setSize(int newSize) {
        //修改次数+1
        modCount++;
        //容量不够，就进行扩容
        if (newSize > elementCount) {
            ensureCapacityHelper(newSize);
        } else {
        	//容量太大了，进行缩容，没有用的位置设置为null，利于垃圾收集
            for (int i = newSize ; i < elementCount ; i++) {
                elementData[i] = null;
            }
        }
        elementCount = newSize;
    }

    /**
     * 获得Vector的容量
     */
    public synchronized int capacity() {
        return elementData.length;
    }

    /**
     * 获得Vector的实际大小
     */
    public synchronized int size() {
        return elementCount;
    }

    /**
     * 是否为空
     */
    public synchronized boolean isEmpty() {
        return elementCount == 0;
    }

    /**
     * 返回Vector的枚举类
     */
    public Enumeration<E> elements() {
    	//匿名类实现
        return new Enumeration<E>() {
            //初始化为0
            int count = 0;

            //是否有下一个元素
            public boolean hasMoreElements() {
                return count < elementCount;
            }

            //获得下一个元素
            public E nextElement() {
                synchronized (Vector.this) {
                    if (count < elementCount) {
                        return elementData(count++);
                    }
                }
                throw new NoSuchElementException("Vector Enumeration");
            }
        };
    }

    /**
     * 查看是否含有o对象
     */
    public boolean contains(Object o) {
        return indexOf(o, 0) >= 0;
    }

    /**
     * 查找o对象的位置
     */
    public int indexOf(Object o) {
        return indexOf(o, 0);
    }

    /**
     * 从index开始到末尾，查找o对象的位置
     */
    public synchronized int indexOf(Object o, int index) {
    	//o对象是否为null
        if (o == null) {
        	//for循环查找
            for (int i = index ; i < elementCount ; i++)
                if (elementData[i]==null)
                    return i;
        } else {
        	//for循环查找
            for (int i = index ; i < elementCount ; i++)
                if (o.equals(elementData[i]))
                    return i;
        }
        //查找失败
        return -1;
    }

    /**
     * 从末尾开始查找o对象
     */
    public synchronized int lastIndexOf(Object o) {
        return lastIndexOf(o, elementCount-1);
    }

    /**
     * 从index位置到0，开始查找o对象 
     */
    public synchronized int lastIndexOf(Object o, int index) {
        if (index >= elementCount)
            throw new IndexOutOfBoundsException(index + " >= "+ elementCount);
        //o对象是否为null
        if (o == null) {
        	//for循环查找
            for (int i = index; i >= 0; i--)
                if (elementData[i]==null)
                    return i;
        } else {
        	//for循环查找
            for (int i = index; i >= 0; i--)
                if (o.equals(elementData[i]))
                    return i;
        }
        //查找失败
        return -1;
    }

    /**
     * 查找index位置上的元素
     */
    public synchronized E elementAt(int index) {
        if (index >= elementCount) {
            throw new ArrayIndexOutOfBoundsException(index + " >= " + elementCount);
        }
        //返回该位置元素
        return elementData(index);
    }

    /**
     * 获得第一个元素
     */
    public synchronized E firstElement() {
        if (elementCount == 0) {
            throw new NoSuchElementException();
        }
        return elementData(0);
    }

    /**
     * 获得最后一个元素
     */
    public synchronized E lastElement() {
        if (elementCount == 0) {
            throw new NoSuchElementException();
        }
        return elementData(elementCount - 1);
    }

    /**
     * 设置第index位置上的元素为obj
     */
    public synchronized void setElementAt(E obj, int index) {
        if (index >= elementCount) {
            throw new ArrayIndexOutOfBoundsException(index + " >= " +
                                                     elementCount);
        }
        elementData[index] = obj;
    }

    /**
     * 删除index位置上的元素
     */
    public synchronized void removeElementAt(int index) {
    	//修改次数+1
        modCount++;
        //位置不合法
        if (index >= elementCount) {
            throw new ArrayIndexOutOfBoundsException(index + " >= " +
                                                     elementCount);
        }
        //位置不合法
        else if (index < 0) {
            throw new ArrayIndexOutOfBoundsException(index);
        }
        //j是需要移动元素的个数
        int j = elementCount - index - 1;
        if (j > 0) {
        	//index+1开始到末尾，全部元素向前移动1位
            System.arraycopy(elementData, index + 1, elementData, index, j);
        }
        //数目减少一个
        elementCount--;
        elementData[elementCount] = null; /* to let gc do its work */
    }

    /**
     * 在index位置上插入对象obj
     */
    public synchronized void insertElementAt(E obj, int index) {
    	//修改次数+1
        modCount++;
        if (index > elementCount) {
            throw new ArrayIndexOutOfBoundsException(index
                                                     + " > " + elementCount);
        }
        //查看是否需要扩容
        ensureCapacityHelper(elementCount + 1);
        //将index开始到末尾，全部数据往后移动1位
        System.arraycopy(elementData, index, elementData, index + 1, elementCount - index);
        //数目+1
        elementData[index] = obj;
        elementCount++;
    }

    /**
     * 在数组的末尾添加上元素
     */
    public synchronized void addElement(E obj) {
        modCount++;
        ensureCapacityHelper(elementCount + 1);
        elementData[elementCount++] = obj;
    }

    /**
     * 删除数组中的第一个obk对象
     */
    public synchronized boolean removeElement(Object obj) {
        //修改次数+1
        modCount++;
        //查找位置
        int i = indexOf(obj);
        if (i >= 0) {
        	//删除元素
            removeElementAt(i);
            return true;
        }
        return false;
    }

    /**
     * 删除所有元素
     */
    public synchronized void removeAllElements() {
        modCount++;
        // Let gc do its work
        for (int i = 0; i < elementCount; i++)
            elementData[i] = null;

        elementCount = 0;
    }

    /**
     * 重写Object对象的克隆方法
     */
    public synchronized Object clone() {
        try {
            @SuppressWarnings("unchecked")
                Vector<E> v = (Vector<E>) super.clone();
            v.elementData = Arrays.copyOf(elementData, elementCount);
            v.modCount = 0;
            return v;
        } catch (CloneNotSupportedException e) {
            // this shouldn't happen, since we are Cloneable
            throw new InternalError(e);
        }
    }

    /**
     * 将Collection对象转换为数组
     */
    public synchronized Object[] toArray() {
        return Arrays.copyOf(elementData, elementCount);
    }

    /**
     * 将Collection对象转换为指定类型的数组
     */
    @SuppressWarnings("unchecked")
    public synchronized <T> T[] toArray(T[] a) {
    	//如果传入的数组小于Vector的实际大小
        if (a.length < elementCount)
        	//创建一个新数组并返回
            return (T[]) Arrays.copyOf(elementData, elementCount, a.getClass());
        //直接将Vector中的元素复制给数组a
        System.arraycopy(elementData, 0, a, 0, elementCount);

        if (a.length > elementCount)
            a[elementCount] = null;

        return a;
    }

   
    //返回指定位置的元素
    @SuppressWarnings("unchecked")
    E elementData(int index) {
        return (E) elementData[index];
    }

    /**
     * 获得指定位置的元素
     */
    public synchronized E get(int index) {
        if (index >= elementCount)
            throw new ArrayIndexOutOfBoundsException(index);

        return elementData(index);
    }

    /**
     * 设置index位置的元素为element
     */
    public synchronized E set(int index, E element) {
        if (index >= elementCount)
            throw new ArrayIndexOutOfBoundsException(index);

        E oldValue = elementData(index);
        elementData[index] = element;
        return oldValue;
    }

    /**
     * 添加元素
     */
    public synchronized boolean add(E e) {
    	//修改次数+1
        modCount++;
        //查看是否需要扩容
        ensureCapacityHelper(elementCount + 1);
        //插入元素
        elementData[elementCount++] = e;
        return true;
    }

    /**
     * 删除o对象
     */
    public boolean remove(Object o) {
        return removeElement(o);
    }

    /**
     * 在指定位置插入元素
     */
    public void add(int index, E element) {
        insertElementAt(element, index);
    }

    /**
     * 删除index位置上的元素
     */
    public synchronized E remove(int index) {
    	//修改次数+1
        modCount++;
        if (index >= elementCount)
            throw new ArrayIndexOutOfBoundsException(index);
        //获得删除之前的旧值
        E oldValue = elementData(index);

        //计算需要移动元素的个数
        int numMoved = elementCount - index - 1;
        if (numMoved > 0)
        	//从index+1到末尾，全部元素向前移动1位
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        //利于垃圾收集
        elementData[--elementCount] = null; // Let gc do its work

        return oldValue;
    }

    /**
     * 清空数组
     */
    public void clear() {
        removeAllElements();
    }


    /**
     * 查看是否数组是否包含c中的所有元素
     */
    public synchronized boolean containsAll(Collection<?> c) {
        return super.containsAll(c);
    }

    /**
     * 添加容器c中的所有元素
     */
    public synchronized boolean addAll(Collection<? extends E> c) {
        //修改次数+1
        modCount++;
        //将c转换为数组
        Object[] a = c.toArray();
        //长度
        int numNew = a.length;
        //查看是否需要扩容
        ensureCapacityHelper(elementCount + numNew);
        //进行复制
        System.arraycopy(a, 0, elementData, elementCount, numNew);
        elementCount += numNew;
        return numNew != 0;
    }

    /**
     * 删除所有集合c中的元素
     */
    public synchronized boolean removeAll(Collection<?> c) {
        return super.removeAll(c);
    }

    /**
     * 保留c中的元素
     */
    public synchronized boolean retainAll(Collection<?> c) {
        return super.retainAll(c);
    }

    /**
     * 从index位置到末尾，添加c中的所有元素
     */
    public synchronized boolean addAll(int index, Collection<? extends E> c) {
        //修改次数+1
        modCount++;
        if (index < 0 || index > elementCount)
            throw new ArrayIndexOutOfBoundsException(index);
        //将c转换为数组
        Object[] a = c.toArray();
        //长度
        int numNew = a.length;
        //查看是否需要扩容
        ensureCapacityHelper(elementCount + numNew);
        //计算需要移动元素的个数
        int numMoved = elementCount - index;
        if (numMoved > 0)
        	//进行移动
            System.arraycopy(elementData, index, elementData, index + numNew,
                             numMoved);
        //将c拷贝进数组中
        System.arraycopy(a, 0, elementData, index, numNew);
        elementCount += numNew;
        return numNew != 0;
    }

    /**
     * 是否相等
     */
    public synchronized boolean equals(Object o) {
        return super.equals(o);
    }

    /**
     * 返回该对象的哈希值
     */
    public synchronized int hashCode() {
        return super.hashCode();
    }

    /**
     * 转为字符串
     */
    public synchronized String toString() {
        return super.toString();
    }

    /**
     * 从fromIndex到toIndex截断元素
     */
    public synchronized List<E> subList(int fromIndex, int toIndex) {
        return Collections.synchronizedList(super.subList(fromIndex, toIndex),
                                            this);
    }

    /**
     * 删除指定范围中的元素
     */
    protected synchronized void removeRange(int fromIndex, int toIndex) {
    	//修改次数+1
        modCount++;
        //计算需要移动的元素个数
        int numMoved = elementCount - toIndex;
        //移动元素
        System.arraycopy(elementData, toIndex, elementData, fromIndex,
                         numMoved);

        //方便垃圾回收
        int newElementCount = elementCount - (toIndex-fromIndex);
        while (elementCount != newElementCount)
            elementData[--elementCount] = null;
    }

    /**
     * 序列化操作
     */
    private void writeObject(java.io.ObjectOutputStream s)
            throws java.io.IOException {
        final java.io.ObjectOutputStream.PutField fields = s.putFields();
        final Object[] data;
        synchronized (this) {
            fields.put("capacityIncrement", capacityIncrement);
            fields.put("elementCount", elementCount);
            data = elementData.clone();
        }
        fields.put("elementData", data);
        s.writeFields();
    }

}

```
## （三）、ArrayList与Vector

 

 1. Vector其实是遗留类，因为方法都加上了synchroized语句，在多线程环境下效率不高，现在大多不再使用了。
 2. ArrayList与Vector拥有相同的扩容机制
 3. 不同点在于ArrayList没有设置增长率，默认扩容为1.5倍。Vector设置了增长率

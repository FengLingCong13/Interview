## ��һ����ArrayList����
## ������������

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```
������ArrayList�Ǽ̳���AbstractList��ͬʱʵ����List�ӿڡ�RandomAccess�ӿڣ�Clonable�ӿڡ�Seriealzable�ӿڡ�

 -  ==AbstractList==��ͬ��Ҳ�̳���List�ӿڣ�����ʵ����List�ӿ��еķ�����
   1. ���ܴ�һ���һ�����ʾ���ΪʲôArrayList��ֱ��ȥʵ��List�ӿڣ�����Ҫͨ��AbstractListʵ��List�ӿ�֮����ȥ�̳С�������Ҫ���漰�����ģʽ�е�֪ʶ����AbstractList�Ѿ�ʵ����List�ӿ�֮�󣬾��Ѿ�ʵ���˴󲿷���Ҫ�Ĺ����ˡ�ArrayListֻ��Ҫ���һЩ��Ҫ����չ���ܼ��ɡ������˴�����ظ���
   2.  ����һ��Ƚ���ֵľ��ǡ�ΪʲôAbstractList�Ѿ�ʵ����List�ӿڣ�ArrayList��Ҫ�ٴ�ʵ��һ��List�ӿڡ���ArrayList������˵������Ҫ�ǵ�ʱ�Լ��Ĵ�����ɵģ�ԭ����Ϊ��Ϊ���Ժ���չ�������̵棬ʵ�ʲ�û��ʲô���á�
```java
//AbstractList��Դ��
public abstract class AbstractList<E> extends AbstractCollection<E> implements List<E>
```
 -  ==RandomAccess==��RandomAccssʵ����һ���սӿڣ�ֻ���𵽱�ʶ���á�
```java
//RandomAcces�ӿڵ�Դ��
public interface RandomAccess {
}
```
1.  ��Ҫ�𵽱�ʶ�����á�ֻҪʵ��������ӿھ�֧��������ʡ����ǿ��Կ�һ��Collections���е�binarySearch�������������ж���List�Ƿ�ʵ����RandomAccess���������ʵ���ˣ���ô��ʹ��indexedBinarySearch����ͨforѭ�����б���������֮û��ʵ��RandomAccess�ӿڵĻ�����ʹ��iteratorBinarySearch�����������б�����
2. �������Ȥ�����ѿ��Ը���һ���鿴���ֱ�����Դ�룬�����в��ԡ��ᷢ�֣�ArrayListʹ����ͨforѭ�����б���Ч�ʸ��ߣ�LinkedListʹ�õ��������б���Ч�ʸ��ߡ���������˽�����ѿ��Կ�һ����ƪ���£�[ArrayList����ʵ��RandomAccess�ӿ��к����ã�Ϊ��LinkedList����ȴûʵ����ӿڣ�](https://blog.csdn.net/weixin_39148512/article/details/79234817)

```java
#Collections���е�binarySearch����
public static <T>
    int binarySearch(List<? extends Comparable<? super T>> list, T key) {
    	#�ж��Ƿ�ʵ����RandomAccess
        if (list instanceof RandomAccess || list.size()<BINARYSEARCH_THRESHOLD)
            return Collections.indexedBinarySearch(list, key);
        else
            return Collections.iteratorBinarySearch(list, key);
    }
```
-  ==Cloneable==��ʵ����Cloneable�ӿڣ��Ϳ���ʵ�ֿ�¡����
-  ==Serializable==��ʵ����Serializable�ӿڣ��Ϳ���ʵ�����л��ͷ����л�����
## ������������

```java
//���л��汾��
 private static final long serialVersionUID = 8683452581122892189L;

    /**
     * Ĭ�ϵĳ�ʼ������
     */
    private static final int DEFAULT_CAPACITY = 10;

    /**
     * ������ArrayList����Ϊ0ʱ�Ŀ�����
     */
    private static final Object[] EMPTY_ELEMENTDATA = {};

    /**
    * ��ʼ��ʱδָ��ArrayList�ĳ���ʱ�Ŀ�����
    */
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

    /**
    * transient�����ܱ����л�������������ڱ�������
    */
    transient Object[] elementData; // non-private to simplify nested class access

    /**
     * ArrayList��ʵ�ʵ����鳤��
     *
     * @serial
     */
    private int size;
```

 1. DEFAULT_CAPACITY��Ĭ�ϵĳ�ʼ������Ϊ10��
 2. EMPTY_ELEMENTDATA��������ArrayList����Ϊ0ʱ�Ŀ����顣
 3. DEFAULTCAPACITY_EMPTY_ELEMENTDATA����ʼ��ʱδָ��ArrayList�ĳ���ʱ�Ŀ����顣
 4. elementData��transient�����ܱ����л�������������ڱ������ݡ�
 5. size�������ʵ�ʳ��ȡ�
 ## ���ģ������췽��
 

```java
   /**
     * һ�������Ĺ��췽����ָ����ʼ������
     */
    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
        	//���ָ������������0���򴴽�һ��ָ�����ȵ�����
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
        	//���ָ��������Ϊ0���ʹ���һ���յ�����
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
        	//���ָ��������С��0�����׳��쳣
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }

    /**
     * �޲ι��췽��������һ�������飬�Զ���ʼ������Ϊ10
     */
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }

    /**
     * ��Collection��Ϊ�����Ĺ��췽������Ҫ��ʵ�ָ��ƹ���
     */
    public ArrayList(Collection<? extends E> c) {
    	//���Ƚ�Collectionת��Ϊ���鲢��ֵ
        elementData = c.toArray();
        //�ж�����ĳ����Ƿ����0
        if ((size = elementData.length) != 0) {
            // �ж��Ƿ�ΪObject���ͣ�������ǵĻ������ǽ���ת��
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // ���ȵ���0����ֵΪ������
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }
```

 1. ArrayList()���޲ι��췽��������һ�������飬�Զ���ʼ������Ϊ10
 2. ArrayList(int initialCapacity)��һ�������Ĺ��췽����ָ����ʼ������
 3. ArrayList(Collection<? extends E> c)����Collection��Ϊ�����Ĺ��췽������Ҫ��ʵ�ָ��ƹ���
## ���壩��Add���������ݲ�����

```java
	/**
     * ��ArrayList��ĩβ�����Ԫ��
     */
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }

    /**
     * ��ָ����λ�������Ԫ��
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
    * ����һ��Collection������ӵ���ǰArrayList��
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
     * ��ָ��λ�ã�����һ��Collection������ӵ���ǰArrayList��
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

 -  ==Add����������Ҫ�漰�������ݲ�������Ҫ�漰�ܶ����Ա���������ǻ������������϶�һ��������һ����������������Ҳ�ܺ��������ס��������add(int index, E element)�������о�����==
  1. ����������add(int index, E element)��Դ�룬���������Ҫ�Ĺ�������ָ��λ�ò���Ԫ�ء�
  

```java
	public void add(int index, E element) {
		//���Ҫ�����λ���Ƿ�Ϸ�
        rangeCheckForAdd(index);
		//ȷ�������������������������������ݲ���
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        //��index+1λ�õ�ĩβ��Ԫ��ȫ�������ƶ�һλ
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
        //��indexλ���ϲ���Ԫ��
        elementData[index] = element;
        //ArrayList��ʵ����������1
        size++;
    }
```
2. ���ǽ��� rangeCheckForAdd(index)�����У�������ʵ�ֺܼ򵥣��ж�index�Ƿ�Ϸ���������Ϸ����׳��쳣��

```java
	/**
     * �鿴����λ���Ƿ�Ϸ�
     */
    private void rangeCheckForAdd(int index) {
        if (index > size || index < 0)
        	//���λ�ò��Ϸ����׳��쳣
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
```
3. �������Ǿͻص�add()�����У�Ȼ����뵽ensureCapacityInternal(size + 1)�����У���Ϊ�ǲ���һ��Ԫ�أ����Ծ���������Ҫ������=ʵ������size+1�����뷽��������Ҫ�ж��Ƿ��ǿ����飬����ǿ�����Ļ���Ҫ���ó�ʼ������Ϊ10��Ȼ�����ensureExplicitCapacity(minCapacity);

```java
	/**
     * �ж��Ƿ��ǿ����飬����ǿ�����Ļ���Ҫ���ó�ʼ������Ϊ10
     */
	private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        	//�ж��Ƿ��ǿ����飬����ǿ�����Ļ���Ҫ���ó�ʼ������Ϊ10
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }
		//���÷�����
        ensureExplicitCapacity(minCapacity);
    }
```
4. Ȼ�����ǽ��뵽ensureExplicitCapacity(int minCapacity)�����С�modCountҪ�ȼ�1�������������AbstractList�еģ���Ҫ������¼�޸Ĳ���������ʶ�����ʧ��(fast-fail����)������Ȥ�����˽�һ�¡�Ȼ���ж���С�����Ƿ���ڵ�ǰ����ĳ��ȣ�������ھ͵���grow()�����������ݡ�

```java
	private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // �ж���С�����Ƿ���ڵ�ǰ����ĳ��ȣ�������ھ͵���grow()������������
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
```
5. ��grow()�����У����Ƚ�ԭ�����������ֵ��oldCapacity��Ȼ��oldCapacity��������Ϊ1.5����ֵ��newCapacity������µ�����newCapacity����С����С��������ô�Ͱ���С������ֵ��newCapacity�����newCapacity��������������������Ҫ����hugeCapacity()�����������ݡ�����������Arrays.copyOf(elementData, newCapacity)����һ�������鸳ֵ��elementData��������ݡ�

```java
	private void grow(int minCapacity) {
        // ��ԭ�����������ֵ��oldCapacity
        int oldCapacity = elementData.length;
        //������λ���㣬��oldCapacity��������Ϊ1.5����ֵ��newCapacity
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        //����µ�����newCapacity����С����С��������ô�Ͱ���С������ֵ��newCapacity
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        //���newCapacity��������������������Ҫ����hugeCapacity()������������
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // ����Arrays.copyOf(elementData, newCapacity)����һ�������鸳ֵ��elementData���������
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```
6. ��һ���е�����hugeCapacity(minCapacity)�������������ʹ������Ԫ���ʽ�������С��������������鳤�ȣ��ͷ������������ֵ�����򷵻�������󳤶ȡ�

```java
	private static int hugeCapacity(int minCapacity) {
		//��Χ���Ϸ�
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        //ʹ������Ԫ���ʽ�������С��������������鳤�ȣ��ͷ������������ֵ�����򷵻�������󳤶�    
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
```
## ��������Remove������ɾ��Ԫ�أ�

```java
	/**
     * ɾ��ָ��λ���ϵ�Ԫ��
     */
	public E remove(int index) {
		//�����жϷ�Χ�Ƿ�Ϸ�
        rangeCheck(index);
		//�޸Ĵ���+1��ǰ���Ѿ��ᵽ����
        modCount++;
        //��ȡindexλ���ϵľ�ֵ
        E oldValue = elementData(index);
		//����ɾ����λ�õ�Ԫ�أ���Ҫ�ƶ�Ԫ�صĸ���
        int numMoved = size - index - 1;
        if (numMoved > 0)
        	//��index+1�����Ԫ��ȫ����ǰ�ƶ�1λ
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        //��size-1λ�õ�ֵ����Ϊnull�����������ռ�
        elementData[--size] = null; // clear to let GC do its work
		//���ر�ɾ����Ԫ��
        return oldValue;
    }
    /**
     * ɾ��ָ�������Ԫ��
     */
    public boolean remove(Object o) {
    	//�������Ϊnull����
        if (o == null) {
        	//��������
            for (int index = 0; index < size; index++)
            	//���������ֵΪnull������fastRemove()����ɾ��Ԫ��
                if (elementData[index] == null) {
                    fastRemove(index);
                    return true;
                }
        } else {
        	//��������
            for (int index = 0; index < size; index++)
            	//���������ֵΪo������fastRemove()����ɾ��Ԫ��
                if (o.equals(elementData[index])) {
                    fastRemove(index);
                    return true;
                }
        }
        //û�и�Ԫ�أ�����false
        return false;
    }
```

 - remove(int index)������ɾ��ָ��λ���ϵ�Ԫ�أ��������£�
1. �����жϷ�Χ�Ƿ�Ϸ�
2. �޸Ĵ���+1��ǰ���Ѿ��ᵽ����
3. ��ȡindexλ���ϵľ�ֵ
4. ����ɾ����λ�õ�Ԫ�أ���Ҫ�ƶ�Ԫ�صĸ���
5. ��index+1�����Ԫ��ȫ����ǰ�ƶ�1λ
6. ��size-1λ�õ�ֵ����Ϊnull�����������ռ�
7. ���ر�ɾ����Ԫ��
 - remove(Object o)������ɾ��ָ��λ���ϵ�Ԫ�أ��������£�
1. �ж϶����Ƿ�Ϊnull����
2. ���Ϊnull���������飬����ɾ��
3. �����Ϊnull��Ҳ�������飬����ɾ��
 

```java
	private void fastRemove(int index) {
		//�޸Ĵ���+1
        modCount++;
        //�����ƶ�Ԫ�صĸ���
        int numMoved = size - index - 1;
        if (numMoved > 0)
        	//��ǰ�ƶ�һλ
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        //���һλ����λnull�����������ռ�
        elementData[--size] = null; // clear to let GC do its work
    }
```
ɾ�������е�����fastRemove(int index)���������������ʵ���ǽ�indexλ�ú��Ԫ��ȫ����ǰ�ƶ�1λ��ʵ��Ԫ��ɾ����
## ���ߣ������л�
ArrayList�������л���صķ�����Ҫ�������� �ֱ���readObject()�Լ�writeObject()

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
1. writeObject()��Ҫ��ɵ������л�����
2. ����Ҫ�ж�������ModCount��ʵ�ʵ�ModCount�Ƿ���ȣ���������˵��������fast-fail������ʧ�ܣ�
3. ����forѭ����һ��Ԫ��д�뵽�������

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
1. readObject()��Ҫ��ɵ��Ƿ����л�����
2. ����Ҫ�ж�ʵ�������Ƿ����0���������0����������������ʵ�ʴ�С������ռ䣬�����Ǹ�������������ռ䡣
3. ����forѭ��������д������
## ���ˣ���trimToSize()����

```java
	/**
     * ��ArrayList����������Ϊʵ�ʴ�С
     */
    public void trimToSize() {
    	//�޸Ĵ���+1
        modCount++;
        //���ʵ�ʴ�СС������
        if (size < elementData.length) {
        	//��Ԫ���ʽ��������
            elementData = (size == 0)
              ? EMPTY_ELEMENTDATA
              : Arrays.copyOf(elementData, size);
        }
    }
```
��ΪArrayList��Ԫ����Ŀ���ܲ����������������������ʱΪ�˽�ʡ�ռ䣬���ArrayList�������ݲ�������ʵ�ʴ�С=����������
## ���ţ���indexOf()����

```java
	/**
	* ��ȡĳ��Ԫ�ص�λ��
	*/
	public int indexOf(Object o) {
		//���Ϊnull
        if (o == null) {
        	//forѭ����������������ھͷ��ظ�λ��
            for (int i = 0; i < size; i++)
                if (elementData[i]==null)
                    return i;
        } else {
        	//��Ϊnull��forѭ����������������ھͷ��ظ�λ��
            for (int i = 0; i < size; i++)
                if (o.equals(elementData[i]))
                    return i;
        }
        //�����ڷ���-1
        return -1;
    }

    /**
     * ������ָ��Ԫ�ص�λ��
     */
    public int lastIndexOf(Object o) {
    	//���Ϊnull
        if (o == null) {
        //forѭ����������������ھͷ��ظ�λ��
            for (int i = size-1; i >= 0; i--)
                if (elementData[i]==null)
                    return i;
        } else {
        	//��Ϊnull��forѭ����������������ھͷ��ظ�λ��
            for (int i = size-1; i >= 0; i--)
                if (o.equals(elementData[i]))
                    return i;
        }
        //�����ڷ���-1
        return -1;
    }
```

 - indexOf()��lastIndexOf()�ֱ𷵻�ָ��Ԫ�����б�������������λ�ã�ʵ�ֽ�Ϊ�򵥣�����������ϸע�ͣ���������⡣
## ��ʮ����toArray()����

```java
	/**
     * ֱ�ӵ���Arrays.copyOf(elementData, size)����һ��������
     */
    public Object[] toArray() {
        return Arrays.copyOf(elementData, size);
    }

    /**
     * �����һ������ת���ķ�����������ת��Ϊָ������
     */
    @SuppressWarnings("unchecked")
    public <T> T[] toArray(T[] a) {
        if (a.length < size)
            // ����һ��T���͵�������
            return (T[]) Arrays.copyOf(elementData, size, a.getClass());
        //����Ԫ��
        System.arraycopy(elementData, 0, a, 0, size);
        //��ʶ����
        if (a.length > size)
            a[size] = null;
        return a;
    }
```

 - toArray()������Ҫ�����б��ת����ʵ��Ҳ��Ϊ���ף�����������ϸע��
 - toArray(T[] a)���д���һЩ�ӣ���ϸ�ɼ���ƪ���£�[����ת�����toArray()��toArray(T[] a)����](https://www.cnblogs.com/goloving/p/7693388.html)
## ��ʮһ����batchRemove()����������ɾ����

```java
	/**
	* ��������ɾ��
	*/
	private boolean batchRemove(Collection<?> c, boolean complement) {
		//��ȡԭ�����е�Ԫ��
        final Object[] elementData = this.elementData;
        //��������ָ�룬��ʼֵΪ0
        int r = 0, w = 0;
        //���ñ�־ֵmodified����ʼֵΪfalse
        boolean modified = false;
        try {
        	//ɾ������
            for (; r < size; r++)
                if (c.contains(elementData[r]) == complement)
                    elementData[w++] = elementData[r];
        } finally {
            //���ɾ����ɺ󣬻���ʣ�ģ��Ͳ��뵽ԭ������
            if (r != size) {
                System.arraycopy(elementData, r,
                                 elementData, w,
                                 size - r);
                w += size - r;
            }
            //���ɾ����������ĳ���С��ԭ����ĳ��ȣ��������ֵ��λnull�����������ռ�
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

 1. batchRemove(Collection<?> c, boolean complement)��������ɾ����complement����ȡtrue��ʱ��Ͳ�ɾ��c�е�Ԫ�أ����Ϊfalse��ɾ��c�е�Ԫ��
 2. ���ɾ����ɺ󣬻���ʣ�ģ��Ͳ��뵽ԭ������
 3. ���ɾ����������ĳ���С��ԭ����ĳ��ȣ��������ֵ��λnull�����������ռ�

## ��ʮ����������һЩ�򵥷���

```java
	/**
     * ����ʵ�ʴ�С
     */
    public int size() {
        return size;
    }

    /**
     * �ж��Ƿ�Ϊ��
     */
    public boolean isEmpty() {
        return size == 0;
    }

    /**
     * �ж��Ƿ������Ԫ��
     */
    public boolean contains(Object o) {
        return indexOf(o) >= 0;
    }
     /**
     * ��дObject�Ŀ�¡����
     */
    public Object clone() {
        try {
        	//���ø���Ŀ�¡����
            ArrayList<?> v = (ArrayList<?>) super.clone();
            //����Ԫ��
            v.elementData = Arrays.copyOf(elementData, size);
            //mouCount����
            v.modCount = 0;
            return v;
        } catch (CloneNotSupportedException e) {
            // this shouldn't happen, since we are Cloneable
            throw new InternalError(e);
        }
    }
    /**
     * ��ȡָ��λ�õ�Ԫ��
     */
    public E get(int index) {
        rangeCheck(index);

        return elementData(index);
    }

    /**
     * ����ָ��λ�õ�Ԫ��ֵ
     */
    public E set(int index, E element) {
        rangeCheck(index);

        E oldValue = elementData(index);
        elementData[index] = element;
        return oldValue;
    }
    /**
     * ���ArrayList()
     */
    public void clear() {
        modCount++;

        // clear to let GC do its work
        for (int i = 0; i < size; i++)
            elementData[i] = null;

        size = 0;
    }
```

 1. size()������ʵ�ʴ�С
 2. isEmpty()���ж��Ƿ�Ϊ��
 3. contains(Object o)���ж��Ƿ������Ԫ��
 4. clone()����дObject�Ŀ�¡����
 5. get(int index)����ȡָ��λ�õ�Ԫ��
 6. set(int index, E element)������ָ��λ�õ�Ԫ��ֵ
 7. clear()�����ArrayList()
 ## ��ʮ������System.arraycopy() �� Arrays.copyOf()֮��ĶԱ�
 

```java
public static native void arraycopy(Object src,  int  srcPos,
                                        Object dest, int destPos,
                                        int length);
```

 - ��������System.arraycopy()���������������Ϊnative�������ǿ�����Դ��ģ�˵������ʹ��java����ʵ�ֵġ���Ҫ���ܾ���System.arraycopy() ��������������ݣ����ᴴ�������顣
 

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
 - Ȼ����Arrays.copyOf()���������ǿ��Կ����������Ǹ���newLength����һ���µ�Object���飬Ȼ��ͬ������System.arraycopy()�����Ƶ��������У������������顣
 ## ��ʮ�ģ���ModCount�ֶε�����
 
 - ModCount�ֶ�����Ҫ��Ϊ�˷�ֹ�ڶ��̻߳����У�ʹ�õ����������У�����̶߳Լ��Ͻ����޸Ĳ�������ɴ���
 - �����������ɵ�����ӣ����Բμ���ƪ���£�[Java�е���ǿforѭ���ĵײ�ʵ��ԭ�����](https://blog.csdn.net/weixin_41799019/article/details/98112586)

 
 







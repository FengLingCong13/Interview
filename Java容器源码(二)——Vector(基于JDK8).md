
## ��һ����Vector����

 - Vecotr�ǻ�������ʵ�ֵģ���һ����̬���飬�������������Զ�����
 - Vector��ArrayList��ʮ�����Ƶģ�����Vector��ÿһ�������ж�������Synchronized�������Σ���֤�̵߳İ�ȫ����ʵ��ʱ��Ҳ�ǲ���ȫ�ģ���Ҫ�ֶ���֤�̰߳�ȫ��
 - ��Ϊ��ArrayList�н�Ϊ��ϸ�ķ�����Դ�룬��Vector�ּ���һ�������Ի�û����ArrayListԴ������ѽ�������ȿ�����ƪ���£�[Java����Դ��(һ)����ArrayList(����JDK8)](https://blog.csdn.net/weixin_41799019/article/details/99468782)��������ƪ����֮���ٻ�����Vector��Դ��ͻ��ر����ɡ�
## ��������VectorԴ�����

```java
package java.util;

import java.util.function.Consumer;
import java.util.function.Predicate;
import java.util.function.UnaryOperator;

/**
*	Vectorͬ��Ҳ�Ǽ̳���AbstractList
*	ʵ����List�ӿڣ�ֻ���𵽱�ʶ���ã�AbstractList���Ѿ�ʵ����List�ӿ�
*	RandomAccess�ӿڱ�ʶ֧���������
*	Cloneable�ӿڱ�ʶ���Խ��п�¡
*	Serializable��������л�
*/
public class Vector<E>
    extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    /**
    *  	Object���͵����飬���ڴ������
     */
    protected Object[] elementData;

   
     /*
     *	�����ʵ�ʳ���
     */
    protected int elementCount;

    /**
     * �������ݵ�ʱ���������
     */
    protected int capacityIncrement;

    /** ���л��İ汾�� */
    private static final long serialVersionUID = -2767605614048989439L;

    /**
     * �������������Ĺ��췽��Vector(int initialCapacity, int capacityIncrement)
     */
    public Vector(int initialCapacity, int capacityIncrement) {
        super();
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        //����initialCapacity��ʼ������
        this.elementData = new Object[initialCapacity];
        //�趨������
        this.capacityIncrement = capacityIncrement;
    }

    /**
     * ����һ�������Ĺ��췽��Vector(int initialCapacity)
     */
    public Vector(int initialCapacity) {
    	//���������Ǹ��������������Ĺ��췽��������������Ϊ0
        this(initialCapacity, 0);
    }

    /**
     * �޲ι��췽��Vector()
     */
    public Vector() {
    	//Ĭ�����õĳ�ʼ����Ϊ10������һ�������Ĺ��췽��
        this(10);
    }

    /**
     * ������ת��ΪVector�Ĺ��췽��Vector(Collection<? extends E> c)
     */
    public Vector(Collection<? extends E> c) {
    	//������ת��Ϊ���飬����ֵ��elementData
        elementData = c.toArray();
        //���������ʵ�ʳ���
        elementCount = elementData.length;
        // ���c���Ǵ��Object���͵����ݣ���ת��ΪObject����
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, elementCount, Object[].class);
    }

    /**
     * ��Vector�����ݶ����������������anArray��
     */
    public synchronized void copyInto(Object[] anArray) {
        System.arraycopy(elementData, 0, anArray, 0, elementCount);
    }

    /**
     * ���������������Ϊʵ�ʴ�С
     */
    public synchronized void trimToSize() {
    	//�޸Ĵ���+1
        modCount++;
        int oldCapacity = elementData.length;
        //���ʵ�ʴ�СС���������ͽ�������
        if (elementCount < oldCapacity) {
            elementData = Arrays.copyOf(elementData, elementCount);
        }
    }

    /**
     * ����ȷ������������㹻
     */
    public synchronized void ensureCapacity(int minCapacity) {
        //�����Ҫ����������0
        if (minCapacity > 0) {
            //�޸Ĵ���+1
            modCount++;
            //���Խ�������
            ensureCapacityHelper(minCapacity);
        }
    }

    /**
     * ����Ƿ���Ҫ��������
     */
    private void ensureCapacityHelper(int minCapacity) {
        // �����Ҫ����������ʵ������
        if (minCapacity - elementData.length > 0)
            //��������
            grow(minCapacity);
    }

    /**
     * �������ݲ���
     */
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        //��oldCapacity����ΪoldCapacity+capacityIncrement
        int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                         capacityIncrement : oldCapacity);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        //����µ������������������������ͳ��Ե��ø��������
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        //����һ���µ����飬��ʵ�ָ���
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

    /**
    *	���Ի�ø��������
    */
    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }

    /**
     * ����Vector��ʵ�ʴ�С
     */
    public synchronized void setSize(int newSize) {
        //�޸Ĵ���+1
        modCount++;
        //�����������ͽ�������
        if (newSize > elementCount) {
            ensureCapacityHelper(newSize);
        } else {
        	//����̫���ˣ��������ݣ�û���õ�λ������Ϊnull�����������ռ�
            for (int i = newSize ; i < elementCount ; i++) {
                elementData[i] = null;
            }
        }
        elementCount = newSize;
    }

    /**
     * ���Vector������
     */
    public synchronized int capacity() {
        return elementData.length;
    }

    /**
     * ���Vector��ʵ�ʴ�С
     */
    public synchronized int size() {
        return elementCount;
    }

    /**
     * �Ƿ�Ϊ��
     */
    public synchronized boolean isEmpty() {
        return elementCount == 0;
    }

    /**
     * ����Vector��ö����
     */
    public Enumeration<E> elements() {
    	//������ʵ��
        return new Enumeration<E>() {
            //��ʼ��Ϊ0
            int count = 0;

            //�Ƿ�����һ��Ԫ��
            public boolean hasMoreElements() {
                return count < elementCount;
            }

            //�����һ��Ԫ��
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
     * �鿴�Ƿ���o����
     */
    public boolean contains(Object o) {
        return indexOf(o, 0) >= 0;
    }

    /**
     * ����o�����λ��
     */
    public int indexOf(Object o) {
        return indexOf(o, 0);
    }

    /**
     * ��index��ʼ��ĩβ������o�����λ��
     */
    public synchronized int indexOf(Object o, int index) {
    	//o�����Ƿ�Ϊnull
        if (o == null) {
        	//forѭ������
            for (int i = index ; i < elementCount ; i++)
                if (elementData[i]==null)
                    return i;
        } else {
        	//forѭ������
            for (int i = index ; i < elementCount ; i++)
                if (o.equals(elementData[i]))
                    return i;
        }
        //����ʧ��
        return -1;
    }

    /**
     * ��ĩβ��ʼ����o����
     */
    public synchronized int lastIndexOf(Object o) {
        return lastIndexOf(o, elementCount-1);
    }

    /**
     * ��indexλ�õ�0����ʼ����o���� 
     */
    public synchronized int lastIndexOf(Object o, int index) {
        if (index >= elementCount)
            throw new IndexOutOfBoundsException(index + " >= "+ elementCount);
        //o�����Ƿ�Ϊnull
        if (o == null) {
        	//forѭ������
            for (int i = index; i >= 0; i--)
                if (elementData[i]==null)
                    return i;
        } else {
        	//forѭ������
            for (int i = index; i >= 0; i--)
                if (o.equals(elementData[i]))
                    return i;
        }
        //����ʧ��
        return -1;
    }

    /**
     * ����indexλ���ϵ�Ԫ��
     */
    public synchronized E elementAt(int index) {
        if (index >= elementCount) {
            throw new ArrayIndexOutOfBoundsException(index + " >= " + elementCount);
        }
        //���ظ�λ��Ԫ��
        return elementData(index);
    }

    /**
     * ��õ�һ��Ԫ��
     */
    public synchronized E firstElement() {
        if (elementCount == 0) {
            throw new NoSuchElementException();
        }
        return elementData(0);
    }

    /**
     * ������һ��Ԫ��
     */
    public synchronized E lastElement() {
        if (elementCount == 0) {
            throw new NoSuchElementException();
        }
        return elementData(elementCount - 1);
    }

    /**
     * ���õ�indexλ���ϵ�Ԫ��Ϊobj
     */
    public synchronized void setElementAt(E obj, int index) {
        if (index >= elementCount) {
            throw new ArrayIndexOutOfBoundsException(index + " >= " +
                                                     elementCount);
        }
        elementData[index] = obj;
    }

    /**
     * ɾ��indexλ���ϵ�Ԫ��
     */
    public synchronized void removeElementAt(int index) {
    	//�޸Ĵ���+1
        modCount++;
        //λ�ò��Ϸ�
        if (index >= elementCount) {
            throw new ArrayIndexOutOfBoundsException(index + " >= " +
                                                     elementCount);
        }
        //λ�ò��Ϸ�
        else if (index < 0) {
            throw new ArrayIndexOutOfBoundsException(index);
        }
        //j����Ҫ�ƶ�Ԫ�صĸ���
        int j = elementCount - index - 1;
        if (j > 0) {
        	//index+1��ʼ��ĩβ��ȫ��Ԫ����ǰ�ƶ�1λ
            System.arraycopy(elementData, index + 1, elementData, index, j);
        }
        //��Ŀ����һ��
        elementCount--;
        elementData[elementCount] = null; /* to let gc do its work */
    }

    /**
     * ��indexλ���ϲ������obj
     */
    public synchronized void insertElementAt(E obj, int index) {
    	//�޸Ĵ���+1
        modCount++;
        if (index > elementCount) {
            throw new ArrayIndexOutOfBoundsException(index
                                                     + " > " + elementCount);
        }
        //�鿴�Ƿ���Ҫ����
        ensureCapacityHelper(elementCount + 1);
        //��index��ʼ��ĩβ��ȫ�����������ƶ�1λ
        System.arraycopy(elementData, index, elementData, index + 1, elementCount - index);
        //��Ŀ+1
        elementData[index] = obj;
        elementCount++;
    }

    /**
     * �������ĩβ�����Ԫ��
     */
    public synchronized void addElement(E obj) {
        modCount++;
        ensureCapacityHelper(elementCount + 1);
        elementData[elementCount++] = obj;
    }

    /**
     * ɾ�������еĵ�һ��obk����
     */
    public synchronized boolean removeElement(Object obj) {
        //�޸Ĵ���+1
        modCount++;
        //����λ��
        int i = indexOf(obj);
        if (i >= 0) {
        	//ɾ��Ԫ��
            removeElementAt(i);
            return true;
        }
        return false;
    }

    /**
     * ɾ������Ԫ��
     */
    public synchronized void removeAllElements() {
        modCount++;
        // Let gc do its work
        for (int i = 0; i < elementCount; i++)
            elementData[i] = null;

        elementCount = 0;
    }

    /**
     * ��дObject����Ŀ�¡����
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
     * ��Collection����ת��Ϊ����
     */
    public synchronized Object[] toArray() {
        return Arrays.copyOf(elementData, elementCount);
    }

    /**
     * ��Collection����ת��Ϊָ�����͵�����
     */
    @SuppressWarnings("unchecked")
    public synchronized <T> T[] toArray(T[] a) {
    	//������������С��Vector��ʵ�ʴ�С
        if (a.length < elementCount)
        	//����һ�������鲢����
            return (T[]) Arrays.copyOf(elementData, elementCount, a.getClass());
        //ֱ�ӽ�Vector�е�Ԫ�ظ��Ƹ�����a
        System.arraycopy(elementData, 0, a, 0, elementCount);

        if (a.length > elementCount)
            a[elementCount] = null;

        return a;
    }

   
    //����ָ��λ�õ�Ԫ��
    @SuppressWarnings("unchecked")
    E elementData(int index) {
        return (E) elementData[index];
    }

    /**
     * ���ָ��λ�õ�Ԫ��
     */
    public synchronized E get(int index) {
        if (index >= elementCount)
            throw new ArrayIndexOutOfBoundsException(index);

        return elementData(index);
    }

    /**
     * ����indexλ�õ�Ԫ��Ϊelement
     */
    public synchronized E set(int index, E element) {
        if (index >= elementCount)
            throw new ArrayIndexOutOfBoundsException(index);

        E oldValue = elementData(index);
        elementData[index] = element;
        return oldValue;
    }

    /**
     * ���Ԫ��
     */
    public synchronized boolean add(E e) {
    	//�޸Ĵ���+1
        modCount++;
        //�鿴�Ƿ���Ҫ����
        ensureCapacityHelper(elementCount + 1);
        //����Ԫ��
        elementData[elementCount++] = e;
        return true;
    }

    /**
     * ɾ��o����
     */
    public boolean remove(Object o) {
        return removeElement(o);
    }

    /**
     * ��ָ��λ�ò���Ԫ��
     */
    public void add(int index, E element) {
        insertElementAt(element, index);
    }

    /**
     * ɾ��indexλ���ϵ�Ԫ��
     */
    public synchronized E remove(int index) {
    	//�޸Ĵ���+1
        modCount++;
        if (index >= elementCount)
            throw new ArrayIndexOutOfBoundsException(index);
        //���ɾ��֮ǰ�ľ�ֵ
        E oldValue = elementData(index);

        //������Ҫ�ƶ�Ԫ�صĸ���
        int numMoved = elementCount - index - 1;
        if (numMoved > 0)
        	//��index+1��ĩβ��ȫ��Ԫ����ǰ�ƶ�1λ
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        //���������ռ�
        elementData[--elementCount] = null; // Let gc do its work

        return oldValue;
    }

    /**
     * �������
     */
    public void clear() {
        removeAllElements();
    }


    /**
     * �鿴�Ƿ������Ƿ����c�е�����Ԫ��
     */
    public synchronized boolean containsAll(Collection<?> c) {
        return super.containsAll(c);
    }

    /**
     * �������c�е�����Ԫ��
     */
    public synchronized boolean addAll(Collection<? extends E> c) {
        //�޸Ĵ���+1
        modCount++;
        //��cת��Ϊ����
        Object[] a = c.toArray();
        //����
        int numNew = a.length;
        //�鿴�Ƿ���Ҫ����
        ensureCapacityHelper(elementCount + numNew);
        //���и���
        System.arraycopy(a, 0, elementData, elementCount, numNew);
        elementCount += numNew;
        return numNew != 0;
    }

    /**
     * ɾ�����м���c�е�Ԫ��
     */
    public synchronized boolean removeAll(Collection<?> c) {
        return super.removeAll(c);
    }

    /**
     * ����c�е�Ԫ��
     */
    public synchronized boolean retainAll(Collection<?> c) {
        return super.retainAll(c);
    }

    /**
     * ��indexλ�õ�ĩβ�����c�е�����Ԫ��
     */
    public synchronized boolean addAll(int index, Collection<? extends E> c) {
        //�޸Ĵ���+1
        modCount++;
        if (index < 0 || index > elementCount)
            throw new ArrayIndexOutOfBoundsException(index);
        //��cת��Ϊ����
        Object[] a = c.toArray();
        //����
        int numNew = a.length;
        //�鿴�Ƿ���Ҫ����
        ensureCapacityHelper(elementCount + numNew);
        //������Ҫ�ƶ�Ԫ�صĸ���
        int numMoved = elementCount - index;
        if (numMoved > 0)
        	//�����ƶ�
            System.arraycopy(elementData, index, elementData, index + numNew,
                             numMoved);
        //��c������������
        System.arraycopy(a, 0, elementData, index, numNew);
        elementCount += numNew;
        return numNew != 0;
    }

    /**
     * �Ƿ����
     */
    public synchronized boolean equals(Object o) {
        return super.equals(o);
    }

    /**
     * ���ظö���Ĺ�ϣֵ
     */
    public synchronized int hashCode() {
        return super.hashCode();
    }

    /**
     * תΪ�ַ���
     */
    public synchronized String toString() {
        return super.toString();
    }

    /**
     * ��fromIndex��toIndex�ض�Ԫ��
     */
    public synchronized List<E> subList(int fromIndex, int toIndex) {
        return Collections.synchronizedList(super.subList(fromIndex, toIndex),
                                            this);
    }

    /**
     * ɾ��ָ����Χ�е�Ԫ��
     */
    protected synchronized void removeRange(int fromIndex, int toIndex) {
    	//�޸Ĵ���+1
        modCount++;
        //������Ҫ�ƶ���Ԫ�ظ���
        int numMoved = elementCount - toIndex;
        //�ƶ�Ԫ��
        System.arraycopy(elementData, toIndex, elementData, fromIndex,
                         numMoved);

        //������������
        int newElementCount = elementCount - (toIndex-fromIndex);
        while (elementCount != newElementCount)
            elementData[--elementCount] = null;
    }

    /**
     * ���л�����
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
## ��������ArrayList��Vector

 

 1. Vector��ʵ�������࣬��Ϊ������������synchroized��䣬�ڶ��̻߳�����Ч�ʲ��ߣ����ڴ�಻��ʹ���ˡ�
 2. ArrayList��Vectorӵ����ͬ�����ݻ���
 3. ��ͬ������ArrayListû�����������ʣ�Ĭ������Ϊ1.5����Vector������������

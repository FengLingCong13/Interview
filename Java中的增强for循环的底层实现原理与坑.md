在JAVA中，遍历集合和数组一般有以下三种形式：

```java
for (int i = 0; i < list.size(); i++) {
   System.out.print(list.get(i) + ",");
}

Iterator iterator = list.iterator();
while (iterator.hasNext()) {
   System.out.print(iterator.next() + ",");
}

for (Integer i : list) {
   System.out.print(i + ",");
}
```

第一种是普通的for循环遍历、第二种是使用迭代器进行遍历，第三种我们一般称之为增强for循环（for each）。
## （一）、实现原理
可以看到，第三种形式是JAVA提供的语法糖，这里我们剖析一下，这种增强for循环底层是如何实现的。
我们对以下代码进行反编译：

```java
for (Integer i : list) {
   System.out.println(i);
}
```
反编译后：

```java
Integer i;
for(Iterator iterator = list.iterator(); iterator.hasNext(); System.out.println(i)){
   i = (Integer)iterator.next();        
}
```
反编译后的代码其实比较复杂，我们按照执行顺序拆解一下：

```
Integer i; 定义一个临时变量i

Iterator iterator = list.iterator(); 获取List的迭代器

iterator.hasNext(); 判断迭代器中是否有未遍历过的元素

i = (Integer)iterator.next(); 获取第一个未遍历的元素，赋值给临时变量i

System.out.println(i) 输出临时变量i的值
```
如此循环往复，直到遍历完List中的所有元素。



通过反编译，我们看到，其实JAVA中的增强for循环底层是通过迭代器模式来实现的。
## （二）、增强for循环的坑
这里说是增强for循环的坑，其实主要是因为有些人不了解增强for循环的实现原理而可能踩入的坑。



既然增强for循环通过迭代器实现，那么必然有迭代器的特性。



Java中有fail-fast机制。在使用迭代器遍历元素的时候，在对集合进行删除的时候一定要注意，使用不当有可能发生ConcurrentModificationException，这是一种运行时异常，编译期并不会发生。只有在程序真正运行时才会爆发。



如以下代码：

```java
for (Student stu : students) {    
   if (stu.getId() == 2)     
       students.remove(stu);    
}
```
会抛出ConcurrentModificationException异常。
Iterator是工作在一个独立的线程中，并且拥有一个 mutex 锁。 Iterator被创建之后会建立一个指向原来对象的单链索引表，当原来的对象数量发生变化时，这个索引表的内容不会同步改变，所以当索引指针往后移动的时候就找不到要迭代的对象，所以按照 fail-fast 原则 Iterator 会马上抛出java.util.ConcurrentModificationException异常。

所以 Iterator 在工作的时候是不允许被迭代的对象被改变的。



但你可以使用 Iterator 本身的方法 remove() 来删除对象，Iterator.remove() 方法会在删除当前迭代对象的同时维护索引的一致性。



正确的在遍历的同时删除元素的姿势：

```java
Iterator<Student> stuIter = students.iterator();    
while (stuIter.hasNext()) {    
   Student student = stuIter.next();    
   if (student.getId() == 2)    
       stuIter.remove();//这里要使用Iterator的remove方法移除当前对象，如果使用List的remove方法，则同样会出现ConcurrentModificationException    
}
```
好啦，这里给你介绍了增强for循环的实现原理，以及使用不当可能踩入的坑。所以，虽然是一个简单的for-each语法，但是也要了解其原理，不然可能导致一些莫名其妙的问题。




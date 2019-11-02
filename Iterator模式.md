## Iterator模式

Iterator模式用于在数据集合中按照顺序遍历集合。

### Iterator模式中的角色

- Iterator（迭代器）

  这是个接口，具体负责声明可以按顺序逐个遍历集合元素的方法。

- ConcreteIterator（具体的迭代器）

  主要实现Iterator的接口，该角色包含了遍历集合所必须 的信息。

- Aggregate（集合）

  这是个接口，声明一个可以“按顺序访问我内部元素的人”。

- ConcreteAggregate（具体的集合）

  该角色实现了Aggregate的所有方法，它会创建出具体的Iterator角色，即ConcreteIterator角色

### Iterator模式的类图

<img src="images\1572687793511.png" alt="1572687793511" style="zoom:80%;" />

### Java中的Iterator模式

在Java源码中也用了Iterator模式，在这以ArrayList为例。

- Iterator迭代器

  在Java中声明了Iterator接口，下面展示其中有关Iterator模式的关键代码

  ```java
  public interface Iterator<E> {
      /**
       * Returns {@code true} if the iteration has more elements.
       * (In other words, returns {@code true} if {@link #next} would
       * return an element rather than throwing an exception.)
       *
       * @return {@code true} if the iteration has more elements
       */
      boolean hasNext();
  
      /**
       * Returns the next element in the iteration.
       *
       * @return the next element in the iteration
       * @throws NoSuchElementException if the iteration has no more elements
       */
      E next();
  
      //......
      }
  }
  
  ```

- Aggregate（集合）

  ArrayList实现了List接口，因此Aggregate角色就是List接口，其List接口有关Iterator模式的部分代码如下：

  ```java
  public interface List<E> extends Collection<E> {
      //......
      
       /**
       * Returns an iterator over the elements in this list in proper sequence.
       *
       * @return an iterator over the elements in this list in proper sequence
       */
      Iterator<E> iterator();
      
      //......
  }
  ```

  代码正如上述所说：List是Aggregate角色，负责定义创建Iterator角色的接口。

- ConcreteIterator（具体的迭代器）

  从下图可以看到有关于Iterator的继承结构

  <img src="images\1572688225455.png" alt="1572688225455" style="zoom:60%;" />

  在ArrayList中可以找到实现List接口的方法：

  ```java
  /**
       * Returns an iterator over the elements in this list in proper sequence.
       *
       * <p>The returned iterator is <a href="#fail-fast"><i>fail-fast</i></a>.
       *
       * @return an iterator over the elements in this list in proper sequence
       */
      public Iterator<E> iterator() {
          return new Itr();
      }
  ```

  在iterator方法中创建了Itr类，再根据Iterator的继承结构图可以看出，Itr()实现了Iterator接口，因此Itr类则是ConcreteIterator（具体的迭代器）。

  Itr类中实现的Iterator方法(这里Itr类是ArrayList的内部类)：

  ```java
  private class Itr implements Iterator<E> {
      	int cursor;       // index of next element to return
          int lastRet = -1; // index of last element returned; -1 if no such
          int expectedModCount = modCount;
  
          // prevent creating a synthetic constructor
          Itr() {}
  
          public boolean hasNext() {
              return cursor != size;
          }
  
          @SuppressWarnings("unchecked")
          public E next() {
              checkForComodification();
              int i = cursor;
              if (i >= size)
                  throw new NoSuchElementException();
              Object[] elementData = ArrayList.this.elementData;
              if (i >= elementData.length)
                  throw new ConcurrentModificationException();
              cursor = i + 1;
              return (E) elementData[lastRet = i];
          }
      
      	final void checkForComodification() {
              if (modCount != expectedModCount)
                  throw new ConcurrentModificationException();
          }
      
      //......
  }
  ```

- ConcreteAggregate（具体的集合）

  ArrayList便是ConcreteAggregate具体的集合。
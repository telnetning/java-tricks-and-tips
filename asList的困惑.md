###asList的困惑
目录：  

+   [List\<T\>中的T](#1)
+   [List\<T\>中的List](#2)

----

<h4 id="1">返回的 List 中的元素类型</h4>
asList 是 Java 中比较常用的方法，用来将数组转换成 List。它定义在 Arrays 类中，Arrays 正如它的形式一样，是用来给数组提供各种操作的。  

既然是给数组提供操作，用来将数组转换成 List 的，那么很容易就这样使用：  

```Java
int[] spam = new int[]{1, 2, 3};
Arrays.asList(spam);
```

这里如果我们想把 asList 的结果赋给一个 List，就会发现问题了。由于 List 中的数据类型不能是基本类型，而我们得到的 List 是从 int[] 数组转换过来的，也就是 int，然而我们无法定义 List<int> 去接收 asList 返回的结果。  

在 Java 1.5 之前，集合中的元素是没有类型的，都是 Object ，所以那个时候，不需要定义 List<int>，可以认为，所有的 List 定义都是 List<Object>。可能是由于这个原因，上面的 Arrays.asList(spam) 在 java 1.4 中返回的是一个包含三个数字的列表。  

在 Java 1.5 之后，由于 List 中的对象有了类型，List 中并不能存储 int 。所以无法再用任何 List 接收原有的 asList 一个 int[] 的结果了。所以在 1.5 之后，asList 一个 int[] 其行为也发生了变化，它返回的是一个 List<int[]> 类型。这个时候，程序员想把 int[] 转换成 List 的梦想可能就落空了，程序员得到的不是一个包含了若干 int 值的集合，而是一个仅仅包含了原 int[] 的 List。  

如下：

```Java
        int[] spam = new int[]{1, 2, 3};
        List<int[]> s = Arrays.asList(spam);

        System.out.println(s.size());   // print 1
        for(int[] i : s) {
            System.out.println(i.length);  // print 3
        }
```

一个比较容易的解决方法就是，利用 Java 提供的 Integer 类，来表示 List 中的集合的类型：List<Integer>。 

```java
        Integer[] spam = new Integer[]{1, 2, 3};
        List<Integer> s = Arrays.asList(spam);
```

这个时候，就跟 List 中装的是其它对象是一样的效果。  

在这里你可能会想用自动装箱：  

```java
int[] spam = new int[]{1, 2, 3};
List<Integer> s = Arrays.asList(spam);
```

就跟平时很多场景使用自动装箱一样，你可能认为它会自动处理，将得到的 List 中的元素从 int 装箱成 Integer，但是，正如前面所说，你根本无法得到 List<int> 类型，你得到的是 List<int[]>。 

尽管这看起来不那么符合 asList 的定义：返回一个拥有原数组元素类型的 List。一直需要记住的是，int 作为基本类型，没法返回成 List<int>，所以它拥有特殊的处理，也不足为奇。  

```java
static <T> List<T>	asList(T... a)
``` 

<h4 id="2">返回的 List 是什么 List</h4>
另外一个问题，asList 返回的 List 类型是什么？  

程序员可能会写出这样的代码：  

```java
Integer[] spam = new Integer[]{1, 2, 4};
ArrayList<Integer> s = Arrays.asList(spam);
```

程序意图用 ArrayList 去接收 asList 的结果。然而，得到了运行时错误：  

```sh
Error:(12, 45) java: 不兼容的类型
  需要: java.util.ArrayList<java.lang.Integer>
  找到:    java.util.List<java.lang.Integer>
```

有人就想，那我强制转换一下：  

```java
Integer[] spam = new Integer[]{1, 2, 4};
ArrayList<Integer> s = (ArrayList<Integer>)Arrays.asList(spam);
```
结果编译时就不通过了：  

```java
java.lang.ClassCastException: java.util.Arrays$ArrayList cannot be cast to java.util.ArrayList
```

asList 返回的 ArrayList 无法转换成 ArrayList。为啥？  

因为 asList 返回的 arrayList 是 java.util.Arrays$ArrayList 而不是我们所熟知的 java.util.ArrayList!  

$符号表示，asList 返回的 ArrayList 是 asList 定义的内部类，和 java.util.ArrayList 并没有任何关系。  

所以我们可以猜测是，asList 返回了一个 Arrays 自己定义的 List 的子类，叫 ArrayList。而且它应该实现了 List 接口，所有我们才能用 List 去接收它的结果。  

看看源码：  

```java
3354    public static <T> List<T> More ...asList(T... a) {
3355        return new ArrayList<T>(a);
3356    }

3360
3361    private static class More ...ArrayList<E> extends AbstractList<E>
3362        implements RandomAccess, java.io.Serializable
3363    {
3364        private static final long serialVersionUID = -2764017481108945198L;
3365        private final E[] a;
3366
3367        More ...ArrayList(E[] array) {
3368            if (array==null)
3369                throw new NullPointerException();
3370            a = array;
3371        }
3372
3373        public int More ...size() {
3374            return a.length;
3375        }
3376
3377        public Object[] More ...toArray() {
3378            return a.clone();
3379        }
3380
3381        public <T> T[] More ...toArray(T[] a) {
3382            int size = size();
3383            if (a.length < size)
3384                return Arrays.copyOf(this.a, size,
3385                                     (Class<? extends T[]>) a.getClass());
3386            System.arraycopy(this.a, 0, a, 0, size);
3387            if (a.length > size)
3388                a[size] = null;
3389            return a;
3390        }
3391
3392        public E More ...get(int index) {
3393            return a[index];
3394        }
3395
3396        public E More ...set(int index, E element) {
3397            E oldValue = a[index];
3398            a[index] = element;
3399            return oldValue;
3400        }
3401
3402        public int More ...indexOf(Object o) {
3403            if (o==null) {
3404                for (int i=0; i<a.length; i++)
3405                    if (a[i]==null)
3406                        return i;
3407            } else {
3408                for (int i=0; i<a.length; i++)
3409                    if (o.equals(a[i]))
3410                        return i;
3411            }
3412            return -1;
3413        }
3414
3415        public boolean More ...contains(Object o) {
3416            return indexOf(o) != -1;
3417        }
3418    }
```

可以看出，asList 返回的是 Arrays 内部定义的类，ArrayList。ArrayList 继承了 AbstractList，而 AbstractList 实现了 List 接口。  

所以 asList 返回的 ArrayList 也实现了 List 接口。但是我们注意到在 ArrayList 中并没有重写 add 操作和 remove 操作。所以我们看看在 AbstractList 中的 add 操作和 remove 操作：  

```java
147    public void More ...add(int index, E element) {
148        throw new UnsupportedOperationException();
149    }
       ..........
160    public E More ...remove(int index) {
161        throw new UnsupportedOperationException();
162    }
```

所以 Arrays 定义的这个数组是不支持的 add 和 remove 操作的，一旦尝试，就会抛出 UnsupportedOperationException 错误。所以 asList 返回的 List ，其长度无法再被改变。  


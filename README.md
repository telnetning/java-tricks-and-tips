###Java tricks and tips
记录 Java 学习过程中容易犯的错误

----

####等于操作
和其它语言一样，Java 中也有 == 操作。但是 Java 中的 == 的行为对于初学者来说，比较容易犯错误。  
Java 中的 == 操作，是用来比较两个引用是否相同，也即比较两个对象是否为同一个实例。  
当你使用下面的代码时：

```Java
char a = 'A';
char b = 'A';
Character a1 = 'A';
Character b1 = 'A';
a == b;  //true
a1 == b1;  //true
```

得到的两个 true 的结果可能会让人觉得理所当然，然后，下面的比较结果可能就让人迷惑了：

```Java
Character a = 'A';
Character b = new Character('A');
a == b;  //false
```

可能出乎意料的，这里的结果为false，尽管 a 和 b 的值都为 A。   
原因在于，这里的 a 和 b 都是 Character，即都是引用，不再是基本类型，它们引用的结果都是 A，但是由于这里对于 b 采用了 new 操作，导致 a 和 b 并不是引用的同一个对象。   
而 Java 中的 == ，作用于引用类型时，就是用来比较两个引用的实例是否为同一个实例。   
如果要比较值呢？那么就需要用到 equals 方法。equals 方法是可以被重写的，所以行为是不定的。不过对于 Character 和 String 类型等，行为即比较对象的值：  

```Java
Character a = 'A';
Character b = new Character('A');
a.equals(b);  //true
```

这种情况下可能还比较明显，稍微注意下就不会那么容易出错。但是有些情况下，可能错误会更加隐晦，比如下面这段来自『Effective Java』中的这段代码：  

```Java
 Comparator<Integer> naturalOrder = new Comparator<Integer>() {       public int compare(Integer first, Integer second) {           return first < second ? -1 : (first == second ? 0 : 1);       }}
```

这里和容易把 first 和 second 参数当做 int 直接使用，很多情况下拆箱之后是可以使用的，然而，使用 == 则几乎一定会犯错误，得到不是想要的结果。比如这里，传入两个参数都是 100，得到的结果依旧会是 1，因为first == second 总是不成立的。  

当 == 遇到装箱拆箱的时候，往往容易出错。



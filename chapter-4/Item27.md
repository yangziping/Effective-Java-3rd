# 消除未检查的警告

当你使用泛型编程时，你会看到很多编译器警告：未检查的转换警告，未检查的方法调用警告，未检查的参数化变量类型警告和未检查的转换警告。你使用泛型获得的经验越多，你得到的警告也就越少，但不要指望新编写的代码能够干净地编译。

大部分未检查的警告是很容易消除的。比如说，假设你偶然地写了下面这个声明：

```java
Set<Lark> exaltation = new HashSet();
```

编译器会轻轻地提醒你做错了什么：

```java
Venery.java:4: warning: [unchecked] unchecked conversion
Set<Lark> exaltation = new HashSet();
^
required: Set<Lark>
found: HashSet
```



然后，你可以进行指定的纠正，使警告消失。注意，实际上你没必要指定类型参数，只要用Java7引进的钻石操作符（`<>`）表示一下就好。然后编译器就可以推断出正确的实际类型参数(在本例中为`Lark`)：

```java
Set<Lark> exaltation = new HashSet<>();
```

有一些警告将会很难消除。本节内容会举很多这类警告的例子。当你遇到需要一些思考的警告时，坚持住。你要尽可能地消除所有未检查的警告。如果你消除了所有警告，那么你就可以确保代码是类型安全的，这是一件非常好的事情。这意味着你不会遇到运行时的类型转换异常（`ClassCastException`），并且它会让你更加相信你的程序会像你预期那样去执行。

**如果你不能消除警告，但是你可以证明引发警告的代码是类型安全的，那么只能使用注释`@SuppressWarnings(“unchecked”)`来消除这个警告了**。如果在没有先证明代码是类型安全的情况下，你就消除了警告，那么你正在给自己一个假的安全感。即便代码在不发出任何警告的情况下通过编译，它仍然可能在运行期抛出一个类型转换异常（`ClassCastException`）。如果你忽略那些你知道是安全的未检查的警告（而不去消除他们），那么当一个新的代表真正问题的警告突然出现时，你不会注意到它的。这个新的警告会混合在所有假的你没有消除的警告中。

从单个局部变量声明到整个类，`SuppressWarnings`注解可以在任意声明上使用。**尽可能在最小的范围上使用`SuppressWarnings`注解**。一般来说，这个范围在一个变量声明或者一个非常短的方法或者构造函数上。永远不要在整个类上使用`SuppressWarnings`。这样做可能会掩盖关键的警告。

如果你发现自己在超过一行的方法或构造函数上使用了`SuppressWarnings`注解，那么你可能需要将其移到局部变量声明中。你可能不得不声明一个新变量，但是这样做不值得。比如，想一下`ArrayList`的`toArray`方法：

```java
public <T> T[] toArray(T[] a) {
    if (a.length < size)
    	return (T[]) Arrays.copyOf(elements, size, a.getClass());
    
    System.arraycopy(elements, 0, a, 0, size);
    if (a.length > size)
   	 	a[size] = null;
    return a;
}
```

如果你编译`ArrayList`，该方法会生成如下警告：

```java
ArrayList.java:305: warning: [unchecked] unchecked cast
return (T[]) Arrays.copyOf(elements, size, a.getClass());
^
required: T[]
found: Object[]
```

# Fixed-Size Arrays
# 固定长度数组

Early programming languages didn't have very fancy arrays. You'd create the array with a specific size and from that moment on it would never grow or shrink. Even the standard arrays in C and Objective-C are still of this type.
早期的编程语言没有非常奇特的数组。 您将创建具有特定大小的阵列，从那时起它将永远不会增长或缩小。 甚至C和Objective-C中的标准数组仍然是这种类型。

When you define an array like so,
当您定义这样的数组时，

	int myArray[10];

the compiler allocates one contiguous block of memory that can hold 40 bytes (assuming an `int` is 4 bytes):
编译器分配一个可以容纳40个字节的连续内存块（假设`int`是4个字节）：

![An array with room for 10 elements](Images/array.png)

That's your array. It will always be this size. If you need to fit more than 10 elements, you're out of luck... there is no room for it.
那是你的数组。 它总是这么大。 如果你需要超过10个元素，那你就不幸了......没有空间。

To get an array that grows when it gets full you need to use a [dynamic array](https://en.wikipedia.org/wiki/Dynamic_array) object such as `NSMutableArray` in Objective-C or `std::vector` in C++, or a language like Swift whose arrays increase their capacity as needed.
要获得一个在变满时生长的数组，你需要在Objective-C或`std :: vector`中使用[动态数组](https://en.wikipedia.org/wiki/Dynamic_array)对象，如`NSMutableArray` 在C ++中，或像Swift这样的语言，其数组根据需要增加容量。

A major downside of the old-style arrays is that they need to be big enough or you run out of space. But if they are too big you're wasting memory. And you need to be careful about security flaws and crashes due to buffer overflows. In summary, fixed-size arrays are not flexible and they leave no room for error.
旧式数组的一个主要缺点是它们需要足够大或者你的空间不足。 但如果它们太大你就会浪费记忆力。 并且您需要注意由于缓冲区溢出导致的安全漏洞和崩溃。 总之，固定大小的数组不灵活，不会留下任何错误。

That said, **I like fixed-size arrays** because they are simple, fast, and predictable.
也就是说，**我喜欢固定大小的数组**因为它们简单，快速且可预测。

The following operations are typical for an array:
以下操作是典型的数组：

- append a new element to the end
- insert a new element at the beginning or somewhere in the middle
- delete an element
- look up an element by index
- count the size of the array

- 在最后附加一个新元素
- 在开头或中间某处插入一个新元素
- 删除元素
- 按索引查找元素
- 计算数组的大小

For a fixed-size array, appending is easy as long as the array isn't full yet:
对于固定大小的数组，只要数组尚未满，则追加很容易：

![Appending a new element](Images/append.png)

Looking up by index is also quick and easy:
按索引查找也很简单：

![Indexing the array](Images/indexing.png)

These two operations have complexity **O(1)**, meaning the time it takes to perform them is independent of the size of the array.
这两个操作具有复杂度**O(1)**，这意味着执行它们所花费的时间与数组的大小无关。

For an array that can grow, appending is more involved: if the array is full, new memory must be allocated and the old contents copied over to the new memory buffer. On average, appending is still an **O(1)** operation, but what goes on under the hood is less predictable.
对于可以增长的数组，更多地涉及追加：如果数组已满，则必须分配新内存并将旧内容复制到新的内存缓冲区。 平均而言，附加仍然是**O(1)**操作，但是在幕后发生的事情是不太可预测的。

The expensive operations are inserting and deleting. When you insert an element somewhere that's not at the end, it requires moving up the remainder of the array by one position. That involves a relatively costly memory copy operation. For example, inserting the value `7` in the middle of the array:
昂贵的操作是插入和删除。 当你在一个不在末尾的某个地方插入一个元素时，它需要将数组的其余部分向上移动一个位置。 这涉及相对昂贵的存储器复制操作。 例如，在数组的中间插入值`7`：

![Insert requires a memory copy](Images/insert.png)

If your code was using any indexes into the array beyond the insertion point, these indexes are now referring to the wrong objects.
如果您的代码在插入点之外的数组中使用了任何索引，则这些索引现在引用了错误的对象。

Deleting requires a copy the other way around:
删除需要相反的副本：

![Delete also requires a memory copy](Images/delete.png)

This, by the way, is also true for `NSMutableArray` or Swift arrays. Inserting and deleting are **O(n)** operations -- the larger the array the more time it takes.
顺便说一下，对于`NSMutableArray`或Swift数组也是如此。 插入和删除是**O(n)**操作 - 数组越大，所需的时间越长。

Fixed-size arrays are a good solution when:
固定大小的数组是一个很好的解决方案：

1. You know beforehand the maximum number of elements you'll need. In a game this could be the number of sprites that can be active at a time. It's not unreasonable to put a limit on this. (For games it's a good idea to allocate all the objects you need in advance anyway.)
2. It is not necessary to have a sorted version of the array, i.e. the order of the elements does not matter.

1. 您事先知道您需要的最大元素数量。 在游戏中，这可能是一次可以激活的精灵数量。 对此加以限制并非没有道理。 （对于游戏，最好事先分配你需要的所有对象。）
2. 没有必要具有数组的排序版本，即元素的顺序无关紧要。

If the array does not need to be sorted, then an `insertAt(index)` operation is not needed. You can simply append any new elements to the end, until the array is full.
如果数组不需要排序，则不需要`insertAt(index)`操作。 您可以简单地将任何新元素附加到末尾，直到数组已满。

The code for adding an element becomes:
添加元素的代码变为：

```swift
func append(_ newElement: T) {
  if count < maxSize {
    array[count] = newElement
    count += 1
  }
}
```

The `count` variable keeps track of the size of the array and can be considered the index just beyond the last element. That's the index where you'll insert the new element.
`count`变量跟踪数组的大小，可以认为是最后一个元素之外的索引。 这是您将插入新元素的索引。

Determining the number of elements in the array is just a matter of reading the `count` variable, a **O(1)** operation.
确定数组中元素的数量只是读取`count`变量，**O(1)**操作。

The code for removing an element is equally simple:
删除元素的代码同样简单：

```swift
func removeAt(index: Int) {
  count -= 1
  array[index] = array[count]
}
```

This copies the last element on top of the element you want to remove, and then decrements the size of the array.
这会将要删除的元素顶部的最后一个元素复制，然后减小数组的大小。

![Deleting just means copying one element](Images/delete-no-copy.png)

This is why the array is not sorted. To avoid an expensive copy of a potentially large portion of the array we copy just one element, but that does change the order of the elements.
这就是数组未排序的原因。 为了避免昂贵的数组副本，我们只复制一个元素，但这确实改变了元素的顺序。

There are now two copies of element `6` in the array, but what was previously the last element is no longer part of the active array. It's just junk data -- the next time you append an new element, this old version of `6` will be overwritten.
现在在数组中有两个元素`6`的副本，但之前的最后一个元素不再是活动数组的一部分。 它只是垃圾数据 - 下次添加新元素时，这个旧版本的`6`将被覆盖。

Under these two constraints -- a limit on the number of elements and an unsorted array -- fixed-size arrays are still perfectly suitable for use in modern software.
在这两个约束条件下 - 元素数量和未排序数组的限制 - 固定大小的数组仍然非常适合在现代软件中使用。

Here is an implementation in Swift:
这是Swift中的一个实现：

```swift
struct FixedSizeArray<T> {
  private var maxSize: Int
  private var defaultValue: T
  private var array: [T]
  private (set) var count = 0
  
  init(maxSize: Int, defaultValue: T) {
    self.maxSize = maxSize
    self.defaultValue = defaultValue
    self.array = [T](repeating: defaultValue, count: maxSize)
  }
  
  subscript(index: Int) -> T {
    assert(index >= 0)
    assert(index < count)
    return array[index]
  }
  
  mutating func append(_ newElement: T) {
    assert(count < maxSize)
    array[count] = newElement
    count += 1
  }
  
  mutating func removeAt(index: Int) -> T {
    assert(index >= 0)
    assert(index < count)
    count -= 1
    let result = array[index]
    array[index] = array[count]
    array[count] = defaultValue
    return result
  }
  
  mutating func removeAll() {
    for i in 0..<count {
      array[i] = defaultValue
    }
    count = 0
  }
}
```

When creating the array, you specify the maximum size and a default value:
创建数组时，指定最大大小和默认值：

```swift
var a = FixedSizeArray(maxSize: 10, defaultValue: 0)
```

Note that `removeAt(index: Int)` overwrites the last element with this `defaultValue` to clean up the "junk" object that gets left behind. Normally it wouldn't matter to leave that duplicate object in the array, but if it's a class or a struct it may have strong references to other objects and it's good boyscout practice to zero those out.
注意`removeAt(index：Int)`用这个`defaultValue`覆盖最后一个元素来清理留下的“垃圾”对象。 通常将重复的对象留在数组中并不重要，但是如果它是一个类或结构，它可能具有对其他对象的强引用，并且将这些对象归零是很好的做法。

*Written for Swift Algorithm Club by Matthijs Hollemans*

*作者：Matthijs Hollemans*  
*翻译：[Andy Ron](https://github.com/andyRon)*
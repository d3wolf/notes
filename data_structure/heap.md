#堆
##概念
通常我们所说的堆，就是二叉树的一种变形，所以它本质也是一棵二叉树，只不过有一些自己的特点，所以我们有叫堆为二叉堆
* 堆，也叫二叉堆，它是一棵完全二叉树
* 堆的每棵子树都是一个堆
* 堆可以分为最大堆和最小堆
* 堆中的元素必须可以比较

##堆的常用场景
* 构建优先队列（可以参考Java的优先队列PriorityQueue<E>）
* 支持堆排序
* 快速找出一个集合中的最小值（或者最大值）

比如我们要实现一个优先队列的时候，通常会以下几种底层数据结构

| 数据结构 | 入队 | Header |
| :---: | :---: | :---: |
| 普通线性结构   | O（1）[顺序入队]   | O（n）[每次都求优先级最高，类似求最大值] |
| 顺序线性结构 | O（n）[入队，每次都找到插入的位置] |O（1） [因为已经排好序，直接取优先级最高]|
| 堆 | O（logn） | O（logn） |


##最大堆和最小堆

堆分为两种：

* 最大堆（大根堆）:
    在最大堆中，父结点的值比所有子结点的值都要大（或相等）
* 最小堆（小根堆）:
    在最小堆中，父结点的值要比所有子结点的值要小（或相等）

注意：
![1](https://img-blog.csdnimg.cn/20190428173425991.png)
* 从最大堆的特性中，我们知道，父结点的值一定是大于等于孩子结点的值，那么如果有一个最大堆的高是3，排除根结点，高层级的结点一定是大于低层级结点的值吗？ 这个是不一定的，我们可以看到上面图中的最大堆，第三层的结点E的值就比第四层结点H的值要小。

##最大堆实现基础
通常在代码的实现中，二叉堆的底层数据结构是使用数组而不是二叉链，因为如果使用数组，二叉堆可以存在以下特性:
![2](https://img-blog.csdnimg.cn/20190429153743475.png)

如果我们以数组来存储堆的结点元素，从数组的第二个索引1开始存储，堆中的取任意结点，索引为n, 那么可以满足：

    父结点索引为 n / 2
    左孩子索引为 2 * n
    右孩子索引为(2 * n) + 1

>为什么要从数组的第二个位置，索引1开始存放元素呢？因为很多的教材就是从索引开始存放的，公式也简单好记。只不过在我们自己实现代码时，可能就要多注意一下地方。

在我们了解堆使用数组存储的特性后，为了让代码更简洁高效，我们要从数组第一个位置开始存储，即索引为0的地方也存放元素，优化一下，所以规律就变成了

    父结点索引为 (n-1)/2
    左孩子索引为 2*n + 1
    右孩子索引为 (2*n + 1) + 1 = 2*n + 2

##堆的操作
###上浮 shift up
* 新添加的元素放到数组末尾
* 判断新添加的元素与其父结点的大小，如果新添元素大于其父结点，则上浮
* 直到新添元素小于其父结点值，或新添元素已经上浮到根结点位置
![3](https://img-blog.csdnimg.cn/20190429161825844.png)

###下沉 shift down
* 首先判断下沉元素的孩子结点，看是左孩子大还是右孩子大？取最大的那个孩子与下沉结点比较，如果下沉元素比最大孩子的值还要大，就不需要下沉，已经满足最大堆特性
* 如果下沉元素比最大孩子小，则交换位置。
* 不断重复判断是否执行下沉操作，直到下沉元素比孩子结点的值大或者下沉元素已经是叶子结点
![4](https://img-blog.csdnimg.cn/20190429164415896.png)
![5](https://img-blog.csdnimg.cn/20190429164431420.png)

###添加元素 add
* 时间复杂度O(logn)
* 追加元素到数组尾部
* 对新添元素进行上浮操作，直到满足最大堆特性

###取最大值 extractMax
* 时间复杂度O(logn)
* 取出最大值，把堆中（数组）最后的元素与根结点交换位置，删除最后的元素
* 对交换后的根结点元素进行下沉操作，直到满足最大堆特性

###取最大值,并插入新元素 replace
* 原思想，extractMax + add 两个O(logn)操作
* 但是，我们直接把要插入的元素替换到根结点位置，再下沉，就只需要一个O(logn)了

###将任意数组堆化 heapify
* 重点步骤：
  * 先找到堆的第一个非叶子节点（方式可以通过找到最后一个结点，它的父结点，就是第一个非叶子结点）
  * 所有非叶子结点，逐一下沉，直到根结点也完成下沉，就是整棵完全二叉树堆化完成

* 好处：
  * 将n各元素逐个插入到一个空堆中，时间复杂度是O(nlogn)
  * heapify的时间复杂度是O(n), 推算比较复杂，这里记住就好
  
##Java代码实现
```$xslt
public class Array<E> {

    private E[] data;
    private int size;

    public Array(int capacity) {  //  user assign size
        data = (E[]) new Object[capacity];
        size = 0;
    }

    public Array() {
        this(10); // default size
    }

    public Array(E[] arr) {
        data = (E[]) new Object[arr.length];
        for (int i = 0; i < arr.length; i++) {
            data[i] = arr[i];
        }
        size = arr.length;
    }


    public int getSize() {
        return size;
    }

    public int getCapacity() {
        return data.length;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    public void rangeCheck(int index) {
        if (index < 0 || index >= size) {
            throw new IllegalArgumentException("Index is Illegal!");
        }
    }

    public void add(int index, E e) {
        if (index < 0 || index > size) {
            throw new IllegalArgumentException("Index is Illegal ! ");
        }
        if (size == data.length) {
            resize(data.length * 2);
        }
        for (int i = size - 1; i >= index; i--) {
            data[i + 1] = data[i];
        }
        data[index] = e;
        size++;
    }

    private void resize(int newCapacity) {
        E[] newData = (E[]) new Object[newCapacity];
        for (int i = 0; i < size; i++) {
            newData[i] = data[i];
        }
        data = newData;
    }

    public void addLast(E e) { //末尾添加
        add(size, e);
    }

    public void addFirst(E e) { //头部添加
        add(0, e);
    }


    public E get(int index) {
        rangeCheck(index);
        return data[index];
    }

    public E getLast() {
        return get(size - 1);
    }

    public E getFirst() {
        return get(0);
    }

    public void set(int index, E e) {
        rangeCheck(index);
        data[index] = e;
    }

    public boolean contains(E e) {
        for (int i = 0; i < size; i++) {
            if (data[i].equals(e)) {
                return true;
            }
        }
        return false;
    }

    public int find(E e) {
        for (int i = 0; i < size; i++) {
            if (data[i].equals(e)) {
                return i;
            }
        }
        return -1;
    }


    public E remove(int index) {  // remove data[index] and return the value
        rangeCheck(index);
        E res = data[index];
        for (int i = index; i < size - 1; i++) {
            data[i] = data[i + 1];
        }
        size--;
        data[size] = null;//loitering objects  != memory  leak
        if (size == data.length / 4 && data.length / 2 != 0) {
            resize(data.length / 2); //防止复杂度的震荡
        }
        return res;
    }

    public E removeFirst() {
        return remove(0);
    }

    public E removeLast() {
        return remove(size - 1);
    }


    public void removeElement(E e) { //only remove one(may repetition) and user not know whether is deleted.
        int index = find(e);
        if (index != -1) {
            remove(index);
        }
    }

    // new method
    public void swap(int i, int j) {
        if (i < 0 || i >= size || j < 0 || j >= size) {
            throw new IllegalArgumentException("Index is illegal.");
        }

        E t = data[i];
        data[i] = data[j];
        data[j] = t;
    }

    @Override
    public String toString() {
        StringBuilder res = new StringBuilder();
        res.append(String.format("Array : size = %d, capacity = %d\n", size, data.length));
        res.append("[");
        for (int i = 0; i < size; i++) {
            res.append(data[i]);
            if (i != size - 1) {
                res.append(", ");
            }
        }
        res.append("]");
        return res.toString();
    }
}
```

##堆排序
###什么是堆排？
本来这里主要是讲一下堆的结构和实现，但是其实堆排序其实也是一个挺重要的数据结构知识，毕竟属于基本的八大排序之一嘛，所以这里就再补充一些堆排的知识。
我们知道，堆的底层结构就是一个数组，所以我们就可以利用这个数组同时是堆的底层结构的特性，利用堆的特性，对这个数组进行排序，得到一个有序数组。 利用堆的特性对底层无序数组排序的过程就是堆排。

###特性
堆排不跟其他排序算法一样，不依赖什么东西，堆排的实现非常的依赖堆这个数据结构，所以无堆则无堆排，所以如果我们要对一个无序数组进行排序，首先就要将该无序数组构造成一个最大堆或最小堆，不同种的堆也会造成不同的顺序排序
* 最大堆堆排后的结果是升序序列
* 最小堆堆排后的结果是降序序列

###时间复杂度
![6](https://img-blog.csdnimg.cn/20190430113643811.png)

###实现步骤
* 看无序数组是想进行什么排序?升序还是降序？
* 如果升序，就先将无序数组堆化成最大堆，反之则最小堆。通过heapify去实现
* 堆化后，将堆顶元素与末尾元素交换，堆的结点长度减1，对当前堆进行重建，下沉，直到重新满足堆特性
* 遍历堆中未交换过的结点，遍历交换完毕后，就是数组排序的结束

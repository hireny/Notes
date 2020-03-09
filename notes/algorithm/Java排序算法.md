# 排序算法

- 约定
- 冒泡排序
- 选择排序
- 插入排序
- 希尔排序
- 归并排序
	- 归并方法
	- 自顶向下归并排序
	- 自底向上归并排序
	
- 快速排序
	- 基本算法
	- 切分
	- 性能分析
	- 算法改进
	- 基于切分的快速选择算法

- 堆排序
	- 堆的数据结构
	- 堆排序
	- 性能分析
	
- 小结
	- 排序算法的比较
	- Java的排序算法实现

# 约定

待排序的元素需要实现 Java 的 Comparable 接口，该接口有 compareTo() 方法，可以用它来判断两个元素的大小关系。

使用辅助函数 less() 和 exchange() 来进行比较和交换的操作，使得代码的可读性和可移植性更好。

排序算法的成本模型是比较和交换的次数。

```java
public abstract class AbstractSort<T extends Comparable<T>> {
    /**
     * 排序的抽象方法
     * @param sourceArray
     */
    public abstract void sort(T[] sourceArray);

    /**
     * 比较大小
     * @param v
     * @param w
     * @return      v < w = true
     */
    protected boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }

    /**
     * 数组中元素交换
     * @param a
     * @param i
     * @param j
     */
    protected void exchange(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
}
```

# 冒泡排序

从左到右不断交换相邻逆序的元素，在一轮的循环之后，可以让未排序的最大元素上浮到右侧。

在一轮循环中，如果没有发生交换，那么说明数组已经是有序的，此时可以直接退出。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/0f8d178b-52d8-491b-9dfd-41e05a952578.gif" width="200px"> </div><br>
```java
public class BubbleSort<T extends Comparable<T>> extends AbstractSort<T> {
    @Override
    public void sort(T[] sourceArray) {
        int n = sourceArray.length;
        boolean isSorted = false;
        for (int i = 1; i < n && !isSorted; i++) {
            isSorted = true;
            for (int j = 0; j < sourceArray.length - i; j++) {
                if (less(sourceArray[j + 1], sourceArray[j])) {
                    isSorted = false;
                    exchange(sourceArray, j, j + 1);
                }
            }
        }
    }
    public static void main(String[] args) {
        Integer[] arr = new Integer[]{9, 32, 10, 21, 999, 53, 12, 0, -1, 74};
        AbstractSort<Integer> sort = new BubbleSort<>();
        sort.sort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

# 选择排序

从数组中选择最小元素，将它与数组的第一个元素交换位置。再从数组剩下的元素中选择出最小的元素，将它与数组的第二个元素交换位置。不断进行这样的操作，直到将整个数组排序。

选择排序需要 \~N<sup>2</sup>/2 次比较和 \~N 次交换，它的运行时间与输入无关，这个特点使得它对一个已经排序的数组也需要这么多的比较和交换操作。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/bc6be2d0-ed5e-4def-89e5-3ada9afa811a.gif" width="230px"> </div><br>
```java
public class SelectionSort<T extends Comparable<T>> extends AbstractSort<T> {
    @Override
    public void sort(T[] sourceArray) {
        int n = sourceArray.length;
        for (int i = 0; i < n - 1; i++) {
            int min = i;
            for (int j = i + 1; j <n; j++) {
                if (less(sourceArray[j], sourceArray[min])) {
                    min = j;
                }
            }
            exchange(sourceArray, min, i);
        }
    }
    public static void main(String[] args) {
        Integer[] arr = new Integer[]{12, 32, 10, 21, 999, 53, 12, 0, -1, 74};
        AbstractSort<Integer> sort = new SelectionSort<>();
        sort.sort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

# 插入排序

每次都将当前元素插入到左侧已经排序的数组中，使得插入之后左侧数组依然有序。

对于数组 {3, 5, 2, 4, 1}，它具有以下逆序：(3, 2), (3, 1), (5, 2), (5, 4), (5, 1), (2, 1), (4, 1)，插入排序每次只能交换相邻元素，令逆序数量减少 1，因此插入排序需要交换的次数为逆序数量。

插入排序的时间复杂度取决于数组的初始顺序，如果数组已经部分有序了，那么逆序较少，需要的交换次数也就较少，时间复杂度较低。

- 平均情况下插入排序需要 \~N<sup>2</sup>/4 比较以及 \~N<sup>2</sup>/4 次交换；
- 最坏的情况下需要 \~N<sup>2</sup>/2 比较以及 \~N<sup>2</sup>/2 次交换，最坏的情况是数组是倒序的；
- 最好的情况下需要 N-1 次比较和 0 次交换，最好的情况就是数组已经有序了。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/35253fa4-f60a-4e3b-aaec-8fc835aabdac.gif" width="200px"> </div><br>
```java
public class InsertionSort<T extends Comparable<T>> extends AbstractSort<T> {
    @Override
    public void sort(T[] sourceArray) {

        int n = sourceArray.length;
        for (int i = 1; i < n; i ++) {
            for (int j = i; j > 0 && less(sourceArray[j], sourceArray[j-1]); j--) {
                exchange(sourceArray, j-1, j);
            }
        }
    }
    public static void main(String[] args) {
        Integer[] arr = new Integer[]{9, 32, 10, 21, 999, 53, 12, 0, -1, 74};
        AbstractSort<Integer> sort = new InsertionSort<>();
        sort.sort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

# 希尔排序

对于大规模的数组，插入排序很慢，因为它只能交换相邻的元素，每次只能将逆序数量减少 1。希尔排序的出现就是为了解决插入排序的这种局限性，它通过交换不相邻的元素，每次可以将逆序数量减少大于 1。

希尔排序使用插入排序对间隔 h 的序列进行排序。通过不断减小 h，最后令 h=1，就可以使得整个数组是有序的。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/7818c574-97a8-48db-8e62-8bfb030b02ba.png" width="450px"> </div><br>
```java
public class ShellSort<T extends Comparable<T>> extends AbstractSort<T> {

	@Override
	public void sort(T[] sourceArray) {
		int n = sourceArray.length;
		// 区间
		int h = 1;
		while (h < n / 3) {
			h = 3 * h + 1;
		}

		while (h >= 1) {
			// 将数组变成h有序
			for (int i=h; i < n; i++) {
				//将a[i]插入到a[i-h],a[i-2*h],a[i-3*h]...之中
				for (int j=i; j>=h && less(sourceArray[j], sourceArray[j-h]); j -=h) {
					a++;
					exchange(sourceArray, j, j - h);
				}
			}
			h = h/3;
		}
	}
	public static void main(String[] args) {

		Integer[] arr = new Integer[]{9, 32, 10, 21, 999, 53, 12, 0, -1, 74};
		AbstractSort<Integer> sort = new ShellSort<>();
		sort.sort(arr);
		System.out.println(Arrays.toString(arr));
	}
}
```

希尔排序的运行时间达不到平方级别，使用递增序列 1, 4, 13, 40, ...  的希尔排序所需要的比较次数不会超过 N 的若干倍乘于递增序列的长度。后面介绍的高级排序算法只会比希尔排序快两倍左右。

# 归并排序

归并排序：一种简单的递归排序算法。归并排序的核心思想是分治法，是先（递归地）将一个数组分成两半分别排序，然后将结果归并起来。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/ec840967-d127-4da3-b6bb-186996c56746.png" width="300px"> </div><br>
## 1. 归并方法

归并方法将数组中两个已经排序的部分归并成一个。

```java
public abstract class MergeSort<T extends Comparable<T>> extends AbstractSort<T> {

    // 归并排序所需的辅助数组
    protected T[] aux;
    /**
     * 归并操作
     *
     * 该方法先将所有元素复制到aux[]中，然后再归并回a[]中。
     * 方法在归并时（第二个for循环）进行4个条件判断：
     *  1、先左半边用尽（取右半边的元素）
     *  2、右半边用尽（取左半边的元素）
     *  3、右半边的当前元素小于左半边的元素（取右半边的元素）
     *  4、右半边的当前元素大于等于左半边的当前元素（取左半边的元素）
     * @param a     数组
     * @param lo    第一部分数组的开始位置
     * @param mid   第一部分数组归并的结束位置
     * @param hi    第二部分数组归并的结束位置
     */
    protected void merge(T[] a, int lo, int mid, int hi) {
        // 将a[lo..mid] 和 a[mid+1..hi]归并
        int i = lo, j = mid + 1;
        // 将a[lo..hi]复制到aux[lo..hi]
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }
        for (int k = lo; k <= hi; k++) {
            // 归并回到a[lo..hi]
            if(i > mid) {
                a[k] = aux[j++];
            } else if(j > hi) {
                a[k] = aux[i++];
            } else if(less(aux[j], aux[i])) {
                a[k] = aux[j++];
            } else {
                a[k] = aux[i++];
            }
        }
    }
}
```

## 2. 自顶向下归并排序

自顶向下的归并排序算法就是把一个大数组不断的进行二分，直到子数组的元素个数为一，因为这个时候子数组必定是有序的，然后进行有序合并，直到合并成一个有序的数组。

因为每次都将问题对半分成两个子问题，这种对半分的算法复杂度一般为 O(NlogN)。

```java
public class MergeUDSort<T extends Comparable<T>> extends MergeSort<T> {

    @Override
    public void sort(T[] sourceArray) {
        //一次性分配空间
        aux = (T[]) Array.newInstance(sourceArray.getClass().getComponentType(), sourceArray.length);
        sort(sourceArray, 0, sourceArray.length-1);
    }
    private void sort(T[] a, int lo, int hi) {
        // 将数组a[lo...hi]排序
        if(hi <= lo) {
            return;
        }
        int mid = lo + (hi - lo) / 2;
        sort(a, lo, mid); // 将左半边排序
        sort(a, mid+1, hi); // 将右半边排序
        merge(a, lo, mid, hi); //归并结果
    }
    public static void main(String[] args) {

        //自顶向下的归并排序
        Integer[] arr = new Integer[]{9, 32, 10, 21, 999, 53, 12, 0, -1, 74};
        MergeSort<Integer> sort = new MergeUDSort<>();
        sort.sort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```


## 3. 自底向上归并排序

自底向上的归并排序算法的思想就是数组汇总先一个一个归并成两两有序的序列，两两有序的序列归并成四个四个有序的序列，一次类推，直到归并的长度大于整个数组的长度，此时数组有序。需要注意的是数组按照归并长度划分，最后一个子数组可能不满足长度要求，这个情况特殊处理。

```java
public class MergeDUSort<T extends Comparable<T>> extends MergeSort<T> {

    @Override
    public void sort(T[] sourceArray) {
        // 进行 lgN次两两归并
        int length = sourceArray.length;
        aux = (T[]) Array.newInstance(sourceArray.getClass().getComponentType(), length);

        // sz表示归并子数组的长度，1表示一个一个归并，依次增长
        for (int sz=1; sz <length; sz = sz + sz) { //sz子数组大小
            for (int lo = 0; lo < length - sz; lo += sz + sz) { //lo:子数组索引
                merge(sourceArray, lo, lo + sz - 1, Math.min(lo + sz + sz - 1, length - 1));
            }
        }
    }
    public static void main(String[] args) {

        //自底向上的归并排序
        Integer[] arr = new Integer[]{9, 32, 10, 21, 999, 53, 12, 0, -1, 74};
        MergeSort<Integer> sort = new MergeDUSort<>();
        sort.sort(arr);
        System.out.println(Arrays.toString(arr));
    }
```

# 快速排序

## 1. 基本算法

快速排序是一种分治的排序算法。快速排序是选取数组中的一个切分元素并将数组分成两部分，左子数组小于等于切分元素，右子数组大于等于切分元素，在重复步骤，直到整个数组变得有序。

快速排序和归并排序是互补的：归并排序将数组分成两个子数组分别排序，并将有序的子数组归并以将整个数组排序；而快速排序将数组排序的方式则是当两个子数组都有序时整个数组也就自然有序。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/6234eb3d-ccf2-4987-a724-235aef6957b1.png" width="280px"> </div><br>
```java
public class QuickSort<T extends Comparable<T>> extends AbstractSort<T> {

	@Override
	public void sort(T[] sourceArray) {
		sort(sourceArray, 0, sourceArray.length - 1);
	}

	private void sort(T[] a, int lo, int hi) {
		if(hi <= lo) {
			return;
		}
		int j = partition(a, lo, hi); //切分
		sort(a, lo, j-1); // 将左半部分a[lo...j-1]排序
		sort(a, j+1, hi); // 将有半部分a[j+1...hi]排序
	}
	public static void main(String[] args) {
		Integer[] arr = new Integer[]{12, 32, 10, 21, 999, 53, 12, 0, -1, 74};
		AbstractSort<Integer> sort = new QuickSort<>();
		sort.sort(arr);
		System.out.println(Arrays.toString(arr));
	}
}
```

## 2. 切分

取 a[l] 作为切分元素，然后从数组的左端向右扫描直到找到第一个大于等于它的元素，再从数组的右端向左扫描找到第一个小于它的元素，交换这两个元素。不断进行这个过程，就可以保证左指针 i 的左侧元素都不大于切分元素，右指针 j 的右侧元素都不小于切分元素。当两个指针相遇时，将切分元素 a[l] 和 a[j] 交换位置。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/c4859290-e27d-4f12-becf-e2a5c1f3a275.gif" width="320px"> </div><br>
```java
/**
 * 快速排序的切分,将数组a[lo..hi]进行切分,使得[lo..j-1] <= a[j] <= a[j+1...hi],并返回索引
 * @param a
 * @param lo
 * @param hi
 * @return
 */
private int partition(T[] a, int lo, int hi) {
	// 将数组切分成a[lo...i-1],a[i],a[i+1...hi]
	int i = lo, j = hi + 1; //左右扫描指针
	T v = a[lo]; //切分元素
	while(true) {
		// 扫描左右,检查扫描是否结束并交换元素
		while(less(a[++i], v)) {	// 查找左子数组大于切分元素 while循环是小于切分元素就一直循环
			if(i == hi) {
				break;
			}
		}
		while(less(v, a[--j])) {	// 查找右侧数组小于切分元素 while循环是大于切分元素就一直循环
			if(j == lo) {
				break;
			}
		}
		if(i >= j) {
			break;
		}
		// 找出左子数组大于切分元素与右子数组小于切分元素的两个元素并交换
		exchange(a, i, j);
	}
	exchange(a, lo, j); //将 v = a[j]放入正确的位置
	return j; //a[lo...j-1] <= a[j] <= a[j+1..hi] 达成
}
```

## 3. 性能分析

快速排序是原地排序，不需要辅助数组，但是递归调用需要辅助栈。

快速排序最好的情况下是每次都正好将数组对半分，这样递归调用次数才是最少的。这种情况下比较次数为 C<sub>N</sub>=2C<sub>N/2</sub>+N，复杂度为 O(NlogN)。

最坏的情况下，第一次从最小的元素切分，第二次从第二小的元素切分，如此这般。因此最坏的情况下需要比较 N<sup>2</sup>/2。为了防止数组最开始就是有序的，在进行快速排序时需要随机打乱数组。

## 4. 算法改进

#### 4.1 切换到插入排序

因为快速排序在小数组中也会递归调用自己，对于小数组，插入排序比快速排序的性能更好，因此在小数组中可以切换到插入排序。

#### 4.2 三数取中

最好的情况下是每次都能取数组的中位数作为切分元素，但是计算中位数的代价很高。一种折中方法是取 3 个元素，并将大小居中的元素作为切分元素。

#### 4.3 三向切分

对于有大量重复元素的数组，可以将数组切分为三部分，分别对应小于、等于和大于切分元素。

三向切分快速排序对于有大量重复元素的随机数组可以在线性时间内完成排序。

```java
public class Quick3waySort<T extends Comparable<T>> extends AbstractSort<T> {

    @Override
    public void sort(T[] sourceArray) {
        sort(sourceArray, 0, sourceArray.length - 1);
    }
    private void sort(T[] a, int lo, int hi) {
        if(hi <= lo) {
            return;
        }
        int lt = lo, i = lo + 1, gt = hi;
        T v = a[lo];
        while (i <= gt) {
            int cmp = a[i].compareTo(v);
            if (cmp < 0) {
                exchange(a, lt++, i++);
            } else if (cmp > 0) {
                exchange(a, i, gt--);
            } else {
                i++;
            }
        }
        // 现在 a[lo...lt-1] < v = a[lt...gt] < a[gt+1...hi]成立
        sort(a, lo, lt-1); // 将左半部分a[lo...lt-1]排序
        sort(a, gt+1, hi); // 将有半部分a[gt+1...hi]排序
    }
    public static void main(String[] args) {
        Integer[] arr = new Integer[]{12, 32, 10, 21, 999, 53, 12, 0, -1, 74};
        AbstractSort<Integer> sort = new Quick3waySort<>();
        sort.sort(arr);
        System.out.println(Arrays.toString(arr));
    }
}
```

## 5. 基于切分的快速选择算法

快速排序的 `partition()` 方法，会返回一个整数 j 使得 a[l..j-1] 小于等于 a[j]，且 a[j+1..h] 大于等于 a[j]，此时 a[j] 就是数组的第 j 大元素。

可以利用这个特性找出数组的第 k 个元素。

该算法是线性级别的，假设每次能将数组二分，那么比较的总次数为 (N+N/2+N/4+..)，直到找到第 k 个元素，这个和显然小于 2N。

```java
public T select(T[] nums, int k) {
    int l = 0, h = nums.length - 1;
    while (h > l) {
        int j = partition(nums, l, h);

        if (j == k) {
            return nums[k];
        } else if (j > k) {
            h = j - 1;
        } else {
            l = j + 1;
        }
    }
    return nums[k];
}
```

# 堆排序

堆排序是一种属性选择排序，是对直接选择排序的有效改进。

## 1. 堆的数据结构

堆是一个树形结构，堆的底层是一棵完全二叉树。

堆分为两种：最大堆和最小堆，两个差别在于节点的排序方式。

- 父节点的值大于等于每一个子结点的值，则为最大堆
- 父节点的值小于等于每一个子结点的值，则为最小堆

> 在这里只谈论满足最大堆条件的堆。

堆可以用数组表示。在一个堆中，位置 k 的节点的父节点位置为 k/2，而它的两个子节点的位置分别为 2k 和 2k+1。这样在不使用指针的情况下也可以通过计算数组的索引在树中上下移动：从 a[k] 向上一层就令 k 等于 k/2，向下一层则令 k 等于 2k 或 2k+1。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/f48883c8-9d8a-494e-99a4-317d8ddb8552.png" width="170px"> </div><br>
## 2. 堆排序

无序数组先构建一个堆（大顶堆），然后把最大元素和当前堆中数组的最后一个元素交换位置，并且不删除它，那么就可以得到一个从尾到头的递减序列，从正向来看就是一个递增序列，这就是堆排序。

#### 2.1 堆的构建

无序数组建立堆，只需要从左至右遍历数组，用上浮操作扫描左侧的所有元素已经是一棵堆有序的完全树即可。一个更高效的方法是从右至左进行下沉操作构建子堆，如果一个结点的两个子结点都已经是堆了，那么在该结点上调用下沉操作可以将它们变成一个堆，这个过程会递归地建立起堆的秩序。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/c2ca8dd2-8d00-4a3e-bece-db7849ac9cfd.gif" width="210px"> </div><br>
#### 2.2 下沉排序

堆排序的第二阶段是使用while循环将最大元素 a[1] 和 a[length] 交换并修复了堆，如此重复直到堆变空。将`exchange()`和`less()`的实现中的索引间一即可得到和其它排序算法一致的实现（将a[0]至a[length-1]排序）。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/d156bcda-ac8d-4324-95e0-0c8df41567c9.gif" width="250px"> </div><br>
```java
public class HeapSort<T extends Comparable<T>> extends AbstractSort<T> {
	@Override
	public void sort(T[] a) {
		int length = a.length;
		// 构造堆有序
		for(int k = length/2; k >=1; k--) {
			sink(a, k, length);
		}
		// 下沉排序
		while(length > 1) {
			exchange(a, 1, length--);
			sink(a, 1, length);
		}
	}

	/**
	 * 由上至下的堆有序化（下沉）的实现 <br>
	 *
	 * 小的结点往下移动
	 * @param a		数组
	 * @param k		开始移动的位置
	 * @param n		下沉结束的位置
	 */
	private void sink(T[] a, int k, int n) {
		// 满足向下移动的条件
		while(2*k <= n) {
			int j = 2*k;
			// 从a[j]和a[j+1]中比较出较大的元素
			if (j < n && less(a, j, j+1)) {
				j++;
			}
			// 如果a[k]>a[j],则不下沉（就是父结点的元素比子结点的元素大就不下沉）
			if (!less(a, k, j)) {
				break;
			}
			// 将大的元素移动到父结点上去
			exchange(a, k, j);
			k = j;
		}
	}

	private boolean less(T[] pq, int i, int j) {
		return less(pq[i-1], pq[j-1]);
	}

	@Override
	protected void exchange(T[] a, int i, int j) {
		T t = a[i-1];
		a[i-1] = a[j-1];
		a[j-1] = t;
	}

	public static void main(String[] args) {
		Integer a[] = {9,3,45,69,25,1,24,89,40,15,595,238,666,42,5,99};
		AbstractSort<Integer> sort = new HeapSort<>();
		sort.sort(a);
		System.out.println(Arrays.toString(a));
	}
}
```

## 3. 性能分析

一个堆的高度为 logN，因此在堆中插入元素和删除最大元素的复杂度都为 logN。

对于堆排序，由于要对 N 个节点进行下沉操作，因此复杂度为 NlogN。

堆排序是一种原地排序，没有利用额外的空间。

现代操作系统很少使用堆排序，因为它无法利用局部性原理进行缓存，也就是数组元素很少和相邻的元素进行比较和交换。

# 小结

## 1. 排序算法的比较

下表总结了排序算法的各种重要性质。除了希尔排序（它的复杂度只是一个近视）、插入排序（它的复杂度取决于输入元素的排列情况）和快速排序的两个版本（它的复杂度和概率有关，取决于输入元素的分布情况）之外，将这些运行时间的增长数量级乘以适当的常数就能够大致估计出其运行时间。

| 算法 | 稳定性 | 时间复杂度 | 空间复杂度 | 备注 |
| :---: | :---: |:---: | :---: | :---: |
| 选择排序 | × | N<sup>2</sup> | 1 | |
| 冒泡排序 | √ |  N<sup>2</sup> | 1 | |
| 插入排序 | √ |  N \~ N<sup>2</sup> | 1 | 时间复杂度和初始顺序有关 |
| 希尔排序 | ×  |  N 的若干倍乘于递增序列的长度 | 1 | 改进版插入排序 |
| 快速排序 | ×  | NlogN | logN | |
| 三向切分快速排序 | ×  |  N \~ NlogN | logN | 适用于有大量重复主键|
| 归并排序 | √ |  NlogN | N | |
| 堆排序 | ×  |  NlogN | 1 | 无法利用局部性原理|

快速排序是最快的通用排序算法，它的内循环中的指令很少，而且它还能利用缓存，因为它总是顺序地访问数据。所以它的运行时间的增长数量级为 ~cNlogN，而这里的 c 比其它线性对数级别的排序算法的相应常数都要小。使用三向切分快速排序，实际应用中可能出现的某些分布的输入能够达到线性级别，而其它排序算法仍然需要线性对数时间。

因此，在大多数实际情况中，快速排序是最佳选择，如果未定型很重要而空间又不是问题，归并排序可能是最好的。

一些性能优先的应用的重点可能是将数字排序，因此更合理的做法是跳过引用直接将原始数据类型的数据排序。例如，double和Double的差别。对于前者我们可以直接交换这些数并将数组排序；而对于后者，我们交换的是存储了这些数字的Double对象的引用。因此只是将一大组数排序的话，跳过引用可以节省存储所有引用所需的空间和通过引用来访问数字的成本。

## 2. Java 的排序算法实现

Java 系统库中的主要排序方法为 java.util.Arrays.sort()。根据不同的参数类型，它实际上代表了一系列排序方法：

- 每种原始数据类型都有一个不同的的排序方法
- 一个适用于所有实现了 `Comparable` 接口的数据类型的排序方法
- 一个适用于实现了比较器 `Comparator` 的数据类型的的排序方法

Java的系统程序员选择了对原始数据类型使用三向切分的快速排序，对于引用类型使用归并排序。 





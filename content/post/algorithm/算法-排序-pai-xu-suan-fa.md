---
title: 算法-排序
date: 2021-09-08 10:03:34.356
updated: 2022-01-12 12:52:35.024
categories: [算法]
tags: [算法]
---

# 算法-排序



## Introduction

以下为自己整理总结的常用排序算法与实现。
时间复杂度一次完整的计算：[http://discrete.gr/complexity/](http://discrete.gr/complexity/)

> 基础的三种排序：冒泡排序、插入排序、选择排序
> 三种常见的排序：归并排序、快速排序、插入排序

简单接口说明：

```Java
public interface Sort {
    int[] sort(int[] arr);
}
```

### 复杂度浏览

| Algorithm                                                    | Time Complexity |                  |                  | Space Complexity | staility |
| ------------------------------------------------------------ | --------------- | ---------------- | ---------------- | ---------------- | -------- |
|                                                              | Best            | Average          | Worst            | Worst            |          |
| [Bubble Sort](http://en.wikipedia.org/wiki/Bubble_sort)      | `Ω(n)`          | `Θ(n^2)`         | `O(n^2)`         | `O(1)`           | Y        |
| [Insertion Sort](http://en.wikipedia.org/wiki/Insertion_sort) | `Ω(n)`          | `Θ(n^2)`         | `O(n^2)`         | `O(1)`           | Y        |
| [Selection Sort](http://en.wikipedia.org/wiki/Selection_sort) | `Ω(n^2)`        | `Θ(n^2)`         | `O(n^2)`         | `O(1)`           | N        |
| [Quicksort](http://en.wikipedia.org/wiki/Quicksort)          | `Ω(n log(n))`   | `Θ(n log(n))`    | `O(n^2)`         | `O(log(n))`      | N        |
| [Mergesort](http://en.wikipedia.org/wiki/Merge_sort)         | `Ω(n log(n))`   | `Θ(n log(n))`    | `O(n log(n))`    | `O(n)`           | Y        |
| [Heapsort](http://en.wikipedia.org/wiki/Heapsort)            | `Ω(n log(n))`   | `Θ(n log(n))`    | `O(n log(n))`    | `O(1)`           | N        |
| [Shell Sort](http://en.wikipedia.org/wiki/Shellsort)         | `Ω(n log(n))`   | `Θ(n(log(n))^2)` | `O(n(log(n))^2)` | `O(1)`           | N        |
| [Counting Sort](https://en.wikipedia.org/wiki/Counting_sort) | `Ω(n+k)`        | `Θ(n+k)`         | `O(n+k)`         | `O(k)`           | Y        |
| [Tree Sort](https://en.wikipedia.org/wiki/Tree_sort)         | `Ω(n log(n))`   | `Θ(n log(n))`    | `O(n^2)`         | `O(n)`           | Y        |
| [Bucket Sort](http://en.wikipedia.org/wiki/Bucket_sort)      | `Ω(n+k)`        | `Θ(n+k)`         | `O(n^2)`         | `O(n)`           |          |
| [Radix Sort](http://en.wikipedia.org/wiki/Radix_sort)        | `Ω(nk)`         | `Θ(nk)`          | `O(nk)`          | `O(n+k)`         |          |
| [Cubesort](https://en.wikipedia.org/wiki/Cubesort)           | `Ω(n)`          | `Θ(n log(n))`    | `O(n log(n))`    | `O(n)`           |          |
| [Timsort](http://en.wikipedia.org/wiki/Timsort)              | `Ω(n)`          | `Θ(n log(n))`    | `O(n log(n))`    | `O(n)`           |          |


参考：[复杂度浏览表](https://www.bigocheatsheet.com/)

## 冒泡排序

每次从头开始进行比较，将大的数往后移动，在第n次循环结束的时候，第n大的数就位于数组的第n-1位。

```Java
public class BubbleSort implements Sort {
    @Override
    public int[] sort(int[] arr) {
        int n = arr.length;
        for (int i = 0; i < n; i++) {
            // 若没有发生交换的时候表示循环结束
            boolean isSwap = false;
            // j < n - i 减少循环次数
            for (int j = 1; j < n - i; j++) {
                if (arr[j - 1] > arr[j]) {
                    swap(arr, j - 1, j);
                    isSwap = true;
                }
            }
            if (!isSwap) {
                break;
            }
        }
        return arr;
    }
}

```

## 插入排序

将数找到合适的位置，将该位置的数全部向后移动，留出插入的空间进行插入。（未有交换操作）

```Java
public class InsertionSort implements Sort {
    @Override
    public int[] sort(int[] arr) {
        int n = arr.length;
        for (int i = 1; i < n; i++) {
            // 选中的数
            int key = arr[i];
            int j = i - 1;
            // 寻找合适的位置
            for (;j >= 0 && arr[j] > key;j--) {
                arr[j + 1] = arr[j];
            }
            arr[j + 1] = key;
        }
        return arr;
    }
}

```

## 选择排序

在尚未排序的序列中找到一个最小的元素，放入到排序完序列的最后一位。

```Java
public class SelectionSort implements Sort {
    @Override
    public int[] sort(int[] arr) {
        int n = arr.length;
        for (int i = 0; i < n; i++) {
            // 选定最小的数
            int min = i;
            for (int j = i + 1; j < n; j++) {
                if (arr[j] < arr[min]) {
                    min = j;
                }
            }
            if (min != i) {
                swap(arr, min, i);
            }
        }
        return arr;
    }
}
```

## 归并排序

> 将两个有序的数组归并成一个较大的数组。
> 要将一个数组排序，先（递归）分成两半分别排序，然后将递归结果合并。将复杂问题进行简单化，然后解决完后这个复杂问题也就解决。

**特点：**

- 将任意长度为N的数组排序所需的时间和NlgN成正比

**缺点：**

- 所需的额外空间和N成正比

归并排序的递归调用发生在处理数组之前，且在内循环的时候仍然在进行数组的移动。

```Java
public class MergeSort implements Sort {
    @Override
    public int[] sort(int[] arr) {
        mergeSort(arr, 0, arr.length - 1);
        return arr;
    }

    private void mergeSort(int[] arr, int left, int right) {
        if (left < right) {
            int mid = left + ((right - left) >> 1);
            // 注意这里递归调用是mid的值
            // 如果以left ～ mid-1 可能会stackoverflow
            mergeSort(arr, left, mid);
            mergeSort(arr, mid + 1, right);
            merge(arr, left, mid, right);
        }
    }

    private void merge(int[] arr, int left, int mid, int right) {
        int size = right - left + 1;
        // 创建一个size大小的数组存放归并后的数组
        int[] temp = new int[size];
        int i = left, j = mid + 1, index = 0;
        while (i <= mid && j <= right) {
            if (arr[i] < arr[j]) {
                temp[index++] = arr[i++];
            } else {
                temp[index++] = arr[j++];
            }
        }
        while (i <= mid) {
            temp[index++] = arr[i++];
        }
        while (j <= right) {
            temp[index++] = arr[j++];
        }
        System.arraycopy(temp, 0, arr, left, size);
    }

}

```

## 快速排序

> 快排是一种分治排序。
> 将一个数组分为两个子数组，将两部分独立排序。与归并排序是互补的，其归并是递归调用发生在处理整个数组之后，二快排则是之前。

**特点：**

- 原地排序 → 需要辅助栈

- 将任意长度为N的数组排序所需的时间和NlgN成正比

- 快排的内循环比大多数排序算法都要短 → 依赖于切分数组的效果 → 依赖于切分元素的值

**缺点：**

- 非常脆弱（不稳定）

快排的切分方法的内循环是使用一个递增的索引将数组元素与定值做比较，这种简洁的方式，比起归并和shell排序，它们仍需要在内循环中移动数组。相比起归并，

其次，快排的另一个优势在于它的比较次数较小。

**解决快排的优化方法：**

- 快排不稳定的原因：

  - 数组切分不平衡 → 如果第一次最小元素切分，第二次从第二小的元素进行切分，如此这般，每次调用只会移除一个元素。

  - 大量重复的元素

- 解决办法 → 数组随机排序：

  - 切换到插入排序
    对于小数组而言，快排比插入排序慢
    因为递归，快排中的sort（）方法会在小数组中也调用自己

  - 三取样切分
    使用子数组的一小部分元素的中位数来切分数组，切分更好但是会带来计算中位数的开销。

  - 墒最优排序
    如果数组中出现大量的重复元素，一般来说这时就无须进行下去，但是快排仍会继续进行数组切分。
    三向切分，维护一个lt，使得a[lo..lt-1]中的元素都小于v，维护一个rt，使得a[rt+1...a.len-1]中的所有元素都大于v。
    多了维护的开销。

```Java
class QuickSort {
    public int[] sortArray(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
        return nums;
    }


    private void quickSort(int[] nums, int l, int r) {
        if(l < r) {
            int pivot = randomPartition(nums, l, r);
            quickSort(nums, l, pivot - 1);
            quickSort(nums, pivot + 1, r);
        }
    }

    private int randomPartition(int[] nums, int l, int r) {
        int i = new Random().nextInt(r - l + 1) + l;
        swap(nums, r, i);
        return partition(nums, l, r);
    }

    private int partition(int[] nums, int l, int r) {
        // pivot -> nums[r]
        int i=l-1;
        for(int j=l; j<=r-1; j++) {
            if(nums[j] <= nums[r]) {
                swap(nums, j, ++i);
            }
        }
        swap(nums, i + 1, r);
        return i + 1;
    }


    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

## 堆排序

> 基于*二叉堆*数据结构的一种优先队列的实现，用数组保存元素并按照一定条件排序。实现高效的删除最大元素和插入最大元素的操作。

在二叉堆数组中，每个元素都要保证大于等于两个特定的元素。在一个堆中，位置k的结点的父结点的位置为[k/2]，而它的两个子结点的位置则分别为2k和2k+1。

用它实现排序的时候，需要进行堆化操作，使用一个面向最大元素的优先队列（最大堆-最大元素位于根），并重复删除最大元素，来实现堆排序。

1. 先将初始序列K[0:n]建成一个大顶堆, 那么此时第一个元素K1最大, 此堆为初始的无序区.  

2. 再将关键字最大的记录K1 (即堆顶, 第一个元素)和无序区的最后一个记录 Kn 交换, 由此得到新的无序区K[1:n−1]和有序区K[n], 且满足K[1:n−1].keys≤K[n].key  

3. 交换K1 和 Kn 后, 堆顶可能违反堆性质, 因此需将K[1..n−1]调整为堆. 然后重复步骤②, 直到无序区只有一个元素时停止.

- 节点k的左子节点：2*k

- 节点k的右子节点：2*k+1

- 父节点：floor(k/2)

```Java
public class HeapSort implements Sort {
    @Override
    public int[] sort(int[] arr) {
        // 1.构建大顶堆
        int n = arr.length;
        for (int i = n / 2; i >= 0; i--) {
            maxHeapify(arr, i, n - 1);
        }
        // 2.调整堆结构 + 交换堆顶与末尾元素 -> 从而达到排序的目的
        for (int j = n - 1; j > 0; j--) {
            // 堆顶的元素由于是最大的，与末尾元素进行交换
            // 使得arr[j]是所有元素中最大的数
            swap(arr, 0, j);
            // 删除最大元素 j
            maxHeapify(arr, 0, j);
        }
        return arr;
    }

    private static void buildHeap(int[] arr, int len) {
        for(int i=len/2; i>=0; i--) {
            maxHeapify(arr, i, len);
        }
    }

    private static void maxHeapify(int[] arr, int i, int len) {
        for(;(i << 1) < len;) {
            int left = i * 2, right = i * 2 + 1, largest = i;
            if(left < len && arr[largest] < arr[left]) {
                largest = left;
            }
            if(right < len && arr[largest] < arr[right]) {
                largest = right;
            }
            if(largest != i) {
                swap(arr, i, largest);
                i = largest;
            } else {
                break;
            }
        }
    }
}
```
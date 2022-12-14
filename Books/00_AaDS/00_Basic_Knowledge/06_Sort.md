---
title: 排序
---

# 排序 - Sort

## 如何分析一个排序算法？

学习排序算法，我们除了学习它的算法原理、代码实现之外，更重要的是要学会如何评价、分析一个排序算法。那分析一个排序算法，要从哪几个方面入手呢？

### 排序算法的执行效率

对于排序算法执行效率的分析，我们一般会从这几个方面来衡量：

#### 1. 最好情况、最坏情况、平均情况时间复杂度

我们在分析排序算法的时间复杂度时，要分别给出最好情况、最坏情况、平均情况下的时间复杂度。除此之外，你还要说出最好、最坏时间复杂度对应的要排序的原始数据是什么样的。

为什么要区分这三种时间复杂度呢？第一，有些排序算法会区分，为了好对比，所以我们最好都做一下区分。第二，对于要排序的数据，有的接近有序，有的完全无序。有序度不同的数据，对于排序的执行时间肯定是有影响的，我们要知道排序算法在不同数据下的性能表现。

#### 2. 时间复杂度的系数、常数 、低阶

我们知道，时间复杂度反应的是数据规模 n 很大的时候的一个增长趋势，所以它表示的时候会忽略系数、常数、低阶。但是实际的软件开发中，我们排序的可能是 10 个、100 个、1000 个这样规模很小的数据，所以，在对同一阶时间复杂度的排序算法性能对比的时候，我们就要把系数、常数、低阶也考虑进来。

#### 3. 比较次数和交换（或移动）次数

基于比较的排序算法的执行过程，会涉及两种操作，一种是元素比较大小，另一种是元素交换或移动。所以，如果我们在分析排序算法的执行效率的时候，应该把比较次数和交换（或移动）次数也考虑进去。

### 排序算法的内存消耗

我们前面讲过，算法的内存消耗可以通过空间复杂度来衡量，排序算法也不例外。不过，针对排序算法的空间复杂度，我们还引入了一个新的概念，**原地排序**（Sorted in place）。原地排序算法，就是特指空间复杂度是 O(1) 的排序算法。我们今天讲的三种排序算法，都是原地排序算法。

### 排序算法的稳定性

仅仅用执行效率和内存消耗来衡量排序算法的好坏是不够的。针对排序算法，我们还有一个重要的度量指标，**稳定性**。这个概念是说，如果待排序的序列中存在值相等的元素，经过排序之后，相等元素之间原有的先后顺序不变。

比如在实际项目中，排序时不可能都只有一个维度并且是整数。所以需要稳定性来说明。

假设在商场订单中进行排序，原始数据是以下单时间来进行排序的，所以这是你按照订单金额来排序时，就不能让相同金额的订单出现时间顺序紊乱的情况。

所以需要更加稳定的排序算法。

## 排序算法概述

### 排序算法分类

十种常见排序算法可以分为两大类：

- **比较类排序**：通过比较来决定元素间的相对次序，由于其时间复杂度不能突破 O(nlogn)，因此也称为非线性时间比较类排序。
- **非比较类排序**：不通过比较来决定元素间的相对次序，它可以突破基于比较排序的时间下界，以线性时间运行，因此也称为线性时间非比较类排序。

| 排序方法 | 时间复杂度（平均） | 时间复杂度（最坏） | 时间复杂度（最好）   | 空间复杂度           | 稳定性 |
| -------- | ------------------ | ------------------ | -------------------- | -------------------- | ------ |
| 插入排序 | O(n<sup>2</sup>)   | O(n<sup>2</sup>)   | O(n)                 | O(1)                 | 稳定   |
| 选择排序 | O(n<sup>2</sup>)   | O(n<sup>2</sup>)   | O(n<sup>2</sup>)     | O(1)                 | 不稳定 |
| 冒泡排序 | O(n<sup>2</sup>)   | O(n<sup>2</sup>)   | O(n)                 | O(1)                 | 稳定   |
| 希尔排序 | O(n<sup>1.3</sup>) | O(n<sup>2</sup>)   | O(n)                 | O(1)                 | 不稳定 |
| 归并排序 | O(nlogn)           | O(nlogn)           | O(nlog<sub>2</sub>n) | O(n)                 | 不稳定 |
| 快速排序 | O(nlogn)           | O(n<sup>2</sup>)   | O(nlog<sub>2</sub>n) | O(nlog<sub>2</sub>n) | 不稳定 |
| 堆排序   | O(nlogn)           | O(nlogn)           | O(nlog<sub>2</sub>n) | O(1)                 | 不稳定 |
| 计数排序 | O(n+k)             | O(n+k)             | O(n+k)               | O(n+k)               | 稳定   |
| 桶排序   | O(n+k)             | O(n<sup>2</sup>)   | O(n)                 | O(n+k)               | 稳定   |
| 基数排序 | O(n\*k)            | O(n\*k)            | O(n\*k)              | O(n+k)               | 稳定   |

![排序-思维导图](https://archive.static.spiritling.net/images/06_Sort-MindMapping.png)

_注：以下代码有部分来自于网络上，部分图片找不到网站可以设计，只能使用[博客园网站上的图片](https://www.cnblogs.com/onepixel/articles/7674659.html)_

### 1. 插入排序（Insertion Sort）

插入排序（Insertion-Sort）的算法描述是一种简单直观的排序算法。它的工作原理是通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。

#### 算法描述

一般来说，插入排序都采用 in-place 在数组上实现。具体算法描述如下：

- 从第一个元素开始，该元素可以认为已经被排序；
- 取出下一个元素，在已经排序的元素序列中从后向前扫描；
- 如果该元素（已排序）大于新元素，将该元素移到下一位置；
- 重复步骤 3，直到找到已排序的元素小于或者等于新元素的位置；
- 将新元素插入到该位置后；
- 重复步骤 2~5。

#### 动图演示

![排序-插入排序](https://archive.static.spiritling.net/images/06_Sort-InsertionSort.gif)

#### 代码展示

```js
function insertionSort(numberList) {
  const len = numberList.length;
  let preIndex, current;
  for (let i = 1; i < len; i++) {
    preIndex = i - 1;
    current = numberList[i];
    while (preIndex >= 0 && numberList[preIndex] > current) {
      numberList[preIndex + 1] = numberList[preIndex];
      preIndex--;
    }
    numberList[preIndex + 1] = current;
  }
  return numberList;
}
```

#### 算法分析

插入排序在实现上，通常采用 in-place 排序（即只需用到 O(1)的额外空间的排序），因而在从后向前扫描过程中，需要反复把已排序元素逐步向后挪位，为最新元素提供插入空间。

### 2. 选择排序（Selection Sort）

选择排序(Selection-sort)是一种简单直观的排序算法。它的工作原理：首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。

#### 算法描述

n 个记录的直接选择排序可经过 n-1 趟直接选择排序得到有序结果。具体算法描述如下：

- 初始状态：无序区为 R[1..n]，有序区为空；
- 第 i 趟排序(i=1,2,3…n-1)开始时，当前有序区和无序区分别为 R[1..i-1]和 R(i..n）。该趟排序从当前无序区中-选出关键字最小的记录 R[k]，将它与无序区的第 1 个记录 R 交换，使 R[1..i]和 R[i+1..n)分别变为记录个数增加 1 个的新有序区和记录个数减少 1 个的新无序区；
- n-1 趟结束，数组有序化了。

#### 动图演示

![排序-选择排序](https://archive.static.spiritling.net/images/06_Sort-SelectionSort.gif)

#### 代码实现

```js
function selectionSort(arr) {
  var len = arr.length;
  var minIndex, temp;
  for (var i = 0; i < len - 1; i++) {
    minIndex = i;
    for (var j = i + 1; j < len; j++) {
      if (arr[j] < arr[minIndex]) {
        // 寻找最小的数
        minIndex = j; // 将最小数的索引保存
      }
    }
    temp = arr[i];
    arr[i] = arr[minIndex];
    arr[minIndex] = temp;
  }
  return arr;
}
```

#### 算法分析

表现最稳定的排序算法之一，因为无论什么数据进去都是 O(n2)的时间复杂度，所以用到它的时候，数据规模越小越好。唯一的好处可能就是不占用额外的内存空间了吧。理论上讲，选择排序可能也是平时排序一般人想到的最多的排序方法了吧。

### 3. 冒泡排序（Bubble Sort）

冒泡排序是一种简单的排序算法。它重复地走访过要排序的数列，一次比较两个元素，如果它们的顺序错误就把它们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端。

#### 算法描述

1. 比较相邻的元素。如果第一个比第二个大，就交换它们两个；

2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这样在最后的元素应该会是最大的数；

3. 针对所有的元素重复以上的步骤，除了最后一个；

4. 重复步骤 1~3，直到排序完成

#### 动图演示

![排序-冒泡排序](https://archive.static.spiritling.net/images/06_Sort-BubbleSort.gif)

#### 代码实现

```js
function bubbleSort(numberList) {
  const len = numberList.length;
  for (let i = 0; i < len - 1; i++) {
    for (let j = 0; j < len - 1 - i; j++) {
      if (numberList[j] > numberList[j + 1]) {
        // 相邻元素两两对比
        const tempSwapSpace = numberList[j + 1]; // 元素交换
        numberList[j + 1] = numberList[j];
        numberList[j] = tempSwapSpace;
      }
    }
  }
  return numberList;
}
```

### 4、希尔排序（Shell Sort）

1959 年 Shell 发明，第一个突破 O(n2)的排序算法，是简单插入排序的改进版。它与插入排序的不同之处在于，它会优先比较距离较远的元素。希尔排序又叫**缩小增量排序**。

#### 算法描述

先将整个待排序的记录序列分割成为若干子序列分别进行直接插入排序，具体算法描述：

- 选择一个增量序列 t1，t2，…，tk，其中 ti>tj，tk=1；
- 按增量序列个数 k，对序列进行 k 趟排序；
- 每趟排序，根据对应的增量 ti，将待排序列分割成若干长度为 m 的子序列，分别对各子表进行直接插入排序。仅增量因子为 1 时，整个序列作为一个表来处理，表长度即为整个序列的长度。

#### 动图演示

![排序-希尔排序](https://archive.static.spiritling.net/images/06_Sort-ShellSort.gif)

#### 代码实现

```js
// 修改于 2019-03-06
function shellSort(arr) {
    varl en = arr.length;
    for(var gap = Math.floor(len / 2); gap > 0; gap = Math.floor(gap / 2)) {
        // 注意：这里和动图演示的不一样，动图是分组执行，实际操作是多个分组交替执行
        for(var i = gap; i < len; i++) {
            var j = i;
            var current = arr[i];
            while(j - gap >= 0 && current < arr[j - gap]) {
                 arr[j] = arr[j - gap];
                 j = j - gap;
            }
            arr[j] = current;
        }
    }
    return arr;
}
```

#### 算法分析

希尔排序的核心在于间隔序列的设定。既可以提前设定好间隔序列，也可以动态的定义间隔序列。动态定义间隔序列的算法是《算法（第 4 版）》的合著者 Robert Sedgewick 提出的。

### 5. 归并排序（Merge Sort）

归并排序是建立在归并操作上的一种有效的排序算法。该算法是采用分治法（Divide and Conquer）的一个非常典型的应用。将已有序的子序列合并，得到完全有序的序列；即先使每个子序列有序，再使子序列段间有序。若将两个有序表合并成一个有序表，称为 2-路归并。

#### 算法描述

- 把长度为 n 的输入序列分成两个长度为 n/2 的子序列；
- 对这两个子序列分别采用归并排序；
- 将两个排序好的子序列合并成一个最终的排序序列。

#### 动图演示

![排序-归并排序](https://archive.static.spiritling.net/images/06_Sort-MergeSort.gif)

#### 代码实现

```js
function mergeSort(arr) {
  var len = arr.length;
  if (len < 2) {
    return arr;
  }
  var middle = Math.floor(len / 2),
    left = arr.slice(0, middle),
    right = arr.slice(middle);
  return merge(mergeSort(left), mergeSort(right));
}

function merge(left, right) {
  var result = [];

  while (left.length > 0 && right.length > 0) {
    if (left[0] <= right[0]) {
      result.push(left.shift());
    } else {
      result.push(right.shift());
    }
  }

  while (left.length) result.push(left.shift());

  while (right.length) result.push(right.shift());

  return result;
}
```

#### 算法分析

归并排序是一种稳定的排序方法。和选择排序一样，归并排序的性能不受输入数据的影响，但表现比选择排序好的多，因为始终都是 O(nlogn）的时间复杂度。代价是需要额外的内存空间。

### 6. 快速排序（Quick Sort）

快速排序的基本思想：通过一趟排序将待排记录分隔成独立的两部分，其中一部分记录的关键字均比另一部分的关键字小，则可分别对这两部分记录继续进行排序，以达到整个序列有序。

#### 算法描述

快速排序使用分治法来把一个串（list）分为两个子串（sub-lists）。具体算法描述如下：

- 从数列中挑出一个元素，称为 “基准”（pivot）；
- 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这个称为分区（partition）操作；
- 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序。

#### 动图演示

![排序-快速排序](https://archive.static.spiritling.net/images/06_Sort-QuickSort.gif)

#### 代码实现

```js
function quickSort(arr, left, right) {
  var len = arr.length,
    partitionIndex,
    left = typeofleft != 'number' ? 0 : left,
    right = typeofright != 'number' ? len - 1 : right;

  if (left < right) {
    partitionIndex = partition(arr, left, right);
    quickSort(arr, left, partitionIndex - 1);
    quickSort(arr, partitionIndex + 1, right);
  }
  return arr;
}

function partition(arr, left, right) {
  // 分区操作
  var pivot = left, // 设定基准值（pivot）
    index = pivot + 1;
  for (var i = index; i <= right; i++) {
    if (arr[i] < arr[pivot]) {
      swap(arr, i, index);
      index++;
    }
  }
  swap(arr, pivot, index - 1);
  return index - 1;
}

function swap(arr, i, j) {
  var temp = arr[i];
  arr[i] = arr[j];
  arr[j] = temp;
}
```

### 7. 堆排序（Heap Sort）

堆排序（Heapsort）是指利用堆这种数据结构所设计的一种排序算法。堆积是一个近似完全二叉树的结构，并同时满足堆积的性质：即子结点的键值或索引总是小于（或者大于）它的父节点。

#### 算法描述

- 将初始待排序关键字序列(R1,R2….Rn)构建成大顶堆，此堆为初始的无序区；
- 将堆顶元素 R[1]与最后一个元素 R[n]交换，此时得到新的无序区(R1,R2,……Rn-1)和新的有序区(Rn),且满足 R[1,2…n-1]<=R[n]；
- 由于交换后新的堆顶 R[1]可能违反堆的性质，因此需要对当前无序区(R1,R2,……Rn-1)调整为新堆，然后再次将 R[1]与无序区最后一个元素交换，得到新的无序区(R1,R2….Rn-2)和新的有序区(Rn-1,Rn)。不断重复此过程直到有序区的元素个数为 n-1，则整个排序过程完成。

#### 动图演示

![排序-堆排序](https://archive.static.spiritling.net/images/06_Sort-HeapSort.gif)

#### 代码实现

```js
var len; // 因为声明的多个函数都需要数据长度，所以把len设置成为全局变量

function buildMaxHeap(arr) {
  // 建立大顶堆
  len = arr.length;
  for (var i = Math.floor(len / 2); i >= 0; i--) {
    heapify(arr, i);
  }
}

function heapify(arr, i) {
  // 堆调整
  var left = 2 * i + 1,
    right = 2 * i + 2,
    largest = i;

  if (left < len && arr[left] > arr[largest]) {
    largest = left;
  }

  if (right < len && arr[right] > arr[largest]) {
    largest = right;
  }

  if (largest != i) {
    swap(arr, i, largest);
    heapify(arr, largest);
  }
}

function swap(arr, i, j) {
  var temp = arr[i];
  arr[i] = arr[j];
  arr[j] = temp;
}

function heapSort(arr) {
  buildMaxHeap(arr);

  for (var i = arr.length - 1; i > 0; i--) {
    swap(arr, 0, i);
    len--;
    heapify(arr, 0);
  }
  return arr;
}
```

### 8. 计数排序（Counting Sort）

计数排序不是基于比较的排序算法，其核心在于将输入的数据值转化为键存储在额外开辟的数组空间中。 作为一种线性时间复杂度的排序，计数排序要求输入的数据必须是有确定范围的整数。

#### 算法描述

- 找出待排序的数组中最大和最小的元素；
- 统计数组中每个值为 i 的元素出现的次数，存入数组 C 的第 i 项；
- 对所有的计数累加（从 C 中的第一个元素开始，每一项和前一项相加）；
- 反向填充目标数组：将每个元素 i 放在新数组的第 C(i)项，每放一个元素就将 C(i)减去 1。

#### 动图演示

![排序-计数排序](https://archive.static.spiritling.net/images/06_Sort-CountingSort.gif)

#### 代码实现

```js
function countingSort(arr, maxValue) {
  var bucket = newArray(maxValue + 1),
    sortedIndex = 0;
  (arrLen = arr.length), (bucketLen = maxValue + 1);

  for (var i = 0; i < arrLen; i++) {
    if (!bucket[arr[i]]) {
      bucket[arr[i]] = 0;
    }
    bucket[arr[i]]++;
  }

  for (var j = 0; j < bucketLen; j++) {
    while (bucket[j] > 0) {
      arr[sortedIndex++] = j;
      bucket[j]--;
    }
  }

  return arr;
}
```

#### 算法分析

计数排序是一个稳定的排序算法。当输入的元素是 n 个 0 到 k 之间的整数时，时间复杂度是 O(n+k)，空间复杂度也是 O(n+k)，其排序速度快于任何比较排序算法。当 k 不是很大并且序列比较集中时，计数排序是一个很有效的排序算法。

### 9. 桶排序（Bucket Sort）

桶排序是计数排序的升级版。它利用了函数的映射关系，高效与否的关键就在于这个映射函数的确定。桶排序 (Bucket sort)的工作的原理：假设输入数据服从均匀分布，将数据分到有限数量的桶里，每个桶再分别排序（有可能再使用别的排序算法或是以递归方式继续使用桶排序进行排）。

#### 算法描述

- 设置一个定量的数组当作空桶；
- 遍历输入数据，并且把数据一个一个放到对应的桶里去；
- 对每个不是空的桶进行排序；
- 从不是空的桶里把排好序的数据拼接起来。

#### 图片演示

![排序-桶排序](https://archive.static.spiritling.net/images/06_Sort-BucketSort.png)

#### 代码实现

```js
function bucketSort(arr, bucketSize) {
    if(arr.length === 0) {
      return arr;
    }

    var i;
    var minValue = arr[0];
    var maxValue = arr[0];
    for(i = 1; i < arr.length; i++) {
      if(arr[i] < minValue) {
          minValue = arr[i];                // 输入数据的最小值
      } elseif(arr[i] > maxValue) {
          maxValue = arr[i];                // 输入数据的最大值
      }
    }

    // 桶的初始化
    var DEFAULT_BUCKET_SIZE = 5;            // 设置桶的默认数量为5
    bucketSize = bucketSize || DEFAULT_BUCKET_SIZE;
    var bucketCount = Math.floor((maxValue - minValue) / bucketSize) + 1;
    var buckets = newArray(bucketCount);
    for(i = 0; i < buckets.length; i++) {
        buckets[i] = [];
    }

    // 利用映射函数将数据分配到各个桶中
    for(i = 0; i < arr.length; i++) {
        buckets[Math.floor((arr[i] - minValue) / bucketSize)].push(arr[i]);
    }

    arr.length = 0;
    for(i = 0; i < buckets.length; i++) {
        insertionSort(buckets[i]);                      // 对每个桶进行排序，这里使用了插入排序
        for(var j = 0; j < buckets[i].length; j++) {
            arr.push(buckets[i][j]);
        }
    }

    return arr;
}
```

#### 算法分析

桶排序最好情况下使用线性时间 O(n)，桶排序的时间复杂度，取决与对各个桶之间数据进行排序的时间复杂度，因为其它部分的时间复杂度都为 O(n)。很显然，桶划分的越小，各个桶之间的数据越少，排序所用的时间也会越少。但相应的空间消耗就会增大。

### 10. 基数排序（Radix Sort）

基数排序是按照低位先排序，然后收集；再按照高位排序，然后再收集；依次类推，直到最高位。有时候有些属性是有优先级顺序的，先按低优先级排序，再按高优先级排序。最后的次序就是高优先级高的在前，高优先级相同的低优先级高的在前。

#### 算法描述

- 取得数组中的最大数，并取得位数；
- arr 为原始数组，从最低位开始取每个位组成 radix 数组；
- 对 radix 进行计数排序（利用计数排序适用于小范围数的特点）；

#### 动图演示

![排序-基数排序RadixSort动图](https://archive.static.spiritling.net/images/06_Sort-RadixSort.gif)

#### 代码实现

```js
var counter = [];
function radixSort(arr, maxDigit) {
  var mod = 10;
  var dev = 1;
  for (var i = 0; i < maxDigit; i++, dev *= 10, mod *= 10) {
    for (var j = 0; j < arr.length; j++) {
      var bucket = parseInt((arr[j] % mod) / dev);
      if (counter[bucket] == null) {
        counter[bucket] = [];
      }
      counter[bucket].push(arr[j]);
    }
    var pos = 0;
    for (var j = 0; j < counter.length; j++) {
      var value = null;
      if (counter[j] != null) {
        while ((value = counter[j].shift()) != null) {
          arr[pos++] = value;
        }
      }
    }
  }
  return arr;
}
```

#### 算法分析

基数排序基于分别排序，分别收集，所以是稳定的。但基数排序的性能比桶排序要略差，每一次关键字的桶分配都需要 O(n)的时间复杂度，而且分配之后得到新的关键字序列又需要 O(n)的时间复杂度。假如待排数据可以分为 d 个关键字，则基数排序的时间复杂度将是 O(d\*2n) ，当然 d 要远远小于 n，因此基本上还是线性级别的。

基数排序的空间复杂度为 O(n+k)，其中 k 为桶的数量。一般来说 n>>k，因此额外空间需要大概 n 个左右。

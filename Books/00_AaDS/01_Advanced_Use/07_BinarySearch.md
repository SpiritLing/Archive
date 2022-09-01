---
title: 二分查找
---

# 二分查找 - BinarrySearch

二分查找算法是一种针对有序数据集合的查找算法，也叫折半查找算法。

二分查找的思想非常简单，但是看似非常简单的东西往往越难掌握好，想要灵活运用就更加困难。

**二分查找针对的是一个有序的数据集合，查找思想有点类似分治思想。每次都通过跟区间的中间元素对比，将待查找的区间缩小为之前的一半，直到找到要查找的元素，或者区间被缩小为 0。**

## O(logn) 惊人的查找速度

因为 logn 是一个非常“恐怖”的数量级，即便 n 非常非常大，对应的 logn 也很小。比如 n 等于 2 的 32 次方，这个数很大了吧？大约是 42 亿。也就是说，如果我们在 42 亿个数据中用二分查找一个数据，最多需要比较 32 次。

我们前面讲过，用大 O 标记法表示时间复杂度的时候，会省略掉常数、系数和低阶。对于常量级时间复杂度的算法来说，O(1) 有可能表示的是一个非常大的常量值，比如 O(1000)、O(10000)。所以，常量级时间复杂度的算法有时候可能还没有 O(logn) 的算法执行效率高。

反过来，对数对应的就是指数。有一个非常著名的“阿基米德与国王下棋的故事”，你可以自行搜索一下，感受一下指数的“恐怖”。这也是为什么我们说，指数时间复杂度的算法在大规模数据面前是无效的。

## 二分查找的递归与非递归实现

**最简单的情况**就是**有序数组中不存在重复元素**，我们在其中用二分查找值等于给定值的数据。我用 Java 代码实现了一个最简单的二分查找算法。

```typescript
const noRepeat = [1, 3, 4, 5, 6, 7, 8, 10, 11, 18, 20];
function BinarySearch(arr: number[], value: number) {
  let low = 0;
  let high = arr.length - 1;
  while (low <= high) {
    const half = (high - low) >> 1;
    const binaryValue = low + half;
    if (arr[binaryValue] === value) {
      return binaryValue;
    } else if (arr[binaryValue] < value) {
      low = binaryValue + 1;
    } else {
      high = binaryValue - 1;
    }
  }
  return -1;
}
console.log(BinarySearch(noRepeat, 8));
```

这个代码我稍微解释一下，low、high、mid 都是指数组下标，其中 low 和 high 表示当前查找的区间范围，初始 `low=0`， `high=arr.length - 1`。mid 表示[low, high]的中间位置。我们通过对比 a[mid]与 value 的大小，来更新接下来要查找的区间范围，直到找到或者区间缩小为 0，就退出。如果你有一些编程基础，看懂这些应该不成问题。现在，我就着重强调一下容易出错的 3 个地方。

![二分查找-非递归实现](https://archive.static.spiritling.net/images/07_BinarySearch-NonRecursiveImplementation.png)

#### 1. 循环退出条件

注意是 low<=high，而不是 low<high。这一点在递归形式中看起来更好理解。

#### 2.mid 的取值

实际上，mid=(low+high)/2 这种写法是有问题的。因为如果 low 和 high 比较大的话，两者之和就有可能会溢出。改进的方法是将 mid 的计算方式写成 low+(high-low)/2。更进一步，如果要将性能优化到极致的话，我们可以将这里的除以 2 操作转化成位运算 low+((high-low)>>1)。因为相比除法运算来说，计算机处理位运算要快得多。

#### 3.low 和 high 的更新

low=mid+1，high=mid-1。注意这里的 +1 和 -1，如果直接写成 low=mid 或者 high=mid，就可能会发生死循环。比如，当 high=3，low=3 时，如果 a[3]不等于 value，就会导致一直循环不退出。

如果你留意我刚讲的这三点，我想一个简单的二分查找你已经可以实现了。实际上，二分查找除了用循环来实现，还可以用递归来实现，过程也非常简单。

我用 Java 语言实现了一下这个过程，正好你可以借此机会回顾一下写递归代码的技巧。

```typescript
const noRepeat = [1, 3, 4, 5, 6, 7, 8, 10, 11, 18, 20];
function RecursionBinarySearch(
  arr: number[],
  value: number,
  low: number = 0,
  high: number = arr.length,
): number {
  if (low === high) {
    // 相同时，也需要进行值判断。
    // while 循环中条件含有相等
    return arr[low] == value ? low : -1;
  }
  const half = (high - low) >> 1;
  const binaryValue = low + half;
  if (arr[binaryValue] === value) {
    return binaryValue;
  } else if (arr[binaryValue] < value) {
    low = binaryValue + 1;
    return RecursionBinarySearch(arr, value, low, high);
  } else {
    high = binaryValue - 1;
    return RecursionBinarySearch(arr, value, low, high);
  }
}
console.log(RecursionBinarySearch(noRepeat, 10));
```

## 二分查找应用场景的局限性

前面我们分析过，二分查找的时间复杂度是 O(logn)，查找数据的效率非常高。不过，并不是什么情况下都可以用二分查找，它的应用场景是有很大局限性的。那什么情况下适合用二分查找，什么情况下不适合呢？

### 首先，二分查找依赖的是顺序表结构，简单点说就是数组。

那二分查找能否依赖其他数据结构呢？比如链表。答案是不可以的，主要原因是二分查找算法需要按照下标随机访问元素。我们在数组和链表那两节讲过，数组按照下标随机访问数据的时间复杂度是 O(1)，而链表随机访问的时间复杂度是 O(n)。所以，如果数据使用链表存储，二分查找的时间复杂就会变得很高。

二分查找只能用在数据是通过顺序表来存储的数据结构上。如果你的数据是通过其他数据结构存储的，则无法应用二分查找。

## 二分查找应用实例

上面例子中使用的二分法是最简单的二分法，而二分法比较难的地方在于它的变形。

所以下面使用几个典型的二分法变形来讲解。为了只专注于二分法上面，所有数据都是从小到大排序为前提 。

- 查找第一个值等于给定值的元素
- 查找最后一个值等于给定值的元素
- 查找第一个大于给定值的元素
- 查找最后一个小于等于给定值的元素

### 查找第一个值等于给定值的元素

比如现在有一个有序数组，它的值为：`1,3,4,5,6,8,8,8,11,18` 。其中 `arr[5],arr[6],arr[7]` 的值都等于 8，是重复数据，现在我们需要查找第一个等于 8 的元素，也就是下标为 5 的值。在

![二分查找-第一个值等于给定值](https://archive.static.spiritling.net/images/07_BinarySearch-FirstValueEqualGivenValue.png)

```typescript
const arr = [1, 3, 4, 5, 6, 8, 8, 8, 11, 18];

function FindFirstMinBinarySearch(arr: number[], value: number) {
  let low = 0;
  let high = arr.length - 1;
  while (low <= high) {
    let half = (high - low) >> 1;
    let mid = low + half;
    if (arr[mid] > value) {
      high = mid - 1;
    } else if (arr[mid] < value) {
      low = mid + 1;
    } else {
      if (mid === 0 || arr[mid - 1] !== value) {
        return mid;
      } else {
        high = mid - 1;
      }
    }
  }
  return -1;
}
console.log(FindFirstMinBinarySearch(arr, 8));
```

上面代码中，主要在于最后一个 `else` 中，通过判断，如果 `mid` 等于 0 时，则就是数组第一个，说明查找第一个值等于给定值的元素。如果不等于 0 时，则判断当前值的前一位是否和所查找的值一致，如果相等，则说明现在不是第一个的给定值，所以需要继续二分查找第一个给定值。

一般来说，二分法比较难写是因为许多人追求非常完美和简洁的写法，比如下面代码：

```typescript
const arr = [1, 3, 4, 5, 6, 8, 8, 8, 11, 18];

function FindFirstBinarySearch(arr: number[], value: number) {
  let low = 0;
  let high = arr.length - 1;
  while (low <= high) {
    let half = (high - low) >> 1;
    let mid = low + half;
    if (arr[mid] >= value) {
      high = mid - 1;
    } else if (arr[mid] < value) {
      low = mid + 1;
    }
  }

  if (low < arr.length - 1 && arr[low] == value) {
    return low;
  } else {
    return -1;
  }
}
console.log(FindFirstBinarySearch(arr, 8));
```

看完上面这个代码，是不是感觉很不好理解？虽然它比较简洁，但是如果死记硬背的话，过不了几天就会忘记，并且再次自己写，会有 90%写错。但是我们在之前写的那种方式就比较好理解和记忆。对于开发来说，代码易读懂、没 BUG，其实更重要。

### 查找最后一个值等于给定值的元素

比如现在有一个有序数组，它的值为：`1,3,4,5,6,8,8,8,8,11,18` 。其中 `arr[5],arr[6],arr[7],arr[8]` 的值都等于 8，是重复数据，现在我们需要查找最后一个等于 8 的元素，也就是下标为 8 的值。但是在二分法直接查找中会查找到下标为 5 的，但是下标为 5 的并不是最后一个所查找的值。

![二分查找-最一个值等于给定值](https://archive.static.spiritling.net/images/07_BinarySearch-LastValueEqualGivenValue.png)

```typescript
const arr = [1, 3, 4, 5, 6, 8, 8, 8, 8, 11, 18];

function FindLastBinarySearch(arr: number[], value: number) {
  let low = 0;
  let high = arr.length - 1;
  while (low <= high) {
    let half = (high - low) >> 1;
    let mid = low + half;
    if (arr[mid] > value) {
      high = mid - 1;
    } else if (arr[mid] < value) {
      low = mid + 1;
    } else {
      if (mid === 0 || arr[mid + 1] !== value) {
        return mid;
      } else {
        low = mid + 1;
      }
    }
  }
  return -1;
}
console.log(FindLastBinarySearch(arr, 8));
```

上面代码其实和查找第一个值等于给定值的元素相似，只是换个方向而已，所以会了第一个例子，那么第二个例子也就会了。

### 查找第一个大于等于给定值的元素

后面两个例子从网上找的。

在有序数组中，查找第一个大于等于给定值的元素。比如，数组中存储的这样一个序列：3，4，6，7，10。如果查找第一个大于等于 5 的元素，那就是 6。

```c

public int bsearch(int[] a, int n, int value) {
  int low = 0;
  int high = n - 1;
  while (low <= high) {
    int mid =  low + ((high - low) >> 1);
    if (a[mid] >= value) {
      if ((mid == 0) || (a[mid - 1] < value)) return mid;
      else high = mid - 1;
    } else {
      low = mid + 1;
    }
  }
  return -1;
}
```

如果 a[mid]小于要查找的值 value，那要查找的值肯定在[mid+1, high]之间，所以，我们更新 low=mid+1。

对于 a[mid]大于等于给定值 value 的情况，我们要先看下这个 a[mid]是不是我们要找的第一个值大于等于给定值的元素。如果 a[mid]前面已经没有元素，或者前面一个元素小于要查找的值 value，那 a[mid]就是我们要找的元素。这段逻辑对应的代码是第 7 行。

如果 a[mid-1]也大于等于要查找的值 value，那说明要查找的元素在[low, mid-1]之间，所以，我们将 high 更新为 mid-1。

### 查找最后一个小于等于给定值的元素

查找最后一个小于等于给定值的元素。比如，数组中存储了这样一组数据：3，5，6，8，9，10。最后一个小于等于 7 的元素就是 6。是不是有点类似上面那一种？实际上，实现思路也是一样的。

```c

public int bsearch7(int[] a, int n, int value) {
  int low = 0;
  int high = n - 1;
  while (low <= high) {
    int mid =  low + ((high - low) >> 1);
    if (a[mid] > value) {
      high = mid - 1;
    } else {
      if ((mid == n - 1) || (a[mid + 1] > value)) return mid;
      else low = mid + 1;
    }
  }
  return -1;
}
```

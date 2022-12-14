---
title: 概述
---

# 概述

> ❤ AaDS（Algorithms and Data Structures 算法与数据结构）

## 什么是数据结构？

**官方解释**：

数据结构是一门研究非数值计算的程序设计问题中的操作对象，以及他们之间的关系和操作等相关问题的学科。

**普通解释**：

数据结构就是把数据元素按照一定的关系组织起来的集合，**用来组织和存储数据**

## 数据结构分类

传统上，我们可以把数据结构分为逻辑结构和物理结构两大类。

#### 逻辑结构分类

逻辑结构是从具体问题中抽象出来的模型，是抽象意义上的结构，按照对象中数据元素之间的相互关系分类。

a.集合结构：集合结构中数据元素除了属于同一个集合外，他们之间没有任何其他的关系。

![概述-集合结构](https://archive.static.spiritling.net/images/00_Overview-CollectionStructure.png)

b. 线性结构：线性结构中的数据元素之间存在一对一的关系

![概述-线性结构](https://archive.static.spiritling.net/images/00_Overview-LinearStructure.png)

c. 树形结构：树形结构中的数据元素之间存在一对多的层级关系

![概述-树形结构](https://archive.static.spiritling.net/images/00_Overview-TreeStructure.png)

d.图形结构：图形结构的数据元素是多对多的关系

![概述-图形结构](https://archive.static.spiritling.net/images/00_Overview-GraphicStructure.png)

#### 物理结构

逻辑结构在计算机中真正的表示方式（又称为映像）称为物理结构，也可以叫做存储结构。常见的物理结构有顺序存储结构、链式存储结构。

**顺序存储结构：**

把数据元素放到地址连续的存储单元里面，其数据间的逻辑关系和物理关系是一致的 ，比如我们常用的数组就是顺序存储结构。

![概述-顺序存储结构](https://archive.static.spiritling.net/images/00_Overview-SequentialStorageStructure.png)

顺序存储结构存在一定的弊端，就像生活中排队也有人插队也可能有特殊情况离开，这个时候整个结构都处于变化中，此时就需要链式存储结构

**链式存储结构**

把数据元素放在任意的存储单元里面，这组存储单元可以是连续的也可以是不连续的。此时，数据元素之间并不能反映元素间的逻辑关系，因此在链式存储结构中引入一个指针存放数据元素的地址，这样可以通过地址找到相关数据元素的位置。

![概述-链式存储结构](https://archive.static.spiritling.net/images/00_Overview-ChainStorageStructure.png)

## 什么是算法？🤔

**官方解释：**

算法是指解题方案的准确而完整的描述，是一系列解决问题的清晰指令，算法代表着用系统的方法解决问题的策略机制，也就是说，能够对一定规范的输入，在有限时间内获得所要求的输出。

**普通解释：**

根据一定的条件，对一些数据进行计算，得到需要的结果。

### 算法初体验

在生活或学习中，我们如果遇到一个问题，常常解决方案不是唯一的。

例如从某地到某地去，可以乘坐各种交通工具，而不同的方式会带来不同的时间和金钱成本。比如坐飞机用的时间最少，但是费用最高，步行费用虽然最低，但是时间成本最大。

再比如说，做同一道数学题，可能班级里面的同学的方法都不一样，但是最终解却是一样的。

在程序中，可以用不同算法解决相同的问题，而不同的算法的成本也是不相同的。总体上，一个优秀的算法追求一下两个目标：

1. **耗费最少的时间完成**
2. **占用最少的内存完成**

#### 需求

**计算 1-1000 的和。**

##### 第一种解法

```js
var sum = 0;
var n = 1000;
for (let i = 1; i <= n; i++) {
  sum += i;
}
console.log(sum);
```

这种解法要完成题解，需要以下几个动作：

1. 定义两个变量
2. 执行 1000 次加法运算
3. 打印到控制台

##### 第二种解法

```js
var sum = 0;
var n = 1000;
// 高斯公式求和
sum = ((n + 1) * n) / 2;
console.log(sum);
```

这种解法要完成题解，需要以下几个动作：

1. 定义两个变量
2. 执行 1 次加法运算，1 次乘法运算，1 次除法运算
3. 打印到控制台

很明显，第二种方式更加简单，花费的时间最少。

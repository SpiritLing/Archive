---
title: 散列表
---

# 散列表 - Hash Table

散列表（Hash Table）也叫哈希表。

我的一系列文章都是通过 [Typora](https://typora.io/) 编辑器来编写的，因为它支持 Markdown 语法格式，而且不花哨，只有最基本的 Markdown 语法支持，不必担心以后的迁移问题。没有各种扩展语法。

在这款编辑器中有个拼写检查功能，一旦我们输入错误的单词或者指定语言的错误词语，它会用标红的方式提示“拼写错误”。虽然这个功能很小，但是很实用，在微信公众号编辑器和 word 中都会有这种拼写检查。但是你有没有想过，这个功能如何实现的呢？

这就和本次章节**散列表（Hash Table）**有关系。

## 散列思想

散列表（Hash Table）也叫哈希表。哈希一般在实际业务学习和使用时都会接触到，比如我们给用户密码加上盐后进行哈希转换生成一个不可逆的加密字符串。

**散列表用的是数组支持按照下标随机访问数据的特性，所以散列表其实就是数组的一种扩展，由数组演化而来。可以说，如果没有数组，就没有散列表。**

散列表是是根据键（Key）而直接访问在内存储存位置的数据结构。也就是说，它通过计算一个关于键值的函数，将所需查询的数据映射到表中一个位置来访问记录，这加快了查找速度。这个映射函数称做[散列函数](https://zh.wikipedia.org/wiki/散列函数)，存放记录的数组称做**散列表**。

![散列表-散列表](https://archive.static.spiritling.net/images/09_HashTable-HashTable.png)

一个通俗的例子是，为了查找电话簿中某人的号码，可以创建一个按照人名首字母顺序排列的表（即建议人名 `x` 到首字母 `F(x)` 的一个函数关系），在首字母为 W 的表中查找“王”姓的电话号码，显然比直接查找就要快得多。这里使用人名作为关键字，“取首字母”是这个例子中散列函数的函数法则 `F()` ，存放首字母的表对应散列表。关键字和函数法则理论上可以任意确定。

事实上，散列表用的就是数组支持按照下标随机访问的时候，时间复杂度是 O(1) 的特性。通过散列函数把元素的键值映射为下标，然后将数据存储在数组中对应下标的位置。当我们按照键值查询元素时，用同样的散列函数，将键值转化数组下标，从对应的数组下标的位置取数据。

## 散列函数

对于散列函数来说，需要满足下面基本要求：

- 散列函数计算得到的散列值是一个非负整数
- 如果 key1 = key2 ，那么 hash(key1) = hash(key2)
- 如果 key1 != key2 ，那么 hash(key1) != hash(key2)

但是当数据规模非常大时，也会出现 key1 != key2 ，而 hash(key1) = hash(key2) 情况。这种情况就被成为 **冲突（Collision）**。具有相同函数值的关键字对该散列函数来说称做**同义词**。在真实的情况下，要想找到一个不同的 key 对应的散列值都不一样的散列函数，几乎是不可能的。即便像业界著名的 MD5、SHA、CRC 等哈希算法，也无法完全避免这种**散列冲突**。而且，因为数组的存储空间有限，也会加大散列冲突的概率。

所以我们几乎无法找到一个完美的无冲突的散列函数，即便能找到，付出的时间成本、计算成本也是很大的，所以针对散列冲突问题，我们需要通过其他途径来解决。

## 散列冲突

再好的散列函数也无法避免散列冲突。那么如何解决掉散列冲突问题呢？我们常用的散列冲突解决方法有两类

- 开放定址法（也可以叫开放寻址法，open addressing）
- 单独链表法（chaining）

### 开放定址法

开放定址法的核心思想是，如果出现散列冲突，我们就需要重写探测一个空闲位置，将其插入。

#### 线行探查法

线行探查法是开放定址法中最简单的冲突处理方法，它从发生冲突的单元起，依次判断下一个单元是否为空，当达到最后一个单元时，再从表首依次判断。直到碰到空闲的单元或者探查完全部单元为止。

![散列表-线性探查法](https://archive.static.spiritling.net/images/09_HashTable-LinearProbing.png)

比如上图所示，红色区块表示已经存在数据，当存放 x 时，发现 x 经过 Hash 计算后，被散列标记到下标为 7 的位置。但是这个位置已经有数据了，所以就产生了冲突。于是我们顺着顺序地往后一个一个找，看看有没有空闲位置，遍历到尾部也没有找到，便从表头开始找起，直到找到空闲位置 2，于是将其插入到这个位置。

在散列表中查找元素的过程有点儿类似插入过程。我们通过散列函数求出要查找元素的键值对应的散列值，然后比较数组中下标为散列值的元素和要查找的元素。如果相等，则说明就是我们要找的元素；否则就顺序往后依次查找。如果遍历到数组中的空闲位置，还没有找到，就说明要查找的元素并没有在散列表中。

散列表跟数组一样，不仅支持插入、查找操作，还支持删除操作。对于使用线性探测法解决冲突的散列表，删除操作稍微有些特别。我们不能单纯地把要删除的元素设置为空。

在查找的时候，一旦我们通过线性探测方法，找到一个空闲位置，我们就可以认定散列表中不存在这个数据。但是，如果这个空闲位置是我们后来删除的，就会导致原来的查找算法失效。本来存在的数据，会被认定为不存在。

我们可以将删除的元素，特殊标记为 deleted。当线性探测查找的时候，遇到标记为 deleted 的空间，并不是停下来，而是继续往下探测。

当散列表中插入的数据越来越多时，散列冲突发生的可能性就会越来越大，空闲位置会越来越少，线性探测的时间就会越来越久。极端情况下，我们可能需要探测整个散列表，所以最坏情况下的时间复杂度为 O(n)。同理，在删除和查找时，也有可能会线性探测整张散列表，才能找到要查找或者删除的数据。

#### 二次探测（Quadratic probing）

所谓的平方探查法，跟线性探测很像，线性探测每次探测的步长是 1，那它的探测的下标序列就是平方。用发生冲突的单元 d[i], 加上 1<sup>2</sup>、 2<sup>2</sup>等。即 d[i] + 1<sup>2</sup>，d[i] + 2<sup>2</sup>, d[i] + 3<sup>2</sup>...直到找到空闲单元。

在实际操作中，平方探查法不能探查到全部剩余的单元。不过在实际应用中，能探查到一半单元也就可以了。若探查到一半单元仍找不到一个空闲单元，表明此散列表太满，应该重新建立。

#### 双重散列（Double hashing）

所谓双重散列，意思就是不仅要使用一个散列函数。我们使用一组散列函数 hash1(key)，hash2(key)，hash3(key)……我们先用第一个散列函数，如果计算得到的存储位置已经被占用，再用第二个散列函数，依次类推，直到找到空闲的存储位置。

### 载荷因子

不管采用哪种探测方法，当散列表中空闲位置不多的时候，散列冲突的概率就会大大提高。为了尽可能保证散列表的操作效率，一般情况下，我们会尽可能保证散列表中有一定比例的空闲槽位。我们用**载荷因子（load factor）**来表示空位的多少。

散列表的载荷因子定义为：

```
α = 填入表中的元素个数 / 散列表的长度
```

**α 是散列表装满程度的标志因子。由于表长是定值，α 与“填入表中的元素个数”成正比，所以，α 越大，表明填入表中的元素越多，产生冲突的可能性就越大；反之，α 越小，标明填入表中的元素越少，产生冲突的可能性就越小。实际上，散列表的平均查找长度是载荷因子 α 的函数，只是不同处理冲突的方法有不同的函数。**

对于开放定址法，荷载因子是特别重要因素，应严格限制在 0.7-0.8 以下。超过 0.8，查表时的 CPU 缓存不命中（cache missing）按照指数曲线上升。因此，一些采用开放定址法的 hash 库，如 Java 的系统库限制了荷载因子为 0.75，超过此值将 resize 散列表。

### 链表法

链表法是一种更加常用的散列冲突解决办法，相比开放寻址法，它要简单很多。我们来看这个图，在散列表中，每个“桶（bucket）”或者“槽（slot）”会对应一条链表，所有散列值相同的元素我们都放到相同槽位对应的链表中。

![散列表-链表法](https://archive.static.spiritling.net/images/09_HashTable-LinkedListMethod.png)

当插入的时候，我们只需要通过散列函数计算出对应的散列槽位，将其插入到对应链表中即可，所以插入的时间复杂度是 O(1)。当查找、删除一个元素时，我们同样通过散列函数计算出对应的槽，然后遍历链表查找或者删除。

这两个操作的时间复杂度跟链表的长度 k 成正比，也就是 O(k)。对于散列比较均匀的散列函数来说，理论上讲，k=n/m，其中 n 表示散列中数据的个数，m 表示散列表中“槽”的个数。

## 散列应用 - 单词拼写检查

所以说的 Typora 实现的单词拼写检查，以及其他软件实现的单词拼写检查，都可以使用散列表实现。

常用的英文单词有 20 万个左右，假设单词的平均长度是 10 个字母，平均一个单词占用 10 个字节的内存空间，那 20 万英文单词大约占 2MB 的存储空间，就算放大 10 倍也就是 20MB。对于现在的计算机来说，这个大小完全可以放在内存里面。所以我们可以用散列表来存储整个英文单词词典。

当用户输入某个英文单词时，我们拿用户输入的单词去散列表中查找。如果查到，则说明拼写正确；如果没有查到，则说明拼写可能有误，给予提示。借助散列表这种数据结构，我们就可以轻松实现快速判断是否存在拼写错误。

## 设计散列函数

散列表的查询效率并不能笼统地说成是 O(1)。它跟散列函数、载荷因子、散列冲突都有关系。如果散列函数设计的不好，或者载荷因子过高，都可能导致散列冲突发生的概率升高，查询效率下降。

在极端情况下，有些恶意的攻击者，还有可能通过精心构造的数据，使得所有的数据经过散列函数之后，都散列到同一个槽中。如果我们使用的是基于链表的冲突解决方法，那这个时候，散列表就会退化为链表，查询的时间复杂度就从 O(1) 急剧退化为 O(n)。

如果散列表中有 10 万个数据，退化后的散列表查询的效率就下降了 10 万倍。更直接点说，如果之前运行 100 次查询只需要 0.1 秒，那现在就需要 1 万秒。这样就有可能因为查询操作消耗大量 CPU 或者线程资源，导致系统无法响应其他请求，从而达到拒绝服务攻击（DDoS）的目的。这也就是散列表碰撞攻击的基本原理。

所以我们需要设计一个可以应对各种异常情况的工业级散列表，来避免在散列冲突的情况下，散列表性能的急剧下降，并且能抵抗散列碰撞攻击。

首先，**散列函数的设计不能太复杂**。过于复杂的散列函数，势必会消耗很多计算时间，间接地影响到散列表的性能。其次**散列函数生成的值要尽可能随机并且均匀分布**，这样才能避免或者最小化散列冲突，而且即便出现冲突，散列到每个槽里的数据也会比较平均，不会出现某个槽内数据特别多的情况。

在实际工作中，还需要考虑各种因素。这些因素有关键字的长度、特点、分布、还有散列表的大小等。散列函数各式各样。

举个例子说明下，上面说过的拼写检查功能。这里的散列函数，我们就可以这样设计：将单词中的每个字母 ASCII 码值“进位”相加，然后再跟散列表的大小求余、取模，作为散列值。比如英文单词 nice，我们转化出来的散列值就是下面这样：

```
hash("nice")=(("n" - "a") * 26*26*26 + ("i" - "a")*26*26 + ("c" - "a")*26+ ("e"-"a")) / 78978
```

实际上，散列函数的设计方法还有很多，比如直接寻址法、平方取中法、折叠法、随机数法等。

### 载荷因子过大怎么办？

载荷因子越大，说明散列表中的元素越多，空闲位置越少，散列冲突的概率就越大。不仅插入数据的过程要多次寻址或者拉很长的链，查找过程也会因此变得很慢。

对于没有频繁插入和删除的静态数据集合来说，我们很容易根据数据的特点、分布等，设计出完美的、极少冲突的散列函数，因为毕竟之前数据都是已知的。

对于动态散列表来说，数据集合是频繁变动的，我们事先无法预估将要加入的数据个数，所以我们也无法事先申请一个足够大的散列表。随着数据慢慢加入，载荷因子就会慢慢变大。当载荷因子大到一定程度之后，散列冲突就会变得不可接受。这个时候，我们该如何处理呢？

在数组和链表中当我们数据多了的时候，我们是如何操作的？当然就是进行“动态扩容”。

针对散列表，当载荷因子过大时，我们也可以进行动态扩容，重新申请一个更大的散列表，将数据搬移到这个新散列表中。假设每次扩容我们都申请一个原来散列表大小两倍的空间。如果原来散列表的载荷因子是 0.8，那经过扩容之后，新散列表的载荷因子就下降为原来的一半，变成了 0.4。

针对数组的扩容，数据搬移操作比较简单。但是，针对散列表的扩容，数据搬移操作要复杂很多。因为散列表的大小变了，数据的存储位置也变了，所以我们需要通过散列函数重新计算每个数据的存储位置。

你可以看我图里这个例子。在原来的散列表中，21 这个元素原来存储在下标为 0 的位置，搬移到新的散列表中，存储在下标为 10 的位置。

![散列表-基本动态扩容](https://archive.static.spiritling.net/images/09_HashTable-BasicDynamicExpansion.png)

插入一个数据，最好情况下，不需要扩容，最好时间复杂度是 O(1)。最坏情况下，散列表载荷因子过高，启动扩容，我们需要重新申请内存空间，重新计算哈希位置，并且搬移数据，所以时间复杂度是 O(n)。用摊还分析法，均摊情况下，时间复杂度接近最好情况，就是 O(1)。

实际上，对于动态散列表，随着数据的删除，散列表中的数据会越来越少，空闲空间会越来越多。如果我们对空间消耗非常敏感，我们可以在载荷因子小于某个值之后，启动动态缩容。当然，如果我们更加在意执行效率，能够容忍多消耗一点内存空间，那就可以不用费劲来缩容了。

我们前面讲到，当散列表的载荷因子超过某个阈值时，就需要进行扩容。载荷因子阈值需要选择得当。如果太大，会导致冲突过多；如果太小，会导致内存浪费严重。

载荷因子阈值的设置要权衡时间、空间复杂度。如果内存空间不紧张，对执行效率要求很高，可以降低负载因子的阈值；相反，如果内存空间紧张，对执行效率要求又不高，可以增加负载因子的值，甚至可以大于 1。

### 如何避免低效扩容？

刚刚在上面分析到，大部分情况下，动态扩容的散列表插入一个数据都很快，但是在特殊情况下，当载荷因子已经到达阈值，需要先进行扩容，再插入数据。这个时候，插入数据就会变得很慢，甚至会无法接受。

如果散列表当前大小为 1GB，要想扩容为原来的两倍大小，那就需要对 1GB 的数据重新计算哈希值，并且从原来的散列表搬移到新的散列表，听起来就很耗时。

如果我们的业务代码服务于用户，尽管大部分情况下，插入一个数据都很快，但是，极个别非常慢的插入操作，也会让用户崩溃。这个时候，“一次性”扩容的机制就不符合我们的需求了。

为了解决一次性扩容耗时过多的情况，我们可以将扩容操作穿插在插入操作的过程中，分批完成。当装载因子触达阈值之后，我们只申请新空间，但并不将老的数据搬移到新散列表中

当有新数据要插入时，我们将新数据插入新散列表中，并且从老的散列表中拿出一个数据放入到新散列表。每次插入一个数据到散列表，我们都重复上面的过程。经过多次插入操作之后，老的散列表中的数据就一点一点全部搬移到新散列表中了。这样没有了集中的一次性数据搬移，插入操作就都变得很快了。

![散列表-避免低效扩容](https://archive.static.spiritling.net/images/09_HashTable-AvoidInefficientExpansion.png)

上面是数据扩容，那么在上面过程中如何去查找呢？对于查询来说，为了兼容新、旧散列表中的数据，我们先从新散列表中查找，如果没有找到，再去旧的散列表中查找。

通过这样均摊的方法，将一次性扩容的代价，均摊到多次插入操作中，就避免了一次性扩容耗时过多的情况。这种实现方式，任何情况下，插入一个数据的时间复杂度都是 O(1)。

### 如何选择冲突解决方法？

在散列表刚开始学习时，说了两种主要的散列冲突的解决方法，开放定址法和链表法。这两种冲突解决办法在实际的软件开发中都非常常用。

#### 开放定址法

开放定址法（也有人叫做开放寻址法）不像链表法，需要拉很多链表。散列表中的数据都储存在数组中，可以有效地利用 CPU 缓存加快查询速度。而且，这种方法实现的散列表，序列化起来比较简单。链表法包含指针，序列化起来就没有那么容易了。

开放定址法有哪些缺点？

用开放定址法解决冲突的散列表，删除数据的时候比较麻烦，需要特殊标记已经删除掉的数据。而且，在开放定址法中，所有的数据都存储在一个数组中，比起链表法来说，冲突的代价更高。所以，使用开放定址法解决冲突的散列表，装载因子的上限不能太大。这也导致这种方法比链表法更浪费内存空间。

所以，**当数据量比较小、装载因子小的时候，适合采用开放定址法**。这也是 Java 中的 `ThreadLocalMap` 使用开放定址法解决散列冲突的原因。

#### 链表法

首先，链表法对内存的利用率比开放寻址法要高。因为链表结点可以在需要的时候再创建，并不需要像开放寻址法那样事先申请好。实际上，这一点也是链表优于数组的地方。

链表法比起开放寻址法，对大装载因子的容忍度更高。开放寻址法只能适用装载因子小于 1 的情况。接近 1 时，就可能会有大量的散列冲突，导致大量的探测、再散列等，性能会下降很多。但是对于链表法来说，只要散列函数的值随机均匀，即便装载因子变成 10，也就是链表的长度变长了而已，虽然查找效率有所下降，但是比起顺序查找还是快很多。

链表因为要存储指针，所以对于比较小的对象的存储，是比较消耗内存的，还有可能会让内存的消耗翻倍。而且，因为链表中的结点是零散分布在内存中的，不是连续的，所以对 CPU 缓存是不友好的，这方面对于执行效率也有一定的影响。

当然，如果我们存储的是大对象，也就是说要存储的对象的大小远远大于一个指针的大小（4 个字节或者 8 个字节），那链表中指针的内存消耗在大对象面前就可以忽略了。

而实际使用上，对链表法稍加改造，可以实现一个更加高效的散列表。那就是，我们将链表法中的链表改造为其他高效的动态数据结构，比如跳表、红黑树。这样，即便出现散列冲突，极端情况下，所有的数据都散列到同一个桶内，那最终退化成的散列表的查找时间也只不过是 `O(logn)`。这样也就避免了上面说的散列碰撞。

所以，**基于链表的散列冲突处理方法比较适合存储大对象、大数据量的散列表，而且，比起开放寻址法，它更加灵活，支持更多的优化策略，比如用红黑树代替链表**。

### 工业级散列函数举例

Java 中的 `HashMap` 这样一个工业级的散列表，来具体看下，这些技术是怎么应用的。

#### 初始大小

`HashMap` 默认的初始大小是 16，当然这个默认值是可以设置的，如果事先知道大概的数据量有多大，可以通过修改默认初始大小，减少动态扩容的次数，这样会大大提高 `HashMap` 的性能。

#### 装载因子和动态扩容

最大装载因子默认是 0.75，当 `HashMap` 中元素个数超过 0.75\*capacity（capacity 表示散列表的容量）的时候，就会启动扩容，每次扩容都会扩容为原来的两倍大小。

#### 散列冲突解决方案

`HashMap` 底层采用链表法来解决冲突。即使负载因子和散列函数设计得再合理，也免不了会出现拉链过长的情况，一旦出现拉链过长，则会严重影响 `HashMap` 的性能。

于是，在 JDK1.8 版本中，为了对 `HashMap` 做进一步优化，我们引入了红黑树。而当链表长度太长（默认超过 8）时，链表就转换为红黑树。我们可以利用红黑树快速增删改查的特点，提高 `HashMap` 的性能。当红黑树结点个数少于 8 个的时候，又会将红黑树转化为链表。因为在数据量较小的情况下，红黑树要维护平衡，比起链表来，性能上的优势并不明显。

#### 散列函数

散列函数的设计并不复杂，追求的是简单高效、分布均匀。

```java
int hash(Object key) {
    int h = key.hashCode()；
    return (h ^ (h >>> 16)) & (capicity -1); //capicity表示散列表的大小
}
```

其中，`hashCode()` 返回的是 Java 对象的 hash code。比如 String 类型的对象的 `hashCode()` 就是下面这样：

```java
public int hashCode() {
  int var1 = this.hash;
  if(var1 == 0 && this.value.length > 0) {
    char[] var2 = this.value;
    for(int var3 = 0; var3 < this.value.length; ++var3) {
      var1 = 31 * var1 + var2[var3];
    }
    this.hash = var1;
  }
  return var1;
}
```

### 设计散列函数总结

函数要求：

- 支持快速地查询、插入、删除操作；
- 内存占用合理，不能浪费过多的内存空间；
- 性能稳定，极端情况下，散列表的性能也不会退化到无法接受的情况。

设计思路：

- 设计一个合适的散列函数；
- 定义装载因子阈值，并且设计动态扩容策略；
- 选择合适的散列冲突解决方法。
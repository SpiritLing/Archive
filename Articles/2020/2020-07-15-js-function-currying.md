

| Property     | Content                                                      |
| ------------ | ------------------------------------------------------------ |
| Id           | 54432a21-88df-4a83-a02f-762296b72609                         |
| Name         | 2020-07-15-js-function-currying.md                           |
| ArticleLink  | https://blog.spiritling.cn/articles/54432a21-88df-4a83-a02f-762296b72609 |
| Github Issue | https://github.com/SpiritLing/Comments/issues/14             |
| Gitee Issue  | https://gitee.com/SpiritLing/Comments/issues/I54NUX          |

# js 函数柯里化（Currying）

> 在计算机科学中，柯里化（Currying），又译为卡瑞化或加里化，是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。

## 从一道面试题谈谈函数柯里化从一道面试题谈谈函数柯里化

> 题目：使用 js 实现 add(1)(2)(3)(4) 返回 10

函数柯里化要求多个参数转为单一参数，所以相当于

```js
function add() {
    return Array.from(arguments);
}
// 调用：add(1, 2, 3, 4) => [1, 2, 3, 4]
// 转换为下面形式
function addCurrying() {
    // do something
}
// 调用：addCurrying(1)(2)(3)(4) => [1, 2, 3, 4]
```

## 闭包拿参数

闭包：定义在一个函数内部的函数，静态保存所有了父级作用域的内部函数。所以每次的值可以保存住，到了最后可以全部访问到

所以，我们先来一个闭包来拿取到所有参数

```js
var add = function (value) {
    var result = [];
    result.push(value);
    // _add 函数就是一个闭包，拿取到父级作用域的值，因为_add函数已获取到父级数据，所以父级一直存在，没有释放
    var _add = function (value2) {
        result.push(value);
        return _add;
    };
    // 此方法调用返回数组
    _add.getResult = function () {
        return result;
    };
    return _add;
};
```

这样通过上面函数就可以实现基本的柯里化要求

执行：

```js
// addCurrying(1)(2)(3)(4).getResult => [1, 2, 3, 4]
// addCurrying(1)(2)(3)(4)
// =>
/*
ƒ (value2) {
    result.push(value);
    return _add;
}
*/
```

## 重写内置函数返回结果

上面的代码虽然已经获取到所有的参数，但是返回结果并没有自动返回，而是需要调用函数才返回。

并且由于不知道参数长度，也就无法通过参数数组长度来及时返回结果。

那么有什么方法可以解决呢？🤔

当然有了，在 js 中函数是有原型链的，所以每个函数都继承了基本的一些方法。

当你定义一个函数后，你如果打印时只输入函数名，并不执行，则函数内部信息就被打印出来。

那么我们可以重写这个方法，来在结束后执行。

函数的 `toString` 方法可以打印函数体，所以我们改造下，让 `toString` 返回结果

```js
//首先我们需要一个入口函数进行基本处理
var add = function (value) {
    //定义一个保存所有传入的参数的数组
    var result = [];
    // 将第一个传入的参数放置进去
    result.push(value);
    // 定义函数 _add 引用result变量，使其保持不被销毁掉
    var _add = function (subValue) {
        // 将参数push进入result中去
        result.push(subValue);
        // 返回函数 _add 进行下次一调用
        return _add;
    };
    // 将原生自带的toString 进行重写
    _add.toString = function () {
        return result;
    };
    // 返回 _add 函数进行下次调用
    return _add;
};
```

执行结果

-   `add(1)` => `[1]`
-   `add(1)(2)` => `[1,2]`
-   `add(1)(2)(3)` => `[1,2,3]`

![函数执行流程图](https://archive.static.spiritling.net/images/screenshot_1535697196480.png)



## 结束

通过上面的研究，解决一个函数柯里化问题。
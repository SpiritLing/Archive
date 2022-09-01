| Property     | Content                                                      |
| ------------ | ------------------------------------------------------------ |
| Id           | 8088a1a3-4e07-47b1-bbfa-6d0b741b5114                         |
| Name         | 2020-05-20-node-small-detail.md                              |
| ArticleLink  | https://blog.spiritling.cn/articles/8088a1a3-4e07-47b1-bbfa-6d0b741b5114 |
| Github Issue | https://github.com/SpiritLing/Comments/issues/10             |
| Gitee Issue  | https://gitee.com/SpiritLing/Comments/issues/I4VV5C          |

# Nodejs 一些细节

## nodejs 路径以及 path 相关

### `process.cwd()`、`__dirname`、`__filename`

三者的区别是

| 命令            | 说明                                       |
| --------------- | ------------------------------------------ |
| `process.cwd()` | 获得当前执行 node 命令时候的文件夹目录名   |
| `__dirname`     | 获得当前执行文件所在目录的完整目录名       |
| `__filename`    | 获得当前执行文件的带有完整绝对路径的文件名 |

例子说明：
假设我们创建一个全局的 cli

它的项目路径在 `D:\MyProject\mycli` 文件夹中

而它的 `mycli init` 命令执行的函数在 `D:\MyProject\mycli\src\init.js` 中

在 `init.js` 存在以下代码

```js
console.log(process.cwd());
console.log(__dirname);
console.log(__filename);
console.log(path.join("./index.html"));
console.log(path.resolve("./index.html"));
```

那么在 `D:\MyProject\mycli` 下执行命令输出

```
D:\MyProject\mycli
D:\MyProject\mycli\src
D:\MyProject\mycli\src\init.js
index.html
D:\MyProject\mycli\index.html
```

如果在 `D:\otherDir` 中执行命令输出为

```
D:\otherDir
D:\MyProject\mycli\src
D:\MyProject\mycli\src\init.js
index.html
D:\otherDir\index.html
```

主要区别就是 `process.cwd()` 和 `path` 连接命令

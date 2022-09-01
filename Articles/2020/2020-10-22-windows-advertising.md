

| Property     | Content                                                      |
| ------------ | ------------------------------------------------------------ |
| Id           | f5a82147-b24e-4bd6-8d3c-a878bde87a2c                         |
| Name         | 2020-10-22-windows-advertising.md                            |
| ArticleLink  | https://blog.spiritling.cn/articles/f5a82147-b24e-4bd6-8d3c-a878bde87a2c |
| Github Issue | https://github.com/SpiritLing/Comments/issues/15             |
| Gitee Issue  | https://gitee.com/SpiritLing/Comments/issues/I54NV0          |

# windows 广告定位与解决

windows 各种弹出广告，影响使用和美观。如何解决呢？

如何定位广告是由于那个软件弹出来的？

<!-- more -->

## 前言-神出鬼没的广告

最近也快到双十一了，各种广告满天飞，这不一不小心就中招了。

今天突然发现桌面出现了一个广告，如下所示

![广告图](https://archive.static.spiritling.net/images/2020-10-22_10-25-53.png)

最简单的便是点击叉号即可关闭，但是这也是治标不治本啊，过上一段时间，也是还会出现的，而且想把显示广告的软件删除掉，都比较难啊，一看程序那么多，都不知道是哪一个隐藏起来整出来的。所以想卸载也无法准确定位到。

但是“魔高一尺，道高一丈”，下面就说下如何彻底解决掉烦人的广告。

## 安装官网进程管理工具

首先需要安装 windows 官方给的进程管理工具 [Process Explorer](https://docs.microsoft.com/en-us/sysinternals/downloads/process-explorer)。

![ProcessExplorer图](https://article.cdn.spiritling.pub/f5a82147-b24e-4bd6-8d3c-a878bde87a2c/processexplorer.jpg)

安装完毕后直接打开即可，上面显示的界面图是官方网站里面的图。

## 定位广告

点击软件后，在主界面会有一个按钮，如下所示：

![主界面按钮](https://archive.static.spiritling.net/images/2020-10-22_10-18-27.png)

点击红框处的按钮，并拖动到广告上去。

一定要**按下并拖动**

然后在界面进程中会自动定位到该广告发出的进程。

![定位进程](https://archive.static.spiritling.net/images/2020-10-22_10-27-23.png)

鼠标放置停留几秒后，会显示详细的信息。

## 定位软件位置

如果你看到这个进程比较熟悉，或者知道是那个程序，那么这里就不需要看了，直接卸载软件，一了百了。

但是如果你不熟悉软件名称，不知道是哪一个，那么还是需要继续往下看。

嗯(⊙﹏⊙)，因为自己有点手快，就给把进程删除了，所以查找程序地址我就使用了其他的进程作为例子 `Everything`。

先右键进程，在列表中选择 `Properties...`。

![右键进程](https://archive.static.spiritling.net/images/2020-10-22_11-26-59.png)

点击属性进入详细后，选择 Threads 下面的 Module

![详细属性](https://archive.static.spiritling.net/images/2020-10-22_11-27-11.png)

点击后可以查看到运行程序位置，当然如果出现错误，可以使用上面的 Image 选项卡，里面也含有运行程序的地址。

![程序信息](https://archive.static.spiritling.net/images/2020-10-22_11-28-30.png)

定位到了软件信息，那么就是删除软件了。

大功告成 O(∩_∩)O。
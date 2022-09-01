| Property     | Content                                                      |
| ------------ | ------------------------------------------------------------ |
| Id           | 8555088a-f796-48fb-a8d6-e28305955ec4                         |
| Name         | 2020-01-14-iis-url-rewrite.md                                |
| ArticleLink  | https://blog.spiritling.cn/articles/8555088a-f796-48fb-a8d6-e28305955ec4 |
| Github Issue | https://github.com/SpiritLing/Comments/issues/6              |
| Gitee Issue  | https://gitee.com/SpiritLing/Comments/issues/I4VSZS          |

# IIS进行反向代理和URL重写——实现https重定向，文件类型隐藏访问重写，nodejs等服务重写等等

## Why?

### 什么是 URL 重写

URL 重写和反向代理就是首先获得一个进入的 URL 请求然后把它重新写成网站可以处理的另一个 URL 的过程。

举个例子来说，如果通过浏览器进来的 URL 是“UserProfile.aspx?ID=1”那么它可以被重写成 “UserProfile/1.aspx”，这样的 URL，这样的网址可以更好的被网站所阅读。

根据不同的服务器就会有不同的 URL 重写规则，比如 iis apache nginx 这三种重写方式都是不同的，并非完全一样的。

今天只说 IIS 的重写

### 为什么要使用

首先有需求就有使用，所以，是我的需求诞生了我去使用重写功能

第一次接触的后端为 Nodejs，一个 80 或者 443 端口只能绑定一个程序。使用 windows 自带的 iis 服务器来使用，想要一个服务器上部署多个站点程序，则需要使用到反向代理才行。

不使用反向代理如下图所示：

比如我的网站是：https://demo.example.com:4436/posts/82f57ccc

![URL重写之前NodeJS的URL](https://archive.static.spiritling.net/images/20200119161522.png)

明眼一看就是有点不够友好，正准备记下网址，这一瞅，直接放弃了，太麻烦了，还有端口

但是经过 URL 重写，也就是反向代理后就改为这样的：https://demo.example.com/4436/posts/82f57ccc ，只需要记域名即可

![URL重写之后](https://article.cdn.spiritling.pub/8555088a-f796-48fb-a8d6-e28305955ec4/20200119161557.png)

只有你的服务器够大，通过反向代理和 URL 重写就可以代理无数个本地服务，哪怕本地无数个 nodejs 服务器从 10000-65535 监听，都可以的，只需要做好反向代理即可

## 前置条件

1. 首先需要有个 IIS 服务器
2. 然后去官网下载 [web 平台安装工具](https://www.iis.net/downloads/microsoft/web-platform-installer)
3. 然后安装工具并打开

![ web平台安装工具](https://archive.static.spiritling.net/images/1076304-20180412200431336-787173232.png)

4. 搜索 Application Request Routing 或者 应用程序请求路由 ，和 url 重写工具 或者 url rewrite；因为有可能是英文也有可能是中文，所以当一个搜索不到时，搜索另一个语言，一定要在产品全部中搜索

![ web平台安装工具](https://archive.static.spiritling.net/images/1076304-20180412200848094-824241073.png)

5. 最近（2019 底 2020 初）不知道 web 平台安装工具出什么问题了，一直安装不了，有时候甚至会打不开，所以这里留个官网下载单模块的链接：[url rewrite](https://www.iis.net/downloads/microsoft/url-rewrite)，[Application Request Routing](https://www.iis.net/downloads/microsoft/application-request-routing)，[All Modules Downloads](https://www.iis.net/downloads)；拉到底下会有下载链接，url rewrite 有对应的中文版本。

6. 下载安装即可

### 网盘链接

1. 百度网盘：

-   Application Request Routing：链接：https://pan.baidu.com/s/1Wg6u6IJVMqa-DeK5y8Ym6w 提取码：rcic
-   url rewrite：链接：https://pan.baidu.com/s/1ePW6HOkqqyHPqNNWyaQ43w 提取码：x27v

2. 腾讯网盘：

-   Application Request Routing：链接：https://share.weiyun.com/5iI6q1W 密码：u47bv6
-   url rewrite：链接：https://share.weiyun.com/52XIKfL 密码：57fkan

## 应用程序请求路由设置

1. 打开 IIS 工具，选择上面安装的请求路由

![应用程序请求路由设置](https://archive.static.spiritling.net/images/1076304-20180412201157869-978989213.png)

2. 选择 Server Proxy Settings

![应用程序请求路由设置](https://archive.static.spiritling.net/images/1076304-20180412201244421-675549952.png)

3. 在中间区域，选择勾选 Enable proxy，不用修改内容，当然也可以根据需求自己修改

![应用程序请求路由设置](https://archive.static.spiritling.net/images/1076304-20180412201358722-1759010199.png)

4. 点击应用即可，完成请求路由的设置

![应用程序请求路由设置](https://archive.static.spiritling.net/images/1076304-20180412201437572-1668674268.png)

## url 重写设置：这里讲基本参数，后面才会是例子

1. 打开站点，选择需要 url 重写的站点

![url重写设置](https://archive.static.spiritling.net/images/1076304-20180412201808214-1496956504.png)

2. 当安装完成 url 重写时，会出现 url 重写这个工具，选择工具，名字也有可能是英文

![url重写设置](https://archive.static.spiritling.net/images/1076304-20180412201916597-1448053593.png)

3. 打开工具，选在右侧栏第一行添加规则，打开对话框，选择空白规则

![url重写设置](https://archive.static.spiritling.net/images/1076304-20180412202043308-320482397.png)

4. 输入名称，随意，但是尽量能表示出重写目标的含义

![url重写设置](https://archive.static.spiritling.net/images/1076304-20180412203113345-943932106.png)

5. 匹配 url，请求的 url(字面意思)：与模式匹配（推荐），与模式不匹配；使用(匹配使用的方式)：正则表达式（推荐），通配符，完全匹配；

![url重写设置](https://archive.static.spiritling.net/images/1076304-20180412203336729-93686218.png)

6. 模式：比如说 host 代表主机域名，则在这里面写的就是 host 之后的正则匹配表达式，比如匹配在 host/blog/，之下的都转到 nodejs 搭建的服务上，则这里填写^blog/(.\*)；点击测试模式，我们填写下面图片内容进行测试，发现测试结果中有个{R:1}，我们需要将其导向至 nodejs 搭建的服务，也就是重写目标

![url重写设置](https://archive.static.spiritling.net/images/1076304-20180412203903048-2138965636.png)

7. 忽略大小写，自然就是字面意思

8. 下面便是条件，按照图片来进行填写，这里不具体细数条件类型和规则模式填写，后面有三个例子进行了基本讲述，其余的请查看官方文档

![url重写设置](https://archive.static.spiritling.net/images/1076304-20180412204422154-761160361.png)

9. 服务器变量可以不写，操作类型可以因情况而定，一般使用 重定向或者重写（http 强制跳转 https 一般为重定向，其余大多数为重写）

重写 URL：比如博客是在 nodejs 搭建的服务上，并且监听本地 3001 端口时，填写http://127.0.0.1:3001/{R:1}，这个{R:1}就是上面模式匹配的字符串，其实访问www.example.com/blog/index.html，就是相当于访问http://127.0.0.1:3001/index.html，这样监听本地3001，不用公网3001，可以减少服务器危险

停止后续规则，自然就是字面意思，一旦勾选后，匹配处理结束，将不再处理下面的其他规则

![url重写设置](https://archive.static.spiritling.net/images/1076304-20180412204907092-626474199.png)

10. 点击右侧栏应用即可，当应用显示灰色时，证明有必填项没有填写，所以无法应用，到这里也就重写完毕。

## 反向代理例子

目前共有四个，分别是：

-   http 强制跳转 https 上（一般网站都会使用，全站 https 是趋势）
-   所有页面都返回同一个静态页（适合网站调整，整改，以及说明时）
-   隐藏其余端口访问，一个暴露的服务器后面隐藏多个监听本地的服务（代理多个内部服务）
-   隐藏访问的文件类型（\*.php 文件可以隐藏或更改文件后缀）

### http 强制跳转 https 上

1. 重写规则名称，以及请求的正则匹配

![http强制跳转https](https://archive.static.spiritling.net/images/20200120135333.png)

2. 请求条件筛选

![http强制跳转https](https://archive.static.spiritling.net/images/20200120135842.png)

域名和 https 状态一定要核对清除，并且匹配要选择全部匹配

3. 请求匹配，条件过滤后就是要重定向的地址

![http强制跳转https](https://archive.static.spiritling.net/images/20200120140954.png)

其中 {R:1} 就是指原请求中的路径，比如：http://demo.example.com/blog/posts/dec13d34 它的请求路径就是 blog/posts/dec13d34，所以在重定向的域名后需要添加 `/`

然后就可以将 http 请求强制跳转到 https 上

在最后会附有 `web.config` 配置文件

### 所有页面都返回同一个静态页

在上面我们将每个 http 请求都重定向到 https 上，相当于一个 http 对应一个 https，通过 `{R:1}` 来动态赋值

但是如果深入一想，假设我们将 `{R:1}` 固定指向 `index.html`，那么每一个请求都会返回 `index.html` 内容，在将重定向改为重写，保持客户端 url 不变，但是内容已经被重写为 `index.html` 的内容了

重写域名可以使用 `http://localhost:port/` 或者 `http://127.0.0.1:port/`

所以实现访问任何页面都返回同一个内容是很轻松的。

在最后会附有 `web.config` 配置文件

### 隐藏其余端口访问，一个暴露的服务器后面隐藏多个监听本地的服务（代理多个内部服务）

在可以重写到指定的其他服务地址上去，那么同样也可以将本地监听非 80 443 端口的服务进行服务器层面的隐藏

比如原本访问 https://demo.example.com:4436/posts/82f57ccc 内容，但是可以使用重写进行改变

重写请求模式，也就是正则匹配内容为：`^4436/(.*)`

服务器条件，域名来源为：`^demo\.example\.com$`

重写 url 可以为：`https://demo.example.com:4436/{R:1}`

那么可以将明面上的请求 https://demo.example.com/4436/posts/82f57ccc 进行 url 重写

先通过请求匹配 `^4436/(.*)` 获取到 `{R:1}` 为 `posts/82f57ccc`

然后将 `{R:1}` 转发到 `https://demo.example.com:4436/` 上，就可以完成请求代理

### 隐藏访问的文件类型（\*.php 文件可以隐藏或更改文件后缀）

相应的，既然请求匹配可以匹配指定路径，那么也可以匹配指定文件，比如说：请求的为 `php` 文件

比如原本访问 https://demo.example.com/posts/82f57ca2phpf 内容，但是可以使用重写进行改变

重写请求模式，也就是正则匹配内容为：`(.*)phpf$`

服务器条件，域名来源为：`^demo\.example\.com$`

重写 url 可以为：`https://demo.example.com/{R:1}.php`

那么可以将明面上的请求 https://demo.example.com/posts/82f57ca2phpf 进行 url 重写

先通过请求匹配 `(.*)phpf$` 获取到 `{R:1}` 为 `posts/82f57ca2`

然后将 `{R:1}` 转发到 `https://demo.example.com/{R:1}.php` 上，实际请求为：`https://demo.example.com/posts/82f57ca2.php`

## `web.config`配置文件

`web.config` 配置文件放置在 iis 站点的根目录

可以根据自己需求进行更改

在线代码块：[gist](https://gist.github.com/SpiritLing/c0f12694158378594991a3084c433df3)，[bitbucket](https://bitbucket.org/spiritling-team/workspace/snippets/Br4MdR)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--- Origin Release：https://blog.spiritling.cn/posts/82f57ccc/ -->
<configuration>
    <system.webServer>
        <rewrite>
            <rules>
                <clear />
                <!-- htpt => https -->
                <rule name="demo-https" enabled="true" stopProcessing="true">
                    <match url="(.*)" />
                    <conditions logicalGrouping="MatchAll" trackAllCaptures="false">
                        <add input="{HTTPS}" pattern="^OFF$" />
                        <add input="{HTTP_HOST}" pattern="^demo\.example\.com$" />
                    </conditions>
                    <action type="Redirect" url="https://demo.example.com/{R:1}" />
                </rule>
                <!-- https domain/4436 => https domain:4436 -->
                <rule name="demo" enabled="true" stopProcessing="true">
                    <match url="^4436/(.*)" />
                    <conditions logicalGrouping="MatchAll" trackAllCaptures="false">
                        <add input="{HTTPS}" pattern="^ON$" />
                        <add input="{HTTP_HOST}" pattern="^demo\.example\.com$" />
                    </conditions>
                    <action type="Rewrite" url="https://demo.example.com:4436/{R:1}" />
                </rule>
                <!-- domain => https://localhost -->
                <rule name="index" stopProcessing="true">
                    <match url="(.*)" />
                    <conditions>
                        <add input="{HTTP_HOST}" pattern="^test\.example\.com$" />
                    </conditions>
                    <action type="Rewrite" url="https://localhost" />
                </rule>
            </rules>
        </rewrite>
        <caching enabled="false" enableKernelCache="false" />
    </system.webServer>
</configuration>
```

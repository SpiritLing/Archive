

| Property     | Content                                                      |
| ------------ | ------------------------------------------------------------ |
| Id           | 93535928-cc52-4039-9e8e-08904e381682                         |
| Name         | 2020-06-18-choco-and-mkcert-loacl-https.md                   |
| ArticleLink  | https://blog.spiritling.cn/articles/93535928-cc52-4039-9e8e-08904e381682 |
| Github Issue | https://github.com/SpiritLing/Comments/issues/13             |
| Gitee Issue  | https://gitee.com/SpiritLing/Comments/issues/I54NUR          |

# choco 安装 和 mkcert 本地https

## Choco

### 命令行安装

直接拷贝执行即可，注意需要**管理员身份**运行

`cmd` 安装：

```shell
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

`PowerShell` 安装：

```shell
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

### 检查安装是否成功

```
choco -v
```

### 设置安装路径

![choco安装路径](https://archive.static.spiritling.net/images/chocolatey-install-path-config.png)

修改路径后，需要添加安装路径下的`bin`目录到`path`环境中。

有时安装完毕后，下载的软件无法使用，有可能是`bin`目录没有在`path`环境中。

### 常用指令

[Commands](https://chocolatey.org/docs/commands-reference)

-   choco list -li 查看本地安装的软件
-   choco search nodejs 查找安装包
-   choco install sublimetext3 下载
-   choco uninstall sublimetext3 卸载
-   choco upgrade sublimetext3 更新（update）

## mkcert

mkcert 是一个使用 go 语言编写的生成本地自签证书的小程序，具有跨平台，使用简单，支持多域名，自动信任 CA 等一系列方便的特性可供本地开发时快速创建 https 环境使用。

### 安装 mkcert

安装方式也非常简单，由于 go 语言的静态编译和跨平台的特性，官方提供各平台预编译的版本，直接下载到本地，给可执行权限(Linux/Unix 需要)就可以了。下载地址: https://github.com/FiloSottile/mkcert/releases/latest

此外，mkcert 已经推送至 Homebrew, MacPorts, Linuxbrew, Chocolatey, Scoop 等包管理平台中，也可以直接借助对应的包管理平台安装。如:

```
choco install mkcert
```

安装成功后，应该可以使用 mkcert 命令了：

```
PS C:\Users\abcfy\projects> mkcert
Using the local CA at "C:\Users\abcfy\AppData\Local\mkcert" ✨
Usage of mkcert:

        $ mkcert -install
        Install the local CA in the system trust store.

        $ mkcert example.org
        Generate "example.org.pem" and "example.org-key.pem".

        $ mkcert example.com myapp.dev localhost 127.0.0.1 ::1
        Generate "example.com+4.pem" and "example.com+4-key.pem".

        $ mkcert "*.example.it"
        Generate "_wildcard.example.it.pem" and "_wildcard.example.it-key.pem".

        $ mkcert -uninstall
        Uninstall the local CA (but do not delete it).

For more options, run "mkcert -help".
```

### mkcert 基本使用

从上面自带的帮助输出来看，mkcert 已经给出了一个基本的工作流，规避了繁杂的 openssl 命令，几个简单的参数就可以生成一个本地可信的 https 证书了。更详细的用法直接看官方文档就好。

#### 将 CA 证书加入本地可信 CA

```
$ mkcert -install
Using the local CA at "C:\Users\abcfy\AppData\Local\mkcert" ✨
```

仅仅这么一条简单的命令，就帮助我们将 mkcert 使用的根证书加入了本地可信 CA 中，以后由该 CA 签发的证书在本地都是可信的。

在 Windows 的可信 CA 列表可以找到该证书

#### 生成自签证书

生成自签证书的命令十分简单:

```
mkcert domain1 [domain2 [...]]
```

直接跟多个要签发的域名或 ip 就行了，比如签发一个仅本机访问的证书(可以通过 127.0.0.1 和 localhost，以及 ipv6 地址::1 访问)

```
mkcert localhost 127.0.0.1 ::1
Using the local CA at "C:\Users\abcfy\AppData\Local\mkcert" ✨

Created a new certificate valid for the following names 📜
 - "localhost"
 - "127.0.0.1"
 - "::1"

The certificate is at "./localhost+2.pem" and the key at "./localhost+2-key.pem" ✅
```

通过输出，我们可以看到成功生成了 localhost+2.pem 证书文件和 localhost+2-key.pem 私钥文件，只要在 web server 上使用这两个文件就可以了。

默认生成的证书格式为 PEM(Privacy Enhanced Mail)格式，任何支持 PEM 格式证书的程序都可以使用。比如常见的 Apache 或 Nginx 等，也可以使用参数指定生成的格式，比如 windows 的 pfx 格式证书

#### 高级参数

```
-cert-file FILE, -key-file FILE, -p12-file FILE
	Customize the output paths.

-client
	Generate a certificate for client authentication.

-ecdsa
	Generate a certificate with an ECDSA key.

-pkcs12
	Generate a ".p12" PKCS #12 file, also know as a ".pfx" file,
	containing certificate and key for legacy applications.

-csr CSR
	Generate a certificate based on the supplied CSR. Conflicts with
	all other flags and arguments except -install and -cert-file.
```

##### pem 证书生成

不需要额外的任何操作，使用 `mkcert domain.com`，即可生成 `domain.com.pem` 和 `domain.com-key.pem`

##### pfx 证书生成

密码：changeit

```
C:\Users\albertxiao>mkcert -pkcs12 *.example.com
Using the local CA at "C:\Users\albertxiao\AppData\Local\mkcert" ✨

Created a new certificate valid for the following names 📜
 - "*.example.com"

Reminder: X.509 wildcards only go one level deep, so this won't match a.b.example.com ℹ️

The PKCS#12 bundle is at "./_wildcard.example.com.p12" ✅

The legacy PKCS#12 encryption password is the often hardcoded default "changeit" ℹ️
```

##### crt 证书

```
mkcert -key-file localhost.key -cert-file localhost.crt localhost
```

## 局域网构建

有时候我们需要在局域网内测试 https 应用，这种环境可能不对外，因此也无法使用像 Let's encrypt 这种免费证书的方案给局域网签发一个可信的证书，而且 Let's encrypt 本身也不支持认证 Ip。

先来回忆一下证书可信的三个要素:

-   由可信的 CA 机构签发
-   访问的地址跟证书认证地址相符
-   证书在有效期内

如果期望我们自签证书在局域网内使用，以上三个条件都需要满足。很明显自签证书一定可以满足证书在有效期内，那么需要保证后两条。我们签发的证书必须匹配浏览器的地址栏，比如局域网的 ip 或者域名，此外还需要信任 CA。

我们先重新签发一下证书，加上本机的局域网 ip 认证:

```
mkcert localhost 127.0.0.1 ::1 192.168.31.170
Using the local CA at "C:\Users\abcfy\AppData\Local\mkcert" ✨

Created a new certificate valid for the following names 📜
 - "localhost"
 - "127.0.0.1"
 - "::1"
 - "192.168.31.170"

The certificate is at "./localhost+3.pem" and the key at "./localhost+3-key.pem" ✅
```

再次验证发现使用https://192.168.31.170本机访问也是可信的。然后我们需要将**CA证书发放给局域网内其他的用户**。

```
mkcert -CAROOT
C:\Users\abcfy\AppData\Local\mkcert
```

使用 mkcert -CAROOT 命令可以列出 CA 证书的存放路径

可以看到 CA 路径下有两个文件 rootCA-key.pem 和 rootCA.pem 两个文件，用户需要信任 rootCA.pem 这个文件。将 rootCA.pem 拷贝一个副本，并命名为 rootCA.crt(因为 windows 并不识别 pem 扩展名，并且 Ubuntu 也不会将 pem 扩展名作为 CA 证书文件对待)，将 rootCA.crt 文件分发给其他用户，手工导入。

windows 导入证书的方法是双击这个文件，在证书导入向导中将证书导入受信任的根证书颁发机构:

![导入证书](https://archive.static.spiritling.net/images/window-import-mkcert-ca.png)

Ubuntu 的做法可以将证书文件(必须是 crt 后缀)放入/usr/local/share/ca-certificates/，然后执行 sudo update-ca-certificates

Android 和 IOS 信任 CA 证书的做法参考[官方文档](https://github.com/FiloSottile/mkcert#mobile-devices)。
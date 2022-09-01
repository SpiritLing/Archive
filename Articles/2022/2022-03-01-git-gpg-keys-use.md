

| Property     | Content                                                      |
| ------------ | ------------------------------------------------------------ |
| Id           | 32c339d0-a19b-4a87-ad62-7be933bedcd0                         |
| Name         | 2022-03-01-git-gpg-keys-use.md                               |
| ArticleLink  | https://blog.spiritling.cn/articles/32c339d0-a19b-4a87-ad62-7be933bedcd0 |
| Github Issue | https://github.com/SpiritLing/Comments/issues/18             |
| Gitee Issue  | https://gitee.com/SpiritLing/Comments/issues/I54NWP          |

# Git 使用 GPG keys 进行签名

## 本地生成新的 GPG 密钥

访问下载地址 https://www.gnupg.org/download/ ，下载适合你的操作系统的版本。

一般高版本下的 git 会携带有 GPG 命令操作。如果没有，请自行安装

1. 打开 Git Bash
2. 生成 GPG 密钥对，由于 GPG 有多个版本，因此您可能需要查询相关的[手册页](https://en.wikipedia.org/wiki/Man_page)以找到适当的密钥生成命令。 密钥必须使用 RSA。

当前使用版本高于 2.1.17，使用以下命令生成 GPG 密钥对

```shell
gpg --full-generate-key
```

1. 在提示时，指定要生成的密钥类型，或按 `Enter` 键接受默认值。
2. 在提示时，指定想要的密钥大小，或按 `Enter`键接受默认值。 密钥必须至少是 `4096`位（最大是4096位，输入最大的即可）。

1. 输入密钥的有效时长。 按 `Enter`键将指定默认选择，表示该密钥不会过期。
2. 验证您的选择是否正确。

1. 输入您的用户 ID 信息。

请保持和你的git账户一致的信息

1. 输入安全密码（后续启动提交签名认证时需要输入）
2. 使用 `gpg --list-secret-keys --keyid-format=long`命令列出您拥有其公钥和私钥的长形式 GPG 密钥。 签名提交或标记需要私钥。

注：Linux上的一些 GPG 安装可能需要使用 `gpg2 --list-keyid-form LONG`查看您现有密钥的列表。 在这种情况下，您还需要运行 `git config --global gpg.program gpg2`来配置 Git 使用 git gpg2。

1. 从 GPG 密钥列表中复制您想要使用的 GPG 密钥 ID 的长形式。 在此例中，GPG 密钥 ID 是 `3AA5C34371567BD2`：

```shell
$ gpg --list-secret-keys --keyid-format=long
/Users/hubot/.gnupg/secring.gpg
------------------------------------
sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2017-03-10]
uid                          Hubot 
ssb   4096R/42B317FD4BA89E7A 2016-03-10
```

1. 粘贴下面的文本（替换为您要使用的 GPG 密钥 ID）。 在此例中，GPG 密钥 ID 是 `3AA5C34371567BD2`：

```shell
$ gpg --armor --export 3AA5C34371567BD2
# Prints the GPG key ID, in ASCII armor format
```

1. 复制 GPG 密钥，从 -----BEGIN PGP PUBLIC KEY BLOCK----- 开始，到 -----END PGP PUBLIC KEY BLOCK----- 结束。
2. 将 GPG 密钥添加到具体的 账户上即可。



## 启动 GPG 密钥验证

1. 打开 Git Bash
2. 使用 `gpg --list-secret-keys --keyid-format=long` 命令列出您拥有其公钥和私钥的长形式 GPG 密钥。 签名提交或标记需要私钥。

```shell
$ gpg --list-secret-keys --keyid-format=long
```

1. 从 GPG 密钥列表中复制您想要使用的 GPG 密钥 ID 的长形式。 在此例中，GPG 密钥 ID 是 `3AA5C34371567BD2`：

```shell
$ gpg --list-secret-keys --keyid-format=long
/Users/hubot/.gnupg/secring.gpg
------------------------------------
sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2017-03-10]
uid                          Hubot 
ssb   4096R/42B317FD4BA89E7A 2016-03-10
```

1. 要在 Git 中设置 GPG 签名密钥，请粘贴下面的文本，替换您想要使用的 GPG 密钥 ID。 在此例中，GPG 密钥 ID 是 `3AA5C34371567BD2`：

```shell
git config --global user.signingkey 3AA5C34371567BD2
git config --local user.signingkey 3AA5C34371567BD2
```

## 对提交和标记签名

### 提交签名

**提示：**

要将您的 Git 客户端配置为默认对本地仓库的提交签名，请在 Git 版本 2.0.0 及更高版本中，运行 git config commit.gpgsign true。 要在计算机上的任何本地仓库中默认对所有提交签名，请运行 git config --global commit.gpgsign true。

要存储 GPG 密钥密码，以便无需在每次对提交签名时输入该密码，我们建议使用以下工具：

- 对于 Mac 用户，[GPG Suite](https://gpgtools.org/) 允许您在 Mac OS 密钥链中存储 GPG 密钥密码。
- 对于 Windows 用户，[Gpg4win](https://www.gpg4win.org/) 将与其他 Windows 工具集成。

您也可以手动配置 [gpg-agent](http://linux.die.net/man/1/gpg-agent) 以保存 GPG 密钥密码，但这不会与 Mac OS 密钥链（如 ssh 代理）集成，并且需要更多设置。

1. 当本地分支中的提交更改时，请将 S 标志添加到 git commit 命令：

```shell
$ git commit -S -m "your commit message"
# Creates a signed commit
```

1. 如果您使用 GPG，则创建提交后，提供您生成 GPG 密钥时设置的密码。
2. 在本地完成创建提交后，将其推送到 GitHub 上的远程仓库：

```shell
$ git push
# Pushes your local commits to the remote repository
```

完成提交签名，对于签名状态可以去对应git程序上面查看。请注意，如果删除了已经添加到账户中的 GPG 密钥，可能导致原有的提交签名由 `Verified`变为 `Unverified`。



### 标记签名

1. 要对标记签名，请将 `-s` 添加到您的 `git tag` 命令。

```shell
$ git tag -s mytag
# Creates a signed tag
```

1. 通过运行 `git tag -v [tag-name]` 验证您签名的标记。

```shell
$ git tag -v mytag
# Verifies the signed tag
```

## GPG Keys 命令操作

列出所有key

```shell
gpg --list-keys
```

列出所有秘钥

```shell
gpg --list-secret-keys
```

每个key都会有一个uid，不论是公开key还是密钥



删除key

```shell
gpg --delete-key [uid]
```

删除密钥

```shell
gpg --delete-secret-key [uid]
```

导出密钥：

```shell
gpg --export-secret-keys [uid] > private.key
```

导入密钥：

```shell
gpg --import private.key
```

## 参考：

- [Github GPG Docs](https://docs.github.com/cn/authentication/managing-commit-signature-verification/about-commit-signature-verification)
- [GnuPG Download](https://www.gnupg.org/download/)
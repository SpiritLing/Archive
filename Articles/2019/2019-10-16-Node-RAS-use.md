| Property     | Content                                                      |
| ------------ | ------------------------------------------------------------ |
| Id           | e09a8fce-63c0-4fb3-aa37-89aa888ff870                         |
| Name         | 2019-10-16-Node-RAS-use.md                                   |
| Article Link | https://blog.spiritling.cn/articles/e09a8fce-63c0-4fb3-aa37-89aa888ff870 |
| Github Issue | https://github.com/SpiritLing/Comments/issues/2              |
| Gitee Issue  | https://gitee.com/SpiritLing/Comments/issues/I4VSTK          |

# Node-RSA 非对称加密使用

## 关于非对称加密算法

[如何用通俗易懂的话来解释非对称加密?](https://www.zhihu.com/question/33645891)

## Node 服务端使用非对称加密算法

1. 首先引入 `node-rsa` 包

```shell
npm install node-rsa --save
```

2. 生成获取私钥和公钥

```js
const key = new NodeRSA({ b: 512 });
// 一般情况下使用时建议将下面指定格式，可以在后面到导入时有效确认格式
const publicKey = key.exportKey("pkcs8-public-pem");
const privateKey = key.exportKey("pkcs8-private-pem");
```

3. 导入私钥和公钥

```js
const key2 = new NodeRSA();
// 需要与生成的核对一致，否则会出现错误
key2.importKey(publicKey, "pkcs8-public-pem");
key2.importKey(privateKey, "pkcs8-private-pem");
```

4. 公钥加密私钥解密 --- 用于加解密

```js
const encryptPublic = key.encrypt(data, "base64", "utf8");
const decryptPrivate = key.decrypt(encryptPublic, "json");
```

-   `data` 就是需要加密的明文数据，可以是 `string`, `Buffer`，也可以是 `Array/Object`，`Array/Object` 首先会自动编码成 `json` 字符串
-   `base64` 就是需要加密的结果以什么格式显示，这里选择以`base64`显示，默认为：buffer
-   `utf8` 数据源是什么编码格式
-   `json` 解密之后以什么格式出现，这里选择`json`格式

5. 私钥加密公钥解密 --- 用于签名

```js
const encryptPrivate = key.encryptPrivate(data, "base64", "utf8");
const decryptPublic = key.decryptPublic(encryptPrivate, "json");
```

-   `data` 就是需要加密的明文数据，可以是 `string`, `Buffer`，也可以是 `Array/Object`，`Array/Object` 首先会自动编码成 `json` 字符串
-   `base64` 就是需要加密的结果以什么格式显示，这里选择以`base64`显示，默认为：buffer
-   `utf8` 数据源是什么编码格式
-   `json` 解密之后以什么格式出现，这里选择`json`格式

6. 返回结果

```js
ctx.body = {
    privateKey,
    publicKey,
    publicData: data,
    encryptPublic,
    decryptPrivate,
    encryptPrivate,
    decryptPublic,
};
```

> 如果下面不存在代码块时，请检查网络是否可以访问 github 的 [gist](https://gist.github.com/) ，无法访问 gist 时，可以点击此处访问 [bitbucket](https://bitbucket.org/spiritling-team/workspace/snippets/dLGAn9) 的代码块

<script src="https://gist.github.com/SpiritLing/3503dc24df084d8d007d470973c4d721.js"></script>


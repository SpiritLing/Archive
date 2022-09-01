

| Property     | Content                                                      |
| ------------ | ------------------------------------------------------------ |
| Id           | 53007723-2d22-4da5-a9c6-d372fb9171aa                         |
| Name         | 2020-06-01-jenkins-credentials.md                            |
| ArticleLink  | https://blog.spiritling.cn/articles/53007723-2d22-4da5-a9c6-d372fb9171aa |
| Github Issue | https://github.com/SpiritLing/Comments/issues/12             |
| Gitee Issue  | https://gitee.com/SpiritLing/Comments/issues/I54NUH          |

# Jenkins 凭据使用

## 环境变量

### jenkinsfile 使用环境变量

代码：

```
pipeline {
  agent {
    docker {
      image 'spiritling/node:10.15.3'
    }

  }
  stages {
    stage('get') {
      environment {
        VERSION = sh(script: 'node script/auto-versioning.js', , returnStdout: true)
      }
      steps {
        sh 'echo "VERSION: "$VERSION'
    }
  }
}
```

将 auto-versioning.js 执行后返回的文本或数字存入到 `VERSION` 环境变量中去

在 `steps` 中使用 `$VERSION` 来获取环境变量

## 凭据

### 账号密码凭据管理

创建凭据，以下为例子：

类型：Username with password
范围：全局
用户名：root
密码：rootxxxx
ID：BIRRARY_ID
描述：随意填写

在 jenkinsfile 中使用

```
pipeline {
  agent {
    docker {
      image 'spiritling/node:10.15.3'
    }

  }
  stages {
    stage('get') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'BIRRARY_ID', passwordVariable: 'password', usernameVariable: 'username')]) {
          sh 'git remote set-url origin https://${username}:${password}@github.com/spiritling/blog.git'
        }
        sh 'echo "获取凭据"'
    }
  }
}
```

可以在 jenkinsfile 文件的构建过程中获取到 username 和 password 的凭据，并且可以在后续将其插入进去

### 加密文本凭据管理

创建凭据，以下为例子：

类型：Secret text
范围：全局
Secret：rootxxxx
ID：BIRRARY_ID
描述：随意填写

在 jenkinsfile 中使用

```
pipeline {
  agent {
    docker {
      image 'spiritling/node:10.15.3'
    }

  }
  stages {
    stage('get') {
      steps {
        withCredentials([string(credentialsId: 'ID：BIRRARY_ID', variable: 'secret')]) { //set SECRET with the credential content
          sh 'echo -e "registry=https://npmjs.org/spiritling/\n_auth = ${secret}\nemail = spirit_ling_cn@163.com\nalways-auth = true\n$PATH" > .npmrc'
        }
        sh 'echo "获取凭据"'
    }
  }
}
```

可以在 jenkinsfile 文件的构建过程中获取到 secret 的凭据，并且可以在后续将其插入进去
| Property     | Content                                                      |
| ------------ | ------------------------------------------------------------ |
| Id           | 1a400a2d-a8f7-49d1-a1e3-1a88eb19e86c                         |
| Name         | 2020-02-26-dotnetcore-ef-mysql-generate-entity.md            |
| ArticleLink  | https://blog.spiritling.cn/articles/1a400a2d-a8f7-49d1-a1e3-1a88eb19e86c |
| Github Issue | https://github.com/SpiritLing/Comments/issues/8              |
| Gitee Issue  | https://gitee.com/SpiritLing/Comments/issues/I4VT3R          |

# .Net Core + EF + mysql 从数据库生成实体

> 因为 mysql 官方给出 MySql.Data.EntityFrameworkCore 新版本 8.0.20 版本，所以这里有了新的生成方式，并且几乎没有各种问题

## 安装 NuGet 包

```
Install-Package Microsoft.EntityFrameworkCore
Install-Package Microsoft.EntityFrameworkCore.Design
Install-Package Microsoft.EntityFrameworkCore.Tools
Install-Package MySql.Data.EntityFrameworkCore
```

都要安装新版，支持.net core 3.1

版本分别是，按照我使用安装时的版本为最低：

-   MySql.Data.EntityFrameworkCore 8.0.20 起步
-   其他为 3.1.6 起步

> 修改：因为已经出了.Net 5，所以版本已经升级到 5.x 了，如果你依旧使用的是.net core 3.x，则只需要安装到 3.x 最高版本即可，截至修改文章时版本号为 3.1.11

对于 `MySql.Data.EntityFrameworkCore` 目前最高版本为 8.0.22（截至修改文章时），但是在这个最高版本会出现一些问题（`Could not find type mapping for column 'blogsite.users.updatedTime' with data type 'datetime'. Skipping column. `），但是在 8.0.20 版本不会出现，所以一般请按照下面的版本号来使用

```
Install-Package Microsoft.EntityFrameworkCore -version 3.1.11
Install-Package Microsoft.EntityFrameworkCore.Design -version 3.1.11
Install-Package Microsoft.EntityFrameworkCore.Tools -version 3.1.11
Install-Package MySql.Data.EntityFrameworkCore -version 8.0.20
```

## 运行命令

```
Scaffold-DbContext "server=localhost;port=3306;user=root;password=123456789;database=sakila" MySql.Data.EntityFrameworkCore -OutputDir sakila -f
```

来源查看 mysql 官方文档：https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework-core-scaffold-example.html

## 以下都是旧版

原文地址：https://www.cnblogs.com/yangjinwang/p/9516988.html

## 安装 NuGet 包

点击 `工具` -> `NuGet包管理器` -> `程序包管理器控制台`

分别安装以下几个包

Mysql 版本：

```
Install-Package MySql.Data.EntityFrameworkCore -Pre
Install-Package Pomelo.EntityFrameworkCore.MySql
Install-Package Microsoft.EntityFrameworkCore.Tools
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

Sql server 版本：

```
Install-Package Microsoft.EntityFrameworkCore
Install-Package Microsoft.EntityFrameworkCore.SqlServer
Install-Package Microsoft.EntityFrameworkCore.Tools
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

## 运行命令生成实体

Mysql 版本：

```
Scaffold-DbContext "server=.;userid=tech5_kj;pwd=xxx;port=3306;database=tech5_kj;sslmode=none;" Pomelo.EntityFrameworkCore.MySql -OutputDir Models -Force

或者

Scaffold-DbContext "server=.;userid=tech5_kj;pwd=xxx;port=3306;database=tech5_kj;sslmode=none;" Pomelo.EntityFrameworkCore.MySql -OutputDir Models -UseDatabaseNames -Force
```

Sql server 版本:

```
Scaffold-DbContext "Data Source=.;Initial Catalog=EFCore_dbfirst;User ID=sa;Password=sa.123" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Force
```

## 参数说明

```
-OutputDir *** 实体文件所存放的文件目录
-ContextDir *** DbContext文件存放的目录
-Context *** DbContext文件名
-Schemas *** 需要生成实体数据的数据表所在的模式
-Tables *** 需要生成实体数据的数据表的集合
-DataAnnotations
-UseDatabaseNames 直接使用数据库中的表名和列名（某些版本不支持）
-Force 强制执行，重写已经存在的实体文件
```


| Property     | Content                                                      |
| ------------ | ------------------------------------------------------------ |
| Id           | f068c8ad-92e5-47c8-9c99-f716a6e7068d                         |
| Name         | 2021-01-19-asp-dot-net-core-linq-extension.md                |
| ArticleLink  | https://blog.spiritling.cn/articles/f068c8ad-92e5-47c8-9c99-f716a6e7068d |
| Github Issue | https://github.com/SpiritLing/Comments/issues/17             |
| Gitee Issue  | https://gitee.com/SpiritLing/Comments/issues/I54NWG          |

# Asp.Net Core 扩展 Linq，简化自定义

## 前言 -为什么需要扩展 `Linq` 方法

> Linq 在 .net 中使用是比较多的，而微软开发的 linq 相关函数无法满足实际项目开发中的需求，我们需要自己来扩展一些方法。

[c# 扩展方法](/posts/e24afe6f/)

在 Asp.Net Core 开发中或者其他的后端开发中都会有一个需求（尤其对于中台或者后台管理），那就是展示数据列表；当然不是普普通通的数据列表展示，而是需要进行**排序**、**分页**、**查询关键字**来获取列表。

甚至在有些时候需要三个同时处理来更精确的筛选数据，而对于 Asp.Net Core 来说，用的语言是 C#，是一门强类型语言，在许多时候具有很大便利性，但是正因为这种原因，在某些时候却不太方便使用，需要进行额外方式来进行处理，才可以达到目标。

## 普通查询

对于 Linq 查询来说，`Where` 和 `OrderBy` 使用时需要直接点出来属于或者字段才行，如下所示：

```csharp
// 数据结构
public class ArticleTag
{
    public int Id { get; set; }

    public Guid KeyId { get; set; }

    public string Name { get; set; }

    public string BgColor { get; set; }

    public string TextColor { get; set; }

    public string UrlParam { get; set; }

    public string Remark { get; set; }

    public DateTime CreatedTime { get; set; }

    public DateTime UpdatedTime { get; set; }
}
```

```csharp
public static void Test(){
  var articleTags = new List<ArticleTag>();
  // where 查找 Name中含有Admin的数据，orderby 通过 id 来进行排序
  var result = articleTags.Where(p => p.Name.Contains("Admin")).OrderBy(p => p.Id);
}
```

而所谓的一些限制，指的就是如上所示的，在进行 `where` 时，是通过 `.` 出来属性进行查询的，但是实际使用中，从前端传递过来的一般都是字符串 `"Name"`，而在后端进行查询时，以目前方式是无法将属性的`key`写到`where`函数中，也就无法执行查询通过`"Name"`来过滤数据；如果通过 `if...else` 来判断，那么将会是一个非常大的工程量，每个实体上面有 m 个属性，而一个项目中有 n 张表，那么几乎需要 `m*n`个判断进行处理，非常的差劲，不利于后续扩展和维护。

但是天无绝人之路，在 c#中拥有扩展方法、表达式目录树和反射，可以将上面的方式进行优化。

## 查询条件参数公共类型

先需要定义查询条件的公共参数，用于统一规范

```csharp
namespace BlogSite.CommonLib.CommonEntity
{
    /// <summary>
    /// 查询基本实体
    /// </summary>
    public class CoditionEntity
    {
        [JsonPropertyName("paginationKeyValue")]
        public PaginationKeyValue PaginationKeyValue { get; set; }

        [JsonPropertyName("orderByKeyValue")]
        public OrderByKeyValue OrderByKeyValue { get; set; }

        [JsonPropertyName("searchKeyValue")]
        public SearchKeyValue SearchKeyValue { get; set; }
    }
    /// <summary>
    /// 关键字搜索
    /// </summary>
    public class SearchKeyValue
    {
        private string _propertyName;
        [JsonPropertyName("enable")]
        public bool Enable { get; set; }
        [JsonPropertyName("propertyName")]
        public string PropertyName
        {
            get
            {
                // 因为前端传递的是小写字母开头的，但是在后端都是大写字母开头的，我们需要在获取时将首字母大写
                return _propertyName.ToUpperFirstLetter();
            }
            set
            {
                _propertyName = value;
            }
        }
        [JsonPropertyName("searchContent")]
        public string SearchContent { get; set; }
    }

    /// <summary>
    /// 排序字段格式
    /// </summary>
    public class OrderByKeyValue
    {
        private string _propertyName;
        [JsonPropertyName("enable")]
        public bool Enable { get; set; }
        [JsonPropertyName("propertyName")]
        public string PropertyName {
            get
            {
                return _propertyName.ToUpperFirstLetter();
            }
            set
            {
                _propertyName = value;
            }
        }
        [JsonPropertyName("orderDirection")]
        public OrderDirection OrderDirection { get; set; }
    }
    /// <summary>
    /// 分页格式
    /// </summary>
    public class PaginationKeyValue
    {
        [JsonPropertyName("enable")]
        public bool Enable { get; set; }
        [JsonPropertyName("count")]
        public int Count { get; set; }
        [JsonPropertyName("pageSize")]
        public int PageSize { get; set; }
        [JsonPropertyName("pageNumber")]
        public int PageNumber { get; set; }
    }

    /// <summary>
    /// 排序类型
    /// </summary>
    public enum OrderDirection
    {
        /// <summary>
        /// 升序
        /// </summary>
        [Description("升序")]
        ASC = 0,
        /// <summary>
        /// 降序
        /// </summary>
        [Description("降序")]
        DESC = 1
    }
}

```

建立了查询条件基本结构，然后需要基于这个结构来进行处理

## Linq 扩展方法

对于 Linq 扩展方法来说，需要使用到表达式目录树和反射等高级操作，本人目前对于此处理解不是太深，就越过这里了，直接说如何实现即可

```csharp
public static class LinqExtension
{
    /// <summary>
    /// Orders the by.
    /// </summary>
    /// <typeparam name="T"></typeparam>
    /// <param name="source">The source.</param>
    /// <param name="propertyName">Name of the property.</param>
    /// <param name="direction">The direction.</param>
    /// <returns></returns>
    public static IOrderedQueryable<T> OrderBy<T>(this IQueryable<T> source, string propertyName, OrderDirection direction)
    {
        switch (direction)
        {
            case OrderDirection.ASC:
                return source.OrderBy(ToLambda<T>(propertyName));

            case OrderDirection.DESC:
                return source.OrderByDescending(ToLambda<T>(propertyName));

            default:
                return source.OrderBy(ToLambda<T>(propertyName));
        }

    }

    /// <summary>
    /// Orders the by.
    /// </summary>
    /// <typeparam name="T"></typeparam>
    /// <param name="source">The source.</param>
    /// <param name="propertyName">Name of the property.</param>
    /// <param name="direction">The direction.</param>
    /// <returns></returns>
    public static IOrderedQueryable<T> OrderBy<T>(this IQueryable<T> source, string propertyName, int direction)
    {
        var dir = (OrderDirection)direction;

        switch (dir)
        {
            case OrderDirection.ASC:
                return source.OrderBy(ToLambda<T>(propertyName));

            case OrderDirection.DESC:
                return source.OrderByDescending(ToLambda<T>(propertyName));

            default:
                return source.OrderBy(ToLambda<T>(propertyName));
        }

    }


    /// <summary>
    /// Orders the by.
    /// </summary>
    /// <typeparam name="T"></typeparam>
    /// <param name="source">The source.</param>
    /// <param name="propertyName">Name of the property.</param>
    /// <returns></returns>
    public static IOrderedQueryable<T> OrderBy<T>(this IQueryable<T> source, string propertyName)
    {
        return source.OrderBy(ToLambda<T>(propertyName));
    }

    /// <summary>
    /// Orders the by descending.
    /// </summary>
    /// <typeparam name="T"></typeparam>
    /// <param name="source">The source.</param>
    /// <param name="propertyName">Name of the property.</param>
    /// <returns></returns>
    public static IOrderedQueryable<T> OrderByDescending<T>(this IQueryable<T> source, string propertyName)
    {
        return source.OrderByDescending(ToLambda<T>(propertyName));
    }

    public static IQueryable<T> Where<T>(this IQueryable<T> source, string propertyName, string content)
    {
        return source.Where(ToLambdaWhere<T>(propertyName, content));
    }

    public static IQueryable<T> WhereList<T>(this IQueryable<T> source, string propertyName, List<string> contentList)
    {
        return source.Where(ToLambdaWhere<T>(propertyName, contentList));
    }

    /// <summary>
    /// Where Lambda 表示式
    /// </summary>
    /// <typeparam name="T"></typeparam>
    /// <param name="propertyName"></param>
    /// <param name="contentList"></param>
    /// <returns></returns>
    private static Expression<Func<T, bool>> ToLambdaWhere<T>(string propertyName, List<string> contentList)
    {
        // 传递到表达式目录树的参数 x 和它的类型
        ParameterExpression x = Expression.Parameter(typeof(T));

        MemberExpression property = Expression.Property(x, propertyName);
        ConstantExpression constant = Expression.Constant(contentList, typeof(List<string>));

        var methodCall = Expression.Call(constant, "Contains", Type.EmptyTypes, property);


        var expression = Expression.Lambda<Func<T, bool>>(methodCall, new ParameterExpression[] { x });
        return expression;
    }

    /// <summary>
    /// Where Lambda 表示式
    /// </summary>
    /// <typeparam name="T"></typeparam>
    /// <param name="propertyName"></param>
    /// <param name="content"></param>
    /// <returns></returns>
    private static Expression<Func<T, bool>> ToLambdaWhere<T>(string propertyName, string content)
    {
        // 传递到表达式目录树的参数 x 和它的类型
        ParameterExpression x = Expression.Parameter(typeof(T));
        // 获取类型的属性
        PropertyInfo prop = typeof(T).GetProperty(propertyName);
        // 设定常量值相当于Contains中传递的值
        ConstantExpression constant = Expression.Constant(content, typeof(string));
        // 使用类型调用对应的属性
        var propExp = Expression.Property(x, prop);
        // 获取方法
        MethodInfo contains = typeof(string).GetMethod("Contains", new Type[] { });
        // 给属性调用上面获取的方法，传递值
        var methodCall = Expression.Call(propExp, contains, new Expression[] { constant });
        // 拼装成表达式目录树
        Expression<Func<T, bool>> expression = Expression.Lambda<Func<T, bool>>(methodCall, new ParameterExpression[] { x });
        return expression;
    }

    /// <summary>
    /// Converts to lambda.
    /// </summary>
    /// <typeparam name="T"></typeparam>
    /// <param name="propertyName">Name of the property.</param>
    /// <returns></returns>
    private static Expression<Func<T, object>> ToLambda<T>(string propertyName)
    {
        // 传递到表达式目录树的参数 x
        ParameterExpression x = Expression.Parameter(typeof(T));
        // 通过传递过来的属性字符串获取对应的属性
        MemberExpression property = Expression.Property(x, propertyName);
        // 创建一个表示类型转换运算的 UnaryExpression。
        // 使用 property.Type  会在最后转换时出现错误
        var propAsObject = Expression.Convert(property, typeof(object));

        Expression<Func<T, object>> expression = Expression.Lambda<Func<T, object>>(propAsObject, x);
        return expression;
    }
}
```

实际使用中可以直接使用上方的扩展方法，扩展方法做成后，只需要按照如下调用即可

```csharp
public static void Test(){
  var articleTags = new List<ArticleTag>();
  // 将属性key字符串传递进来就可以实现查询和排序，方便使用，PropertyName可以随意更改，当PropertyName不属于查询的类型中时，只会抛出错误，需要进一步处理
  var result = articleTags.Where("PropertyName","SerarchKeyword").OrderBy("PropertyName",1);
}
```

## 更进一步

当然在上面我们也定义了通用的查询条件，那么我们直接也可以再进一步扩展，来达到更好的使用方式

```csharp
public static class LinqMethod
{
    /// <summary>
    /// 使用自定linq扩展执行排序，查询，分页功能 item1: 未分页结果，item2：分页后的结果
    /// </summary>
    /// <typeparam name="T"></typeparam>
    /// <param name="source"></param>
    /// <param name="coditionEntity"></param>
    /// <returns></returns>
    public static Tuple<IQueryable<T>, IQueryable<T>> UseCoditionFind<T>(this IQueryable<T> source, CoditionEntity coditionEntity)
    {
        var query = source;
        var resultQuery = source;
        if (coditionEntity.SearchKeyValue != null && coditionEntity.SearchKeyValue.Enable && !coditionEntity.SearchKeyValue.SearchContent.IsNullOrWhiteSpace())
        {
            query = query.Where(coditionEntity.SearchKeyValue.PropertyName, coditionEntity.SearchKeyValue.SearchContent);
        }
        if (coditionEntity.OrderByKeyValue != null && coditionEntity.OrderByKeyValue.Enable)
        {
            query = query.OrderBy(coditionEntity.OrderByKeyValue.PropertyName, coditionEntity.OrderByKeyValue.OrderDirection);
        }
        if (coditionEntity.PaginationKeyValue != null && coditionEntity.PaginationKeyValue.Enable)
        {
            resultQuery = query.Skip((coditionEntity.PaginationKeyValue.PageNumber - 1) * coditionEntity.PaginationKeyValue.PageSize)
                .Take(coditionEntity.PaginationKeyValue.PageSize);
        }
        return new Tuple<IQueryable<T>, IQueryable<T>>(query, resultQuery);
    }
}
```

这样通过 linq 就可以直接调用 `UseCoditionFind` 然后获取返回的 query，然后再 `ToList` 获取数据，返回即可。方便使用，规范查询数据
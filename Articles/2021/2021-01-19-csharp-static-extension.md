

| Property     | Content                                                      |
| ------------ | ------------------------------------------------------------ |
| Id           | 35c297ed-f109-421e-b27a-a0d0b51b9fe6                         |
| Name         | 2021-01-19-csharp-static-extension.md                        |
| ArticleLink  | https://blog.spiritling.cn/articles/35c297ed-f109-421e-b27a-a0d0b51b9fe6 |
| Github Issue | https://github.com/SpiritLing/Comments/issues/16             |
| Gitee Issue  | https://gitee.com/SpiritLing/Comments/issues/I54NWK          |

# C# 扩展静态方法

## 扩展方法

> 扩展方法使你能够向现有类型“添加”方法，而无需创建新的派生类型、重新编译或以其他方式修改原始类型。 扩展方法是一种静态方法，但可以像扩展类型上的实例方法一样进行调用。
>
> -- [《Microsoft 官方文档》](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)

## 实现一个静态扩展方法

现在有个需求，需要对密码进行 SHA512 加密，才可以存放进入数据库中，但是每次通过其他方法调用比较麻烦，有没有一种可以直接连续点出来的方法呢？

连续点的方法一般都是静态方法，而且官方提供了静态方法扩展的可操作性，所以我们可以实现一个自定义的扩展方法。

而加密一般都为字符串，所以只需要在字符串上进行扩展即可。

扩展方法被定义为静态方法，但它们是通过实例方法语法进行调用的。 它们的第一个参数指定方法操作的类型。

参数前面是此修饰符。 仅当你使用 using 指令将命名空间显式导入到源代码中之后，扩展方法才位于范围中。

### 将类型放入到静态方法中

```c#
public static class StringExtension {
    public static string ConvertSHA512(this string currentStr){
        // 现在在函数参数中的 `currentStr` 就是这个函数的字符串
    }
}
```

`"name".SHA512()` 时，上面的 `currentStr` 就是 `"name"`这个字符串。

而所谓的静态类型并没有什么用，只是方便你用来区分扩展方法是那个范围的。

当然你在使用扩展方法时，需要引用当前静扩展函数所在的命名空间才行。

现在你在这个函数中已经拿到了当前执行函数的字符串，那么就可以对这个字符串进行一些操作了。

### 实现 SHA512 加密

```c#
public static class StringExtension {
    public static string ConvertSHA512(this string currentStr)
    {
        var bytes = Encoding.UTF8.GetBytes(currentStr);
        using (var hash = SHA512.Create())
        {
            var hashedInputBytes = hash.ComputeHash(bytes);

            // Convert to text
            // StringBuilder Capacity is 128, because 512 bits / 8 bits in byte * 2 symbols for byte
            var hashedInputStringBuilder = new StringBuilder(128);
            foreach (var b in hashedInputBytes)
                hashedInputStringBuilder.Append(b.ToString("X2"));
            return hashedInputStringBuilder.ToString();
        }
    }
}
```

在静态扩展方法中对传递进来的字符串进行加密，然后返回回去。当然你可以返回任何类型的数据，但是一旦返回的类型变化了，那么你在连点调用加密函数后，只能继续点返回的类型的方法，而不能在继续使用字符串的方法了。

## 一些扩展方法的例子

### 字符串相关的扩展方法

```c#
namespace BlogSite.CommonLib
{
    public static class StringExtension
    {
        /// <summary>
        /// 将字符串进行SHA512加密
        /// </summary>
        /// <param name="s"></param>
        /// <returns></returns>
        public static string ConvertSHA512(this string s)
        {
            var bytes = Encoding.UTF8.GetBytes(s);
            using (var hash = SHA512.Create())
            {
                var hashedInputBytes = hash.ComputeHash(bytes);

                // Convert to text
                // StringBuilder Capacity is 128, because 512 bits / 8 bits in byte * 2 symbols for byte
                var hashedInputStringBuilder = new StringBuilder(128);
                foreach (var b in hashedInputBytes)
                    hashedInputStringBuilder.Append(b.ToString("X2"));
                return hashedInputStringBuilder.ToString();
            }
        }
        public static bool IsNullOrWhiteSpace(this string s)
        {
            return string.IsNullOrWhiteSpace(s);
        }
        public static bool IsNullOrEmpty(this string s)
        {
            return string.IsNullOrEmpty(s);
        }
        /// <summary>
        /// 首字母大写
        /// </summary>
        public static string ToUpperFirstLetter(this string value)
        {
            if (value.IsNullOrWhiteSpace())
            {
                return string.Empty;
            }

            char[] letters = value.ToCharArray();
            letters[0] = char.ToUpper(letters[0]);

            return new string(letters);
        }
        /// <summary>
        /// 首字母小写
        /// </summary>
        public static string ToLowerFirstLetter(this string value)
        {
            if (value.IsNullOrWhiteSpace())
            {
                return string.Empty;
            }

            char[] letters = value.ToCharArray();
            letters[0] = char.ToLower(letters[0]);

            return new string(letters);
        }
        /// <summary>
        /// 字符串转大写
        /// </summary>
        public static string ToLower(this string value)
        {
            if (value.IsNullOrWhiteSpace())
            {
                return string.Empty;
            }

            char[] letters = value.ToCharArray();
            for (int i = 0; i < letters.Length; i++)
            {
                letters[i] = char.ToLower(letters[i]);
            }

            return new string(letters);
        }
    }
}

```

### `Enum` 枚举扩展方法

```c#
namespace BlogSite.CommonLib
{
    /// <summary>
    /// Enum 静态扩展方法
    /// </summary>
    public static class EnumExtension
    {
        /// <summary>
        /// 获取枚举值上的Description特性的说明
        /// </summary>
        /// <returns>特性的说明</returns>
        public static string GetDescription(this Enum en)
        {
            var type = en.GetType();
            FieldInfo field = type.GetField(Enum.GetName(type, en));
            if (!(Attribute.GetCustomAttribute(field, typeof(DescriptionAttribute)) is DescriptionAttribute descAttr))
            {
                return string.Empty;
            }

            return descAttr.Description;
        }
    }
}
```

## 结束

当然扩展方法在实际中很是常用的，扩展方法比较好写，但是比较难的在于函数内部的一些实现，所以这些需要多写写，多练练。
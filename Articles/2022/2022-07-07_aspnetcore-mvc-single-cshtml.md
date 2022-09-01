| Property     | Content                                                      |
| ------------ | ------------------------------------------------------------ |
| Id           | 64298416-2e35-4324-acd8-f199477aea26                         |
| Name         | 2022-07-07_aspnetcore-mvc-single-cshtml.md                   |
| ArticleLink  | https://blog.spiritling.cn/articles/64298416-2e35-4324-acd8-f199477aea26 |
| Github Issue |                                                              |
| Gitee Issue  |                                                              |

# AspNetCore 开发 MVC 页面时，将 cshtml页面单独放出来

## 添加 nuget 包

添加 `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` 包

## 配置项目设置

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net6.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="6.0.2" />
  </ItemGroup>

  <ItemGroup>
    <ViewFiles Include="$(ProjectDir)\Views\**\*.cshtml" />
  </ItemGroup>
  
  <Target Name="CopyViewFilesAfterPublish" AfterTargets="Publish">
    <Copy SourceFiles="@(ViewFiles)"
      DestinationFolder="$(PublishDir)\Views\%(RecursiveDir)"
    />
  </Target>

</Project>
```

然后编译后的代码即可出现单独的cshtml,并没有被编译到 dll 里面

但是请注意，在正式生产环境中不推荐这个做法，可以在测试环境或者开发环境中使用，方便修改，不用每次需要重新发布

参考来源：https://stackoverflow.com/a/71108043
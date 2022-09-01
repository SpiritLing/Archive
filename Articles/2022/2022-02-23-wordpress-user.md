| Property     | Content                                                      |
| ------------ | ------------------------------------------------------------ |
| Id           | 7a53831d-7a66-459c-a909-ef1069ec0576                         |
| Name         | 2022-02-23-wordpress-user.md                                 |
| ArticleLink  | https://blog.spiritling.cn/articles/7a53831d-7a66-459c-a909-ef1069ec0576 |
| Github Issue | https://github.com/SpiritLing/Comments/issues/11             |
| Gitee Issue  | https://gitee.com/SpiritLing/Comments/issues/I54NWN          |

# WordPress 使用

## 编辑器

对于重度使用Markdown的我来说，找一个博客系统首先考虑的就是是否可以使用markdown写作。

所以，在workpress首先将其插件 `WP Githuber MD` 安装上，更改默认的编辑器。

## 插件

`Advanced Custom Fields: Theme Code` 高级自定义字段，虽然我使用的不是很强烈，并且不太会使用，但是不妨碍我去安装它

`FileBird Lite` 媒体库创建文件夹

`UpdraftPlus-备份/恢复` 对站点进行备份和恢复，如果有其他方式备份和恢复可以不使用

`wp外链跳转插件` 外链跳转时进行提示，配合CorrPress主题使用

`WP资源下载管理` 添加下载链接

`XML 站点地图 & Google News` 生成站点地图

## 主题

`corepress` 地址：https://github.com/ghboke/CorePressWPTheme

`WebStack 导航主题` 地址：https://github.com/owen0o0/WebStack

## 评论

对于评论来说，如果使用原始自带的评论可能会出现一些垃圾评论，反垃圾插件大多数属于付费功能。

为了安全起见，我对评论进行额外处理，每篇文章都会分配一个不重复的 `Guid` ，对每一篇文章会在 `github` 上面创建一个 `issue` ，在内容中注明 唯一ID，填写文章地址。

在使用高级自定义字段和 `gitalk`（修改后的，取消登录功能，只能查看）配合后，创建一个在文章页面只能看评论，需要评论的只能去 github 的 issue 上面进行评论。

可以有效解决存放问题和垃圾评论问题，以及没有账号的问题。当然对于没有 github 的（文章大多数为计算机相关的内容，一般来说访问的人员应该都有github账号），可以点击下面的站点反馈链接，到兔小巢进行发帖询问。下一个阶段可能会添加 gitee 的 issue 地址

### 高级自定义字段使用

```php
<?php 
    $link = get_field('github_issue');
    if( $link ): 
        $link_url = $link['url'];
        $link_title = $link['title'];
        $link_target = $link['target'] ? $link['target'] : '_self';
        ?>
        <a class="button" href="<?php echo esc_url( $link_url ); ?>" target="<?php echo esc_attr( $link_target ); ?>"><?php echo esc_html( $link_title ); ?></a>
	<?php endif; ?>
```

在搭配上 `button` 的样式即可实现字段的自定义。
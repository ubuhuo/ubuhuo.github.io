title: "关于维基"
layout: "page"
---

## 小白维基的由来

「维基」这个概念在我脑海中最早形成的具体形象是「库」，最早在看一篇文章讲某位作家（还是记者来着）看完书后都会将书「撕碎」，把碎片按照自己制定好的分类区分开来，如此整理归类以便之后查阅使用。

最终影响我把这个东西实现出来的是博友 [三无计划][1] 的一篇博文：[为什么每个人都应该有自己的 Wiki][2]。

看完这篇文章没多久我就仿照他的基本配置将原本糅杂在博客中的技术类文章全部剥离出来到维基站点上，而博客站点也就只拿来写一些生活方面的规划和思考。

## 小白维基的配置

正如我的网站介绍：语言、框架、架构，技术有关的一切都在这里。

我通过一些简单的「大类别」将所有的文章做了一个粗略的划分，而后在每次写新文章之前挑选一个『大类别』进行匹配并辅以更精确的「小类别」，最后利用 [Hexo][3] 配合 [Wikitten][4] 主题渲染，已达到按照文章分类进行分级的维基站点。

全部技术栈如下：

博客渲染：[Hexo][3]
博客主题：[Wikitten][4]
代码托管：[GitHub][12]
数据分析：[百度统计][5]、[谷歌分析][6]

链接提交：
谷歌：sitemap
百度：sitemap 配合 [hexo-baidu-url-submit][7] 插件

Hexo 插件列表：

| 插件名称 | 插件功能 |
| --- | --- |
| [hexo-abbrlink][8] | 随机生成唯一 URL |
| [hexo-autonofollow][9] | 为非本站链接添加 no-follow 属性 |
| [hexo-baidu-url-submit][7] | 每次更新博客时主动推送文章链接到百度站长 |
| [hexo-generator-sitemap][10] | 自动生成 sitemap |
| [hexo-genertaor-baidu-sitemap][11] | 自动生成百度 sitemap |

## Timeline

### 2020-04-26

开启时间线，记录维基站点变(mo)更(gai)历史

### 2020-04-26

修改文章时效性提示样式

[1]: https://www.imalan.cn/
[2]: https://blog.imalan.cn/archives/108/
[3]: https://hexo.io
[4]: https://github.com/zthxxx/hexo-theme-Wikitten
[5]: https://tongji.baidu.com/web/welcome/login
[6]: https://analytics.google.com/analytics/
[7]: https://github.com/huiwang/hexo-baidu-url-submit
[8]: https://github.com/rozbo/hexo-abbrlink
[9]: https://github.com/liuzc/hexo-autonofollow
[10]: https://github.com/hexojs/hexo-generator-sitemap
[11]: https://github.com/coneycode/hexo-generator-baidu-sitemap
[12]: https://github.com
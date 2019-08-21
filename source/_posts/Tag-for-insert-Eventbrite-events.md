---
title: 'Tag Eventbrite 插件源码分析'
date: 2019-08-22 01:54:37
tags:
- 源码分析
- 插件
- 拓展标签
categories:
- 插件源码分析
---
这个插件可以将你的 Eventbrite 账户中的 Events 显示在 Hexo 文章中。

<!--- more ---->

项目地址：https://github.com/g100g/hexo-tag-eventbrite

插件作用：将用户 [Eventbrite][1] 中的活动展示在项目中。

## index.js

这个项目引用了多个依赖包，包括 

- moment： 用于时间的 format
- lodash： 用于加载依赖
- nbrite： Eventbrite 的 Node.js 的 API Client


在这个文件中，我们首先要看一下底部的注册方法

```javascript
tags.register('eventbrite', eventbrite, {
  async: true
});
```

这个方法注册了一个新的 tag，从而我们可以用 eventbrite 标签来完成我们想要的特性。而其中的 `async` 参数，则可以将这个方法的返回值设置为 promise。

> 相关方法的文档:[Link][2]

随后，我们看一下其注册的方法 eventbrite，这个方法表示我们在执行 `eventbrite` 标签后，会调用 `eventbrite` 方法。

这个方法的内容比较简单，调用了 `getToken` 和 `getTemplate`，并获取对应的内容。


最后，调用 nbrite 提供的接口，获取到用户的信息，并在其回调中，与 template 进行整合，从而返回经过美化后的数据。

这里需要注意，在与模板进行数据整合时，数据使用 map 方法进行了 format ，从而确保展示的数据更加的友好,具体可以看  `beatifyDate` 和 `howManyTime` 方法。

相关文件源码：https://github.com/G100g/hexo-tag-eventbrite/blob/master/index.js

## Question

1. 这个项目还有什么可以优化的点？
   答：可以将配置从 Config 移动到 Hexo 的配置文件中。

[1]:https://www.eventbrite.com/
[2]:https://github.com/hexojs/hexo/blob/651f34bf6d2be5cb9c07009e857a73ecad3b414c/lib/extend/tag.js#L15-L44
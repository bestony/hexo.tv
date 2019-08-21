---
title: Rage Face Tag 插件分析
date: 2019-08-22 02:19:16
tags:
- 源码分析
- 插件
- 拓展标签
categories:
- 插件源码分析
---
这个插件可以帮你快速插入 memes.at 的表情包。

<!--- more --->

项目地址：https://github.com/xunnanxu/hexo-tag-rage-face

插件作用：帮你快速插入 memes.at 的表情包。

## index.js 

这个项目的作者在写作时是十分简单，除了 regsiter 以外，只有两个辅助的函数，作者<del>可能是为了少写一个函数</del>，直接在 register 的参数中写具体的函数当然，**并不推荐大家这样做**。

这个项目的引用中，一个值得学习的点，便是引入了 Hexo 的 Logger，可以方便进行后续的调试

```javascript
var logger = require('hexo-log')(hexo.env);
```

其次，在代码中，实现了一个 [parseArgs 方法][1]，这个方法完成了对于 K:V 形态的参数的解析，从而方便在后续生成标签时，加入具体的样式和属性。

其他值得学习的倒是不多，便不再赘述。如果有问题，可以联系我。

## Qustion

1. hexo-log 的方法有哪些。

[1]:https://github.com/xunnanxu/hexo-tag-rage-face/blob/1935704c7d2145db20d8c09a4825c82dbfcfdf47/index.js#L38-L52
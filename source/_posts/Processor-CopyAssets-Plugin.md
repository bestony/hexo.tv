---
title: Processor CopyAssets 插件源码分析
date: 2019-08-22 01:26:02
tags:
- 源码分析
- 插件
- Assets
categories:
- 插件源码分析
---

这是一个 Hexo 的文件处理插件，能自定义 source 中的指定文件或文件夹，在执行generate命令的时候能直接复制到public文件夹中而不经过渲染。

<!--- more --->

项目地址：https://github.com/f111fei/hexo-processor-copyassets

插件作用：可以指定特定文件不做预处理，直接 Copy 到目标目录

这个插件使用的是 Hexo 的处理器 processor 相关的 API 来实现的功能。

## index.js

首先，在其项目根目录的 `index.js` 文件中，初始化了 processor 变量，用于 Hexo 中的[插件注册][1]。

插件注册部分需要注意的是，**传入的变量为待处理的文件的路径**。所以，在该插件的 regsiter 代码中，先行获取了系统配置，如果尚未配置，则直接推出 false，表示不处理该文件。

如果配置了路径，则对其进行 split 处理，从而拆解出各自的路径，方便进行后续的比对。

在拆解完成后， 进行了一个 O(N) 级的循环进行对比，如果传入的 path 等同于配置中设定的数据，就返回 true。

> 这里有两处需要注意的点：
> 1. 对比使用的是 `===` 三等号进行对比
> 2. 这里的对比进行了 substring 的对比，从而只对比相同长度的，从而确保设定了整个目录，可以将目录下的文件也对比到。

相关代码如下：

```javascript
var rule = function(path){
  if (!config.copyAssets) return false;
  var array = config.copyAssets.split(",");
  for(var i in array)
  {
	  if(path.substring(0,array[i].length) === array[i])
	  {
		  return true;
	  }
  }
  return false;
}
```

对应代码路径：https://github.com/f111fei/hexo-processor-copyassets/blob/3fe878af4724632709ea4ee0d9d1df81e9cf269c/index.js#L10-L21

## copyAssets.js

在第二部分，我们看一看具体执行 copyAssets 的功能实现。在这一部分，定义了 module。

这个 module 中的核心方法接受两个参数，分别是 data 和 callback，根据官方的[说明][1],其中的 data 是文件内容。

随后实例化了 [Assets Model][2] ，这个 Model 将会帮助我们去处理文件。

随后，根据文件的状态，执行删除操作(如果文件被标记为 delete )。

后续则判断该文件是否存在，如果以及存在，则更新 Path ，并标记为 update。

如果文件不存在，则执行重新插入。

```javascript
module.exports = function(data, callback){
  var Asset = hexo.model('Asset'),
    source = data.source.substring(hexo.base_dir.length),
    doc = Asset.get(source);


  if (data.type === 'delete'){
    if (doc){
      hexo.route.remove(data.path);
      doc.remove();
    }


    return callback();
  }


  if (doc){
    doc.path = data.path;
    doc.modified = data.type === 'update';


    doc.save(function(){
      callback();
    });
  } else {
    Asset.insert({
      _id: source,
      path: data.path,
      modified: true
    }, function(){
      callback();
    });
  }
};
```


## Question
1. Hexo 中文件的 4 种状态都是什么，以及其作用是什么？
2. Assets 的几个方法是如何定义的？


[1]:https://hexo.io/zh-cn/api/box#%E5%A4%84%E7%90%86%E5%99%A8%EF%BC%88Processor%EF%BC%89
[2]:https://github.com/hexojs/hexo/blob/master/lib/models/asset.js

# new NPM  

## 1. git
>Git是目前世界上最先进的分布式版本控制系统（没有之一）。 
[Git教程-廖雪峰](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/)  

## 2.加载一个本地的包  
加载一个本地的包，首先需要把项目目录作为一个包来安装
```
npm install -g .
```  

这里遇到一个问题，加载这个包之后依然无法使用require.resolve('greet')找到它  
原因是node的环境变量配置的有问题（干他娘的windwos）    

[npm install -g库后，运行说找不到。](https://cnodejs.org/topic/540d4276b4636f296a746919)  

## 3.构建npm脚本  

对于 npm 包，使用内置的 npm-scripts 来定义一些简单的项目任务。    
修改 `package.json`  

```
"scripts": {
  "build": "babel src --out-dir lib",
  "watch": "babel src --out-dir lib --watch",
  ...
},
```
然后就可以使用 `npm run <scriptsKey>` 来执行这些任务了。



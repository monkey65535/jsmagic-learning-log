# hello npm

## 1.npm概述  
>npm 是 Node 的模块管理器，功能极其强大。它是 Node 获得成功的重要原因之一。  

## 2.安装模块
安装别人写好的模块
```
$npm install
```  

`npm install` 命令用来安装模块到 `node_modules` 目录。
```
npm install <packageName>
```
安装之前，npm install会先检查，node_modules目录之中是否已经存在指定模块。如果存在，就不再重新安装了，即使远程仓库已经有了一个新版本，也是如此。  
如果需要强制覆盖安装，需要使用`-force`命令
```
npm install <packageName> -force
```
## 3.更新模块
如果需要更新已安装的模块，使用`update`命令
```
$npm update <packageName>
```
npm使用`registry服务`来查找模块的最新信息 它的查询网址是  

[registry:https://registry.npmjs.org/](https://registry.npmjs.org/)  

网址后面跟上模块名，就会得到一个JSON对象，里面是该模块所有版本的信息。  

## 4.缓存目录  
npm install或npm update命令，从 registry 下载压缩包之后，都存放在本地的缓存目录。
这个缓存目录，在 Linux 或 Mac 默认是用户主目录下的.npm目录，在 Windows 默认是%AppData%/npm-cache。通过配置命令，可以查看这个目录的具体位置。  

下面以windwos为例：  
这个路径为C:\Users\你的用户名\AppData\Roaming  
*注意：AppData是一个隐藏文件夹*  

![image_1atlto9m21i1f39v8h51jcan8u9.png-21.6kB][1]  

 以babel为例，查看目录结构 
 ![image_1atlu1kqh11pe6orb6b1i5e8c413.png-19.7kB][2]  
 
 打开package文件 
![image_1atltvrv3brc1u12fgl1bq917n0m.png-26.7kB][3]  

除此之外，还会生成一个`{cache}/{hostname}/{path}/.cache.json`文件。比如，从 npm 官方仓库下载react模块的时候，就会生成`registry.npmjs.org/react/.cache.json`文件。这个文件保存的是，所有版本的信息，以及该模块最近修改的时间和最新一次请求时服务器返回的 ETag 。

## 5.模块安装的过程  

 - 发出npm install命令
 - npm 向 registry 查询模块压缩包的网址
 - 下载压缩包，存放在~/.npm目录
 - 解压压缩包到当前项目的node_modules目录  

注意，一个模块安装以后，本地其实保存了两份。一份是~/.npm目录下的压缩包，另一份是node_modules目录下解压后的代码。

但是，运行npm install的时候，只会检查node_modules目录，而不会检查~/.npm目录。也就是说，如果一个模块在～/.npm下有压缩包，但是没有安装在node_modules目录中，npm依然会从远程仓库下载一次新的压缩包。
 
**为了解决这些问题，npm 提供了一个--cache-min参数，用于从缓存目录安装模块。**
`--cache-min`参数指定一个时间（单位为分钟），只有超过这个时间的模块，才会从 registry 下载。 

```
 $ npm install --cache-min 9999999 <packageName>
 //或者
 $ npm install --cache-min Infinity <packageName>
```
**但是，这并不等于离线模式，这时仍然需要网络连接**

## 6.cnpm
由于npm的服务器搭建在国外，我大天朝局域网的情况大家也是懂得的，为了防止npm抽风，阿里的前辈为大家做了一个福利：无论你有没有“被墙”，阿里的福利就在这： 淘宝 NPM 镜像 ；这是一个完整 [npmjs.org 镜像](http://npm.taobao.org/)，你可以用此代替官方版本(只读)，同步频率目前为10分钟一次以保证尽量与官方服务同步；你可以使用cnpm命令行工具替代默认的NPM；还有很多镜像，包括对于Nodejs你所需要的众多重要信息资料；  

### 6.1 使用cnpm代替npm
你可以使用我们定制的 `cnpm` (gzip 压缩支持) 命令行工具代替默认的 `npm`:
```
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
```
或者你直接通过添加 npm 参数 alias 一个新命令:
```
alias cnpm="npm --registry=https://registry.npm.taobao.org \
--cache=$HOME/.npm/.cache/cnpm \
--disturl=https://npm.taobao.org/dist \
--userconfig=$HOME/.cnpmrc"

# Or alias it in .bashrc or .zshrc
$ echo '\n#alias for cnpm\nalias cnpm="npm --registry=https://registry.npm.taobao.org \
  --cache=$HOME/.npm/.cache/cnpm \
  --disturl=https://npm.taobao.org/dist \
  --userconfig=$HOME/.cnpmrc"' >> ~/.zshrc && source ~/.zshrc
```
#### 6.1.1 安装模块
只要使用cnpm代替npm 就可以随意安装模块了，再也不怕npm抽风了！
```
$ cnpm install <packageName>
```
**注意注意：不要在webstrom的命令行工具中使用cnpm，会导致webstrom假死**  

#### 6.1.1 同步
直接通过 sync 命令马上同步一个模块, 只有 cnpm 命令行才有此功能:
```
$ cnpm sync connect
```
当然, 你可以直接通过 web 方式来同步: /sync/connect
```
$ open https://npm.taobao.org/sync/connect
```
#### 6.1.2 其它命令

支持 npm 除了 publish 之外的所有命令, 如:

```
$ cnpm info connect
```

## 7 package.json
生成`package.json`  
```
$npm init
```
然后一路按OK就可以初始化一`个package.json`文件了  
这是一个package.json文件的例子（有些内容可能初始的时候没有，但是随着操作会逐步加进去）
```
{
  "name": "hello-npm",   //项目名称（必须）
  "version": "1.0.0",   //项目版本（必须）
  "description": "",   //项目描述（必须）
  "homepage": "",   //项目主页
  "repository": {    //项目资源库
    "type": "git",
    "url": "https://git.oschina.net/xxxx"
  },
  "scripts": {      //运行脚本命令的npm命令行缩写
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": {    //项目作者信息
    "name": "dilidili",
    "email": ""
  },
  "license": "ISC",    //项目许可协议
  "devDependencies": {    //项目运行所依赖的模块
    "gulp": "^3.8.11",
    "gulp-less": "^3.0.0"
  },
  "devDependencies":{   //指定项目开发所需要的模块  如果使用cnpm加载依赖，这里会有很长一串内容
  }
}
```

## 8.安装项目依赖
```
npm install <packageName> --save-dev
```
其中 **--save-dev - 将这些包作为开发依赖来添加**
 
 
参考资料：  
[npm 模块安装机制简介:阮一峰](http://www.ruanyifeng.com/blog/2016/01/npm-install.html)   
[package.json文件:阮一峰](http://javascript.ruanyifeng.com/nodejs/packagejson.html) 




  [1]: http://static.zybuluo.com/dilidili/maarocvvdbwl55owwpl4tntc/image_1atlto9m21i1f39v8h51jcan8u9.png
  [2]: http://static.zybuluo.com/dilidili/61o9o46t3f8n45dgh8cgzo2m/image_1atlu1kqh11pe6orb6b1i5e8c413.png
  [3]: http://static.zybuluo.com/dilidili/3gw8blwxa79wfiyjtf3wl7c2/image_1atltvrv3brc1u12fgl1bq917n0m.png
  [4]: http://static.zybuluo.com/dilidili/r36mmk3yldqdsd2o9076vfls/image_1atlutu6ssttv1d1i9c1obv1lf91g.png



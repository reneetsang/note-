## module

模块分为三大类：核心模块、内置模块、第三方模块

#### 内置模块

- 内置模块就是不需要./或者../，如：let fs=require('fs')，可以直接引用，不需要下载天生自带

#### 核心模块

####　第三方模块

- 如果require函数只指定名称则视为从node_modules下面加载文件，这样的话你可以移动模块而不需要修改引用的模块路径
- 第三方模块的查询路径包括module.paths和全局目录

```javascript
//判断文件是否存在
let fs=require('fs')
let flag=fs.accessSync('./5.module/1.txt') //文件找到了就不会发生任何异常
```

```javascript
//解决路径问题 常用
let path=require('path')
//resolve方法你可以给她一个文件名，它会按照当前运行的路径，拼出一个绝对路径
//__dirname 当前文件的所在路径，和cwd有区别，cwd可以更改，这个不行
console.log(path.resolve(__dirname,'a')) //解析绝对路径
console.log(path.join(__dirname,'a')) 
//区别
console.log(path.resolve('b','a')) //解析绝对路径
console.log(path.join('b','a')) //b\a 只单单拼路径用，可以传递多个参数 

//获取基本路径
path.basename('a.js','.js') //经常用来获取除了后缀的名字
path.extname('a.js') //获取文件的后缀（最后一个.后的内容）
path.delimiter； //看环境变量是用什么符号隔开的。window下用； mac和linux是：
path.sep//文件路径window用\分隔符，linux用/

//vm 虚拟机模块 
let vm=require('vm') //很像eval，但eval是依赖于环境的
var a=1;
vm.runInThisContext('console.log(a)') //只在干净的作用域下执行，所以这里报错
```

commonjs规范，规定了每一个文件都是一个模块，模块之间是相互独立的

规范定义了，导出的时候用module.exports

定义了，引用一个模块用require

一个模块外面会给你加了一个闭包

```javascript
(function(exports,require,module,__dirname,__filename)){}}
```

模块可以省略后缀，如果是js或者json或者node文件可以省略后缀

```javascript
let result=require('./school');
result=require('./school');
console.log(result); //只会读取一次，同步读取，为了节约性能，还有缓存，将module.exports后面的结果进行缓存，如果有直接把缓存返回去
```

如果自己想实现require功能




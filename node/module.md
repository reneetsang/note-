## module

模块分为三大类：核心模块、内置模块、第三方模块

#### 文件模块、自定义模块

这个就是我们自己写的

#### 核心模块、内置模块

fs path等 

内置模块就是不需要./或者../，如：let fs=require('fs')，可以直接引用，不需要下载天生自带

- fs模块

```javascript
//判断文件是否存在
let fs=require('fs')
let flag=fs.accessSync('./5.module/1.txt') //文件找到了就不会发生任何异常
```

- path模块

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
```

- vm模块

  ```javascript
  //vm 虚拟机模块 
  let vm=require('vm') //很像eval，但eval是依赖于环境的
  var a=1;
  vm.runInThisContext('console.log(a)') //只在干净的作用域下执行，所以这里报错
  ```

####　第三方模块

- 如果require函数只指定名称则视为从node_modules下面加载文件，这样的话你可以移动模块而不需要修改引用的模块路径
- 第三方模块的查询路径包括module.paths和全局目录

#### 实现module模块

- commonjs规范，规定了每一个文件都是一个模块，模块之间是相互独立的
- 规范定义了，导出的时候用module.exports
- 定义了如何引用一个模块require

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

```javascript
//一个模块外边给你加了一个闭包(function (exports, require, module, __dirname, __filename) {})
//模块可以省略后缀 如果是js或者json或者node文件可以省略后缀
console.time('school');
// let res = require('./school')
// 同步读取，并且为了节约性能，还有缓存，将 module.exports 后边的结果进行缓存， 如果又缓存，直接返回结果
//引入需要的模块
let path = require('path')
let fs = require('fs')
let vm = require('vm')
//1.定义模块
function Module (filename) {
  this.filename = filename
  this.exports = {}
}
//2.设置模块可以解析的文件后缀
Module._extentions = ['.js', '.json', '.node']
//3.创建缓存对象
Module._cache = {}

/**
 * #desc 文件路径处理模块
 * @param  {string} filename 解析的文件名或文件名+路径
 * @return {none}
 */
Module.resolvePathName = filename => {
  // 1.拿到路径,进行解析绝对路径
  let p = path.resolve(__dirname, filename)
  //2.判断路径里是路径还是文件名,如果是文件名的话查找文件
  if(!path.extname(p)) {
    //4.如果有文件名,则确定是一个文件,开始
    for(var i =0, arr = Module._extentions, len = arr.length; i < len; i++) {
      let newPath = `${p}${arr[i]}`
      //如果访问的文件不存在， 就会发生异常
      try {
        fs.accessSync(newPath)
        return newPath
      } catch(e) {}
    }
  } else {
    // 3.如果没有文件名,则进行模块化加载:查找同名文件夹下的package.json || index.js
    // 这里没有做处理,只是做了防止报错
    try {
        fs.accessSync(p)
        return p
      } catch(e) {}
  }
}

/**
 * @desc js文件读取模块
 * @param  {object} module 文件引用对象,
 * @return {none}     没有返回值
 */
Module._extentions['js'] = module => {
  // 1.读取js文件的内容
  let script = fs.readFileSync(module.filename, {charset:'utf-8'});
  // 2.使用wrap方法拼接成一个字符串function
  let fnStr = Module.wrap(script)

  //3.在这里拿到函数执行后的结果通过
  //module.exports 当前方法执行的上下文对象
  //module.exports 解析后的值赋值给这个对象那个
  //req 文件加载方法，相当于require
  //module 当前的实例对象
  vm.runInThisContext(fnStr).call(module.exports, module.exports,req, module)

}
/**
 * @desc module 模块解析json文件的方法
 * @param  {object} module 当前的module实例
 * @return {string}        module.exports对象
 */
Module._extentions['json'] = module => {
  // 1.使用fs读取到文件内容
  let script = fs.readFileSync(module.filename);
  // 2.使用JSON的parse方法将字符串转为对象并且赋值给module.expors对象
  module.exports = JSON.parse(script)
  return module.exports
}
/**
 * @desc 函数执行字符串拼接使用的字符串
 * @type {Array}
 */
Module.wrapper = [
  '(function (exports, require, module, __dirname, __filename) {', '})'
]
/**
 * @desc 包裹函数，用来封装执行js的函数
 * @param  {string} script js文件代码
 * @return {string} 拼接好的字符串function
 */
Module.wrap = script => {
  return `${Module.wrapper[0]}${script}${Module.wrapper[1]}`
}
/**
 * @desc 文件引入方法
 * @param  {string} filename 文件名或者文件名+路径
 * @return {none}
 */
Module.prototype.load = function (filename) {
  //模块可能是json 也有可能是js
  let ext = path.extname(filename).slice(1) //去掉拿到文件后缀名上多余的. .js || .json
  //这里的this是Module的实例对象, 根据不同的文件后缀，调用不同的解析方法
  Module._extentions[ext](this)
}

/**
 * @desc 文件引用方法
 * @param  {string} filename 文件名或者文件名+路径
 * @return {object}          返回module,exports对象
 */
function req (filename) {
  //1.我们需要一个绝对路径来，缓存是根据绝对路径的来的
 filename =  Module.resolvePathName(filename)
 if(!filename) return new Error('not find file')
  //判断是否有缓存，有的话返回缓存对象
 let cacheModule = Module._cache[filename]
 if(cacheModule) return cacheModule

  // 2,没有模块 创建模块
  let module = new Module(filename) //创建模块

  //3.加载这个模块{filename: 'c:xx', exports: 'hello world'}
  module.load(filename)
  //4.把夹在好的模块加入缓存
  Module._cache[filename] = module
  return module.exports

}

// let str = req('./school')
let json = req('./school.json')
console.log(json);
console.time('school');
//以下就是exports和module.exports的却别
//exports = module.exports = somethings
//module.exports = somethings
//exports = module.exports
```




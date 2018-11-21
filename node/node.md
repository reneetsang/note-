## node

1.什么是Node

node是基于v8引擎（谷歌浏览器的引擎）渲染JS的工具或者环境。

安装node->把JS代码放到node环境中执行

2.安装Node

node安装完成后

当前电脑会自动安装了npm（Node Package Manager）：一个JS模块（所有封装好可以供其他人调取使用的都可以称之为模块或者包）管理工具，基于npm可以安装下载JS模块

它会生成一个node执行的命令（可以在DOS窗口或者终端命令中执行），如node xxx.js

如果不成功，可以找相同电脑配置的人，把安装成功的Node文件夹拷贝到自己的电脑上，通过配置环境变量，来实现Node实现

3.如何在node中渲染和解析js

->REPL模式（read-evaluate-print-loop,输入-求值-输出-循环）

->直接基于Node来执行js文件

- 在命令窗口中执行（DOS窗口&终端窗口）
- WB中的Terminal中也可以执行Node命令
- 直接在WB中执行，右键run xxx.jsx                                                                                                                                                                                                                                                                              

4.之所以把node作为后台编程语言，是因为：

- 我们可以把node安装在服务器上
- 我们可以把编写的JS代码放到服务器上，通过node来执行它（我们可以使用JS来操作服务器，换句话说，可以使用JS来实现服务器端的一些功能=》其实说Node是后台语言，想要表达的是JS是后台语言，JS是一门全栈编程语言）

5.node做后台的优势和特点

传统后台语言：Java/Python/PHP/C#(.NET)...

node特点：

- 单线程的
- 基于V8引擎渲染：说明快
- 异步无阻塞的I/O操作：I/O(inout/output)对文件的读写
- event-driven事件驱动：类似于发布订阅或者回调函数

6.在WB中开启node内置方法的代码提示

file->settings->Languages & Frameworks->Node.js and NPM->点击Enable

------

JS运行在客户端浏览器中=》前端

- 浏览器给JS提供了很多全局的属性和方法，例如window.xxx(setInterval、setTimeout、eval、alert、JSON...)
- 前端（浏览器运行JS）是限制I/O操作的
- input type='file' 这种算是I/O操作的，但是需要用户手动选择，而且仅仅是一个读取不是写入

JS运行在服务器端的node中=》后台

- node也给JS提供很多的内置属性和方法，例如http、fs、url、path...等对象中，都提供了很多API供JS操作
- node中运行JS是不需要限制I/O操作的

## NPM应用

目前”工程化/自动化“开发（不一定是写后台），都是基于NODE环境，基于NPM管理模块，基于webpack实现模块之间的依赖打包，部署上线等

npm常规操作

```
npm install xxx 把模块安装到当前目录下（在哪个目录下执行的命令，这个目录就是当前目录）
npm view xxx > xxx.verson.txt 查看模块的历史版本信息（npm view jquery > jquery.verson.txt)
npm install xxx@xxx 安装指定版本号的模块 
npm isntall xxx -g 把模块安装在全局目录下
npm uninstall xxx / npm uninstall xxx -g 卸载模块
```

### 切换下载源

npm的默认安装源都是在https://www.npmjs.com网站中查找的，在国内安装，下载速度比较慢，想要下载速度快一些，可以如下处理：

1. 使用淘宝镜像

   - 安装cnpm，后期都是基于cnpm安装管理

   - 安装nrm切源工具，基于nrm把源切换到淘宝源上，这样处理完成后，后期模块的管理依然基于npm即可

     ```
     npm install nrm -g
     nrm ls 查看当前可用源
     nrm use xxx 使用某个xxx源
     ```

2. 基于yarn安装，它也是模块管理器，类似于NPM，但是安装管理的速度比npm快很多。使用yarn只能把模块安装到当前目录下，不能安装到全局环境下

   ```
   npm install yarn -g
   yarn add xxx
   yarn remove xxx
   ```

### npm安装之配置清单和跑环境

在本地项目中基于Npm/yarn安装第三方模块

第一步：在本地项目中创建一个"package.json"的文件。

npm init -y（推荐，信息详细点）或者yarn init -y 生成package.json配置清单，创建配置清单的时候，项目目录不要出现中文和特殊字符，有可能识别不了。

不手动创建，基于yarn安装会默认生成一个“配置清单”，但信息没有手动创建的全面

作用：把当前项目所有依赖的第三方模块信息（包含：模块名称以及版本号等信息）都记录下来：可以在这里配置一些可执行的命令脚本等。

------

第二步：安装

npm安装：

- 开发依赖：只有在项目开发阶段依赖的第三方模块
- 生产依赖：项目部署实施的时候，也需要依赖的第三方模块
- npm install xxx --save 保存到配置清单的生产依赖中
- npm install xxx --save--dev 保存到开发依赖中

yarn安装：

- yarn add xxx 默认就是保存到生产依赖中
- yarn add xxx --dev或者yarn add xxx -D 保存到开发依赖中

------

第三步：部署的时候“跑环境”

不需要自己一个个的安装，只需要执行npm install或者yarn install即可，npm会自己先检测目录中是否有package.json文件，如果有的话，会按照文件中的配置依次安装

------

开发一个项目，我们生成一个配置清单"package.json"，当我们安装第三方模块使用的时候，把安装的模块信息记录到配置清单中，这样以后不管是团队协助开发，还是项目部署上线，我们都没有必要把node_modelues文件发送给别人，只需要把配置清单传递给其他人即可，其他人拿到配置清单按照清单中依赖项及版本号重新安装即可（重新安装就是跑环境）

#### 安装在本地和全局的区别

安装在全局的特点：

所有的项目都可以使用这个模块

- 容易导致版本冲突
- 安装在全局的模块，不能基于CommonJS模块规范调取使用（也就是不能在JS中通过require调取使用）

------

安装在本地的特点

只能当前项目使用这个模块

- 不能直接的使用命令操作（安装在全局可以）

------

为啥安装在全局下可以使用命令？

$ npm root/-g 查看本地项目或者全局环境下，npm的安装目录

安装在全局目录下的模块，大部分都会生成一个xxx.cmd的文件，只要有这个文件，那么xxx就是一个可执行的命令（例如：yarn.cmd ->yarn就是命令）

------

能否即安装在本地，也可以使用命令操作？

可以，但是需要配置package.json的scripts

1. 把模块安装在本地，如果是支持命令操作的，会在node_modules的bin中生成xxx.cmd的命令文件，只不过这个文件无法在全局下执行（所以不能直接使用命令）

2. 在package.json的srcipts中配置需要执行的命令脚本

   ```javascript
   "scripts":{
       "renee":"lessc -v" //属性名自己设置即可，属性值是需要执行的命令脚本，根据需要自己编写（可以配置很多命令）
   }
   ```

3. npm run renee或者yarn renee，这样操作就是把配置的脚本执行

   - 首先到配置清单的scripts中查找
   - 找到把后面对应的属性值（执行脚本）执行
   - 执行脚本的时候，会到本地Node_modulues中的bin文件夹进行查找，没有的话再向npm安装的全局目录下查找

## Node入门

node本身是基于CommonJS模块规范设计的，所以模块是node组成

- 内置模块：Node天生提供给JS调取使用的
- 第三方模块：别人写好的，我们可以基于NPM安装使用（如less、jQuery）
- 自定义模块：自己创建的一些模块

### CommonJS

CommonJS模块化设计的思想（AMD/CMD/ES6 Module都是模块化思想）

- CommonJS规定，每一个JS都是一个单独的模块（模块是私有的：里面涉及的值和变量以及函数都是私有的，和其他的JS文件中的内容都是不冲突）

- CommonJS中可以允许模块中的方法互相调用

  如B模块中想要调取A模块中的方法，A导出B导入

  【导出】

  - CommonJS给每一个模块（每个JS）中都设置了内置的变量/属性/方法
  - module：[object]代表了当前这个模块对象
  - module.exports：[object]模块的这个属性是用来导出当前模块的属性方法的
  - exports：是内置的一个“变量”，也是用来导出当前模块属性方法的，虽然和module.exports不是一个东西，但是对应的值是同一个（module.exports=exports 指向同一个堆内存，值都是对象）

  【导入】

  - require：CommonJS提供的内置变量，用来导入模块的（其实导入的就是module.exports暴露出来的东西），所以导入的值也是[object]类型的

    ```javascript
    // temp1.js
    let a=12,
    	fn=b=>{
            return a*b
    	};
    exports.fn=fn; //把当前模块私有的函数放到exports导出对象中（赋值给他的某一个属性），这样在其他模块中，可以基于require导入进来使用 =>相当于module.exports.fn=fn;
    exports ={}; //是无法导出内容的：默认和module.exports是同一个堆内存，但是这种操作让exports指向新的对内存，而module.exports不受影响。（require导入的是module.exports对应的对内存，而不是exports的）
    var a={n:10},b=a;
    b={n:100};
    // b指向了新的空间，跟a没关系了
    
    module.exports={ //重定向到自己的堆内存，用来实现导出
        fn1:fn1
    }
    module.exports.fn2=fn2; //向新的堆内存中加入导出内容
    export.fn3=100; //无法导出：此时exports和module.exports已经不是同一个堆内存了
    ```

    ```javascript
    // temp2.js
    let a=13,
    	fn=b=>{
            return a/b
    	};
    let temp1=require('./temp1'); //./特意指定是当前目录中的某个模块（.js后缀可以省略）require导入的时候：首先把temp1模块中的JS代码自上而下执行，把exports对应的堆内存导入进来，所以接受的结果是一个对象，所以说require是一个同步操作，只有把导入的模块代码执行完，才可以获取值，然后继续执行本模块下面的代码
    console.log(temp1.fn(10)); //用的还是temp1的a，因为是自己的作用域没有就查找上级作用域的a
    ```

#### CommonJS特点:

1. 所有代码都在模块作用域，不会污染全局作用域（每一个模块都是私有的，包括里面所有东西也都是私有的，不会和其他模块产生干扰）
2. 模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。想要让模块再次运行，必须清楚缓存，为了保证性能，减少模块代码重复执行的次数。
3. 模块加载的顺序，按照其在代码中出现的顺序。CommonJS规范加载模块是同步的，也就是说，只有加载完成，才能执行后面的操作







## 包

全局安装，发布包(package)

通过命令行发布包

npm adduser如果有账号相当于是登录，没有就是注册

只能在官方登录 nrm use npm

$ npm publish发布

package.json里name可以自己更改，但不能跟别人重复

配置bin，可以执行命令行运行文件

```json
{
  'bin':{
    "my-node-renee":"bin/a.js"
  }
}
```

然后在文件夹下$ npm link，可以把当前文件夹链接到全局目录下，npm文件夹里会多一个快捷方式

就可以在命令行里my-node-renee，边开发边测试

注意，当前你执行了my-node-renee，但你没有告诉他这个文件要用什么方式执行

```javascript
//a.js文件头里增加
#! /usr/bin/env node
```

别人用的时候直接下载就行了 npm install my-node-renee
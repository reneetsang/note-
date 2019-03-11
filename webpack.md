## 安装

最好不要全局安装，避免版本不一样

```javascript
npm init
npm install webpack webpack-cli -D
```

## 执行webpack命令

- npx 

webpack4可以零配置，npx是node提供专门来执行当前目录下bin中的webpack的命令，可以找到src下的Index.js进行默认的打包。但一般不会这样用这样的零配置，因为不够智能，会配置单独的命令。

```javascript
$ npx webpack --mode develop //开发模式，不会压缩文件
```

- 配置脚本执行命令

在package.json中，注意json里面不能写注释，下面写了注释只是为了说明。

```javascript
 "scripts": {
    "build": "webpack --config webpack.config.js", //npm run build就会执行webpack 
    "dev": "webpack-dev-server" // 开发的环境，通过下面配的静态服务器，可以在内存打包文件，开发时不会实时产生一堆文件
  },
```

##  配置webpack

- 创建src文件夹

- 创建dist文件夹

  - 创建index.html

- 创建配置文件webpack.config.js

  - entry：配置入口文件的地址
  - output：配置出口文件的地址
  - module：配置模块,主要用来配置不同文件的loader加载器
  - plugins：配置插件(new)
  - devServer：配置开发服务器
  - mode：配置模式，也可以在package.json中配置： "build": "webpack --mode"

  ```javascript
  // 默认情况下就叫webpack.config.js
  // webpack是基于node的 用的语法commonjs规范
  let path = require('path');

  module.exports = {
      // entry多个入口，是没有关系的但是要打包到一起去 可以写一个数组，实现多个文件打包成一个文件
      entry:['./src/index.js','./src/a.js'], // 入口文件
      output:{
          filename:'bundle.[hash:8].js',
          // 必须是绝对路径
          path:path.join(__dirname,'dist'),
      }, // 对象
      module:{}, // 对模块的处理 loader加载器
      plugins:[], // webpack对应的插件
      devServer:{},// 开发服务器的配置
      mode:'development' // 模式的配置 默认两种 production development
  }

  // 实现html打包功能 可以通过一个模板实现打包出引用好路径的html

  ```

###配置文件webpack.config.js和相关插件

默认情况下文件名就叫webpack.config.js，webpack是基于node的 用的语法commonjs规范

```javascript

let path = require('path');
// 多个入口 是没有关系的但是要打包到一起去 可以写一个数组，实现多个文件打包成一个文件
let HtmlWebpackPlugin = require('html-webpack-plugin');
let CleanWebpackPlugin = require('clean-webpack-plugin');
module.exports = {
    entry:['./src/index.js','./src/a.js'], // 入口文件
    output:{
        filename:'bundle.[hash:8].js', // [hash:8]加8位md5戳
        // 必须是绝对路径
        path:path.join(__dirname,'dist'),
    }, // 对象
    module:{}, // 对模块的处理 loader加载器
    plugins:[
        new HtmlWebpackPlugin({
            template:'./src/index.html', // 用哪个html作为模板
            hash:true,
            minify:{
               // collapseWhitespace:true,
               // removeAttributeQuotes:true
            }
        }),
        new CleanWebpackPlugin(['dist']),
    ], // webpack对应的插件
    devServer:{},// 开发服务器的配置
    mode:'development' // 模式的配置
}

// 实现html打包功能 可以通过一个模板实现打包出引用好路径的html

```

### 配置html模版 html-webpack-plugin

想要在src里建的index.html可以在dist中产出，引用的js文件也不需要自己手动修改。实现html打包功能，可以通过一个模板实现打包出引用好路径的html

安装 

```javascript
$ npm i html-webpack-plugin -D
```

- minify 是对html文件进行压缩，可以配置参数，removeAttrubuteQuotes是去掉属性的双引号

- hash 引入产出资源的时候加上哈希避免缓存

  - 还可以在文件名后加md5戳，在出口文件配置，其中[hash:8]8代表hash戳显示8位

  ```javascript
      output:{
          filename:'bundle.[hash:8].js',
          // 必须是绝对路径
          path:path.join(__dirname,'dist'),
      }, 
  ```


- template 模版路径

```javascript
let HtmlWebpackPlugin = require('html-webpack-plugin');
···
    plugins: [ // 数组，里面放着所有webpack插件
+        new HtmlWebpackPlugin({
+        minify: {
+   		collapseWhitespace:true, // 去掉空行
+            removeAttributeQuotes:true //去掉属性的双引号
+        },
+        hash: true, // 加上Hash戳
+        template: './src/index.html',// 用哪个html作为模板
+        filename:'index.html'
    })]
```

### 打包前先清空输出目录

添加了`hash`之后，会导致改变文件内容后重新打包时，文件名不同而内容越来越多，就可以使用`clean-webpack-plugin`在打包前清空输出目录

安装

```javascript
npm i  clean-webpack-plugin -D
```

```javascript
let CleanWebpackPlugin = require('clean-webpack-plugin');
···
    plugins: [
+        new CleanWebpackPlugin(['dist']) // 跟dist目录平级不需要path拼接路径了 ['dist/a','dist/b']也可以指定删除某一个
    })]
```

### 配置多页面开发

index.js是属于 index.html a.js是属于a.html，要配置多页面开发。上面入口文件要配置成对象，出口文件不能写死了，改为'[name].js'，会以入口文件名字一致。 

```javascript
    entry:{
        index:'./src/index.js',
        a:'./src/a.js'
    }, 
    output:{
        filename:'[name].[hash:8].js', 
        path:path.join(__dirname,'dist'),
    }
    ...
    plugins:[
        new HtmlWebpackPlugin({
            filename:'index.html',
            template:'./src/index.html', 
            chunks:['index'], // 当前对应的数据块(上面entry的) 多个就['index','a']
            hash:true,
        }),
        new HtmlWebpackPlugin({
            filename:'a.html',
            chunks:['a'],
            template:'./src/index.html', 
            hash:true,
        }),
        new CleanWebpackPlugin(['dist']),
    ], 
```

### 配置开发服务器 webpack-dev-server

启动一个静态服务器， 默认会自动刷新，热更新（页面可能只有一个组件变化）。打包的文件存在于内存中，并不会产生在dist目录下。

安装

```javascript
npm i webpack-dev-server –D
```

```javascript
    devServer:{ //开发服务器的配置
        contentBase:'./dist',
        progress:true,// 现实进度条
        host:'localhost',
        port:3000,
        open:true,// 是否自动打开浏览器
        hot:true // 开启热更新，但还需要配置一个热更新插件，是webpack自带的
    },
```

不过配置完这些后，JS模块其实还是不能自动热更新的，还需要在你的JS模块中执行一个Webpack提供的API才能实现热更新。

#### 配置热更新/热替换

热更新作用是在应用运行时，无需刷新页面，便能替换、增加、删除必要的模块。热更新的好处可能在vue或者react中有更大的发挥，其中某一个组件被修改的时候就会针对这个组件进行热更新了。

```javascript
let webpack = require('webpack');
···
    plugins:[
        // 可以实现热替换
        new webpack.HotModuleReplacementPlugin()
    ], 
```

配置完毕后，就可以通过之前配置的脚本，执行`npm run dev`命令后，就会启动静态服务器了。

### 支持加载css文件

#### 什么是Loader 

loader特点，希望单一，通过使用不同的Loader，Webpack可以要把不同的文件都转成JS文件,比如CSS、ES6/7、JSX等

- test：匹配处理文件的扩展名的正则表达式
- use：loader名称，就是你要使用模块的名称
- include/exclude:手动指定必须处理的文件夹或屏蔽不需要处理的文件夹
- query：为loaders提供额外的设置选项

loader三种写法

- use
- loader
- use+loader

```javascript
npm install style-loader css-loader less less-loader node-sass sass-loader -D
```

配置加载器(规则)

css-loader 支持 @import这种语法的，解析路径之类

style-loader 就是把css插入到Head的标签中

loader的顺序，默认是从右向左执行，从下到上执行

loader还可以写成对象方式

```javascript
    module: {
        // 抽离样式文件，抽离出以link标签的形式引入
        // extract-text-webpack-plugin@next
        // mini-css-extract-plugin 有bug
        rules: [// 规则
            {
                test: /\.css$/,
                use: cssExtract.extract({
                    use:[
                        // MiniCssExtractPlugin.loader,
                        'css-loader'
                    ]
                })
            },
            {
                test: /\.less$/, use: lessExtract.extract({
                    use:[
                        //MiniCssExtractPlugin.loader,
                        //写成对象可以传一些参数
                        { loader: 'css-loader' },
                        { loader: 'less-loader' }
                    ]
                })
            },
            { test: /\.(scss|sass)$/, use: ['style-loader', 'css-loader', 'sass-loader'] },
            { test: /\.css$/, use: [
                {
                	loader:'style-loader',
                    options:{
                        insertAt:'top', //style标签会插到上面
                    }
            	},
                'css-loader'
            ] },
            { test: /\.less$/, use: [
                {
                	loader:'style-loader',
                    options:{
                        insertAt:'top', //style标签会插到上面
                    }
            	},
                'css-loader',
                'less-;pader' //把less转换为css
            ] },
        ]
    },
```

####抽离样式文件 mini-css-extract-plugin

上面打包加载后的css文件是以行内样式style的标签写进打包后的html页面中，如果样式很多的话，我们更希望直接用link的方式引入进去，这时候需要把css拆分出来。

```javascript
    // 抽离样式文件，抽离出以link标签的形式引入

    // extract-text-webpack-plugin@next

    // mini-css-extract-plugin 有bug
```

安装，因为当前版本只支持webpack3，所以要加个@next

```javascript
npm install --save-dev extract-text-webpack-plugin@next
```

其中上面rules中要改一下，将css用link的方式引入就不再需要style-loader了，要删掉

```javascript
let ExtractTextWebpackPlugin =  require('extract-text-webpack-plugin');
···
{
                test:/\.css$/,
+                use: ExtractTextWebpackPlugin.extract({
+                    use:'css-loader'
+                }),
                include:path.join(__dirname,'./src'),
                exclude:/node_modules/
            },

   plugins: [
+        new ExtractTextWebpackPlugin('css/index.css'),
```

现在用这个mini-css-extract-plugin 

需要自己压缩文件optimize-css-assets-webpack-plugin，用了这个JS不会压缩，需要再用uglifyjs-webpack-plugin

```javascript
yarn add mini-css-extract-plugin 
yarn add optimize-css-assets-webpack-plugin -D
yarn add uglifyjs-webpack-plugin -D
```

```javascript
let path=require('path');
let HtmlWebpackPlugin=require('html-webpack-plugin')
let MiniCssExtractPlug=require('yarn add mini-css-extract-plugin');
let OptimizeCss=require('optimize-css-assets-webpack-plugin');
let UglifyJsPlugin=require('uglifyjs-webpack-plugin')

module.exports={
    optimization:{ //优化项
        minimizer:[
            new UglifyJsPlugin({
                cache:true,
                parallel:true, //是否并发打包，一起打包多个
                sourceMap:true
            }),
            new OptimizeCss()
        ]
    },
    module: {
        rules: [// 规则
            {
                test: /\.css$/,
                use:[
                    MiniCssExtractPlugin.loader, 
                    'css-loader'
                ]
            }
        ]
    },
    plugins: [
        new MiniCssExtractPlug({
            filename:'main.css',

        })
    ],
}

```

#### 自动加前缀 autoprefixer

yarn add postcss-loader autoprefixer

```javascript
let MiniCssExtractPlug=require('yarn add mini-css-extract-plugin');

module: {
    rules: [// 规则
        {
            test: /\.css$/,
            use:[
                MiniCssExtractPlugin.loader, 
                'css-loader',
                'postcss-loader',
            ]
        },
        {
            test: /\.less$/,
            use:[
                MiniCssExtractPlugin.loader, 
                'css-loader',
                'postcss-loader',
                'less-loader'
            ]
        }
    ]
},
```

postcss.config.js 还需要配置文件

```javascript
module.exports={
    plugins:[require('autoprefixer')]
}
```

### 转化ES6语法 babel

babel-loader 进行转换的

@babel/core核心模块，

@babel/preset-env 转化模块，可以把高级语法转换成低级语法

promise和gener不会转换，需要用个包@babel/plugin-transform-runtime和@babel/runtime和@babel/-polyfill(实现一些es7语法)

```javascript
yarn add babel-loader @babel/core @babel/preset-env -D
yarn add @babel/plugin-proposal-decorators -D
yarn add @babel/plugin-transform-runtime -D
yarn add @babel/runtime
yarn add @babel/polyfill
```

```javascript
module: {
    rules: [// 规则
        {
            test: /\.js$/, // normal普通的loader
            use:{
				loader:'babel-loader',
                options:{ //用babel-loader把es6转换成es5
                    presets:[ //预设
                    	'@babel/preset-env'
                    ],
            		plugins:[
                        //处理class类
                        ['@babel/plugin-proposal-decorators',{'legacy':true}],
                        ['@babel/plugin-proposal-class-properties',{"loose":true}],
                        "@babel/plugin-transform-runtime"
            		]
                }
            },
            // 只找src里的 包括
            include:path.resolve(_dirname,'src'),
            // 排除
            exclude:/node_modules/
        },
    ]
},
```

```javascript
require('@babel/polyfill')

'aaa'.includes('a')
```

### 校验代码 ESLint

可以在官网选择配置好，下载.eslintrc.json文件，放入项目中

```
yarn add eslint eslint-loader
```

```javascript
module: {
    rules: [// 规则loader默认是从右边向左执行，从下到上，所以这个应该写到下面，或者可以使用enforce强制先执行
        {
            test: /\.js$/,
            use:{
                loader:'eslint-loader',
                options:{
                    enforce:'pre',// previous
                }
            }
        },
		// ...
    ]
},
```


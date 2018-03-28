## node

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
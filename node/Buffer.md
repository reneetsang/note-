# Buffer

JS语言没有二进制数据类型，而在处理TCP和文件流的时候，必须要处理二进制数据。NodeJS提供了一个Buffer对象来提供对二进制数据的操作，比如文件流的读写、网络请求数据的处理等。Buffer是一个全局类,无需加载就可使用。

## 定义buffer的三种方式

### 1.通过长度创建

- alloc安全，速度会慢
- allocUnsafe速度快，但是不安全

```javascript
// 创建一个长度为 10、且用 0 填充的 Buffer。
const buf1 = Buffer.alloc(10);
// 创建一个长度为 10、且用 0x1 填充的 Buffer。
const buf2 = Buffer.alloc(10, 1);
// 创建一个长度为 10、且未初始化的 Buffer。
const buf3 = Buffer.allocUnsafe(10);
```

### 2.通过数组创建

一般都要合法传递 0-255之间

```javascript
// 创建一个包含 [0x1, 0x2, 0x3] 的 Buffer。
const buf4 = Buffer.from([1, 2, 3]);
```

### 3.通过字符串创建

```javascript
const buf5 = Buffer.from('字符串');
```

## buffer常用方法



## 编码转换问题 iconv-lite

爬虫时，有时候可能会获取到别的格式的页面，如gbk

可以用`iconv-lite`来解析其他编码

```javascript
let fs = require('fs');
let path = require('path');
let iconvLite = require('iconv-lite'); // 这个包需要转化的是buffer
let r = fs.readFileSync(path.resolve(__dirname,'a.txt'));
let result = iconvLite.decode(r,'gbk'); // 进行内容编码的转化
console.log(result); 
```

## BOM头的问题

gb2312 另存为utf8，转化文件时候会在BOM头出现多余的标识符，要去除

```javascript
let fs=require('fs');
let path=require('path');
let index=require('./index');

//截取uft-8的BOM头
function  stripBOM(content) {
  if(Buffer.isBuffer(content)){
    if(content[0]===0xEF&& content[1]===0xbb&&content[2]===0xBF){
      return content.slice(3)
    }
    return content;
  }else {
    if(content.charCodeAt(0)===0xFEFF){
      content=content.slice(1)
    }
    return content;
  }
}

//utf8是unicode的实现
let result=fs.readFileSync(path.join(__dirname,'./1.txt','utf8'));//不传默认就是buffer
result=stripBOM(result);
console.log(resule.toString())
```

## buffer可以和字符串进行转化 string_decoder

如果不能拼成汉字，需要对不能输出的进行缓存。可以用`string_decoder`模块来解决输出问题，不识别的不输出，先攒着

```javascript
let buffer = Buffer.from('珠峰'); 
let a = buffer.slice(0, 2);
let b = buffer.slice(2, 6);
let {StringDecoder} = require('string_decoder');
let sd = new StringDecoder();
console.log(sd.write(a)); //  此时不是正常汉字 则保存在sd的内部
console.log(sd.write(b))// 下次输出时会把上次的结果一同输出
```


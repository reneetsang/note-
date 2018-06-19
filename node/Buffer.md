# Buffer

JS语言没有二进制数据类型，而在处理TCP和文件流的时候，必须要处理二进制数据。NodeJS提供了一个Buffer对象来提供对二进制数据的操作，比如文件流的读写、网络请求数据的处理等。

Buffer是一个全局类,无需加载就可使用。Buffer 存的都是16进制的。

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

### buffer.fill 

手动初始化,擦干净桌子,将buffer内容清0 

- buffer.fill(value[,offset[,end]][,encoding])

```javascript
buffer.fill(0);
```

### buffer.toString 

- bufffer.toString([encoding[, start[, end]]]) 

```javascript
buffer.toString('utf8',3,6)
```

### buffer.write

- buffer.write(string[, offset[, length]][, encoding])  参数为 内容 偏移量 长度 编码 

```javascript
buffer.write('前',0,3,'utf8');
buffer.write('端',3,3,'utf8'); //前端
```

###buffer.slice 

- buf.slice([start[, end]])  slice是浅拷贝 

```javascript
let newBuf = buffer.slice(0,4);
```

```javascript
// Buffer 和二维数组是一样的，Buffer存的都是内存地址，请看实例

let buffer = Buffer.alloc(6, 1)
let newBuffer = buffer.slice(0, 3)
newBuffer[0] = 100
console.log(buffer)
```

### buffer.copy

复制Buffer 把多个buffer拷贝到一个大buffer上 

- bufffer.copy(target[, targetStart[, sourceStart[, sourceEnd]]]) 

  参数：目标buffer 引用buffer 起始为止 结束为止 复制位数 

```javascript
let buf5 = Buffer.from('前端开发');
let buf6 = Buffer.alloc(6);
buf5.copy(buf6,0,0,4);
buf5.copy(buf6,3,3,6);
// buf6=前端
```

> 手写一个copy方法

```javascript
// 目标buffer 目标开始的拷贝位置 源的开始 源的结束位置
Buffer.prototype.mycopy=function(target,targetStart,sourceStart,souceEnd){
    for(var i=0;i<sourceEnd-sourceStart;i++){
        target[i+targetStart]=this[sourceStart+i];
    }
}
buffer2.mycopy(buffer1,1,3,6);
console.log(buffer1.toString())
```

### buffer.concat

- Buffer.concat(list[, totalLength]) 

```javascript
let buf1 = Buffer.from('前');
let buf2 = Buffer.from('端');
let buf3 = Buffer.concat([buf1,buf2],6);
console.log(buf3.toString());
```

> 手写一个concat方法

```javascript
Buffer.concat = function (list,len) {
  if(typeof len === 'undefined'){ // 求拷贝后的长度
    len = list.reduce((current,next,index)=>{
      return current+next.length;
    },0);
  }
  let newBuffer = Buffer.alloc(len); // 申请buffer
  let index = 0;
  list.forEach(buffer =>{ // 将buffer一一拷贝
    buffer.copy(newBuffer, index);
    index+=buffer.length;
  });
  return newBuffer.slice(0,index); // 返回拷贝后的buffer
}
// 接收请求时会采用concat方法进行拼接
console.log(Buffer.concat([buffer1, buffer2, buffer3],10).toString());
```

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


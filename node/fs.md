## fs

转化文件时候会在前面加标识符，这时需要去除uft-8的bom头

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

爬虫时，有时候可能会获取到别的格式的页面，如gbk

可以用`iconv-lite`来解析其他编码

```javascript
let iconv=require('iconv-lite')
let fs=require('fs');
let path=require('path');
let result=fs.readFileSync(path.join(__dirname,'./2.txt'));
iconv.decode(result,'gbk')
console.log(resule.toString())
```

Buffer的乱码问题

可以用`string_decoder`模块来解决输出问题，不识别的不输出，先攒着

```javascript
let buffer=Buffer.from('前端开发');
let buff1=buffer.slice(0,5);
let buff2=buffer.slice(5);
let {StringDecoder}=require('string_decoder');
let sd=new StringDecoder();
console.log(sd.write(buff1).toString());
console.log(sd.write(buff2).toString());
```

## fs常用方法 - 文件操作

fs fileSystem 文件系统，这里提供了很多方法，有同步和异步的

同步的性能低，会阻塞主线程，能用异步的就用异步

异步的大多是多了一个callback，callback的第一个参数是错误对象

### 异步读取文件

> fs.readFile(path[, options], callback)
>
> options
>
> - encoding
> - flag flag 默认 = 'r'

```javascript
let fs=require('fs');
let path=require('path');
//读取文件的时候默认是null,null代表就是二进制
//回调中err代表错误，data代表读到的结果
fs.readFile(path.join(__dirname,'1.txt'),{flag:'r'}，function(err,data){
  if(err) return console.log(err);
  console.log(data)
})
```

### 同步读取

> fs.readFileSync(path[, options])

```javascript
fs.readFile('./1.txt',{encoding:'utf8',flag:'r',},function (err,data) {
   if(err) return console.log(err);
   console.log(data);
});
```

### 异步写入文件

> fs.writeFile(file, data[, options], callback)
>
> options
>
> - encoding 以什么编码格式写进文件内，不写默认情况utf-8
> - flag flag 默认 = 'w'
> - mode 读写权限，默认为0o666(八进制)

```javascript
//第一个参数：路径,第二：数据,第三：encoding以什么编码格式写进文件内，一般不写默认情况utf-8。mode表示权限（儿媳一直死读书），默认0o666（八进制）表示可读可写
//cmd->ls -l 1.txt输出文件的详细信息
fs.writeFile(path.join(__dirname,'1.txt'),'hello'，{mode:0o666},function(err){
  console.log(err)
})
```

### 同步写入

> fs.writeFileSync(file, data[, options])

### 拷贝文件

```javascript
let fs=require('fs');
let path=require('path');
function copy(source,target){
  fs.readFile(path.join(__dirname,source),function(err,data){
    if(err) return console.log(err);
    fs.writeFile(path.join(__dirname,target),data,function(err){
      if(err) console.log(err)
    })
  })
}
copy('1.txt','2.txt ')
//如果内存只有12g，读一个20g文件肯定读不了
```

- fs.copyFile

  node后来出了copy的方法，这个方法需要node8.5以上的版本，但也有内存读取撑爆的问题

```javascript
fs.copyFile(path.join(__dirname,'1.txt'),path.join(__dirname,'2.txt'),function(){
  console.log('拷贝成功')
})
```

### 打开文件

> fs.open(filename,flags,[mode],callback);
>
> - FileDescriptor 是文件描述符
> - FileDescriptor 可以被用来表示文件
> - in -- 标准输入(键盘)的描述符
> - out -- 标准输出(屏幕)的描述符
> - err -- 标准错误输出(屏幕)的描述符

```javascript
fs.open('./1,txt','r',0600,function(err,fd){});
```

### 从指定位置处开始读取文件

> fs.read(fd, buffer, offset, length, position, callback((err, bytesRead, buffer)))
>
> fs.read(文件描述符,读取到哪个buffer,内容从buffer的哪个位置上开始,读取文中内容的长度,读取文件的位置)

```javascript
//比如1g的文件，先弄个内存先读取一下，然后再去写
//读取
//fs.read限制读取个数，手动读取
//先要用fs.open打开文件，才能对文件进行操作
//fd（file descriptor）文件描述符，代表对当前文件的描述
//process.stdout.write()标准输出 1
//process.stderr.write()错误输出 2
let buffer=Buffer.alloc(3)
fs.open(path.join(__dirname,'1.txt'),'r',0o666,function(err,fd){
  //第三个参数：offset表示buffer从哪个开始存储
  //第四个参数：length一次想读几个
  //第五个参数：position文件的读取位置，默认可以写null，当前位置从0开始
  fs.read(fd,buffer,0,3,0,function(err,bytesRead){
    //bytesRead读到个数
    console.log 
  })
})
```

### 从指定位置处写入文件

> fs.write(fd, buffer[, offset[, length[, position]]], callback)

```javascript
//写
//如果flag是a,那写的position参数就不生效了
fs.open(path.join(__dirname,'2.txt'),'w',0o666,function(err.fd){
    
  //第三个参数：offset表示buffer从哪个参数开始写
  //第四个参数：length写几个
  //第五个参数：写到文件的哪个位置
  fs.write(fd,Buffer.from('珠峰'),3,3,null,function(err,byteWritten){
    if(err) return console.log(err);
    console.log(byteWritten)
  })
})
```

### 同步磁盘缓存

> fs.fsync(fd,[callback]);

```javascript
fs.open(path.join(__dirname,'2.txt'),'w',function(err,fd){
   fs.write(fd,Buffer.from('珠峰'),0,6,0,function(err,byteWritten){
    // 当write方法触发了回调函数 并不是真正的文件背写入了，先把内容写入到缓存区
        // 强制将缓存区的内容 写入后再关闭文件
        fs.fsync(fd,function(){
            fs.close(fd,function(){
                console.log('关闭')
            })
        })
   })
});
```

### 关闭文件

> fs.close(fd,[callback]);
>
> 文件打开是需要关闭的，不关会占用性能

### 结合应用，写个copy

```javascript
function copy(source,target){
    let size = 3; // 每次来三个
    let buffer = Buffer.alloc(3);
    fs.open(path.join(__dirname,source),'r',function(err,rfd){
        fs.open(path.join(__dirname,target),'w',function(err,wfd){
            function next(){
                fs.read(rfd,buffer,0,size,null,function(err,bytesRead){
                    if(bytesRead>0){ // 读取完毕了 没读到东西就停止了
                        fs.write(wfd,buffer,0,bytesRead,null,function(err,byteWritten){
                            next();
                        })
                    }else{
                        fs.close(rfd,function(){}); // 关闭读取的
                        // 同步磁盘缓存fs.fsync(fd,[callback]);
                        // 当write方法触发了回调函数 并不是真正的文件背写入了，先把内容写入到缓存区
                        // 强制将缓存区的内容 写入后再关闭文件
                        fs.fsync(wfd,function(){ // 确保内容 写入到文件中 
                            fs.close(wfd,function(){ // 关闭写入的
                                console.log('关闭','拷贝成功')
                            })
                        })
                    }
                })
            }
            next();
        })
    });
}
copy('1.txt','2.txt');
```

 ##　fs常用方法 - 目录操作

### 创建目录

>  fs.mkdir(path[, mode], callback)
>
> 要求父目录必须存在

### 判断一个文件是否有权限访问

> fs.access(path[, mode], callback)

```javascript
fs.access('/etc/passwd', fs.constants.R_OK | fs.constants.W_OK, (err) => {
  console.log(err ? 'no access!' : 'can read/write');
});
```

### 读取目录下所有的文件

> fs.readdir(path[, options], callback)

### 查看文件目录信息

> fs.stat(path, callback)

- stats.isFile() 是否是文件
- stats.isDirectory() 是否是文件夹
- atime(Access Time)上次被读取的时间。
- ctime(State Change Time)：属性或内容上次被修改的时间。
- mtime(Modified time)：档案的内容上次被修改的时间。




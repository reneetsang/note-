## AJAX基础知识及核心原理解读

### AJAX基础知识

AJAX全称async javascript and xml，异步的js和xml。

`xml`：可扩展的标记语言，作用是用来存储数据的，通过自己扩展的标记名称清晰的展示出数据结构

ajax之所以称为`异步的Js和xml`，主要原因是，当初最开始用ajax实现客户端和服务器端数据通信的时候，传输的数据格式一般都是xml格式的数据。但现在一般基于json格式进行数据传输的

```xml
<?xml verson='1.0' encoding='UTF-8'?>
<root>
    <student>
    	<name>renee</name>
        <age>18</age>
        <score>
        	<english>80</english>
        </score>
    </student>
</root>
```

`异步的Js`：这里的异步不是说AJAX只能基于异步请求（虽然建议都是使用异步编程），这里的异步指的是**局部刷新**

#### 局部刷新 VS 全局刷新

在非完全前后端分离项目中，前端开发只需要完成页面的制作，并且把一些基础的人机交互效果使用JS完成即可，页面中需要动态呈现内容的部分，都是交给后台开发工程师做数据绑定和基于服务器进行渲染的

#####　全局刷新

**好处：**

1、动态绑定数据在页面的源代码中可以看见，有利于seo优化推广（有利于搜索引擎的收录和抓取）

2、从服务器获取的结果就已经是最后要呈现的结果了，不需要客户端做额外的事情，所以页面加载速度快（前提是服务器端处理的速度够快，能够处理过来），所以类似于京东、淘宝这些网站，首屏数据一般都是经由服务器端渲染的

**弊端：**

1、如果页面存在需要实时更新的数据，每一次想要展示更新的数据，页面都要重新的刷新一次，这样肯定不行

2、都交给服务器做数据渲染，服务器端的压力太大了，如果服务器处理不过来，页面呈现的速度更慢（所以京东淘宝这类网站，除了首屏都是服务器渲染的，其他屏都是客户端做数据渲染绑定）

3、这种模式不利于开发（开发效率低）

##### 局部刷新

目前市场上大部分项目都是前后端完全分离的项目

前后端完全分离的项目，页面中需要动态绑定的数据是交给客户端完成渲染的

1、向服务器端发送AJAX请求

2、把从服务器端获取的数据解析处理，拼接称为我们需要展示的HTML字符串

3、把拼接好的字符串替换页面中某一部分的内容（局部刷新），页面整体不需要重新加载，局部渲染即可

**优势：**

1、我们可以根据需求任意修改页面中某一部分的内容改变（例如实时刷新），整体页面不刷新，性能好体验好（所有表单验证、需要实时刷新的等需求都要基于AJAX实现）

2、有利于开发，提高开发效率

1）前后台完全分离，后台不需要考虑前端如何实现，前端也不需要考虑后台用什么技术，真正意义上实现了技术的划分

2）可以同时进行开发，项目开发开始，首先指定前后端数据交互的接口文档（文档中包含了，调取哪个接口或者哪些数据等协议规范），后台把接口先写好（目前很多公司也需要前端自己拿Node来模拟这些接口），客户端按照接口调取即可，后台再次去实现接口功能即可

**弊端：**

1、不利于seo优化，第一次从服务器端获取的内容各种不包含需要动态绑定的数据，所以页面的源代码没有这些内容，不利于seo收录，后期通过JS添加到页面中的内容，并不会写在页面的源代码中（注意不是页面结构）

2、交给客户端渲染，首先需要把页面呈现，然后再通过JS的异步AJAX请求获取数据，然后数据绑定，浏览器再把动态增加的部分重新渲染，无形中浪费了一些时间，没有服务器端渲染页面速度快

### 基于原生实现AJXA

```javascript
// 创建一个AJAX对象
let xhr=new XMLHttpRequest()；// 不兼容ie6及更低浏览器（ActiveXObject）

// 打开请求地址（可以理解为一些基础配置，但是并没有发送请求）
xhr.open([method],[url],[async],[username],[user password])

// 监听AJAX状态改变，获取响应信息（获取响应头信息、获取响应主体信息）
xhr.onreadystatechange=()=>{
    if(xhr.readyState===4 && xhr.status===200){
        let result=xhr.responseText;// 获取响应主体中的内容
    }
}

// 发送AJAX请求(括号中传递的内容就是请求主体的内容)
xhr.send(null)
```

#### 分析第二步中的细节点：

> xhr.open([method],[url],[async],[username],[user password])

**[HTTP/AJAX请求方式]**

1、GET系列的请求（获取）

- get
- delete：从服务器上删除某些资源文件
- head：只想获取服务器返回的响应头信息（响应主体内容不想获取）
- ……

2、POST系列请求（推送）

- post
- put：向服务器中增加指定的资源文件
- ……

不管哪一种请求方式，客户端都可以把这些信息传递给服务器，服务器也可可以把信息返回给客服端，只是GET系列以获取为主（给的少，拿回来的多），而POST系列一般以推送为主（给的多，拿回来的少）

1）我们想获取一些动态展示信息（新闻列表等），一般使用GET，因为只需要向服务器端发送请求，告诉服务器端想要什么，服务器端就会把需要的数据返回

2）在实现注册功能的时候，我们需要把客户端输入的信息发送给服务器进行存储，服务器一般返回成功还是失败等状态，此时我们一般都是基于POST请求完成的

GET系列请求和POST系列请求，在项目实战中存在很多的区别

1、GET请求传递给服务器内容一般没有POST请求传递给服务器的内容多

原因：GET请求传递给服务器内容，一般都是基于``url地址问号传递参数``来实现的，而POST请求一般都是基于``设置请求主体``来实现的。各浏览器，都有自己的URL最大长度限制（谷歌：8kb，火狐：7kb，IE：2kb，所以一般我们只传2kb），超过限制长度的部分，浏览器会自动截取掉，导致传递给服务器的数据缺失

理论上POST请求通过主体传递是没有大小限制的，真实项目中为了保证传输的速率，我们也会限制大小（例如上传头像图片，我们都会做大小限制）。

2、GET请求很容易会出现缓存（这个缓存不可控，一般我们都不需要），而POST不会出现缓存（除非自己做特殊处理）

原因：GET是通过URL问号传递信息给服务器，而POST是设置请求主体。设置请求主体不会出现缓存，而URL传递参数就会

```javascript
// 每隔一分钟重新请求服务器端最新的数据，然后展示在页面中（页面中某些数据实时刷新）
setTimeout(()=>{
    $.ajax({
        get:'getList?lx=news',
        ...
        success=>{
            // 第一次请求数据回来，间隔一分钟后，浏览器又发送一次请求，但是新发送的请求，不管是地址还是传递的参数都和第一次一样，浏览器很有可能会把上一次数据获取，而不是获取最新的数据
        }
    })
},60000);

// 解决方案
// 每一次重新请求的时候，在URL末尾追加一个随机数，保证每一次请求的地址不完全一样，就可以避免是从缓存中读取数据
setTimeout(()=>{
    $.ajax({
        get:'getList?lx=news&_='+Math.random(),
        ...
        success=>{
			
        }
    })
},60000);
```

3、GET请求没有POST请求安全（POST也并不是十分安全，只是相对安全）

原因：还是因为GET是URL传参给服务器

有一种比较简单的黑客技术：URL劫持，也就是可以把客户端传递给服务器的数据劫持掉，导致信息泄露

**URL：请求数据的地址（API地址）**

真实项目中，后台开发工程师会编写一个API文档，在API文档中汇总了获取哪些数据需要使用哪些地址，我们按照文档

**ASYNC：异步（SYNC同步）**

设置当前AJAX请求是异步还是同步的，不写默认是异步（TRUE），如果设置未FALSE，则代表当前请求是同步的

**用户名和密码**

这两个参数一般不用，如果你请求的URL地址所在的服务器设定了访问权限，则需要我们提供可通行的用户名和密码才可以（一般服务器都是可以允许匿名访问的）

#### 第三部分细节研究

 ```javascript
// 监听AJAX状态改变，获取响应信息（获取响应头信息、获取响应主体信息）
xhr.onreadystatechange=()=>{
    if(xhr.readyState===4 && xhr.status===200){
        let result=xhr.responseText;// 获取响应主体中的内容
    }
}
 ```

```javascript
let xhr=new XMLHttpRequest()
dir(xhr)；
// 可以看到状态码等AJAX方法
```

**AJAX状态码(xhr.readyState)：描述当前AJAX操作的状态的**

0：UNSENT 未发送，只要创建一个AJAX对象，默认值就是零

1：OPENED 我们已经执行了xhr.open这个操作

2：HEADERS_RECEIVED 当前AJAX的请求已经发送，并且已经接收到服务器端返回的`响应头`信息了

3： LOADING `响应主体内容`正在返回的路上

4：DONE `响应主体内容`已经返回到客户端

**HTTP网络状态码(xhr.status)：记录了当前服务器返回信息的状态**

200：成功，一个完整的HTTP事务完成（以2开头的状态码一般都是成功）

以3开头一般也是成功，只不过服务器端做了很多特殊的处理

301：Moved Permanently 永久转移（永久重定向），一般应用于域名迁移

302：Moved temporarily临时转移（临时重定向，新的HTTP版本中任务307是临时重定向），一般用于服务器的负载均衡，当前服务器处理补了，我把当前请求临时交给其他的服务器处理（一般图片请求经常出现302，很多公司都有单独的图片服务器）

304：Not Modified从浏览器缓存中获取数据，把一些不经常更新的文件或者内容缓存到浏览器中，下一次从缓存中获取，减轻服务器压力，也提高页面加载速度  

以4开头的，一般都是失败，而且客户端的问题偏大

400：请求参数错误

401：无权限访问

404：访问地址不存在

以5开头的，一般都是失败，而且服务器问题偏大

500：Inter Server Error 未知的服务器错误

503：Service Unavailable 服务器超负载

……

### AJAX中其他常用的属性和方法

> 面试题：AJAX中总共支持几个方法

#### 属性：

- readyState：存储的是当前AJAX的状态码
- response / responseText / responseXML：都是用来接受服务器返回的主体内容，只是根据服务器返回内容的格式不一样，我们使用不同的属性接受即可
  - responseText 是最常用的，接收到的结果是字符串格式的（一般服务器返回的数据都是JSON格式字符串）
  - responseXML 偶尔会用到，如果服务器返回的是XML文档数据，我们需要使用这个属性接受
- status：记录了服务器返回的HTTP状态码
- statusText：对返回的状态码的描述
- timeout：设置当前AJAX请求的超时时间，假设我们设置时间为3000ms，从AJAX请求发送请求开始，3秒后响应注意内容还没有返回，浏览器会把当前AJAX请求任务强制断开

#### 方法：

- abort()：强制中断AJAX请求 xhr.obort()
- getAllResponseHeaders()：获取全部的响应头信息（获取的结果是一堆字符串文本）
- getResponseHeader(key)：获取指定属性名的响应头信息，例如：xhr.getResponseHeader('date')获取响应投中存储的服务器时间（获取的服务器时间是格林尼治时间）
- open()：打开一个url地址
- overrideMimeType()：重写数据的MIME类型
- send()：发送AJAX请求（括号中写的内容是客户端基于请求主体把信息传递给服务器）
- setRequestHeader(key,value)：设置请求头信息（可以是设置的自定义请求头信息，请求头部的内容中不能出现中文汉字）

#### 事件：

- onabort：当AJAX被中断请求会触发这个事件
- onreadtstatechange：AJAX状态发生改变，会触发这个事件
- ontimeout：当AJAX请求超时， 会触发这个事件
- ……

```javascript
let xhr=new XMLHttpRequest();
// xhr.setRequestHeader('aaa','xxx'); 设置请求头信息必须在open之后和send之前
xhr.open('get','temp.xml?_='+Math.random(),true);
// xhr.setRequestHeader('cookie','洁莹'); 设置的请求内容不是一个有效的值（请求头部的内容不得出现中文汉字）
xhr.setRequestHeader('aaa','xxx');

// 设置超时
xhr.timeout=10;
xhr.ontimeout=()=>{
    console.log('当前请求已经超时');
    xhr.abort();
}
xhr.onreadystatechange=()=>{
    let {readtState:state,status}=xhr;
    
    // 说明数据成功了
    if(!/^(2|3)\d{2}$/.test(status)) return;
    
    //在状态为2的时候就可以获取响应头信息
    if(state===2){
        let headerAll = xhr.getAllResponseHeaders(),
            serverDate=xhr.getResponseHeader('date'); // 获取的服务器时间是格林尼治时间，相比北京时间差了8小时候，可以通过new Date转换为北京时间
        console.log(headerAll,new Date(serverDate));
        return
    }
    
    // 在状态为4的时候响应主体内容就已经回来了
    if(state===4){
        let valueText=xhr.responseText, // 获取到的结果一般都是JSON字符串（可以使用JSON.PARSE把其转化为JSON对象）
            valueXML=xhr.responXML; // 获取到的结果是XML格式的数据（可以通过XML的一些常规操作获取存储的指定信息）
        console.log(valueText,valueXML);
    }
};
xhr.send('name=renee&age=18')
```

### JS中常用的编码解码

#### 正常的编码解码（非加密）

1、escape / unescape：主要就是把中文汉字进行编码和解码的（一般只有JS语言支持，也经常应用于前端页面通信时候的中文汉字编码）

```javascript
let str = '曾洁莹'
escape(str)
unescape(str)
```

2、encodeURI / decodeURI：基本所有语言都支持

```javascript
let str = '曾洁莹'
encodeURI(str)
decodeURI(str)
```

3、encodeURIComponent / decodeURIComponent：和第二种方式非常类似，区别在于

```javascript
let str = '曾洁莹'
encodeURIComponent(str)
decodeURIComponent(str)
```

需求：我们URL问号传递参数的时候，我们传递的参数值还是一个URL或者包含很多特殊的字符，此时为了不影响主要的URL，我们需要把传递的参数值进行编码。使用encodeURL不能编码一些特殊字符，所以只能使用encodeURIComponent处理

```javascript
let str = 'http://www.baidu.com/?'
obj={
    name:'珠峰',
    age=9,
    url:''http://www.baidu.com/?a=1'
}
// 把OBJ中的每一项属性名和属性值拼接到URL的末尾（问号传参方式）
for(let key in obj){
    str+=`${key}=${encodeURIComponent(obj[key])}?&`
    // 不能使用encodeURI必须使用encodeURIComponent,原因是encodeURI不能编码特殊的字符
}
str.replace(/&$/g,'')

// 后期获取URL问号参数的时候，我们把获取的值再一次解码即可
String.prototype.myQueryUrlParameter=function myQueryUrlParameter(){
    let reg=/[?&]([^?&=]+)(?:=([^?&=]*))?/g,
        obj={};
    this.replace(reg,(...arg)=>{
        let [,key,value]=arg;
        obj[key]=decodeURIComponent(value); // 解码
    });
    return obj;
}
```

#### 也可以通过加密的方法进行编码解码

1、可逆转加密（一般都是团队自己玩的规则）

2、不可逆转加密（一般都是基于MD5加密完成的，可能会把MD5加密后的结果二次加密，因为一次加密很容易给解密 ）

 ### AJAX中的同步和异步编程 

AJAX这个任务：发送请求接收到响应主体内容（完成一个完整的HTTP事务）

xhr.open()：任务开始

xhr.readyState===4：任务结束

```javascript
let xhr=new XMLHttpRequest();
xhr.open('get','temp.json',false);
xhr.onreadystatechange=()=>{
    console.log(xhr.readyState);
};
xhr.send();
// 只输出一次结果是4
```

有图

```javascript
let xhr=nex XMLHttpRequest();
xhr.open('get','temp.json',false);
xhr.send(); // [同步]开始发送AJAX请求，开启AJAX任务，在任务没有完成之前，什么事情都做不了（下面绑定事件也做不了）。LOADING->当readyState===4的时候AJAX任务完成，开始下面的操作
xhr.onreadystatechange=()=>{
    console.log(xhr.readyState);
}
// 绑定方法之前的状态已经变为4了，此时AJAX的状态不会再改变成其他值了，所以事件永远不会被触发，一次都没执行方法。（使用AJAX同步编程，不要把send放在事件监听前，这样我们无法在绑定的方法中获取到响应主体的内容）
```

```javascript
let xhr=new XMLHttpRequest();
xhr.open('get','temp.json');
xhr.onreadystatechange=()=>{
    console.log(xhr.readyState);
}
xhr.send();
//输出三次，结果分别是 2 3 4

let xhr=new XMLHttpRequest();
xhr.open('get','temp.json');
xhr.send();
xhr.onreadystatechange=()=>{
    console.log(xhr.readyState);
}
//输出三次，结果分别是 2 3 4

let xhr=new XMLHttpRequest();
xhr.onreadystatechange=()=>{
    console.log(xhr.readyState);
}
xhr.open('get','temp.json');
xhr.send();
//输出四次，结果分别是1 2 3 4

let xhr=new XMLHttpRequest();
// xhr.readyState===0
xhr.onreadystatechange=()=>{
    console.log(xhr.readyState);
}
xhr.open('get','temp.json',false);
// xhr.readyState===1 
// AJAX特殊处理的一件事：执行open状态变为1，会主动把之前监听的方法执行一次，然后再去执行send
xhr.send();
// xhr.readyState===4 AJAX任务结束，主任务队列完成
```


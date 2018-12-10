## url内置模块

> url.parse(url[,flag])：把一个url地址进行解析，把地址中的每一部分按照对象键值对的方式存储起来
>
> 第二个参数默认是false，设置true可以把问号传参的部分也解析称为对象键值对的方式，一般true

```javascript
let url=require('url');
console.log(url.parse('http://www.zhufengpeixun.cn/main/guide/index/html?form=qq&lx=stu#video'))

Url {
  protocol: 'http:', =>协议
  slashes: true, =>是否有双斜线
  auth: null, 
  host: 'www.zhufengpeixun.cn', =>域名+端口
  port: null, =>端口
  hostname: 'www.zhufengpeixun.cn', =>域名
  hash: '#video', =>哈希值
  search: '?form=qq&lx=stu', =>问号传递的参数[string]
  query: 'form=qq&lx=stu', =>问号传递的参数[string]，不包含问号
  pathname: '/main/guide/index/html', =>请求资源的路径名称
  path: '/main/guide/index/html?form=qq&lx=stu', =>pathname+search
  href: 'http://www.zhufengpeixun.cn/main/guide/index/html?form=qq&lx=stu#video' =>原始解析地址    
}

console.log(url.parse('http://www.zhufengpeixun.cn/main/guide/index/html?form=qq&lx=stu#video',true))
//=>第二个参数默认是false，设置true可以把问号传参的部分也解析称为对象键值对的方式
{
    ...
    query:{form:'qq',lx:'stu'} 和不加true的区别就在query上 
    ...    
}
```

## http内置模块

- let server=http.createServer(); 创建服务

- server.listen(); 监听端口

注意：基于node创建后台程序，我们一般都创建一个server模块，在模块中实现创建web服务，和对于请求的处理（并且我们一般都会把server模块放到当前项目的根目录下）

服务器上有一堆项目代码，这堆项目代码中即可能有服务器端的程序代码，也有可能有客户端的程序代码，而客户端程序代码我们都放到static这个文件夹中

```javascript
static
	都是服务器端需要返回给客户端，由客户端浏览器渲染和解析的(前端项目：包括页面、css、JS、图片等)
server.js
	都是需要在服务器端基于Node执行的（后端项目：一般只有JS）
utils
	fsPromise.js
    
```

我们创建的web服务需要处理两类请求：

1. 静态资源文件的请求处理：想要文件
2. AIP接口的请求处理：想要数据

区别：第一类请求的地址中有后缀名，第二类没有后缀



npm init -y=>package.json，可以配置一下脚本命令

```javascript
{
    ...
    "main":"server.js",
    "scripts":{
     	"server":"node server.js"   
    }    
}
```

server.js server模块

```javascript
let http=require('http'),
    url=require('url'),
    path=require('path'),
    fs=require('fs');

// =>创建web服务
// let server=http.createServer();
// server.listen();
// 或者如下链式写法
let port=8686;
http.createServer(()=>{
    // 当服务创建成功，并且客户端向当前服务器发送了请求，才会执行此回调函数，并且发送一次请求，回调函数就会被触发一次
    console.log(`hello world`)
}).listen(port,()=>{
    // 当服务创建成功，并且端口号已经监听成功后，触发的回调函数
    console.log(`s erver is success,listen on ${prot}`)
});
/*
 * listen EACCES 0.0.0.0:80 
 * 这种错误都是因为端口号被占用了
 *
 * 当服务创建成功，命令行中会一直存在光标闪烁，证明服务器正在运行中（一定要保证服务是运行的）
 * ctrl+c可以结束服务
 *
 * 客户端如何向创建的服务器发请求？
 *   1.对应好协议、域名、端口等信息，在浏览器中或者AJAX中等发送请求即可
 *   2.http://localhost:8686/... 服务在电脑上，localhost本机域名，也就是本机的客户端浏览器，访问本机的服务器端程序
 *	 3.http://IP:8686/... IP做域名访问，如果是内网IP，相同局域网下的用户可以访问这个服务（局域网下访问，需要互相关掉防火墙）。如果是外网IP，所有能联网的基本都可以访问这个服务
 */
```

```javascript
let port=8686;
// 改成实名函数，在这里写内容即可
let handle=function handle(req,res){
    // =>req:request 请求对象，包含了客户端请求的信息
    //   req:url 存储的是请求资源的路径地址及问号传参，例如:/stu/index.html?name=xxx
    //	 req.method 客户端请求的方式，例如GET
    //	 req.headers 客户端的请求头信息，是一个对象
    //	 ...
    // =>res:response 响应对象，包含了一些属性和方法，可以让服务器端返回给客户端内容
    //	 res.write 基于这个方法，服务器端可以向客户端返回内容
    //	 res.end 结束响应
    //	 res.writeHead 重写响应头信息
    
    // =>把请求的url地址中：路径名称&问号传参，分别解析出来
    let {pathname,query}=url.parse(req.url,true);
    
    // res.write('hello');
    // res.end();
    // 也可以这么写，乱码可以在响应头设置编码类型
    res.writeHead(200,{
        'content-type':'text/plain;charset=utf-8'
    })
    res.end('hello'); //服务器端返回给客户端的内容一般都是string或者buffer格式的数据   
}
http.createServer(handle).listen(port,()=>{
    console.log(`s erver is success,listen on ${prot}`)
});
```

创建静态资源服务器server.js

在github里搜mime

npm install mime

yarn add qs

```javascript
// http
let http=require('http'),
    url=require('url'),
    path=require('path'),
    fs=require('fs');
let {readFile,writeFile}=require('./utils/fsPromise'),
    mime=require('mime'),
    qs=require('qs');

//=>公共方法
let responseResult=function(res,returnVal){
    res.writeHead(200,{
        'content-type':'application/json;charset=utf-8'
    })
    res.end(JSON.stringify(returnVal))
}
let readUSER=function readUSER(){
    return readFile(`./json/USER.JSON`).then(result=>{
        return result=JSON.parse(result)
    })
}
let readVOTE=function readUSER(){
    return readFile(`./json/VOTE.JSON`).then(result=>{
        return result=JSON.parse(result)
    })
}

let port=8686;
let handle=function handle(req,res){
    // 客户端请求资源文件(pathname)，服务器端都是到static文件下进行读取，也是根据客户端请求的路径名称进行读取，所以在服务器端基于fs读取文件中内容的时候，直接加上'./static'即可
    let {method,headers:requestHeaders}=req,
        {pathname,query}=url.parse(req.url,true),
        pathREG=/\.([a-z0-9]+)$/i; //捕获文件后缀名的正则
    //=>静态资源文件处理
    if(pathREG.test(pathname)){
        readFile(`./static${pathname}`).then(result=>{
            // 读取成功：根据请求资源文件的类型，设置响应内容的MIME
            let suffix=pathREG.exec(pathname)[1];
            res.writeHead(200,{
                'Content-type':`${mime.getType(suffix)};charset=utf-8`
            });
            res.end(result)
        }).catch(error=>{
            // 读取失败：最可能由于文件不存在（也就是客户端请求的地址是错误的，我们应该响应的内容是404）
            res.writeHead(404,{'Content-type':'text/plain;charset=utf-8'});
            res.end('not found!')
        });
        return;
    }
    
    //=>API接口处理

    //=>GET-USER:根据传递的ID获取指定用户的信息
    if(pathname==='/getUser' && method==='GET'){
        //=>问号传递的信息都在query中存储着
        let {userId=0}=query,
            returnVal={code:1,message:'nozx data!',data:null};
        readUSER().then(result=>{
            //=>result已经是json格式的对象了
            let data=result.find(item=>parseFloat(item['id']===parseFlpat(userId)));
            if(data){
                returnVal={code:0,message:'ok',data};
                responseResult(res,returnVal);
                return;
            }
            throw new Error('');//=>目的是没有数据的时候，让它执行catch中的操作，这样我们只需要then方法中有异常信息即可
        }).catch(error=>responseResult(res,returnVal))

        // 第二种写法，但node版本暂时不支持finally
        // readUSER().then(result=>{
        //     let data=result.find(item=>parseFloat(item['id']===parseFlpat(userId)));
        //     data?returnVal={code:0,message:'ok',data}:null;
        // }).finally(()=>{
        //     //=>node版本暂时不支持finally
        //     responseResult(res,returnVal)
        // })
        
        return;
    }

    //=>register:注册用户(能用GET就用GET，涉及安全用POST)
    //客户端通过请求主体传给服务器，所以内容相对多一点，服务器端需要往USER.JSON多加一条数据
    //把所有的user都得到，然后拼进去，再写进USER.JSON
    if(pathname==='/register'&& method==='POST'){
        //=>接收客户端请求主体传递的内容
        let pass=``;
        //=>当正在接受请求主体内容，因为是一段流一段流接收的，可能会被触发执行很多次。chunk获取的都是本次接收的buffer格式的数据
        req.on('data',chunk=>{
            pass+=chunk;
        })
        //所有的事件绑定都是异步的
        req.on('end',()=>{
            //=>已经把请求主体内容接收完成了，pass是一个URLENCODED格式字符串，我们需要把它解析未对象
            pass=qs.parse(pass);
            let maxId=result.length<=0?0:parseFloat(result[result.length-1][id]);
            //因为简单的md5很容易给解密，所以再进行二次加密，前四位后四位去掉
            pass.password=pass.password.substring(4,24).split('').reverse().join('');
            readUSER().then(result=>{
                //=>FORMAT-PASS
                let newData={
                    id=maxId+1,
                    name:'',
                    picture:`img/${pass.sex!=0?`woman`:`man`}.png,
                    phone:'',
                    sex:0,
                    password:"",
                    bio:'',
                    time:new Date.getTime(),
                    isMatch:0,
                    slogan:'',
                    voteNum:0,
                    ...pass
                }

                //=>把newData追加到result末尾，然后把最新的结果重新写入到文件
                result.push(newData)
                return writeFile('./json/USER.JSON',result)

            }).then(result=>{
                responseResult(res,{
                    code:0,
                    message:'ok'
                })
            }).catch(error=>{
                responseResult(res,{
                    code:1,
                    message:'no'
                });
            });
        })

        return;
    }

    //=>如果都走完了，发现都没有匹配上，说明请求的都不是以上接口，直接返回404
    res.writeHead(404);
    res.end('')
}
http.createServer(handle).listen(port,()=>{
    console.log(`s erver is success,listen on ${prot}`)
});
```

static->test.html 测试接口

```html
<script sec='js/axios.min.js'></script>
<script sec='js/md5.min.js'></script>
<script>
	axios.defaults.baseURL='http://localhost:8686';
    axios.interceptors.response.use(result=>result.data); //=>增加响应拦截器，在所有axios成功后，把获取的结果中data单独返回（data就是服务器返回的json数据，其他信息不需要）
    axios.defaults.transformRequest=data=>{
        //=>基于这个请求拦截器可以把post和put等传递给服务器的请求主体内容进行格式化处理，data就是配置的请求主体内容
        let str=``;
        // data存在并且是个对象才走
        if(data && typeof data==='object'){
            for(let key in data){
                if(data.hasOwnProperty(key)){
                    str+=`${key}=${data[key]}&`
                }
            }
            data=sthr.substring(0,str.length-1); //把最后一个&去掉
        }
        return data
    }
    
    
    // get请求的使用
    axios.get('/getUser',{
        params:{
            //=>GET请求问号传参：设置到params中即可
            userId:1
        }
    }).then(result=>{
        console.log(result)
    })
    
    // post请求的使用
    //=》axios默认基于请求主体传递给服务器的是RAW格式的:'{"name":"xxx"}'，真实项目中我们和服务器约定的传输格式应该是X-WWW-URL-ENCODEED:"name=xxx&xxx"，所以需要配置请求拦截器转一下
    axios.post('/register',{
        //=>请求主体中需要传递给服务器的内容
    },{
        //=>config配置信息：例如可以设置请求头等信息
    })
    axios.post('/register',{
        //=>请求主体中需要传递给服务器的内容
        name:'测试name',
        password:hex_md5('000000'),//密码需要经过md5加密（md5是不可逆转加密）
        phone:'1234567890',
        sex:0,
        bio:'xxxxxxxxx'
    }).then(result=>{
        console.log(result)//成功后的结果
    })
</script>
```






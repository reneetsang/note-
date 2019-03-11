## express

```javascript
// express
let express=require('express'),
    app=express(); //app是个函数
let bodyParser=require('body-parser'),
    session=require('express-session');

//=>创建服务监听端口两件事都处理了，并且以后有请求过来执行的是APP这个方法
let port=8686;
app.listen(port,()=>{
    console.log(`server is success,listen on ${port}`)
}); 

//=>静态资源文件处理
app.use(express.static('./static'));

//=>API处理
app.get('/getUser',(req,res)=>{

    // 当客户端向服务器端发送请求，如果请求方式是get，请求路径是'/getUser'，就会把回调函数触发执行，里面有三个参数，req、res、next
    //  req:request 它不是我们之前原生node中的req，它是express框架封装处理的，但也是存储了很多客户端传递信息的对象
    //      req.params 存储的是路径参数
    //      req.path 请求的路径名称
    //      req.query 请求的问号传参信息（GET请求都是这样传递的），是个对象
    //      req.body 当请求的方式是post，我们基于body-parse中间件处理后，会把客户端请求主体中传递的内容存放到body属性上
    //      req.session 当我们基于express-session中间件处理后，会把session操作放到这个属性上，基于这个属性可以操作session信息
    //      req.cookies 当我们基于cookie-parse中间件处理后，会把客户端传递的cookie信息存放到这个属性上
    //      req.get() 获取指定请求头信息
    //      req.param() 基于这个方法，可以把url-encoded格式字符串（或者路径参数）中的某一个属性名对应的信息获取到
    //      ...

    //  res:response 也不是原生的res，也是经过express封装处理的，目的是为了提供一些属性和方法，可以供服务器端向客户端返回内容
    //      res.cookie(name,value[,options]) 设置cookie信息，通过响应头set-cookie返回给客户端，客户端把返回的cookie信息种到本地
    //      res.type() 设置响应内容的mime类型
    //		res.set() 设置响应头
    //      res.status() 设置返回的状态码 *
    //      res.sendStatus() 设置返回的状态码，但它是会结束响应，把状态码对应的信息当作响应主体返回，一般都用res.status()，自己设置响应主体内容
    //      res.sendFile([pathname]) 把pathname指定的文件中内容得到，然后把内容返回给客户端浏览器（完成了文件读取和响应两步操作）也会自动结束响应执行end
    //      res.json() 向客户端返回json格式的字符串，但是允许传递json格式对象，方法会帮我们转换为字符串再返回（执行此方法后，会自动结束响应，也就是自动执行res.end）
    //      res.send() 你想返回啥随便（综合体）*
    
    //      res.redirect() 响应是重定向的（状态码302）*
    //      res.render() 只有需要页面是服务器渲染的时候，才会用这个
    //		...

    res.send({
        message:'ok'
    });
})

// app.use 就是中间件(middleware)
// app.use('/xxx',(req,res,next)=>{
//     // 请求的path地址中是以'/xxx'开头的，例如:'/admin/user'...    、
//     next();//=>不执行next是无法走到下一个中间件或者请求中的，next就是执行下一个的意思，可能是下一个中间件，也有可能是下一个请求。注意上面GET请求不用写，因为需要的请求已经执行完了，没必要继续往下走。next是同步的
// })
// app.use((req,res,next)=>{
//     // 所有的请求都会走这个中间件，而且中间件执行的顺序是按照书写的先后顺序执行的   
//     next();
// })

//=>body-parsr:如果是post/put请求，会把基于请求主体传递的信息预先截获
//  如果传递的是json格式的字符串，基于bodyParser.json()会把它转换为json格式的对象
//  如果传递的是url-encoded格式的字符串，会基于bodyParser.urlencoded()把它转换为对象键值对的方式
//  ...
//  把转换后的结果挂在到req.body属性上
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended:false}))

//=>express-session:这个中间件是供我们后续操作session的，基于这个中间件，我们可以设置客户端cookie的过期时间（也理解为session在服务器端存储的时间），当中间件执行完成后，会在req上挂载一个session的属性，用来操作session
app.use(session({
    secret:'zfpx',// 密钥
    saveUninitialized:false,
    resave:false,
    cookie:{maxAge:1000*60*60*24*30} // 设置cookie最大存储期限
}))

app.post('./register',(req,res)=>{
    // GET请求，接收问号传参的信息，可以使用:req.query/req.param()
    // POST请求，接收请求主体传递的信息，此时我们需要使用一个中间件（body-parser）

    console.log(req.body); //=>获取的是请求主体内容
    req.session.xxx='xxx'; //设置session
    console.log(req.session.xxx); //获取session
})

let $=document.querySelector.bind(document);
function move(ele,target){
    return new Promise((resolve,reject)=>{
        let left=0;
        let timer=setInterval(function(){
            if(left>=target){
                clearInterval(timer);
                return resolve();
            }
            left++;
            ele.style.left=left+'px';
        },6)
    })
}

async function gen(){
    let b=100;
    try{
        let a=await move($('.box1'),300);
        let b=await move($('.box2'),200);
        let c=await move($('.box3'),100);
    }catch(e){
        console.log('catch e')
    }
    return d;
}
gen().then((data)=>{ //最后会走成功，因为上面在里面try catch都截获了
    console.log(data)
}).catch((err)=>{
    console.log('err',err)
})


//
let express=require('express'),
    app=express(); //app是个函数
let bodyParser=require('body-parser'),
    session=require('express-session');

let { readFile, writeFile }=require('./utils/fsPromise'),
    pathDataUSER='./json/USER.json',
    pathDataVOTE='./json/VOTE.json';

let port=8686;

//=>创建服务
app.listen(port,()=>{
    console.log(`正在监听${port}端口`)
})

//=>处理API

//=>处理静态资源请求
app.use(express.static('./static')); //如果请求的是静态资源文件，就不再往下走了
app.use((req,res,next)=>{});

```


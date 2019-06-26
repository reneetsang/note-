## AJAX类库的封装

### JQ中的AJAX及每一个配置的作用

```javascript
$.ajax({
    url:'xxx.txt', // 请求API的地址
    method:'get', // 请求方式GET/POST...在老版本中JQ中使用的是type，使用type和method
    dataType:'json', // dataType只是我们预设获取结果的类型，不会影响服务器的返回（服务器端一般给我们返回的都是JSON格式字符串），如果我们预设的是json，那么类库中把服务器返回的字符串转换为json对象。如果我们预设的是text（默认值），我们把服务器获取的结果直接拿过来操作即可。我们预设的值还可以是xml等
    cathe:false, // 设置是否清楚缓存，只对GET系列请求有作用，默认是TRUE不清除缓存，手动设置为FALSE，JQ类库会在请求URL的末尾追加一个随机数来清除缓存
    data:null, // 我们通过data可以把一些信息传递给服务器。GET系列请求会把data中的内容拼接在URL的末尾通过问号传参的方式传递给服务器，POST系列请求会把内容放在请求主体中传递给服务器。data的值可以设置为两种格式：字符串、对象，如果是字符串，设置的值是什么传递给服务器的就是什么，如果设置的是对象，JQ会把对象变为xxx=xxx&xxx=xxx这样的字符串传递给服务器
    success:function(result){
        // 当AJAX请求成功(readyState==4&status是以2或者3开头的)
        // 请求成功后JQ会把传递的回调函数执行，并且把获取的结果当做实参传递给回调函数(result就是我们从服务器端获取的结果)
    }，
    error:function(msg), // 请求错误触发回调函数
    complate:function(){}, // 完成触发，不管请求时错误还是正确的，都会触发这个回调函数
    ……
})
```

### 封装自己的AJAX库

```javascript
~function () {
    class ajaxClass{
        // send ajax
        init(){
            // 注意：this是实例
            let xhr=new XMLHttpRequest();
            xhr.onreadystatechange=()=>{
                if(!/^[23]\d{2}$/.test(xhr.status)) return
                if(xhr.readyState===4){ // 成功
                    let result=xhr.responseText;
                    // DATATYPE
                    try{
                        switch(this.dataType.toUpperCase()){
                            case 'TEXT':
                            case 'HTML':
                                break;
                            case 'JSON':
                                result=JSON.parse(result);
                                break;
                            case 'XML':
                                result=xhr.responseXML;  
                        } 
                    }catch(e){
                        
                    }

                    this.success(result);
                }
            }
            // DATA
            if(this.data!==null){
                this.formatData();
                // 是GET请求就把内容拼接在url末尾
                if(this.isGET){
                    this.url+=this.querySymbol()+this.data;
                    this.data=null; // 这样写之后，可以直接在send里放data，这里是null，xhr.send(this.data);
                }
            }

            // 是否处理CACHE
            this.isGET?this.cacheFn():null;
            xhr.open(this.method,this.url,this.async);
            xhr.send(this.data);
        }
        // 把传递的格式对象data转换为字符串格式data
        formatData(){
            // 如果是对象
            if(({}).toString.call(this.data)=='[object Object]'){
                let obj=this.data,
                    str='';
                for(let key in obj){
                    if(obj.hasOwnProperty(key)){
                        str+=`${key}=${obj[key]}&`;
                    }
                    // 最后一个多出来的&要删掉
                    str=str.replace(/&$/g,'');
                    this.data=str;
                }
            }
        }
        cacheFn(){
            !this.cache?this.url+=`${this.querySymbol()}_=${Math.random()}`:null;
        }
        querySymbol(){
            // 如果有问号，说明url后面有带参数了，要用&符号
            return this.url.indexOf('?')>-1?'&':'?';
        }

    }
    let ajax=function(){}
    window.ajax=function({
        url=null,
        method='GET',
        type=null,
        data=null,
        dataType='JSON',
        cache=true,
        async=true,
        success=null
    }={}){
        let _this=new ajaxClass();
        ['url','method','data','dataType','cache','async','success'].forEach((item)=>{
            if(item==='method'){
                _this.method=type===null?method:type;
                return;
            }
            if(item==='success'){
                _this.success=typeof success ==='function'?success:new Function();
                return;
            }
            _this[item]=eval(item);// 把属性变成对应的变量
        })
        // example.url=url;
        // example.method=type===null?method:type;
        // example.data=data;
        // example.dataType=dataType;
        // example.cache=cache; // false代表清缓存
        // example.async=async;
        // example.success=typeof success ==='function'?success:new Function();
        _this.isGET=/^(GET|DELETE|HEAD)$/i.test(_this.method);
        _this.init();
        return _this;
    }
}()
ajax({})
```


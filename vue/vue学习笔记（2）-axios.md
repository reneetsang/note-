## axios

```javascript
<!--基于promise的-->
<script src="node_modules/axios/dist/axios.js"></script>
<script>
    let vm=new Vue({
        el:'#app',
        created(){ //vue提供专门获取ajax的方法，在数据被初始化后会调用，this指向的也是vm实例，钩子函数
            //this是实例
            //Promise解决回调问题的
            axios.get('./json/cart.json').then((res)=>{ //success
                console.log(res); //res里有一堆信息，数据放在data里
                this.products=res.data; //若写回调函数，this是window，所以用箭头函数
            }, (err) => { //error

            })
        },
        data:{
            products:[]
        }
    })
</script>
```

## promise

```javascript
let a='';
function buy(callback) {
    setTimeout(()=>{
        a='蘑菇';
        callback(a)
    },2000)
}
buy(function cookie(val) {
    console.log(val);
})
//回调函数 将后续的处理逻辑传入到当前要做的事，事情做好后调用此函数
//promise 解决回调问题的
//promise有三个状态 成功态 失败态 等待

// resolve代表的是转向成功态
// reject代表的是转向失败态 resolve和reject均是函数
// promise的实例有一个then方法，then方法中有两个参数,resolve（成功）执行调用then第一个方法
let p=new Promise((resolve,reject)=>{

})
p.then((data)=>{console.log(data)},(err)=>{console.log(err)})

```

> 自己写类似于axios的方法

```javascript
function ajax({url='',type='get',dataType='json'}) {
    return new Promise((resolve,reject)=>{
        let xhr = new XMLHttpRequest();
        xhr.open(type,url,true);
        xhr.responseType=dataType;
        xhr.onload=function () {
            if(xhr.status===200){
                resolve(xhr.response) //成功调用的方法
            }else {
                reject('no found')
            }
        };
        xhr.onerror=function (err) {
            reject(err); //失败调用失败的方法
        }
        xhr.send()
    })
}

created(){ //vue提供专门获取ajax的方法，在数据被初始化后会调用，this指向的也是vm实例，钩子函数
  //this是实例
  //Promise解决回调问题的
  ajax({url:'./json/cart.json'}).then((res)=>{ //success
    console.log(res); //res里有一堆信息，数据放在data里
    this.products=res.data; //若写回调函数，this是window，所以用箭头函数
  }, (err) => { //error

  })
}
```


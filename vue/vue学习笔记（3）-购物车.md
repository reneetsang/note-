## vue购物车

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>vue购物车</title>
    <link rel="stylesheet" href="node_modules/bootstrap/dist/css/bootstrap.css">

</head>
<body>
<div id="app">
    <div class="container">
        <div class="row">
            <table class="table table-hover table-bordered">
                <caption class="h2">购物车</caption>
                <tr>
                    <!--click点击是，checkbox的状态还没有改变，所以拿到的总是相反的，change可以保证只当值变化后再触发函数-->
                    <th>全选<input type="checkbox" v-model="checkAll"></th>
                    <th>商品</th>
                    <th>单价</th>
                    <th>数量</th>
                    <th>小计</th>
                    <th>操作</th>
                </tr>
                <tr v-for="(product,index) in products">
                    <td><input type="checkbox" v-model="product.isSelected"></td>
                    <!--动态绑定数据v-bind可以缩写成冒号，在属性里想取变量要用冒号-->
                    <td>
                        <img :src='product.productCover' :title="product.productName">
                        {{product.productName}}
                    </td>
                    <td>
                        {{product.productPrice}}
                    </td>
                    <td>
                        <!--number是让输入框的值变成数字类型，.lazy当输入框失去焦点时更新数据-->
                        <input type="number" v-model.number.lazy="product.productCount" min="1">
                    </td>
                    <td>
                        <!--过滤器，原数据不变的情况下，只是改变显示的效果，管道符-->
                        <!--比如有时候前面要加钱符号-->
                        {{product.productCount*product.productPrice | toFixed(2 )}}
                    </td>
                    <td><button class="btn btn-danger" @click='remove(product)'>删除</button></td>
                </tr>
                <tr>
                    <!--{{sum() 数据一变化就会重新调用此函数，算出最新的结果，不会缓存上一次解决，computed可以解决这个问题}}-->
                    <!--sum是计算属性，不是方法了-->
                    <td colspan="6">总价格:{{sum|toFixed(2)}}</td>
                </tr>
            </table>
        </div>
    </div>
</div>

<script src="node_modules/vue/dist/vue.js"></script>
<!--基于promise的-->
<script src="node_modules/axios/dist/axios.js"></script>
<!--<script src="js/promise.js"></script>-->
<script>
    let vm=new Vue({
        el:'#app',
        //当给全选赋值时，要影响其他人的变化，当页面刷新时，获取全选值，是根据下面的checkbox计算出来的结果给全选赋值 Object.definePropert
        computed:{ //放在computed中最后也会放在vm上，所以不能和methods和data重名
            checkAll:{
                //也会有缓存，当products值变化时会重新计算
                get(){ //get和set指向实例 默认v-model会获取checkAll的值，所以会调用get方法
                    return this.products.every(p=>p.isSelected)
                },
                set(val){ //当给checkbox赋值的时候，val的值是true或false
                    this.products.forEach(p=>p.isSelected=val)
                }
            },
            sum(){ //如果把计算属性写成函数，会默认调用get方法
                    return this.products.reduce((prev,next)=>{
                        if(!next.isSelected)return prev; //如果当前没被选中，就不加这一项
                        return prev+next.productPrice*next.productCount;
                    },0)
                }
//            sum:{ //sum的结果会被缓存，如果依赖的数据没有变化就不会执行
//                get(){
//                    return this.products.reduce((prev,next)=>{
//                        if(!next.isSelected)return prev; //如果当前没被选中，就不加这一项
//                        return prev+next.productPrice*next.productCount;
//                    },0)
//                }
//            }
        },
        filters:{ //可以有好多的自动以过滤器
            toFixed(input,prama1){ //第一个参数就是竖线前面的内容，prama1代表传参
                //这里的this指向window
                //return返回什么，显示就是什么
                return '$'+input.toFixed(prama1)
            }
        },
        created(){ //vue提供专门获取ajax的方法，在数据被初始化后会调用，this指向的也是vm实例，钩子函数
            //this是实例
            this.getData()
        },
        methods:{
/*            sum(){ 这些方法都写成计算属性了，注释掉
                return this.products.reduce((prev,next)=>{
                    if(!next.isSelected)return prev; //如果当前没被选中，就不加这一项
                    return prev+next.productPrice*next.productCount;

                },0)
            },*/
//            change(){
//                this.products.forEach(item=>item.isSelected=this.checkAll)
//            },
            remove(p){ //p代表的是当前点击的这一项
                this.products=this.products.filter(item=>item!==p);
            },
          getData(){
              //Promise解决回调问题的
              axios.get('./json/cart.json').then((res)=>{ //success
                  console.log(res); //res里有一堆信息，数据放在data里
                  this.products=res.data; //若写回调函数，this是window，所以用箭头函数
              }, (err) => { //error

              })
          }
        },
        data:{
//            checkAll:true, 全选这个不要了，这个应该是计算出来的结果
            products:[]
        }
    })
</script>
</body>
</html>
```

### 知识点

#### filters

```
{{'123' | my(1,2,3)}}
filters:{
  my(data,param1,param2,param3){ //第一个参数data就是竖线前面的内容123
	//这里的this指向window
	//return返回什么，显示就是什么
  }
}
```

```html
<div id='app1'>{{'hello' | my}}</div> 
<div id='app2'>{{'hello' | my}}</div>
```

```javascript
//全局过滤器，要放在页面的顶部，放在下面都已经new了，再写没有用
Vue.filter('my',(data)=>{
  return 'renee'+data;
})
new Vue({
  el:'#app1'
})
new Vue({
  el:'#app2'
})
```

### computed计算“属性”，不是方法

> 方法不会有缓存，computed会根据依赖（归vue管理的数据，可以响应式变化的）的属性进行缓存
>
> 两部分组成有get和set（不能只写set），一般情况下通过js赋值影响其他人或者表单元素设置值的时候回调用set方法

```html
<div id='app'>
  <input type='text' v-model='a'>{{msg}}
  <!--根据输入框中的内容 算出了一个错误信息-->
</div>
```

```javascript
let vm=new Vue({
  el:'app',
  data:{a:''},
  computed:{
    msg(){
      if(this.a.length<3){return '少了'}
      if(this.a.length>3){return '多了'}
      return '';
    }
  }
})
```


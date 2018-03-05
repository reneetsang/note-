## 安装

npm install vue axios bootstrap

## vue基础知识点

- vm=>viewModel 数据最终会被vm所代理
- {{a}} 取值表达式，通过{{}}来进行取值，默认可以不写this，里面可以写表达式、赋值运算、计算、三元表达式，但尽量少写逻辑（用computed)
- 指令：dom元素的行间属性，vue提供了内置的指令，必须v-开头，后面的值均为变量

## 指令

- v-model（表单元素） 忽略掉value,checked,selected，将数据绑定到视图上，视图修改后会影响数据的变化

  ```javascript
  <input type='checkbox' v-model='a'> //如果是复选框，只有一个复选框的时候，会把此值转化成boolean类型，true为选中
   
   //如果是多个checkbox，要增加value属性，并且数据类型是数组 b:[] 里面的值是value的内容
  <input type='checkbox' v-model='b' value='游泳'>游泳
  <input type='checkbox' v-model='b' value='爬山'>爬山
  {{b}}
  ```

  ```html
  <select v-model='a'>
    <option val='' disabled>请选择</option>
    <option val='1'>你好</option>
    <option val='2'>不好</option>
    <option val='3'>我好</option>
  </select>
  <!-- 如果value属性不写，默认取得是option的值 -->
  {{a}}

  data:{
    a:''
  }
   
   <!-- 多选select，则数据类型是数组类型 -->
   <select v-model='a' multipe>
    <option val='' disabled>请选择</option>
    <option val='1'>你好</option>
    <option val='2'>不好</option>
    <option val='3'>我好</option>
  </select>
   data:{
    a:[]
  }
   
   <!-- 单选radio -->
   <input type='radio' v-model='gender' value='男'>男
   <input type='radio' v-model='gender' value='女'>女
    data:{
    	gender:'男'
  }
  ```

  ​

- v-text 取代{{}},这个方式并不好，v-cloak 解决闪烁（块级）问题，不常用，若使用v-cloak要加样式

  ```javascript
  //因为js在下面加载，当vue完全加载后，[v-clock]变成block
  [v-clock]{display:none}
  ```

- v-once 绑定一次，数据再变化也不会导致视图刷新，写在不想刷新的标签上

- v-html 将字符串转化为html

- v-for 循环（数组，对象，字符串，数字）

  ```javascript
  <div v-for='(value,index) in/of 数组'></div>
  ```

## 事件v-on(@) 

- 绑定给dom元素，函数需要定义在methods中，不能和data里的内容重名，this指向实例，不能使用箭头函数，事件源对象如果不写括 号，则会自动传入，如果写了括号要传参，只能手动传入**$event**

  ```javascript
  <div @事件名='fn'></div>
  ```

  ​
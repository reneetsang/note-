



## ES6-数组、对象方法

### 数组方法

#### Array.from()

> 将类数组转化数组，arguments dom对象都是类数组 

```javascript
let arr = Array.from({0:1,1:2,2:3,length:3})
console.log(arr); //[1, 2, 3]
```

> 用[...]转化成数组，需要当前类数组有“迭代器” 
>
> 什么是迭代器，迭代器需要返回一个对象对上有一个next方法，每次调用next方法会返回一个新对象，对象上有两个属性，分别是value和done

```javascript
let obj = {0:1,1:2,2:3,length:3,[Symbol.iterator]:function(){
    let index = 0; // 当前迭代到了第几个
    let self = this; // this指代的是当前对象
    return {
        next:function(){ // value代表的是当前的内容 done代表的是是否迭代完成
            return {value:self[index],done:index++ === self.length?true:false}
        }
    }
}}
console.log([...obj]); // 迭代器就是有next方法,直到done为ture就结束
```

#### Array.reduce()  收敛

> reduce() 方法接收一个函数作为累加器，数组中的每个值（从左到右）开始缩减，最终计算为一个值。
>
> reduce() 可以作为一个高阶函数，用于函数的 compose。
>
> **注意:** reduce() 对于空数组是不会执行回调函数的。

```javascript
array.reduce(function(total, currentValue, currentIndex, arr), initialValue)
```

> - total 必需。初始值, 或者计算结束后的返回值。
> - currentValue	必需。当前元素
> - currentIndex	可选。当前元素的索引
> - arr	可选。当前元素所属的数组对象。

> 原理

```javascript
// prev代表的是数组的第一项
// next代表的是数组的第二项
// 当前迭代到了第几项
// current当前数组
// reduce会将执行后的结果作为下一次的prev
// 参数可以多加一项
Array.prototype.myreduce = function(callback){
    let prev = this[0];
    for(var i = 1; i<this.length;i++){
        prev = callback(prev,this[i],i,this); //函数的返回结果会作为下一次的prev
    }
    return prev
}
let result = [1,2,3,4,5].myreduce((prev,next,currentIndex,current)=>{
    if(currentIndex === current.length-1){
        return (prev+next)/current.length
    }
    return prev+next
})
console.log(result);
```

#### Array.map() 映射

> map() 方法返回一个新数组，数组中的元素为原始数组元素调用函数处理后的值。
>
> map() 方法按照原始数组元素顺序依次处理元素。
>
> **注意：** map() 不会对空数组进行检测。
>
> **注意：** map() 不会改变原始数组。

```javascript
array.map(function(currentValue,index,arr), thisValue)
```

>- currentValue 必须。当前元素的值
>- index 可选。当前元素的索引值
>- arr  可选。当前元素属于的数组对象

> 原理

```javascript
Array.prototype.mymap = function(callback){
    let newArr = [];
    for(var i = 0;i<this.length;i++){
        newArr.push(callback(this[i],i))
    }
    return newArr;
}
let newArr = [1,2,3].mymap((item,index)=>{
    return `<li>${item}</li>`
});
console.log(newArr);
```

#### Array.filter() 过滤

> 返回一个过滤后的数组，方法中返回true表示留下
>
> **注意：** filter() 不会改变原始数组。

```javascript
array.filter(function(currentValue,index,arr), thisValue)

let newArr = [1,2,3,4,5,6].filter(function(item,index){
    return item<4
});
console.log(newArr);
```

>- currentValue	必须。当前元素的值
>- index	可选。当前元素的索引值
>- arr	可选。当前元素属于的数组对象

> 原理

```javascript
Array.prototype.myFilter = function(callback){
    let arr = [];
    for(let i = 0;i<this.length;i++){
        callback(this[i],i)?arr.push(this[i]):void 0;
    }
    return arr;
}

let filterArr = [1,2,3,4,5].myFilter(function(item,index){
    if(item>3)return true
});
console.log(filterArr);
```

#### Array.some()

> 找true 找到后返回true ，不继续向下找

#### Array.every()

> 和some相反

```javascript
let boolean = [1,2,3,4,4,4,4,4,4,4].every(function(item,index){
    console.log(1);
    return item === 4
})
console.log(boolean);
```

### 对象方法

#### Object.keys()

> Object.keys方法，返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（ enumerable ）属性的键名。

```javascript
var obj = { foo: "bar", baz: 42 };  
Object.keys(obj)  
// ["foo", "baz"] 
```

#### Object.values()

> Object.values方法返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（ enumerable ）属性的键值。

```javascript
var obj = { foo: "bar", baz: 42 };  
Object.values(obj)  
// ["bar", 42]   
```

## ES5

### 字符串

#### String.substr()

> substr(n,m) 从索引n开始截取m个字符

```javascript
stringObject.substr(start,length)
```

#### String.repeat(count);

> 返回一个新的字符串对象，它的值等于重复了指定次数count的原始字符串。

```javascript
stringObj.repeat(count);
```




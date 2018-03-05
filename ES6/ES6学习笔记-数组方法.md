## ES6学习笔记-数组、对象方法

### 数组方法

#### Array.from()

> 将类数组转化数组，arguments dom对象都是类数组 

```javascript
let arr = Array.from({0:1,1:2,2:3,length:3})
console.log(arr);
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

> 返回一个过滤后的数组,方法中返回true表示留下

```javascript
let newArr = [1,2,3,4,5,6].filter(function(item,index){
    return item<4
});
console.log(newArr);
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


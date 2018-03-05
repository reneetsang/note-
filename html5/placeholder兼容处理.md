## placeholder兼容处理

> 整个IE浏览器对于placeholder兼容性都不好
>
> 1、IE10+虽然兼容这个属性，但是文本框获取焦点后，提示信息就消失了
>
> 2、IE9-不兼容这个属性

```html
<div class="inputBox">
    <input type="text" data-place="请输入用户名" id="userName">
    <!--<span class="placeLike">请输入用户名</span>-->
</div>
```

```javascript
~function () {
    var inputList = document.getElementsByTagName('input'),
        inputAry = [];
    for (var i = 0, len = inputList.length; i < len; i++) {
        var item = inputList[i];
        item.getAttribute('data-place') !== null? inputAry.push(item) : null;
    }

    //在非IE浏览器中，只需要把自定义属性存放在placeholder中即可，浏览器可以自己根据这个属性做好提示的工作

    if (!/(MSIE|Trident)/i.test(navigator.userAgent)) {
        console.log(inputAry);
        var itemInp = null;
        for (var k = 0; k < inputAry.length; k++) {
            itemInp = inputAry[k];
            itemInp.placeholder = itemInp.getAttribute('data-place')
        }
    }
    //IE浏览器（包含IE EDGE）
    for (var z = 0; z < inputAry.length; z++) {
        var inputItem = inputAry[z],
            inputText = inputItem.getAttribute('data-place');
        inputItem.placeholder = '';
        //1、新创建一个span，把其存放在input它爹末尾（作为input的弟弟）
        //2、给span设置一定的样式（相对于父级元素定位，和input的基础样式类似）
        //3、input或者span都要绑定相关的事件行为：完成和内置placeholder相同的效果
        var spanTip = document.createElement('span');
        spanTip.innerHTML = inputText;
        spanTip.className = 'placeLike';
        inputItem.parentNode.appendChild(spanTip);

        //把每一个input和span的索引进行存储
        inputItem.index=spanTip.index=z;
        inputItem.spanTip=spanTip;

        //span的点击行为：点击span让input获取对应的光标
        spanTip.onclick=function () {
            inputAry[this.index].focus()
        }

        //控制input的输入行为（建议使用DOM2事件绑定：防止后期再其他地方也需要通过keyup或者keydown行为处理其他事情）
        inputItem.onkeydown=inputItem.onkeyup=function () {
            var value=this.value,
                spanTip=this.spanTip;
            spanTip.style.display=value.length>0?'none':'block';
        }
    }
}()
```


```javascript
var utils=(function(){
    var flag='getComputedStyle' in window;

    //->listToArray:把类数组转换为数组
    //用slice将类数组转化为数组时 由于DOM元素集合在Ie6-8中不兼容 所以我们不能用
    function listToArray(likeAry) {
        if(flag){return Array.prototype.slice.call(likeAry, 0)}
        var ary = [];
        for (var i = 0; i < likeAry.length; i++) {
            ary[i] = likeAry[i];
        }
        return ary;
    }
    //->toJSON:将JSON格式的字符串转化为JSON格式的对象
    function toJSON(jsonStr) {
        var jsonObj = null;
        try {
            jsonObj = JSON.parse(jsonStr);
        } catch (e) {
            jsonObj = eval("(" + jsonStr + ")");
        }
        return jsonObj;
    }
    //win：获取浏览器的相关盒子模型信息
    function win(attr){
        return document.documentElement[attr] || document.body[attr];
    }

    //->children:获取指定标签名的所有的元素子节点(获取所有的元素子节定，我们可以通过元素的标签名在进行过滤)
    function children(curEle,tagName){
        var ary=[];
        if(flag){
            ary=this.listToArray(curEle.children);
        }else{
            var nodeList=curEle.childNodes;
            for(var i=0;i<nodeList.length;i++){
                var curNode=nodeList[i];
                curNode.nodeType===1?ary[ary.length]=curNode:null;
            }
            nodeList=null;
        }
        if(typeof tagName==="string"){
            for(var k=0;k<ary.length;k++){
                var curEleNode=ary[k];
                if(curEleNode.nodeName.toLowerCase()!==tagName.toLowerCase()){
                    ary.splice(k,1);
                    k--;//
                }
            }
        }
        return ary;
    }

    //->prev:获取上一个哥哥元素节点
    function prev(curEle){
        if(flag){return curEle.previousElementSibling;}
        var pre=curEle.previousSibling;
        while(pre&&pre.nodeType!==1){
            pre=pre.previousSibling;
        }
        return pre;
    }

    //->next:获取下一个弟弟元素节点
    function next(curEle){
        if(flag){ return curEle.nextElementSibling;}
        var nex=curEle.nextSibling;
        while(nex&&nex.nodeType!==1){
            nex=curEle.nextSibling;
        }
        return nex;
    }

    //->prevAll:获取所有的哥哥元素节点
    function prevAll(curEle){
        var ary=[];
        var pre=this.prev(curEle);
        while(pre){
            ary.unshift(pre);
            pre=this.prev(pre);
        }
        return ary;
    }

    //->nextAll:获取所有的弟弟元素节点
    function nextAll(curEle){
        var ary=[];
        var next=this.next(curEle);
        while(next){
            ary.push(next);
            next=this.next(next);
        }
        return ary;
    }

    //->sibling:获取相邻两个元素节点
    function sibling(curEle){
        var pre=this.prev(curEle);
        var nex=this.next(curEle);
        var ary=[];
        pre?ary.push(pre):null;
        nex?ary.push(nex):null;
        return ary;
    }

    //->siblings:获取所有的兄弟元素节点
    function siblings(curEle){
        return this.prevAll(curEle).concat(this.nextAll(curEle))
    }

    //->index:获取当前元素的索引
    function index(curEle){
        return this.prevAll(curEle).length;
    }

    //->firstChild:获取第一个元素子节点
    function firstChild(curEle){
        var chs=this.children(curEle);
        return chs.length>0?chs[0]:null;
    }

    //->lastChild:获取最后一个元素子节点
    function lastChild(curEle){
        var chs=this.children(curEle);
        return chs.length>0?chs[length-1]:null;
    }

    //->append:向指定容器末尾追加元素
    function append(newEle,container){
        container.appendChild(newEle);
    }

    //->prepend:向指定容器的开头追加元素（把新的元素添加到第一个子元素节点前面，如果一个子元素节点都没有，就放在末尾即可）
    function prepend(newEle,container){
        var fir=this.firstChild(container);
        if(fir){
            container.insertBefore(newEle,fir);
        }
        this.append(newEle,container);
    }

    //->insertBefore:把新元素（newEle）追加到指定元素（oldEle）前面
    function insertBefore(newEle,oldEle){
        oldEle.parentNode.insertBefore(newEle,oldEle);
    }

    //->insertAfter:把新元素（newEle）追加到指定元素（oldEle）的后面
    //相当于追加到oldEle弟弟元素的前面，如果弟弟不存在，也就是当前元素是最后一个了，把新元素放在最末尾即可
    function insertAfter(newEle,oldEle){
        var nex=this.next(oldEle);
        if(nex){
            oldEle.parentNode.insertBefore(newEle,nex);
        }
        oldEle.parentNode.appendChild(newEle);
    }

    //getElementsByClass:通过元素的样式名获取一组元素集合(兼容全部的浏览器)
    function getElementsByClass(strClass,context){
        context=context||document;
        if(flag){
            return this.listToArray(context.getElementsByClassName(strClass))
        }
        var ary=[],strClassAry=strClass.replace(/(^ +| +$)/g,"").split(/ +/g);
        var nodeList=context.getElementsByTagName("*");
        for(var i=0,len=nodeList.length;i<len;i++){
            var curNode=nodeList[i];
            var isOk=true;
            for(var k=0;k<strClassAry.length;k++){
                var reg=new RegExp("(^ +|)"+strClassAry[k]+"(| +$)");
                if(!reg.test(curNode.className)){//如果不是要的className
                    isOk=false;
                    break;
                }
            }
            if(isOk){
                //ary[ary.length]=curNode;
                ary[i]=curNode;
            }
        }
        return ary;
    }

    //hasClass:验证当前元素中是否包含className这个样式名
    function hasClass(curEle,className){
        var reg=new RegExp("(^| +)"+className+"( +|$)");
        return reg.test(curEle.className)
    }

    //addClass:给元素增加样式类名
    function addClass(curEle,className){
        var ary=className.replace(/(^ +| +$)/g,"").split(/ +/g);
        for(var i=0,len=ary.length;i<len;i++){
            var curName=ary[i];
            if(!hasClass(curEle,curName)){
                curEle.className+=" "+curName;
            }

        }
    }

    //removeClass:给元素移除样式类名
    function removeClass(curEle,className){
        var ary=className.replace(/(^ +| +$)/g,"").split(/ +/g);
        for(var i=0,len=ary.length;i<len;i++){
            var curName=ary[i];
            if(this.hasClass(curEle,curName)){
                var reg=new RegExp("(^| +)"+className+"( +|$)");
                curEle.className=curEle.className.replace(reg,"")
            }
        }
    }

    //getCss:获取当前元素所有经过浏览器计算的样式(兼容全部的浏览器)
    function getCss(attr){
        var val=null,reg=null;
        if(flag){
            val=window.getComputedStyle(this,null)[attr];
        }else{
            if(attr==="opacity"){
                val=this.currentStyle["filter"];
                reg = /^alpha\(opacity=(.+)\)$/;
                val=reg.test(val)?reg.exec(val)[1] / 100 : 1;
            }else{
                val=this.currentStyle[attr];
            }
        }
        reg = /^-?(\d|([1-9]\d+))(\.\d+)?(px|em|rem|pt)$/;
        reg.test(val) ? val = parseFloat(val) : null;
        return val;
    }


    //setCss:给当前元素的某一个样式属性设置值（增加在行内样式）
    function setCss(attr,value){
        if(attr==="float"){
            this["style"]["cssFloat"]=value;
            this["style"]["styleFloat"]=value;
            return;
        }
        if(attr==="opacity"){
            this["style"]["opacity"]=value;
            this["style"]["filter"]="alpha(opacity="+value*100+")";
            return;
        }
        var reg=/^(width|height|top|bottom|left|right|((margin|padding)(Top|Bottom|Left|Right)?))$/;
        if(reg.test(attr)){
            if(!isNaN(value)){//如果是有效数字
                value+="px";
            }
        }
        this["style"][attr]=value;
    }


    //setGroupCss:给当前元素批量的设置样式属性值
    //因为在css中处理了，所以传进来的一定是对象
    function setGroupCss(options){
/*
        options=options||0;
        if(options.toString()!=="[object Object]"){
            return;
        }
        */
        for(var key in options){
            if(options.hasOwnProperty(key)){
                //this.setCss(curEle,key,options[key])
                setCss.call(this,key,options[key])
            }
        }

    }


    //css:此方法实现了获取、单独设置、批量设置元素的样式值
    //优化:getCss、setCss、setGroupCss都不用了，用css实现这几个功能，把getCss、setCss、setGroupCss中的curEle去掉，把方法里的curEle改成this(this变成当前元素)
    function css(curEle){
        var ary=Array.prototype.slice.call(arguments,1);//把传进来的参数中的curEle去掉
        var argTwo=arguments[1];
        if(typeof argTwo==="string"){
            if(typeof arguments[2]==="undefined"){
                return getCss.apply(curEle,ary); //this变成当前元素
            }
            setCss.apply(curEle,ary)
        }
        argTwo=argTwo||0;
        if(argTwo.toString()==="[object Object]"){
            //console.log(this)
            setGroupCss.apply(curEle,ary)
        }
    }

    return{
        listToArray: listToArray,
        toJSON: toJSON,
        win:win,
        children:children,
        prev:prev,
        next:next,
        prevAll:prevAll,
        nextAll:nextAll,
        sibling:sibling,
        siblings:siblings,
        index:index,
        firstChild:firstChild,
        lastChild:lastChild,
        append:append,
        prepend:prepend,
        insertBefore:insertBefore,
        insertAfter:insertAfter,
        getElementsByClass:getElementsByClass,
        hasClass:hasClass,
        addClass:addClass,
        removeClass:removeClass,
        css:css
    }

})();

```

## tween.js

```javascript
~function(){
    var zhufengEffect = {
        // 匀速运动公式
        Linear: function(t, b, c, d) {
            return t / d * c + b;
        },
        //指数衰减的反弹缓动
        Bounce: {
            easeIn: function(t, b, c, d) {
                return c - zhufengEffect.Bounce.easeOut(d - t, 0, c, d) + b;
            },
            easeOut: function(t, b, c, d) {
                if ((t /= d) < (1 / 2.75)) {
                    return c * (7.5625 * t * t) + b;
                } else if (t < (2 / 2.75)) {
                    return c * (7.5625 * (t -= (1.5 / 2.75)) * t + .75) + b;
                } else if (t < (2.5 / 2.75)) {
                    return c * (7.5625 * (t -= (2.25 / 2.75)) * t + .9375) + b;
                } else {
                    return c * (7.5625 * (t -= (2.625 / 2.75)) * t + .984375) + b;
                }
            },
            easeInOut: function(t, b, c, d) {
                if (t < d / 2) {
                    return zhufengEffect.Bounce.easeIn(t * 2, 0, c, d) * .5 + b;
                }
                return zhufengEffect.Bounce.easeOut(t * 2 - d, 0, c, d) * .5 + c * .5 + b;
            }
        },
        //二次方的缓动
        Quad: {
            easeIn: function(t, b, c, d) {
                return c * (t /= d) * t + b;
            },
            easeOut: function(t, b, c, d) {
                return -c * (t /= d) * (t - 2) + b;
            },
            easeInOut: function(t, b, c, d) {
                if ((t /= d / 2) < 1) {
                    return c / 2 * t * t + b;
                }
                return -c / 2 * ((--t) * (t - 2) - 1) + b;
            }
        },
        //三次方的缓动
        Cubic: {
            easeIn: function(t, b, c, d) {
                return c * (t /= d) * t * t + b;
            },
            easeOut: function(t, b, c, d) {
                return c * ((t = t / d - 1) * t * t + 1) + b;
            },
            easeInOut: function(t, b, c, d) {
                if ((t /= d / 2) < 1) {
                    return c / 2 * t * t * t + b;
                }
                return c / 2 * ((t -= 2) * t * t + 2) + b;
            }
        },
        //四次方的缓动
        Quart: {
            easeIn: function(t, b, c, d) {
                return c * (t /= d) * t * t * t + b;
            },
            easeOut: function(t, b, c, d) {
                return -c * ((t = t / d - 1) * t * t * t - 1) + b;
            },
            easeInOut: function(t, b, c, d) {
                if ((t /= d / 2) < 1) {
                    return c / 2 * t * t * t * t + b;
                }
                return -c / 2 * ((t -= 2) * t * t * t - 2) + b;
            }
        },
        //五次方的缓动
        Quint: {
            easeIn: function(t, b, c, d) {
                return c * (t /= d) * t * t * t * t + b;
            },
            easeOut: function(t, b, c, d) {
                return c * ((t = t / d - 1) * t * t * t * t + 1) + b;
            },
            easeInOut: function(t, b, c, d) {
                if ((t /= d / 2) < 1) {
                    return c / 2 * t * t * t * t * t + b;
                }
                return c / 2 * ((t -= 2) * t * t * t * t + 2) + b;
            }
        },
        //正弦曲线的缓动
        Sine: {
            easeIn: function(t, b, c, d) {
                return -c * Math.cos(t / d * (Math.PI / 2)) + c + b;
            },
            easeOut: function(t, b, c, d) {
                return c * Math.sin(t / d * (Math.PI / 2)) + b;
            },
            easeInOut: function(t, b, c, d) {
                return -c / 2 * (Math.cos(Math.PI * t / d) - 1) + b;
            }
        },
        //指数曲线的缓动
        Expo: {
            easeIn: function(t, b, c, d) {
                return (t == 0)
                    ? b
                    : c * Math.pow(2, 10 * (t / d - 1)) + b;
            },
            easeOut: function(t, b, c, d) {
                return (t == d)
                    ? b + c
                    : c * (-Math.pow(2, -10 * t / d) + 1) + b;
            },
            easeInOut: function(t, b, c, d) {
                if (t == 0)
                    return b;
                if (t == d)
                    return b + c;
                if ((t /= d / 2) < 1)
                    return c / 2 * Math.pow(2, 10 * (t - 1)) + b;
                return c / 2 * (-Math.pow(2, -10 * --t) + 2) + b;
            }
        },
        //圆形曲线的缓动
        Circ: {
            easeIn: function(t, b, c, d) {
                return -c * (Math.sqrt(1 - (t /= d) * t) - 1) + b;
            },
            easeOut: function(t, b, c, d) {
                return c * Math.sqrt(1 - (t = t / d - 1) * t) + b;
            },
            easeInOut: function(t, b, c, d) {
                if ((t /= d / 2) < 1) {
                    return -c / 2 * (Math.sqrt(1 - t * t) - 1) + b;
                }
                return c / 2 * (Math.sqrt(1 - (t -= 2) * t) + 1) + b;
            }
        },
        //超过范围的三次方缓动
        Back: {
            easeIn: function(t, b, c, d, s) {
                if (s == undefined)
                    s = 1.70158;
                return c * (t /= d) * t * ((s + 1) * t - s) + b;
            },
            easeOut: function(t, b, c, d, s) {
                if (s == undefined)
                    s = 1.70158;
                return c * ((t = t / d - 1) * t * ((s + 1) * t + s) + 1) + b;
            },
            easeInOut: function(t, b, c, d, s) {
                if (s == undefined)
                    s = 1.70158;
                if ((t /= d / 2) < 1) {
                    return c / 2 * (t * t * (((s *= (1.525)) + 1) * t - s)) + b;
                }
                return c / 2 * ((t -= 2) * t * (((s *= (1.525)) + 1) * t + s) + 2) + b;
            }
        },
        //指数衰减的正弦曲线缓动
        Elastic: {
            easeIn: function(t, b, c, d, a, p) {
                if (t == 0)
                    return b;
                if ((t /= d) == 1)
                    return b + c;
                if (!p)
                    p = d * .3;
                var s;
                !a || a < Math.abs(c)
                    ? (a = c, s = p / 4)
                    : s = p / (2 * Math.PI) * Math.asin(c / a);
                return -(a * Math.pow(2, 10 * (t -= 1)) * Math.sin((t * d - s) * (2 * Math.PI) / p)) + b;
            },
            easeOut: function(t, b, c, d, a, p) {
                if (t == 0)
                    return b;
                if ((t /= d) == 1)
                    return b + c;
                if (!p)
                    p = d * .3;
                var s;
                !a || a < Math.abs(c)
                    ? (a = c, s = p / 4)
                    : s = p / (2 * Math.PI) * Math.asin(c / a);
                return (a * Math.pow(2, -10 * t) * Math.sin((t * d - s) * (2 * Math.PI) / p) + c + b);
            },
            easeInOut: function(t, b, c, d, a, p) {
                if (t == 0)
                    return b;
                if ((t /= d / 2) == 2)
                    return b + c;
                if (!p)
                    p = d * (.3 * 1.5);
                var s;
                !a || a < Math.abs(c)
                    ? (a = c, s = p / 4)
                    : s = p / (2 * Math.PI) * Math.asin(c / a);
                if (t < 1)
                    return -.5 * (a * Math.pow(2, 10 * (t -= 1)) * Math.sin((t * d - s) * (2 * Math.PI) / p)) + b;
                return a * Math.pow(2, -10 * (t -= 1)) * Math.sin((t * d - s) * (2 * Math.PI) / p) * .5 + c + b;
            }
        }
    };

    // move: 实现多方向的运动动画
    // curEle：当前要操作运动的元素
    // target：当前动画的目标位置{left,top}
    // duration：当前动画的总时间
    // effect： 运动方式
    // callBack：回调函数
    // effect 情况 如果传递进来的是一个数字 0 1 2 3 4
    // effect 如果传递进来的是一个数组：move(curEle, target, duration, [],callBack);
    // 如果不传默认zhufengEffect.Linear匀速效果
    //  ["Linear","Circ-easeInOut", "Elastic-easeOut", "Back-easeOut", "Bounce-easeOut", "Expo-easeIn"];
    function move(curEle,target,duration,effect,callBack){
        var tempEffect=zhufengEffect.Linear;
        if(typeof effect==="number"){
            switch (effect){
                case 0:
                    tempEffect=zhufengEffect.Linear;
                    break;
                case 1:
                    tempEffect=zhufengEffect.Circ.easeInOut;
                    break;
                case 2:
                    tempEffect=zhufengEffect.Elastic.easeOut;
                    break;
                case 3:
                    tempEffect=zhufengEffect.Back.easeOut;
                    break;
                case 4:
                    tempEffect=zhufengEffect.Bounce.easeOut;
                    break;
                case 5:
                    tempEffect=zhufengEffect.Expo.easeIn;
                    break;
            }
        }else if(effect instanceof Array){
            tempEffect=effect.length>=2?zhufengEffect[effect[0]][effect[1]]:zhufengEffect[effect[0]];
        }else if(typeof effect==="function"){
            callBack=effect;
        }

        window.clearInterval(curEle.zhufengTimer);//之前没加，问题所在。执行本次动画之前，清除上次动画
        var begin={},change={};
        for(var key in target){
            if(target.hasOwnProperty(key)){
                begin[key]=utils.css(curEle,key);
                change[key]=target[key]-begin[key];
            }
        }

        var time=0;
        curEle.zhufengTimer=window.setInterval(function(){
            time+=10;
            if(time>=duration){
                utils.css(curEle,target);
                window.clearInterval(curEle.zhufengTimer);
                //  让回调函数中this 变成当前要操作的元素
                // typeof callBack === 'function' ? callBack.call(curEle) : null;
                callBack&&callBack.call(curEle);
                return;
            }
            for(var key in target){
                if(target.hasOwnProperty(key)){
                    var curPos=tempEffect(time,begin[key],change[key],duration);
                    utils.css(curEle,key,curPos);
                }
            }
        },10);
    }

    window.zhufengAnimate=move;
}();

```


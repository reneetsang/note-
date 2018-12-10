## todo

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>todo</title>
    <link rel="stylesheet" href="http://cdn.static.runoob.com/libs/bootstrap/3.3.7/css/bootstrap.min.css">
    <style>
        .del{ text-decoration: line-through; color: #cccccc;}
    </style>
</head>
<body>
<div id="app">
    <nav class="navbar navbar-default">
        <div class="container-fluid">
            <div class="navbar-header">
                <a class="navbar-brand" href="#">TodoList</a>
            </div>
        </div>
    </nav>
    <div class="container">
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-warning">
                    <div class="panel-heading text-danger">
                        <h3>亲，你还有{{count}}件事要完成！</h3>
                        <input type="text" class="form-control" @keyup.enter='add' v-model="title">
                    </div>
                    <div class="panel-body">
                        <ul class="list-group">
                            <li class="list-group-item" v-for="(item,index) in todos" @dblclick="remember(item)">
                                <!--如果isSelected为true的话则加del-->
                                <!--点击要要是输入框表示可修改。当我点击li时，可以知道点击的是哪一项，如果点击的这一项和todo当前循环的那一项相等时，应该显示输入框-->
                                <span :class="{del:item.isSelected}" v-show="cur!=item"><input type="checkbox" v-model="item.isSelected">{{item.title}}</span>
                                <input type="text" v-model="item.title" v-show="cur==item" @keyup.enter="cancel" @blur="cancel">
                                <button class="pull-right btn btn-danger btn-xs" @click="remove(item)">&bigotimes;</button>
                            </li>
                        </ul>
                    </div>
                    <div class="panel-footer">
                        <ul class="nav nav-pills">
                            <li role="presentation" class="active"><a href="#">全部</a></li>
                            <li role="presentation"><a href="#">已完成</a></li>
                            <li role="presentation"><a href="#">未完成</a></li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<script src="node_modules/vue/dist/vue.js"></script>
<!--基于promise的-->
<script src="node_modules/axios/dist/axios.js"></script>
<script>
var vm=new Vue({
    el:'#app',
    data:{
        title:'',
        todos:[{isSelected:false,title:'砍树'},{isSelected:false,title:'浇水'},{isSelected:false,title:'钓鱼'}],
        cur:''
    },
    methods:{
        add(){
            this.todos.push({isSelected:false,title:this.title});
            this.title='';
        },
        remove(todo){
            this.todos=this.todos.filter(item=>item!==todo)
        },
        remember(item){
            this.cur=item;
        },
        cancel(){
            this.cur='';
        }
    },
    computed:{
        count(){
            return this.todos.filter(item=>!item.isSelected).length
        }
    }
})
</script>
</body>
</html>
```


## HTML5基础知识

#### HTML5基础概述

> HTML：超文本标记语言（页面中不仅只有文字，而且可以呈现出图片、音视频等媒体资源）

> XHTML：它是HTML比较规范严谨的一代版本。文档声明比较复杂，需要特殊强调当前页面需要严谨一些

> XML：可扩展的标记语言（HTML中使用的标签都是W3C标准中规定的，XML允许我们自己扩展标签），它的作用不是用来写页面结构的，而是用来存储一些数据的（以自己扩展的标签作为标识，清晰明了的展示出数据的结构）

> HTML5：当代HTML最新的一代版本，也是非常成功的一代版本，目前市场上基本都是基于H5规范进行开发的（它相对于传统的HTML更多的是增加一些有助于开发的内容，对原有规范的调整修改很少）

`HTML5`

```html
<!DOCTYPE html>
<html lang='en'><!-- 声明页面的语言模式:english，如果页面出现了英文单词，浏览器会自主发起是否翻译的功能 -->
    <head>
        <!-- 指定当前页面的编码格式是国际统一编码格式：UTF-8 GB2312中国编码 -->
        <meta charset='UTF-8'>
    </head>
    <body>
        
    </body>
</html>
```

#### HTML5提供的新语法

* 对原有语义化标签的升级

  >语义化标签（标签语义化）：每一个HTML标签都有自己特殊的含义，我们在搭建页面结构的时候，应该让合理的标签做合适的事情

* HTML5中新增加一些语义化标签：

  ```
  article:文章区域
  header:头部区域
  footer:尾部区域
  main:主体内容区域
  section:普通区域，用来做区域划分
  figure:配图区域
  figcaption:配图说明区域
  aside:与主体内容无关的区域（一般用来打广告）
  nav:导航区域

  这些语义化标签在兼容它的浏览器都是块级标签
  ```

* H5中新增加一些标记标签

  ```
  mark:用来标记需要高亮显示的文本
  time:用来标记日期文本
  ```

* H5中对于原有的标签还有一些调整

  ```
  strong:之前是加粗，现在是重点朗读，语义不一样了
  small:之前是变小，现在是附属细则
  hr：之前是一条直线，现在是分割线，用来分隔两个区域
  ```


> 目前不管是在PC端开发还是在移动端开发，我们更应该使用H5规范的语义化标签搭建页面的结构
>
> 问题：
>
> IE6-8中不能识别这些新增加的语义化标签，我们无法为其设置具体的样式
>
> 解决：
>
> 在当前页面的HEAD中（CSS后），我们导入一个JS插件：html5.min.js，它就是用来把页面中所有用到的不兼容H5语义化标签进行兼容处理
>
> 1、把页面所有不兼容的标签进行替换
>
> 2、把CSS中使用标签选择器设置的样式（标签是H5标签）也替换成其他方式
>
> ……
>
> 标准浏览器中不需要引用，只有IE6-8中才需要（使用条件注释来区分浏览器）

```javascript
<head>
    <!--[if It IE 9]>
    <script src='js/html5.min.js'></script>
	<![endif]-->
</head>
<!-- 条件注释中的代码要严格区分大小写以及空格等细节问题 -->

```

#### H5中对表单元素的升级

> 传统表单元素
>
> form
>
> input:text、password（暗纹输入）、button、submit、reset、file、hidden、radio、checkbox……
>
> button
>
> select
>
> label
>
> textarea

```html
<form>
    <!--
        需要用name进行分组，一组只能选中一个
        label有一个for属性，指向对应表单元素的ID，操作label中的内容，也相当于操作表单元素（经常用于单选或者复选框上）
    -->
    <input type="radio" name="sex" id="man" checked><label for="man">男</label>
    <input type="radio" name="sex" id="woman" checked><label for="woman">女</label>

    <input type="submit">
    <!--
        submit默认行为：点击按钮会跳转到form的action对应的地址（表单提交）
        在传统的非前后端分离项目中，会在action中指定一个程序处理页面（一般是后台语言（php/jsp/asp……）完成），我们利用它的默认行为把数据发送给处理页面，由处理页面完成数据的存储等操作
        现有前后端完全分离项目中，我们都是在JS中手动获取到用户输入的内容，并且通过AJAX等技术发送给服务器存储或处理（此时需要阻止submit的默认行为）
    -->
</form>
```

> H5对于表单的升级
>
> 1、给input设置了很多新的类型
>
> search、email、tel、number、range、color、date、time……
>
> 优势：
>
> ①功能强大了
>
> ②使用合适的类型，在移动的开发的时候，用户输入，可以调取出最符合输入内容格式的虚拟键盘，方便用户操作
>
> ③部分类型提供了表单验证（内置的验证机制）
>
> 2、给input新增一个属性：placeholder 给表单框做默认的信息提示
>
> 3、二级下拉框（select是一级下拉框）

```html
<input type="text" id="department" list="departmentList">
<datalist id="departmentList">
    <option value="市场部">市场部</option>
    <option value="技术部">技术部</option>
    <option value="产品部">产品部</option>
</datalist>
```

> H5针对于表单元素升级的部分，在IE低版本（有的IE9和10都不兼容）中不兼容，而且没办法处理兼容，所以一般在移动端使用这些新特性，PC端还是延续传统的操作办法
>
> H5中的表单验证（内置规则不是特别好），所以真实项目中的表单验证依然延续传统的正则验证完成
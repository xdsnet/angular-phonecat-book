# 步骤0（step-0）——准备

你已经准备开始建立AngularJS phonecat程序了。在这一步中，你将熟悉最重要的源代码文件，了解如何开始与angular-seed绑定在一起的开发服务，并且在浏览器中运行程序。

在`angular-phonecat`目录中运行：
```cmd
git checkout -f step-0
```

这将把你的工作区切换到教程程序的 step-0分支中。完成教程学习中你会重复这个步骤（只是后面的数字会不同）以切换到不同步骤分支中。注意的是这将丢失你在原有工作区中做的所有改动。

如果你不确定你已经安装了所有的依赖，需要运行一次：
```cmd
npm install
```

为了在浏览器中运行程序，打开终端/命令行窗口，并运行`npm start`来开始web服务。现在，打开浏览器窗口，在地址栏输入`[http://localhost:8000/app/](http://localhost:8000/app/)`就可以访问到程序了。

如果你在浏览器中看见个页面（不是报错信息），则表示工作是正常的，当然现在显示的内容不让人兴奋，但这还是很好的。

这个HTML页面会显示 "Nothing here yet!"，它是由如下的HTML代码定义的，这些代码包括了我们后面需要进一步利用的Angular关键元素。
`app/index.html`:
```html
<!doctype html>
<html lang="en" ng-app>
<head>
  <meta charset="utf-8">
  <title>My HTML File</title>
  <link rel="stylesheet" href="bower_components/bootstrap/dist/css/bootstrap.css">
  <link rel="stylesheet" href="css/app.css">
  <script src="bower_components/angular/angular.js"></script>
</head>
<body>

  <p>Nothing here {{'yet' + '!'}}</p>

</body>
</html>
```

##这些代码做了什么？
`ng-app`指令：
```html
<html ng-app>
```
这个`ng-app`属性代表一个Angular指令`ngApp`(在Angular约定 `spinal-case`暨连词线拼接词用在定制属性中，`camelCase`暨驼峰拼接词用在指令中，并提供一致的效果）。这个指令标志这个html元素会被Angular用作应用程序的根（root）元素。这将告诉Angular是整个html页面还是部分元素作为Angular程序。

AngularJS脚本标签:
```html
<script src="bower_components/angular/angular.js">
```
这段代码将让浏览器下载angular.js脚本，并注册一个在浏览器完整下载HTML并初始化后会执行的回调。当回调执行了，Angular将搜索`ngApp`指令，如果找到了，就将以ngApp指令定义的DOM作为程序根元素来启动程序。

双大括号（Double-curly）绑定表达式:
```html
Nothing here {{'yet'+'!'}}
```
这一行展示了Angular模板应用的两个核心功能：
* 一个绑定需要被双大括号（Double-curly）`{{ }}`括起来
* 简单表达式`'yet'+'!' `可以用于绑定

绑定告诉Angular需要进行对表达式求值，并把结果插入放置在绑定的DOM内。注意的是这种插入不是一次性的，在接下来的步骤中你会更多的了解体验到，它会自动感知表达式结果值发生的变化，并及时更新。

Angular表达式(`Angular expression`)是类似JavaScript的代码，其在当前的Angular环境数据模型空间（依上下文）中求值，这不同于全局数据空间（window DOM）。

正如预期，这个模板由Angular处理后，在html页面会显示文字: "Nothing here yet!"

##AngularJS程序的启动
使用`ngApp`指令来自动启动AngularJS程序简单且适用于大多数情景。在高级例子中，例如利用脚本加载器，你可以使用 指令来手动控制（参见开发指南的 `imperative / manual way`` 部分）如何启动程序。

在程序启动过程中其实有3件重要的事情发生：
1. `injector`用于创建了一个依赖注入
2. `injector`创建了一个根作用范围(`root scope`)，这将用作程序的数据模型上下文环境
3. Angular会"编译(compile)"`ngApp`为根开始的DOM元素，并在其下执行指令和发现的绑定

一旦程序启动完成，他将等待传入的浏览器事件（例如鼠标点击、键盘输入或者HTTP响应），这都意味着（数据）模型的改变。一旦模型改变发生了，Angular会检测模型改变（改变被找到），Angular会通过更新视图在所有受到影响的绑定中反映出变化。

当前我们的程序结构还十分简单。这个模板只包括了一个指令，还是一个静态的绑定，我们的数据模型其实还是空的，不过马上就会改变！

![步骤0程序执行结构示意](../image/tutorial_00.png)

##在我们的工作目录有哪些文件？
工作区大部分的文件是来自于`angular-seed`，它们通常用于开始一个新的Angular项目。原子项目通常预定义安装了angular框架（通过`bower`安装到`app/bower_components/`目录下）和一些开发常见App会用到的工具（通过`npm`)。

具体到本教程中，我们编辑改变了`angular-seed`这些部分：
* 移除了其所有例子app
* 添加一些手机图片到 `app/img/phones/`
* 添加手机数据文件(JSON格式)到`app/phones/`
* 在`bower.json`文件中添加了对[Bootstrap](http://getbootstrap.com/)的依赖

##尝试
尝试添加一个新的表达式到`index.html`文件中，它可以执行一些数学计算：
```html
<p>1 + 2 = {{ 1 + 2 }}</p>
```

##小结
让我们进入到下个步骤，添加更多内容到web app中











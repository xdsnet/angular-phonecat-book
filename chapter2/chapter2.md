# 步骤2——Angular模板
现在我们让利用AngularJS让页面动态化。我们还将添加一些测试，这将检验我们添加的控制器代码会执行预期的工作。

其实有很多方法来构建应用程序，在Angular中，我们推荐采用[Model-View-Controller（MVC——模型-视图-控制器）的设计模式](http://en.wikipedia.org/wiki/Model%E2%80%93View%E2%80%93Controller)来解耦代码与单独的问题。利用这点，我们在model、view和controller中用到尽量少的Angular和Javascript。
* 程序将展示利用数据动态的列出3部手机列表。

##工作区切换到步骤2
直接用浏览器访问[步骤2在线演示](http://angular.github.io/angular-phonecat/step-2/app)

大多数的重要改变都会列在下面，不过你也可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-1...step-2)看到完整的差异。

##视图（view）和模板（template）
在Angular中，**视图**是数据模型通过HTML**模板**的投影，这意味着任何数据模型的变化，Angular都会针对绑定点刷新视图。

这里的视图组件是由Angular从下面的模板构建的：

`app/index.html`:
```html
<html ng-app="phonecatApp">
<head>
  ...
  <script src="bower_components/angular/angular.js"></script>
  <script src="js/controllers.js"></script>
</head>
<body ng-controller="PhoneListCtrl">

  <ul>
    <li ng-repeat="phone in phones">
      {{phone.name}}
      <p>{{phone.snippet}}</p>
    </li>
  </ul>

</body>
</html>
```
这里我们用`ngRepeat指令`和两个`Angular表达式(expression)`取代了硬编码的电话列表:
* `<li>`的`ng-repeat="phone in phones"`属性就是一个Angular重复指令，它告诉angular在列表中为每个电话信息重复创建`<li>`元素，这里的`<li>`标签就作为了模板。
* 在双大括号中的表达式`{{phone.name}}`和`{{phone.snippet}}`会用实际的值替代。

这里，我们还添加了新的指令，叫做`ng-controller`，它为`<body>`标签指定了一个`PhoneListCtrl`的控制器，对于这点
* 在双大括号中的表达式`{{phone.name}}`和`{{phone.snippet}}`指定的绑定，将参考我们程序中的数据模型，这些都是在我们的`PhoneListCtrl`的控制器进行设置的。

>注意：我们已经通过加载使用`ng-app="phonecatApp"`来指定了一个Angular数据模型，这里`phonecatApp `是我们数据模型的名字，这个模型被包含在`PhoneListCtrl`中。

![MVC模型示意](../image/tutorial_02.png)

##数据模型（Model）和控制器（Controller）
这里的数据**模型（Model）**（一个简单的电话信息数值）是由`PhoneListCtrl`**控制器（Controller）**实例化的。这个控制器只是简单的一个构造函数，它操作了一个$scope参数。

`app/js/controllers.js`:
```js
var phonecatApp = angular.module('phonecatApp', []);

phonecatApp.controller('PhoneListCtrl', function ($scope) {
  $scope.phones = [
    {'name': 'Nexus S',
     'snippet': 'Fast just got faster with Nexus S.'},
    {'name': 'Motorola XOOM™ with Wi-Fi',
     'snippet': 'The Next, Next Generation tablet.'},
    {'name': 'MOTOROLA XOOM™',
     'snippet': 'The Next, Next Generation tablet.'}
  ];
});
```
这里，我们定义了一个叫做`PhoneListCtrl `的控制器，并且注册它到一个叫做`phonecatApp`的AngularJS数据模型中。注意，我们在`<html>`中的`ng-app`指令现在指定了一个`phonecatApp`的数据模型，这将在启动Angular过程中加载。

尽管现在看来控制器并没有做什么特别的事，但它是至关重要的。通过上下文数据模型,控制器允许我们建立模型和视图之间的数据绑定。我们由此连接起二者间的描述、数据和逻辑，它们是如下工作的：
*  `ngController`指令定位到`<body>`标签，引用指定名称的控制器，`PhoneListCtrl`在JavaScript文件`controllers.js`中设定。
* `PhoneListCtrl`控制器把数据链接到`$scope`来注入我们的控制器函数。这个*scope*是一个在应用定义时预定义（指定）的*根作用范围(root scope)*，然后这个控制器通过
`<body ng-controller="PhoneListCtrl">`标签绑定。

##作用范围
在Angular中作用范围的概念至关重要。作用范围被视作把模型、视图和控制器粘合起来协同工作的胶水。Angular利用作用范围来整合模板中的信息、数据模型和控制器，保持模型和视图之间的独立与同步。任何在模型中的变化都被反映到视图中，在视图中的操作变化也反映到数据模型。

要了解更多的关于Angular作用范围的内容，请参考**Angular作用范围文档**主题部分。

##测试
Angular分离了控制器和视图，这使得更容易实施测试驱动的开发。如果我们的控制器可以在全局命名空间起效,那么我们可以在一个模拟的范围内实例化它：
```js
describe('PhoneListCtrl', function(){

  it('should create "phones" model with 3 phones', function() {
    var scope = {},
        ctrl = new PhoneListCtrl(scope);

    expect(scope.phones.length).toBe(3);
  });

});
```
这个对于`PhoneListCtrl`的测试实例检验了手机数组中是否有3条记录。这个例子简单的示范了如何为Angular项目创建一个单元测试。因为测试是软件开发的关键部分，我们可以让这一过程更简单，从而鼓励开发者写测试。

##测试非全局控制器
实际上，你不一定想你的控制器工作在全局名称空间中，反而你更愿意把控制器通过匿名构造函数注册为`phonecatApp`的模块。在这种情况下Angular提供了一种服务(`$controller`)让你可以通过名字查询到控制器。下面就是利用`$controller`进行相同的测试:
`test/unit/controllersSpec.js`:
```js
describe('PhoneListCtrl', function(){

  beforeEach(module('phonecatApp'));

  it('should create "phones" model with 3 phones', inject(function($controller) {
    var scope = {},
        ctrl = $controller('PhoneListCtrl', {$scope:scope});

    expect(scope.phones.length).toBe(3);
  }));

});
```

* 在每个测试前告诉Angular要加载`phonecatApp`数据模型
* 请求Angular向我们的测试函数注入`$controller`服务
* 我们利用`$controller`来创建`PhoneListCtrl`的实例
*通过这个实例，我们检测范围内的电话数组是否有预期的3条记录

##编写并运行测试
Angular开发者一般更喜欢采用Jasmine的行为驱动开发(Behavior-driven Development——BDD)框架来写测试。但是Angular并不限定你只能使用Jasmine。我们（原文作者）的为教程采用Jasmine V1.3编写过所有测试。如果你打算学习下Jasmine，可以到[Jasmine主页](http://jasmine.github.io/)和[Jasmine文档](http://jasmine.github.io/1.3/introduction.html)去访问以学到更多。

angular-seed 预配采用[Kama](http://karma-runner.github.io/)进行单元测试，你需要确保Karma的支持组件安装好了。对此你只需要简单的执行`npm install`

为了运行测试，或者在文件修改后检测，你需要:`npm test`

* Karma会自动打开一个新的Chrome实例，只是忽略它而运行在后台。Karma会使用浏览器来运行测试。
* 你会在控制台看到类似下面的输出：
```cmd
  info: Karma server started at http://localhost:9876/
  info (launcher): Starting  browser "Chrome"
  info (Chrome 22.0): Connected on socket id tPUm9DXcLHtZTKbAEO-n
  Chrome 22.0: Executed 1 of 1 SUCCESS (0.093 secs / 0.004 secs)
  ```
  这代表了测试通过或者没有通过
* 在测试没有主动关闭前，在对源码或者test.js有任何修改后都会自动运行测试。Karma会注意到变化并执行测试返回结果给你。难道这不是很好？
>确认你没有最小化karma打开的浏览器。在一些操作系统中，最小化浏览器会导致内存访问受限，这使得karma测试运行的特别慢。

##尝试
为`index.html`添加其他的绑定，例如：
```html
<p>Total number of phones:{{phones.length}}</p>
```
在控制器中创建一个新的模型属性，并绑定到模板。例如：
```js
$scope.name = "World";
```
为`index.html`添加新的绑定：
```html
<p>Hello,{{name}}!</p>
```
刷新你的浏览器来检测是否会显示"Hello, World!"。
在`./test/unit/controllersSpec.js`中为控制器更新单元测试来反映前面的修改。例如添加：
```js
expect(scope.name).toBe('World');
```
利用`repeater`指令在`index.html`中构建一个简单的表格：
```html
    <table>
      <tr><th>row number</th></tr>
      <tr ng-repeat="i in [0, 1, 2, 3, 4, 5, 6, 7]"><td>{{i}}</td></tr>
    </table>
    ```
现在把列表改成对i都增加1的绑定：
```html
    <table>
      <tr><th>row number</th></tr>
      <tr ng-repeat="i in [0, 1, 2, 3, 4, 5, 6, 7]"><td>{{i+1}}</td></tr>
    </table>
    ```
额外还可以尝试通过使用附加的ngrepeat实现8×8的表格。

改变测试项让单元测试失败:如把`expect(scope.phones.length).toBe(3)`改成`toBe(4)`。

##小结
你现在已经有了一个动态程序，有了分离的数据模型、视图和控制组件，你还测试了它们按预期工作，现在让我们进入步骤3去学习如何为程序添加文字搜索。




























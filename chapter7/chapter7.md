# 步骤7——路由与多视图

这一步，将学习到如何创建布局模板，以及利用Angular路由模块(`ngRoute`)实现的多视图应用。
*当浏览器导航到`app/index.html`时，会重定向到`app/index.html#/phones`来访问呈现一个手机列表。
*当点击手机链接时，URL会改变指向到一款具体的手机，并且显示其详细的信息页面。

##工作区切换到步骤7
直接用浏览器访问[步骤7在线演示](http://angular.github.io/angular-phonecat/step-7/app)

大多数的重要改变都会列在下面，不过你也可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-6...step-7)看到完整的差异。

##依赖
这一步添加的路由功能是由Angular的`ngRoute`模块提供，这是一个独立于Angular核心框架的模块。

我们使用`Bower`来安装客户端依赖。这一步骤中我们会更新`bower.json`配置文件来包含新的依赖关系:
```js
{
  "name": "angular-seed",
  "description": "A starter project for AngularJS",
  "version": "0.0.0",
  "homepage": "https://github.com/angular/angular-seed",
  "license": "MIT",
  "private": true,
  "dependencies": {
    "angular": "1.2.x",
    "angular-mocks": "~1.2.x",
    "bootstrap": "~3.1.1",
    "angular-route": "~1.2.x"
  }
}```
新的依赖关系`"angular-route": "~1.2.x"`告诉`bower`安装版本1.2.x的`angular-route`组件。我们必须明确告诉`bower`下载安装这个依赖。

如果你是在全局环境中使用bower安装，你可能会用直接到`bower install`指令，但是在这个项目中我们已经预配使用`npm`来启动`bower install`，所以你只需要:
`npm install`

##多视图、路由与布局模板
我们的程序逐渐强大，也变得更加复杂。在步骤7（本步骤）之前，我们只有唯一的视图来（用来显示手机列表），并且所有的模板代码都放置在`index.html`中。新的步骤中会添加一个视图来显示列表中每个设备详细的信息（详细说明视图）。

为了添加详细说明视图，我们扩展`index.html`模板文件来包含两个视图，但这将很快引起混乱，为了替代，我们尝试把`index.html`转换到我们称为布局模板（`layout template`）其中有模板（布局模板）在所有视图中通用，其他则是局部模板（`partial templates`），局部模板只包括当前路由`route`——视图当前显示需要的部分。

在Angular中，程序的路由通过`$routeProvider`声明，它是`$route`服务的提供者。这个服务能容易的把控制器、视图模板和浏览器当前的地址栏信息连接起来。使用这个特性，我们可以实现[深层链接(deep linking)](http://en.wikipedia.org/wiki/Deep_linking)，它可以让你可以利用浏览器历史（后退和前进）以及收藏标签。

##关于依赖注入（DI）的提醒：注入器（Injector）和提供者（Providers）
是否注意到，依赖注入（DI）是AngularJS的核心，所以了解一下其是如何实现的是十分重要的。

当程序启动时，Angular创建一个注入器用于查找和注入程序需要的服务。注入器自身是不知道任何关于`$http`或者`$route`服务能做什么的，事实上注入器都不知道这些服务是否存在（除非服务配置在适当的模块定义中）。

注入器只执行以下步骤：
* 加载描述在程序中的模块定义
* 注册所有模块定义的提供者
* 当有实际的请求时，注入器检测具体的功能要求及对应的依赖（服务），再通过其提供者（延时）实例化来完成功能的提供

提供者是一种可以提供（创建）服务实例的对象，其还可以通过配置的API暴露调用接口，用于控制的建立和运行时行为功能。实际上这`$route`服务、`$routeProvider`暴露的API允许为你的程序定义路由。
> 注意：提供者只能注入到`config`功能，因此你不能把`$routeProvider`注册入`$PhoneListCtrl`

Angular模块从应用程序中解决（去除了）全局状态的问题,并提供一个配置注入器的方法。与AMD或者`require.js`模块截然相反，Angular模块不会尝试处理脚本的加载和延时获取等问题。这些目标是完全独立的模块系统，它们可以并列存在和实现他们的目标。

要深入理解Angular的依赖注入，观看[Understanding Dependency Injection](https://github.com/angular/angular.js/wiki/Understanding-Dependency-Injection)


##模板
`$route`服务通常与`ngView`指令联合使用。`ngView`指令规包含了视图模板如何路由到的布局视图。这使得`index.html`模板显得更加完美。

>注意:从AngularJS版本1.2开始`ngRoute`是独立的模块，需要单独加载`angular-route.js`文件，这可以通过`bower`下载到。

`app/index.html`
```html
<!doctype html>
<html lang="en" ng-app="phonecatApp">
<head>
...
  <script src="bower_components/angular/angular.js"></script>
  <script src="bower_components/angular-route/angular-route.js"></script>
  <script src="js/app.js"></script>
  <script src="js/controllers.js"></script>
</head>
<body>

  <div ng-view></div>

</body>
</html>```
我们添加了2个新的`<script>`标签以在我们的index文件中加载额外的JavaScript文件：
* `angular-route.js`：定义了Angular的`ngRoute`模块，用于提供路由功能
* `app.js`:这个文件在程序中提供一个root模块。

注意我们从index.html模板文件中移除了大部分代码，取而代之的是一个定义了`ng-view`属性的`div`标签。移除的代码被放置到`phone-list.html`模板文件中：
`app/partials/phone-list.html`:
```html
<div class="container-fluid">
  <div class="row">
    <div class="col-md-2">
      <!--Sidebar content-->

      Search: <input ng-model="query">
      Sort by:
      <select ng-model="orderProp">
        <option value="name">Alphabetical</option>
        <option value="age">Newest</option>
      </select>

    </div>
    <div class="col-md-10">
      <!--Body content-->

      <ul class="phones">
        <li ng-repeat="phone in phones | filter:query | orderBy:orderProp" class="thumbnail">
          <a href="#/phones/{{phone.id}}" class="thumb"><img ng-src="{{phone.imageUrl}}"></a>
          <a href="#/phones/{{phone.id}}">{{phone.name}}</a>
          <p>{{phone.snippet}}</p>
        </li>
      </ul>

    </div>
  </div>
</div>```

我们还增加了一个包含占位符的手机详细说明视图的模板:
`app/partials/phone-detail.html`:
```html
TBD:detail view for <span>{{phoneId}}</span>```

注意现在我们使用了`phoneId`表达式，它是定义在`PhoneDetailCtrl`控制器中的。

##程序模块
为了改善程序的组织，我们使用Angular的`ngRoute·模块，并且把控制器移动到自己的模块`phonecatControllers`（下面会看见）。

我们添加`angular-route.js`到`index.html`中，并添加了一个新的`phonecatControllers`模块到`controllers.js`中。这些不是所有我们会用到的代码，我们还要添加一些我们程序依赖的模块。下面两个模块都是`phonecatApp`需要的，我们可用指令和服务提供。

`app/js/app.js`:
```js
var phonecatApp = angular.module('phonecatApp', [
  'ngRoute',
  'phonecatControllers'
]);

...```
注意传递给`angular.module`的第2个参数`['ngRoute','phonecatControllers']`，这个数组列出了`phonecatApp`依赖的模块。
```js
...

phonecatApp.config(['$routeProvider',
  function($routeProvider) {
    $routeProvider.
      when('/phones', {
        templateUrl: 'partials/phone-list.html',
        controller: 'PhoneListCtrl'
      }).
      when('/phones/:phoneId', {
        templateUrl: 'partials/phone-detail.html',
        controller: 'PhoneDetailCtrl'
      }).
      otherwise({
        redirectTo: '/phones'
      });
  }]);```

  通过`phonecatApp.config()`方法，我们请求`$routeProvider`注入自己配置的函数，并且使用`$routeProvider.when()`方法提供了自定义路由规则。

我们的程序定义了如下的路由:
* 匹配`/phones` ：当URL结尾片段是`/phones`时显示一个手机列表。为了构造这个输出视图，Angular会使用`phone-list.html`模板和`PhoneListCtrl`控制器
* 匹配`/phones/:phoneId`: 当URL结尾片段匹配`/phones/:phoneId`时会显示对应手机的详细说明视图。这里`:phoneId`是一个URL变量区块。为了生成这个手机详细说明视图，Angular使用`phone-detail.html`模板和`PhoneDetailCtrl`控制器。
* 其他的匹配（重定向到`/phones`）:当浏览器地址栏信息不能匹配到有效路由（自定义的路由设置没有对应项目）时，尝试重定向到`/phones`

我们再次使用了上一步骤的`PhoneListCtrl`控制器，然后添加了一个新的（但是是空的）`PhoneDetailCtrl`控制器到`app/js/controllers.js`文件中来为手机详细说明视图提供数据。

注意在第2个路由声明中的`:phoneId`参数。`$route`服务使用路由声明——`/phones/:phoneId`——作为一个模板来匹配当前的URL。所有的这些变量定义都会在`$routeParams`对象中展开。

##控制器
`app/js/controllers.js`:
```js
var phonecatControllers = angular.module('phonecatControllers', []);

phonecatControllers.controller('PhoneListCtrl', ['$scope', '$http',
  function ($scope, $http) {
    $http.get('phones/phones.json').success(function(data) {
      $scope.phones = data;
    });

    $scope.orderProp = 'age';
  }]);

phonecatControllers.controller('PhoneDetailCtrl', ['$scope', '$routeParams',
  function($scope, $routeParams) {
    $scope.phoneId = $routeParams.phoneId;
  }]);```

再次提醒，我们是创建了一个新的模块叫做`phonecatControllers`。对于很多小的AngularJS程序，共同创造是一个模块为所有控制器(如果有几个)公用。随着程序的增强，很容易为程序添加更多的公用模块。对于大的程序，你需要为每个主要的程序功能创建特定的模块。

因为我们的例子程序是相对比较小的程序，所以我们把所有的控制器都添加到`phoecatControllers`模块中。

##测试
为了自动验证所有的可能，我们写端到端测试来导航到各式URL，并且验证是否能显示正确：
```js
...
   it('should redirect index.html to index.html#/phones', function() {
    browser.get('app/index.html');
    browser.getLocationAbsUrl().then(function(url) {
        expect(url.split('#')[1]).toBe('/phones');
      });
  });

  describe('Phone list view', function() {
    beforeEach(function() {
      browser.get('app/index.html#/phones');
    });
...

  describe('Phone detail view', function() {

    beforeEach(function() {
      browser.get('app/index.html#/phones/nexus-s');
    });


    it('should display placeholder page with phoneId', function() {
      expect(element(by.binding('phoneId')).getText()).toBe('nexus-s');
    });
  });```
你可以用`npm run protractor`来运行并观察测试结果。

##尝试
试着在`index.html`中添加一个`{{orderProp"}}`的绑定观察点，你会发现当手机列表正常显示时，你看不到任何相关信息，这是因为现在`orderProp`数据模型只在`PhoneListCtrl`模型中的作用范围内有效（可见）。为了关联<div ng-view>元素，你需要添加相同绑定到`phone-list.html`模板中，这个绑定将正常工作。
##小结
这一步，我们建立了路由，并实现了手机列表视图，接下来的步骤8中，我们将实现手机详细说明视图。












# 步骤5——XHRs与依赖注入
显然，一个足够强大的程序仅仅依靠硬编码的3条手机数据设置是不行的。这里我们将利用Angular内置的叫做`$http`的服务功能，从web服务器获取一个巨大的数据表。我们还将使用到Angular的依赖注入(dependency injection)向`PhoneListCtrl`控制器提供服务。
* 现在有20条手机信息了，而且这些信息是通过服务器加载的。

##工作区切换到步骤5
直接用浏览器访问[步骤5在线演示](http://angular.github.io/angular-phonecat/step-5/app)

大多数的重要改变都会列在下面，不过你也可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-4...step-5)看到完整的差异。

##数据
在项目`app/phones/phones.json`文件中有一个巨大的列表，，是采用JSON格式组织的手机信息数据。文件中，它们大概是如下的形式:
```js
[
 {
  "age": 13,
  "id": "motorola-defy-with-motoblur",
  "name": "Motorola DEFY\u2122 with MOTOBLUR\u2122",
  "snippet": "Are you ready for everything life throws your way?"
  ...
 },
...
]```

##控制器
这里的控制器中，我们使用了Angular的`$http`服务，利用一个HTTP请求，从服务器中获取到`app/phones/phones.json`文件数据。`$http`是Angular内建web程序通用服务（功能）中的一个，Angular会在程序需要时自动注入这些服务功能。

这些服务由Angular依赖注入子系统进行管理。依赖注入帮助你的程序在好的结构（例如独立的数据、控制和表现/展示)和松耦合（组件间解耦，组件之间的依赖关系不由组件自身确定，而由依赖管理子系统协调）。
`app/js/controllers.js`:
```js
var phonecatApp = angular.module('phonecatApp', []);

phonecatApp.controller('PhoneListCtrl', function ($scope, $http) {
  $http.get('phones/phones.json').success(function(data) {
    $scope.phones = data;
  });

  $scope.orderProp = 'age';
});```
`$http`发起一个HTTP GET请求，其内容是请求web服务器端的`phones/phones.json`文件（这里的URL是相对于`index.html`的相对路径）。服务器端响应这个请求，提供了json文件的内容（响应其实是一个后台服务中动态处理的反馈结果，这对于浏览器或者我们的程序来说这看起来是相同的/透明的。这个教程中为了简单起便，直接是一个json数据文件了。)

`$http`服务得到了一个有`success`方法的`promise object`，然后我们就可以调用方法来处理异步响应和分配手机数据来构建我们控制器中作用范围中的`phones`数据了。注意，这里Angular自动检测了json类型响应，并分析结构化了数据。

为了使用Angular服务，你只用在控制器中需要的地方简单把调用名字作为构造函数的参数，例如:
```js
phonecatApp.controller('PhoneListCtrl', function ($scope, $http) {...}```
Angular的依赖注入管理会在控制器初始化时自动的提供声明的功能，而且依赖注入管理还自动的处理相应的层次依赖关系（通常一个服务功能还取决于其他服务功能，这些问题Angular都会自动处理）。

注意参数的名字很重要（不能随意变动），因为依赖管理会用到这些名字进行查找来解决依赖关系并进行注入。
![依赖注入过程示意图](../image/tutorial_05.png)

## `$`前缀名的约定
你可以创建自己的服务，事实上我们在步骤11中就会这样做。作为一个名称方面的约定，Angular内置的服务，作用范围方法和一些其它的Angular的API有一个`$`前缀。

这样有`$`前缀的都被用于了Angular预定服务，为了防止名称方面的冲突，你在定义服务和数据模型时最好都不要用`$`作为前缀。

在检查代码时你可能会注意到有些作用范围内的数据定义是`$$`来开始的，这意味这这些内容是私人的（该受保护的），你不该在外部进行访问或修改。

##在压缩代码时需要关注的地方
因为Angular的依赖控制采用了以名字作为构造函数的参数传入机制运行，所以如果你想压缩你的`PhoneListCtrl`控制器部分JavaScript代码就需要注意一些细节，否则自动机制下所有的参数名会自动压缩而导致依赖注入功能出错。

对于这样的问题就是提供一个禁止压缩的依赖名称列表，这样列表中的名称在压缩时不会进行缩减替换，这样就能保证压缩后的代码能够正常工作了，对此有两个方法：

* 在控制器构造函数上创建一个`inject`(注入)字符串数组，数组中每个字符串都是需要注入的服务名。在我们的例子中就是这样写：
```js
    function PhoneListCtrl($scope, $http) {...}
    PhoneListCtrl.$inject = ['$scope', '$http'];
    phonecatApp.controller('PhoneListCtrl', PhoneListCtrl);```

* 使用内联注解语句，函数不是仅仅提供功能要求，还包括一个功能名的字符串数组（内联注解数组），例如:
```js
    function PhoneListCtrl($scope, $http) {...}
    phonecatApp.controller('PhoneListCtrl', ['$scope', '$http', PhoneListCtrl]);```


这两种方法都可以被Angular正常识别进行注入，所以你只需要依据你项目风格选择一种即可。

当采用第二种方式，通常定义一个内联的匿名函数供注册器实施注入:
```js
    phonecatApp.controller('PhoneListCtrl', ['$scope', '$http', function($scope, $http) {...}]);```

 从现在开始，教程中我们将采用内联注解方式（第二种方法）进行处理，使得代码支持压缩。让我们为`PhoneListCtrl`添加一个内联注解：
`app/js/controllers.js`:
```js
var phonecatApp = angular.module('phonecatApp', []);

phonecatApp.controller('PhoneListCtrl', ['$scope', '$http',
  function ($scope, $http) {
    $http.get('phones/phones.json').success(function(data) {
      $scope.phones = data;
    });

    $scope.orderProp = 'age';
  }]);```
##测试
`test/unit/controllersSpec.js`:
因为我们应用了依赖注入使得控制器有了依赖关系，所以构建控制器的单元测试变动有点复杂了。我们可以使用`new`运算符在构造中提供`$http`的模拟实现。然而Angular提供了用于单元测试的`$http`模拟，我们需要配置`$httpBackend`实现“模拟”服务器响应来完成单元测试:
```js
describe('PhoneCat controllers', function() {

  describe('PhoneListCtrl', function(){
    var scope, ctrl, $httpBackend;

    // Load our app module definition before each test.
    beforeEach(module('phonecatApp'));

    // The injector ignores leading and trailing underscores here (i.e. _$httpBackend_).
    // This allows us to inject a service but then attach it to a variable
    // with the same name as the service in order to avoid a name conflict.
    beforeEach(inject(function(_$httpBackend_, $rootScope, $controller) {
      $httpBackend = _$httpBackend_;
      $httpBackend.expectGET('phones/phones.json').
          respond([{name: 'Nexus S'}, {name: 'Motorola DROID'}]);

      scope = $rootScope.$new();
      ctrl = $controller('PhoneListCtrl', {$scope: scope});
    }));```
注意：因为需要加载Jasmine和`angular-mocks.js`到测试环境，所以我们有两个辅助方法`module`和`inject`用于访问和配置注入器。

我们在测试环境中创建控制器：
* 我们使用`inject`辅助方法向Jasmine的`$beforeEach`函数中注入`$rootScope`、`$controller``$httpBackend`等功能实例。这些注入的功能实例用来从头到尾创建每一个测试，这可以保证每一个测试都是从共用的起点开始，但有互相隔离的。
*我们在我们的控制器中新建了一个作用范围叫做:`$rootScope.$new()`
* 我们通过`$controller`调用`PhonelistCtrl`（其由名字进行了注入，并且有指定的作用范围）

因为我们的代码使用`$http`服务获取手机列表数据到控制器中，所以之前我们需要在`PhoneListCtrl`中创建子作用范围，我们要告诉测试需要用预期的输入请求（返回结果）替代数据，对此我们需要:
* 在`beforEach`函数中注入`$httpBackend`的请求服务，这个模拟服务功能将模拟生产环境中所有的XHR和JSONP请求。这样的模拟服务允许您编写测试时无需处理本地api和与它们相关的全局状态（服务器端响应结果）——这些都是测试的噩梦

* 使用`$httpBackend.expectGET`这样的`$httpBackend`服务方法链可以模拟预期的HTTP请求和响应。注意：最终结果的返回是在调用了`$httpBackend.flush`方法后

现在我们可以验证`phones`数据模型通过请求构建完成前，是否已经有`scope`了:
```js
    it('should create "phones" model with 2 phones fetched from xhr', function() {
      expect(scope.phones).toBeUndefined();
      $httpBackend.flush();

      expect(scope.phones).toEqual([{name: 'Nexus S'},
                                   {name: 'Motorola DROID'}]);
    });```

* 我们利用`$httpBackend.flush()`激活浏览器请求序列，这导致`$http`服务返回预期的响应。
* 我们验证手机数据模型是否已经存在于作用范围内。

最后，我们验证默认的`orderProp`值是否设置正确:
```js
    it('should set the default value of orderProp model', function() {
      expect(scope.orderProp).toBe('age');
    });```
这时你应该看到类似如下信息的Karma输出：
```cmd
Chrome 22.0: Executed 2 of 2 SUCCESS (0.028 secs / 0.007 secs)```

##尝试
在`index.html`的底部添加`<pre>{{phones | json }}</pre>的数据绑定输出来显示json格式的手机数据。

在`PhoneListCtrl`控制器中，利用手机数字序号预处理http的响应结果来限定数据值的范围，可以把下面的代码加入`$http`回调中：
```js
$scope.phones = data.splice(0, 5);```

##小结
现在你了解学习了使用Angular服务是多么简单（依靠Angular依赖注入机制），下面跳到步骤6，将为手机添加图片缩略图和一些链接。

# 步骤11——REST与定制服务
在这一步，我们将改变数据获取方法：
* 我们定义定制服务来代表一个[RESTful](http://en.wikipedia.org/wiki/Representational_State_Transfer)客户端。使用这样的客户端，我们能够用更简单的方法向服务器请求数据，而不需要处理低层次的`$http`API，HTTP方法以及URL。

##工作区切换到步骤11
直接用浏览器访问[步骤11在线演示](http://angular.github.io/angular-phonecat/step-11/app)

大多数的重要改变都会列在下面，不过你也可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-10...step-11)看到完整的差异。

##依赖
RESTful功能是由Angular中的`ngResource`模块提供，这个模块不属于Angular框架核心模块，所以我们必须要单独的安装和引入。

我们可以使用[Bower](http://bower.io/)来安装客户端依赖，这一步将更新`bower.json`配置以安装:
```json
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
    "angular-route": "~1.2.x",
    "angular-resource": "~1.2.x"
  }
}
```
新的依赖描述`"angular-resource": "~1.2.x"`告诉bower要安装的angular-resource组件兼容版本是1.2.x。我们必须命令bower下载并安装这个依赖，这里我们运行下:
```cmd
npm install
```

>如果你是利用bower全局安装，你可能要单独运行`bower install`，但在这个项目中只需要运行`npm install`即可调用bower


##模板
我们在`app/js/services.js`中定制服务，所以需要在布局模板中引入这个文件。额外，我们也需要引入`angular-resource.js`文件，它提供了`ngResource`模块：
`app/index.html`:
```html
...
  <script src="bower_components/angular-resource/angular-resource.js"></script>
  <script src="js/services.js"></script>
...
```

##服务
我们创建自己的服务提供从服务器访问手机数据：
`app/js/services.js`
```js
var phonecatServices = angular.module('phonecatServices', ['ngResource']);

phonecatServices.factory('Phone', ['$resource',
  function($resource){
    return $resource('phones/:phoneId.json', {}, {
      query: {method:'GET', params:{phoneId:'phones'}, isArray:true}
    });
  }]);
```

我们使用模块API来注册了一个定制服务（作为工厂函数）。我们用`Phone`来代表这个服务(调用工厂函数)。工厂函数类似于一个控制器的构造函数,通过函数参数可以声明依赖注入。这个`Phone`服务描述了对`$resource`服务功能的依赖。

这个`$resource`服务功能只需要很少几行代码就简单的创建一个[RESTful](http://en.wikipedia.org/wiki/Representational_State_Transfer)客户端。这个客户端可以被用于我们的程序以替代低层次的`$http`服务。
`app/js/app.js`
```js
...
angular.module('phonecatApp', ['ngRoute', 'phonecatControllers','phonecatFilters', 'phonecatServices']).
...
```

我们也需要把`phonecatServices`模块添加进`phonecatApp`的模块依赖数组中。

##控制器
通过使用`Phone`服务代理替换低层次的`$http`服务，我们可以让我们的子控制器(`PhoneListCtrl`和`PhoneDetailCtrl`)进一步简化。Angular的`$resource`服务利用RESTful资源模型也提供了比`$http`更好的资源交互（展示），让我们更容易理解控制器中的代码如何工作。

`app/js/controllers.js`:
```js
var phonecatControllers = angular.module('phonecatControllers', []);

...

phonecatControllers.controller('PhoneListCtrl', ['$scope', 'Phone', function($scope, Phone) {
  $scope.phones = Phone.query();
  $scope.orderProp = 'age';
}]);

phonecatControllers.controller('PhoneDetailCtrl', ['$scope', '$routeParams', 'Phone', function($scope, $routeParams, Phone) {
  $scope.phone = Phone.get({phoneId: $routeParams.phoneId}, function(phone) {
    $scope.mainImageUrl = phone.images[0];
  });

  $scope.setImage = function(imageUrl) {
    $scope.mainImageUrl = imageUrl;
  }
}]);
```
注意现在`PhoneListCtrl`中我们把
```js
$http.get('phones/phones.json').success(function(data) {
  $scope.phones = data;
});
```
替换成
```js
$scope.phones = Phone.query();
```
这是一个简单的声明。我们可以查询到所有手机。

注意在上面的代码中一个重要的事情发生了，就是我们在调用`Phone`服务的方法时，没有定义任何的回调函数。虽然这像可以获得同步的返回果，但其实并不是。我们是在"未来"获得一个同步返回结果——一个由XHR响应返回数据填充的对象。因为Angular的数据绑定，我们可以使用这个"未来"结果，并绑定到我们的模板中。到时，当数据获取到后，视图会自动更新。

当然有时这样的"未来"对象以及相关数据绑定并不能满足我们所有的工作要求，所以在具体实现中，我们有时也需要添加回调来处理服务响应。例如`PhoneDetailCtrl`控制器中就包括了设置`mainImageUrl`这样的回调函数。

##测试
因为我们现在使用了`ngResource`模块，所以我们要更新Karma配置来完成测试。
`test/karma.conf.js`:
```js
    files : [
      'app/bower_components/angular/angular.js',
      'app/bower_components/angular-route/angular-route.js',
      'app/bower_components/angular-resource/angular-resource.js',
      'app/bower_components/angular-mocks/angular-mocks.js',
      'app/js/**/*.js',
      'test/unit/**/*.js'
    ],
```

我们编辑单元测试来验证新服务是否能够像预期一样正确发出HTTP请求和处理响应。这些测试也检验我们的控制器能够与服务正常协作。

`$resource`服务利用响应对象（作为参数）方法来更新和移除资源。如果我们使用标准的`toEqual`检测将因为不确定（恰当）的响应（比如包括一些额外方法定义等）而肯定失败，为了处理这样的问题，我们使用新定义一个[Jasmine matcher](https://github.com/pivotal/jasmine/wiki/Matchers)式`toEqualData`完成检测。使用`toEqualData`比较两个对象，将之考虑对象属性而忽略方法。
`test/unit/controllersSpec.js`:
```js
describe('PhoneCat controllers', function() {

  beforeEach(function(){
    this.addMatchers({
      toEqualData: function(expected) {
        return angular.equals(this.actual, expected);
      }
    });
  });

  beforeEach(module('phonecatApp'));
  beforeEach(module('phonecatServices'));


  describe('PhoneListCtrl', function(){
    var scope, ctrl, $httpBackend;

    beforeEach(inject(function(_$httpBackend_, $rootScope, $controller) {
      $httpBackend = _$httpBackend_;
      $httpBackend.expectGET('phones/phones.json').
          respond([{name: 'Nexus S'}, {name: 'Motorola DROID'}]);

      scope = $rootScope.$new();
      ctrl = $controller('PhoneListCtrl', {$scope: scope});
    }));


    it('should create "phones" model with 2 phones fetched from xhr', function() {
      expect(scope.phones).toEqualData([]);
      $httpBackend.flush();

      expect(scope.phones).toEqualData(
          [{name: 'Nexus S'}, {name: 'Motorola DROID'}]);
    });


    it('should set the default value of orderProp model', function() {
      expect(scope.orderProp).toBe('age');
    });
  });


  describe('PhoneDetailCtrl', function(){
    var scope, $httpBackend, ctrl,
        xyzPhoneData = function() {
          return {
            name: 'phone xyz',
            images: ['image/url1.png', 'image/url2.png']
          }
        };


    beforeEach(inject(function(_$httpBackend_, $rootScope, $routeParams, $controller) {
      $httpBackend = _$httpBackend_;
      $httpBackend.expectGET('phones/xyz.json').respond(xyzPhoneData());

      $routeParams.phoneId = 'xyz';
      scope = $rootScope.$new();
      ctrl = $controller('PhoneDetailCtrl', {$scope: scope});
    }));


    it('should fetch phone detail', function() {
      expect(scope.phone).toEqualData({});
      $httpBackend.flush();

      expect(scope.phone).toEqualData(xyzPhoneData());
    });
  });
});
```
我们现在可以看到类似如下的Karma输出了:
```cmd
Chrome 22.0: Executed 4 of 4 SUCCESS (0.038 secs / 0.01 secs)
```

##小结
我们准备在步骤12（最后一步）中为手机图片替换应用上动画效果。








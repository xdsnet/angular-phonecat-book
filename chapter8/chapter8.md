# 步骤8——更多模板
这一步我们会实现手机详细说明视图，它将在我们点击手机列表中的手机链接后显示。
*当点击手机列表中一个手机的链接，指定手机的详细说明页面将显示出对应手机的详细信息。

##工作区切换到步骤8
直接用浏览器访问[步骤8在线演示](http://angular.github.io/angular-phonecat/step-8/app)

大多数的重要改变都会列在下面，不过你也可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-7...step-8)看到完整的差异。

##数据
添加`phones.json`,在`app/phones/`目录中也包括每个手机的json文件:
`app/phones/nexus-s.json`:(一个简单的例子)
```js
{
  "additionalFeatures": "Contour Display, Near Field Communications (NFC),...",
  "android": {
      "os": "Android 2.3",
      "ui": "Android"
  },
  ...
  "images": [
      "img/phones/nexus-s.0.jpg",
      "img/phones/nexus-s.1.jpg",
      "img/phones/nexus-s.2.jpg",
      "img/phones/nexus-s.3.jpg"
  ],
  "storage": {
      "flash": "16384MB",
      "ram": "512MB"
  }
}```
每个文件都有相同的属性数据结构，我们会把这些数据显示在手机详细说明视图中。

##控制器
我们使用`$http`服务请求json文件功能来增强`PhoneDetailCtrl`。它们采用手机列表控制器相同的方式工作。
`app/js/controllers.js`:
```js
var phonecatControllers = angular.module('phonecatControllers',[]);

phonecatControllers.controller('PhoneDetailCtrl', ['$scope', '$routeParams', '$http',
  function($scope, $routeParams, $http) {
    $http.get('phones/' + $routeParams.phoneId + '.json').success(function(data) {
      $scope.phone = data;
    });
  }]);```
为了构建HTTP的URL请求，我们从定义在`$route`功能中的当前路由中扩展出`$routeParams.phoneId`。

##模板
前面用占位符粗略定义的模板被手机详细说明各部分的列表和数据绑定替换。注意我们使用了Angular`{{表达式}}`标签和`ngRepeat`来从我们数据模型中提取手机数据供显示。
`app/partials/phone-detail.html`:
```html
<img ng-src="{{phone.images[0]}}" class="phone">

<h1>{{phone.name}}</h1>

<p>{{phone.description}}</p>

<ul class="phone-thumbs">
  <li ng-repeat="img in phone.images">
    <img ng-src="{{img}}">
  </li>
</ul>

<ul class="specs">
  <li>
    <span>Availability and Networks</span>
    <dl>
      <dt>Availability</dt>
      <dd ng-repeat="availability in phone.availability">{{availability}}</dd>
    </dl>
  </li>
    ...
  <li>
    <span>Additional Features</span>
    <dd>{{phone.additionalFeatures}}</dd>
  </li>
</ul>```

##测试
我们写了一个类似我们在步骤5中`PhoneListCtrl`控制器的新单元测试。
`test/unit/controllersSpec.js`:
```js
  beforeEach(module('phonecatApp'));

  ...

  describe('PhoneDetailCtrl', function(){
    var scope, $httpBackend, ctrl;

    beforeEach(inject(function(_$httpBackend_, $rootScope, $routeParams, $controller) {
      $httpBackend = _$httpBackend_;
      $httpBackend.expectGET('phones/xyz.json').respond({name:'phone xyz'});

      $routeParams.phoneId = 'xyz';
      scope = $rootScope.$new();
      ctrl = $controller('PhoneDetailCtrl', {$scope: scope});
    }));


    it('should fetch phone detail', function() {
      expect(scope.phone).toBeUndefined();
      $httpBackend.flush();

      expect(scope.phone).toEqual({name:'phone xyz'});
    });
  });
...```
你应该可以看到类似下面的Karma输出：
```cmd
Chrome 22.0: Executed 3 of 3 SUCCESS (0.039 secs / 0.012 secs)```

我们也添加一个新的端到端测试导航到Nexus S的详细说明页面验证页面头部是否有"Nexus S"。
`test/e2e/scenarios.js`:
```js
...
  describe('Phone detail view', function() {

    beforeEach(function() {
      browser.get('app/index.html#/phones/nexus-s');
    });


    it('should display nexus-s page', function() {
      expect(element(by.binding('phone.name')).getText()).toBe('Nexus S');
    });
  });
...```
你现在可以运行`npm run protractor`来运行端到端测试了。

##尝试
使用[Protractor API](https://github.com/angular/protractor/blob/master/docs/api.md)，写一个测试在Nexus S详细说明页面会显示4个缩略图的端到端测试项目。

##小结
现在，我们的手机详细说明视图也已经建立好了，进入步骤9，学习如何写自定义的显示过滤器。










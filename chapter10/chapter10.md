# 步骤10——行为处理
在这一步中，我们会对手机图片添加点击支持，点击后会在手机详细说明页面显示新的图形。
* 手机详细说明显示当前手机的一个巨大的图片和一些缩略图。我们点击缩略图会显示对应的大图片，这个效果很令人兴奋。让我们看看如何利用Angular来实现。

##工作区切换到步骤10
直接用浏览器访问[步骤10在线演示](http://angular.github.io/angular-phonecat/step-10/app)

大多数的重要改变都会列在下面，不过你也可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-9...step-10)看到完整的差异。

##控制器
`app/js/controllers.js`:
```js
...
var phonecatControllers = angular.module('phonecatControllers',[]);

phonecatControllers.controller('PhoneDetailCtrl', ['$scope', '$routeParams', '$http',
  function($scope, $routeParams, $http) {
    $http.get('phones/' + $routeParams.phoneId + '.json').success(function(data) {
      $scope.phone = data;
      $scope.mainImageUrl = data.images[0];
    });

    $scope.setImage = function(imageUrl) {
      $scope.mainImageUrl = imageUrl;
    }
  }]);
```

在`PhoneDetailCtrl`控制器中，我们创建了一个`mainImageUrl`模型属性，并且设置默认值为第一个手机图片的URL。

我们也创建可一个`setImage`的行为处理函数，来改变`mainImageUrl`。

##模板
`app/partials/phone-detail.html`:
```html
<img ng-src="{{mainImageUrl}}" class="phone">

...

<ul class="phone-thumbs">
  <li ng-repeat="img in phone.images">
    <img ng-src="{{img}}" ng-click="setImage(img)">
  </li>
</ul>
...
```
我们把`ngSrc`指令通过`mainImageUrl`绑定到大图片的URL。

我们也为缩略图注册了`ngClick`处理，当用户点击任何缩略图的时候进行处理，这个处理会使用`setImage`行为处理函数依据点击的缩略图来改变`mainImageUrl`的值，从而改变大图显示内容。

##测试
为了检测新特性，我们需要写2个端到端测试。其中一个验证手机详细说明页面开始时，主要图（大图）是否是对应显示的第1个手机图片（默认值）。第二个测试检查点击一些缩略图是否会正确进行图片切换。
`test/e2e/secnarios.js`:
```js
...
  describe('Phone detail view', function() {

...

    it('should display the first phone image as the main phone image', function() {
      expect(element(by.css('img.phone')).getAttribute('src')).toMatch(/img\/phones\/nexus-s.0.jpg/);
    });


    it('should swap main image if a thumbnail image is clicked on', function() {
      element(by.css('.phone-thumbs li:nth-child(3) img')).click();
      expect(element(by.css('img.phone')).getAttribute('src')).toMatch(/img\/phones\/nexus-s.2.jpg/);

      element(by.css('.phone-thumbs li:nth-child(1) img')).click();
      expect(element(by.css('img.phone')).getAttribute('src')).toMatch(/img\/phones\/nexus-s.0.jpg/);
    });
  });
```

我们现在可以运行`npm run protractor`来完成测试了。

你也可以重构单元测试，因为这一步添加了`mainImageUrl`模型到`PhoneDetailCtrl`控制器中。下面，我们创建一个叫`xyzPhoneData`的函数，它会返回`images`属性的json数据来完成测试。
`test/unit/controllersSpec.js`:
```js
...
  beforeEach(module('phonecatApp'));

...

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
      expect(scope.phone).toBeUndefined();
      $httpBackend.flush();

      expect(scope.phone).toEqual(xyzPhoneData());
    });
  });
```
现在单元测试能通过了。

##尝试
让我们给`PhoneDetailCtrl`添加新的方法:
```js
  $scope.hello = function(name) {
      alert('Hello ' + (name || 'world') + '!');
  }
```
并把
```html
  <button ng-click="hello('Elmo')">Hello</button>
```
添加到`phone-detail.html`模板中。

##小结
我们的手机图片已经可以在指定位置进行切换了，我们准备进入步骤11来学习一个更好的获取数据的方法。



























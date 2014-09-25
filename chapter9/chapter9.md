# 步骤9——转换器
在这一步骤中，将学习到如何定制一个显示转换器
*在前一步，详细说明页面直接用"true"和"false"来显示某个手机特性是否被支持，在这里，我们将定制一个转换器，实现用符号:✓ 对应 "true", 以及 ✘ 对应 "false"，来进行显示。让我们看看转换器的代码是怎么样的。

##工作区切换到步骤9
直接用浏览器访问[步骤9在线演示](http://angular.github.io/angular-phonecat/step-9/app)

大多数的重要改变都会列在下面，不过你也可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-8...step-9)看到完整的差异。

##定制转换器
为了创建一个新的转换器，你需要创建一个`phonecatFilters`模块并把定制的转换器注册到这个模块中。
`app/js/filters.js`:
```js
angular.module('phonecatFilters', []).filter('checkmark', function() {
  return function(input) {
    return input ? '\u2713' : '\u2718';
  };
});```
我们要定制的转换器叫做"checkmark"。其输入(`input`)是`true`或者`false`，返回的输出结果根据输入是 `true` (\u2713 -> ✓) 或者`false` (\u2718 -> ✘)。

现在我们的转换器已经准备好了，需要注册到`phonecatFilters`模块中，并成为我们主模块`phonecatApp`的一个依赖。

`app/js/app.js`:
```js
...
angular.module('phonecatApp', ['ngRoute','phonecatControllers','phonecatFilters']);
...```

##模板
因为转换器的代码存在于`app/js/filters.js`文件中，所以我们需要把它添加到布局模板中。
`app/index.html`:
```js
...
 <script src="js/controllers.js"></script>
 <script src="js/filters.js"></script>
...```
在Angular模板中采用如下的语法来应用转换器:
```js
{{ expression | filter }}```
让我们把这个转换器应用到手机详细说明模板中:
`app/partials/phone-detail.html`:
```js
...
    <dl>
      <dt>Infrared</dt>
      <dd>{{phone.connectivity.infrared | checkmark}}</dd>
      <dt>GPS</dt>
      <dd>{{phone.connectivity.gps | checkmark}}</dd>
    </dl>
...```

##测试
转换器就像其它组件一样，也需要被测试，而且其测试也很容易写。
`test/unit/filtersSpec.js`:
```js
describe('filter', function() {

  beforeEach(module('phonecatFilters'));

  describe('checkmark', function() {

    it('should convert boolean values to unicode checkmark or cross',
        inject(function(checkmarkFilter) {
      expect(checkmarkFilter(true)).toBe('\u2713');
      expect(checkmarkFilter(false)).toBe('\u2718');
    }));
  });
});
我们在进行任何转换器测试前要保证调用了`beforeEach(module('phonecatFilters'))`，通过这个调用来加载`phonecatFilters`模块并注入测试中来保证测试用例能够正确初始化（被注入）。

注意我们还调用了辅助功能函数`inject(function(checkmarkFilter) { ... })`来访问我们打算测试的转换器，具体函数功能查看`angular.mock.inject()`。

注意注入后，一个后缀"Filter"会被添加到转换器名称后。请查看“Filter Guide ”的相关部分来了解更多。

你现在可以看到类似如下的Karma输出:
```cmd
Chrome 22.0: Executed 4 of 4 SUCCESS (0.034 secs / 0.012 secs)```

##尝试
让我们尝试一些Angular内建的转换器，把这些添加绑定到`index.html`:
*`{{"lower cap string" | uppercase }}`
*`{{foo : "bar",baz :23 | json }}`
*`{{1304375948024 | date }}`
*`{{ 1304375948024 | date:"MM/dd/yyyy @ h:mma" }}`

我们也创建一个输入模块，来包括这些转换器绑定，添加如下代码到`index.html`:
```html
<input ng-model="userInput"> Uppercased: {{ userInput | uppercase }}```

##小结
现在我们已经学习了如何写一个定制转换器，并知道如何进行测试，让我们进入步骤10学习如何使用Angular来增强手机详细说明页面。

















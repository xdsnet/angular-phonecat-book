# 步骤6——模板链接与图形
在这一步，会为手机列表添加缩略图以及对应的购买信息链接。后续步骤中，你将通过链接显示目录中手机的更多附加（详细）信息。
 * 现在就为手机列表添加缩略图和链接。

##工作区切换到步骤6
直接用浏览器访问[步骤6在线演示](http://angular.github.io/angular-phonecat/step-6/app)

大多数的重要改变都会列在下面，不过你也可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-5...step-6)看到完整的差异。

##数据
注意到在`phones.json`中包含了每个手机独一无二的id和图片链接地址。这些链接地址都指向`app/img/phones/`目录。
`app/phones/phones.json`（一段手机数据的快照）
```js
[
  {
    ...
    "id": "motorola-defy-with-motoblur",
    "imageUrl": "img/phones/motorola-defy-with-motoblur.0.jpg",
    "name": "Motorola DEFY\u2122 with MOTOBLUR\u2122",
    ...
  },
  ...
]```

##模板
`app/index.html`:
```html
...
        <ul class="phones">
          <li ng-repeat="phone in phones | filter:query | orderBy:orderProp" class="thumbnail">
            <a href="#/phones/{{phone.id}}" class="thumb"><img ng-src="{{phone.imageUrl}}"></a>
            <a href="#/phones/{{phone.id}}">{{phone.name}}</a>
            <p>{{phone.snippet}}</p>
          </li>
        </ul>
...```
为了动态处理链接（这在后面将导向手机的详细介绍页面），我们给`href`属性的赋值中用到了由双大括号括起来的数据绑定。在步骤2中，我们把`{{phone.name}}`绑定到一个html元素的内容中，这一步`{{phone.id}}`将用来绑定到元素属性中。

我们还为每款手机添加了图片，这里用到了`ngSrc`命令，它会阻止浏览器在Angular还没有初始化并能够正常展开绑定时发出诸如下面一样的无效URL请求:
`http://localhost:8000/app/{{phone.imageUrl}}`，而是仅仅描述当前元素需要一个`src`属性，这个属性（`<img src="{{phone.imageUrl}}">`，会在Angular初始化好后进行绑定），使用`ngSrc`指令能始终阻止浏览器发出明显是无效的http请求。

##测试
`test/e2e/scenarios.js`:
```js
...
    it('should render phone specific links', function() {
      var query = element(by.model('query'));
      query.sendKeys('nexus');
      element.all(by.css('.phones li a')).first().click();
      browser.getLocationAbsUrl().then(function(url) {
        expect(url.split('#')[1]).toBe('/phones/nexus-s');
      });
    });
...```
我们添加一个新的端到端测试来验证程序会处理正确的链接，对于这个手机显示程序来说，正是我们要实现的功能。

你现在可以运行`npm run protractor`来看测试情况。

##尝试
把`ng-src`指令替换为原来的`src`属性，使用诸如Firebug之类的工具或者Chrome浏览器的Web开发组件观察web访问日志，可以观察到如`/app/%7B%7Bphone.imageUrl%7D%7D` 或者 `/app/{{phone.imageUrl}}`这样的无效请求，这些无效请求就是浏览器在Angular还没有正确初始化和进行数据绑定（注入）时，构建`img`标签时发出的，这使得图片不能正确显示。

##小结
我们已经给手机添加了图片和链接，让我们进入步骤7，学习如何对模板进行布局，以及Angular如何容易的创建一个多视图的程序。


# 步骤12——应用动画
在这个最终步骤中，我们通过在以前代码基础上附加CSS和JavaScript实现动画效果来增强手机展示web程序。
* 用`ngAnimater`使得在程序中可以应用动画
* 公用的`ng`指令自动利用钩子(hook)来触发动画
* 当标准的DOM操作被赋予了动画时，对应的元素将会应用指定的动画效果，这些操作包括了利用`ngRepeat`插入或者移除DOM节点，以及利用`ngClass`对元素指定或者去除类型设定。


##工作区切换到步骤12
直接用浏览器访问[步骤12在线演示](http://angular.github.io/angular-phonecat/step-12/app)

大多数的重要改变都会列在下面，不过你也可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-11...step-12)看到完整的差异。

##依赖
动画功能由Angular的`ngAnimate`模块提供，它不属于Angular框架核心。此外我们使用`JQuery`来提供额外的JavaScript动画支持。
我们使用`Bower`来安装客户端依赖。这一步骤中我们会更新`bower.json`配置文件来包含新的依赖关系:
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
    "angular-resource": "~1.2.x",
    "jquery": "1.10.2",
    "angular-animate": "~1.2.x"
  }
}```
* `"angular-animate": "~1.2.x"`告诉bower需要安装1.2.x版本的angular-animate兼容组件。

* `"jquery": "1.10.2"`告诉bower需要1.10.2版本的JQuery，注意，JQuery不属于Angular库，它是一个标准的JQuery库。我们可以用bower安装第三方的库。

我们必须执行bower来下载安装，这里运行`npm install`

>如果你是在全局环境中使用bower安装，你可能会用直接到`bower install`指令，但是在这个项目中我们已经预配使用`npm`来启动`bower install`，所以你只需要:
`npm install`

##如何利用`ngAnimate`实现动画
如何利用AngularJS实现动画，请首先阅读`AngularJS Animation Guide`(AngularJS动画指南)。

##模板
需要在HTML模板代码中进行一些改动以链接诸如`angular-animate.js`等定义动画所需的资源文件。动画模块被称为`ngAnimate`，其在`angular-animate`中定义，并包含让程序应用动画的必要代码。

这里，我们在index文件中进行必要改动。
`app/index.html`
```html
...
  <!-- for CSS Transitions and/or Keyframe Animations -->
  <link rel="stylesheet" href="css/animations.css">

  ...

  <!-- jQuery is used for JavaScript animations (include this before angular.js) -->
  <script src="bower_components/jquery/jquery.js"></script>

  ...

  <!-- required module to enable animation support in AngularJS -->
  <script src="bower_components/angular-animate/angular-animate.js"></script>

  <!-- for JavaScript Animations -->
  <script src="js/animations.js"></script>

...```

>重要提醒:在Angular 1.3中必须要使用jQuery 2.1以上版本，而jQuery1.x版本是不被正式支持的。一定要在所有AngularJS脚本之前加载jQuery，否则AngularJS可能不能正确检测到jQuery而造成动画不按预想工作。

可以创建动画中的CSS代码(`animations.css`)创建和JavaScript代码(`animations.js`)。不过在此之前我们创建一个新依赖ngAnimate的模块，就像前面的`ngResource`服务一样。


##模块与动画
`app/js/animations.js`:
```js
angular.module('phonecatAnimations', ['ngAnimate']);
  // ...
  // this module will later be used to define animations
  // .```
  然后把这个模块加入到我们的程序中...
  `app/js/app.js`:
 ```js
 // ...
angular.module('phonecatApp', [
  'ngRoute',

  'phonecatAnimations',
  'phonecatControllers',
  'phonecatFilters',
  'phonecatServices',
]);
// ...```
现在这个手机展示已经准备好动画环境了，让我们创作一些动画。

##在动态`ngRepeat`中使用CSS的过渡动画
让我们在`phone-list.html`中添加CSS过渡动画到`ngRepeat`指令处理过程中。首先，我们给重复的元素添加一个额外CSS类，这样我们就可以以此钩住（hook）CSS过渡动画。
`app/partials/phone-list.html`
```html
<!--
  Let's change the repeater HTML to include a new CSS class
  which we will later use for animations:
-->
<ul class="phones">
  <li ng-repeat="phone in phones | filter:query | orderBy:orderProp"
      class="thumbnail phone-listing">
    <a href="#/phones/{{phone.id}}" class="thumb"><img ng-src="{{phone.imageUrl}}"></a>
    <a href="#/phones/{{phone.id}}">{{phone.name}}</a>
    <p>{{phone.snippet}}</p>
  </li>
</ul>```
注意我们是怎么添加`phone-listing`CSS类的？这些就是我们获得动画效果所有必须的HTML代码。

现在为CSS过渡动画实现代码
```css
.phone-listing.ng-enter,
.phone-listing.ng-leave,
.phone-listing.ng-move {
  -webkit-transition: 0.5s linear all;
  -moz-transition: 0.5s linear all;
  -o-transition: 0.5s linear all;
  transition: 0.5s linear all;
}

.phone-listing.ng-enter,
.phone-listing.ng-move {
  opacity: 0;
  height: 0;
  overflow: hidden;
}

.phone-listing.ng-move.ng-move-active,
.phone-listing.ng-enter.ng-enter-active {
  opacity: 1;
  height: 120px;
}

.phone-listing.ng-leave {
  opacity: 1;
  overflow: hidden;
}

.phone-listing.ng-leave.ng-leave-active {
  opacity: 0;
  height: 0;
  padding-top: 0;
  padding-bottom: 0;
}```
正如所见，`phone-listing`CSS类通过向对象添加或者去除（CSS类）而使得动画钩子触发。
* `ng-enter`类被赋予一个新加入并渲染展示在页面中的手机元素。
* `ng-move`类赋予在列表中移动的手机元素
* `ng-leave`类赋予从列表中去除的元素

手机列表根据`ng-repeat`属性添加或者删除元素。例如如果转换器输出数据改变，导致在列表中动态的添加或者放置元素。

这点尤为需要注意，在动画工作时，有两套CSS类可被加入元素：
1. 一套是"starting"类，这代表了动画的开始形式
2. 一套"active"类，代表了动画结束的形式

这个命名为`starting`的类会触发一些有`ng-`前缀的行为(例如`enter`,`move`或者`leve`)，像`enter`就会触发`ng-enter`。

而`active`的类像`starting`类一样也会触发一些行为，只是这些行为是有`-active`后缀。这两套CSS类的名称可以在开发中指定动画的实现，是开始还是结束。

在我们的例子中，在列表中添加或者移动时，元素会从高度0扩展到高度120像素，在去除时逐渐消隐掉（缩小），同时还有一个淡入淡出效果。所有这些操作都是通过CSS过渡声明在前面的例子代码中。

尽管许多现代浏览器都能很好的支持[CSS过渡](http://caniuse.com/#feat=css-transitions)和[CSS动画](http://caniuse.com/#feat=css-animation)，但IE9及以前版本是不支持的。如果你想对老的浏览器提供兼容的动画效果，可以考虑使用基于JavaScript的动画，这将在后面描述。

##通过CSS提供`ngView`关键帧动画
接着，让我们为`ngView`中的内容切换提供动画。

首先，还是要添加一个新的CSS类到我们的HTML中，就像上面示例一样。这次，不是插入到`ng-repeat`所在的元素中，而是加入到应用`ng-view`命令所在的元素。为此，我们需要对HTML进行一些小改动，所以我们可以在视图变化时有更多的控制动画。
`app/index.html`
```html
<div class="view-container">
  <div ng-view class="view-frame"></div>
</div>```
通过这点改变，嵌入在`view-container`CSS类元素下的`ng-view`指令所在元素有了`view-frame`CSS类属性。而`view-container`CSS类元素下的各个子元素有了`position: relative`样式设定，使得`ng-view`是相对于父级元素进行定位实现动画转换。

在这里，我们添加一些CSS到`animations.css`中实现转换动画:
`app/css/animations.css`
```css
.view-container {
  position: relative;
}

.view-frame.ng-enter, .view-frame.ng-leave {
  background: white;
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
}

.view-frame.ng-enter {
  -webkit-animation: 0.5s fade-in;
  -moz-animation: 0.5s fade-in;
  -o-animation: 0.5s fade-in;
  animation: 0.5s fade-in;
  z-index: 100;
}

.view-frame.ng-leave {
  -webkit-animation: 0.5s fade-out;
  -moz-animation: 0.5s fade-out;
  -o-animation: 0.5s fade-out;
  animation: 0.5s fade-out;
  z-index:99;
}

@keyframes fade-in {
  from { opacity: 0; }
  to { opacity: 1; }
}
@-moz-keyframes fade-in {
  from { opacity: 0; }
  to { opacity: 1; }
}
@-webkit-keyframes fade-in {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes fade-out {
  from { opacity: 1; }
  to { opacity: 0; }
}
@-moz-keyframes fade-out {
  from { opacity: 1; }
  to { opacity: 0; }
}
@-webkit-keyframes fade-out {
  from { opacity: 1; }
  to { opacity: 0; }
}

/* don't forget about the vendor-prefixes! */```
不是太疯狂！只是一个简单的划入划出效果就出现在页面中。这里的唯一不寻常的是,我们使用绝对定位来前一个页面(其有`ng-leave`类型设定),使其在下一个页面位置(通过`ng-enter`确认)的顶部。前一个页面将被移除，所以会淡出，新的页面将淡入并移动到原来的顶部。

在页面渲染时，一旦离开动画完成（动画对应元素在显示中被移除），或者一旦进入动画完成(被赋予了`ng-enter` 和 `ng-enter-active` CSS 类的元素)，都会移除附加的预订CSS类（即动画相关的CSS类，例如`ng-enter` 、`ng-enter-active`和`ng-leve`等）。这显得十分自然流畅,页面处理流程没有任何跳来跳去。

这些CSS类利用`ng-repeat`赋予所有需要的元素（开始和结束类）。每次页面加载`ng-view`都会创建一个它的副本，下载模板并且添加内容。这将确保所有的内容在一个单页HTML的元素中来允许简单的动画控制。


要了解更多CSS动画，请看[WEB平台文档](http://docs.webplatform.org/wiki/css/properties/animations)。

##利用JavaScript实现`ngClass`动画
我们将在我们的程序中添加其他动画，转换到`phone-detail.html`页面，我们已经有了一个不错的缩略图切换效果：点击缩略图，将显示对应的手机不同角度图片，但我们能否为它添加动画呢？

让我们先考虑一下这一过程：当你点击缩略图,会改变显示不同角度图像（状态）来反映新点选的缩略图。在HTML中要改变描述状态最好是使用类。所以像前面一样，我们可以通过指定CSS类来描述一个动画，这次每当CSS类本身发生变化时会显示动画。

每当一个新手机缩略图被选中，`.active`CSS类就会添加到匹配的图片上，并播放动画。

首先，我们开始调整`phone-detail.html`页面中的HTML代码:

`app/partials/phone-detail.html`
```html
<!-- We're only changing the top of the file -->
<div class="phone-images">
  <img ng-src="{{img}}"
       class="phone"
       ng-repeat="img in phone.images"
       ng-class="{active:mainImageUrl==img}">
</div>

<h1>{{phone.name}}</h1>

<p>{{phone.description}}</p>

<ul class="phone-thumbs">
  <li ng-repeat="img in phone.images">
    <img ng-src="{{img}}" ng-mouseenter="setImage(img)">
  </li>
</ul>```

就像缩略图，我们使用一个转换器来显示所有的概要文件列表图片，但是我们没有动画，也没有重复关联动画。反而，我们把视线投向了`ng-class`指令，因为无论何时，只有`active`类被激活，它就被加入到元素，并作为可视元素渲染显示出来。另外，其他不同角度的图片是隐藏的。在这个例子中，同一时间总有一个元素（图片）是`active`而被显示在屏幕上的。

当`active`类被加入到元素，在`active-add`和`active-add-active`类被添加前AngularJS可以触发一个动画。当元素移除时，`active-remove`和`active-remove-active`类被赋予到元素，这也可以触发另一个动画。

为了确保手机图片中页面第一次加载时正确显示，我们也要处理一下页面CSS样式:
`app/css/app.css`:
```css
.phone-images {
  background-color: white;
  width: 450px;
  height: 450px;
  overflow: hidden;
  position: relative;
  float: left;
}

...

img.phone {
  float: left;
  margin-right: 3em;
  margin-bottom: 2em;
  background-color: white;
  padding: 2em;
  height: 400px;
  width: 400px;
  display: none;
}

img.phone:first-child {
  display: block;
  }```

你可能想我们是不是要创建另外的CSS动画？事实上，我们不这样做，这里我们将借此机会学习如何利用`animation()`方法创建基于JavaScript的动画：
`app/js/animations.js`
```js
var phonecatAnimations = angular.module('phonecatAnimations', ['ngAnimate']);

phonecatAnimations.animation('.phone', function() {

  var animateUp = function(element, className, done) {
    if(className != 'active') {
      return;
    }
    element.css({
      position: 'absolute',
      top: 500,
      left: 0,
      display: 'block'
    });

    jQuery(element).animate({
      top: 0
    }, done);

    return function(cancel) {
      if(cancel) {
        element.stop();
      }
    };
  }

  var animateDown = function(element, className, done) {
    if(className != 'active') {
      return;
    }
    element.css({
      position: 'absolute',
      left: 0,
      top: 0
    });

    jQuery(element).animate({
      top: -500
    }, done);

    return function(cancel) {
      if(cancel) {
        element.stop();
      }
    };
  }

  return {
    addClass: animateUp,
    removeClass: animateDown
  };
});```

注意，这里我们使用了[jQuery](http://jquery.com/)来实现动画。在AngularJ中实现动画时jQuery并不是必须的，但是直接用JavaScript实现动画其实已经超出了本教程的范围。为了更多了解`jQuery.animat`请查看[jQuery文档](http://api.jquery.com/animate/)。

我们注册了`addClass`和`removeClass`回调函数，其会在`.phone`内部的任何元素添加或是移除类属性时调用。当`.active`类型被添加到元素(通过`ng-class`指令)，则JavaScript的`addClass`回调被调用，而且所对应的`element`作为参数传入。最后一个参数`done`是一个回调函数。事实上`done`是为了通知Angular知道JavaScript动画已经完成，从而调用以完成后续工作。

`removeClass`回调也基于同样的机制工作，不过是插入到一个类从元素中移除时。

通过JavaScript的回调，我们创建了对DOM的手动操作，在代码内，这些就是`element.css()` 和`element.animate()`被执行。回调定位了下一个元素在500像素下，并移动前后两张图片，并动态同步移动以实现动画。这就是一个传送带动画，在`animate`功能完成后，调用`done`。

注意:`addClass`和`removeClass`每次返回一个函数，这是一个可选项，以实现函数调用链（可以让一个动画结束后进入下一个动画——或者功能），一个布尔值被传递，让开发者知道动画是否被调用。这通常被用于在动画结束后执行一些清理工作。


##小结
你已经完成了它！我们在一个相对短的时间内就创建了一个web程序。最后（下一页），我们将指出如何进一步学习。
















# 步骤1——静态模板
这里将举例展示Angular是如何增强标准HTML的。你将创建一个纯粹的*静态*HTML页面，下面的步骤你会看到我们如何把HTML代码转为Angular模板，通过数据来动态显示出相同的结果。

在这一步中，我们将添加两部手机的基本信息到一个HTML页面中。
*页面现在显示了两个手机的一个列表信息

##工作区切换到步骤1
让工作区切换到合适的分支(注意是在`angular-phonecat`目录中执行下面的操作，以后各步都会执行类似的操作，只是最后的数字不同，所以将不再详细说明和介绍，只是提示切换到步骤多少)：
> 切换到步骤1
```cmd
git checkout -f step-1
```

你也可以直接用浏览器访问[步骤1在线演示](http://angular.github.io/angular-phonecat/step-1/app)



大多数的重要改变都会列在下面，不过你也可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-0...step-1)看到完整的差异。

##介绍
`app/index.html`:

```html
  <ul>
    <li>
      <span>Nexus S</span>
      <p>
        Fast just got faster with Nexus S.
      </p>
    </li>
    <li>
      <span>Motorola XOOM™ with Wi-Fi</span>
      <p>
        The Next, Next Generation tablet.
      </p>
    </li>
  </ul>
  ```

##尝试
试着再添加一些静态HTML，例如：
```html
<p>总共手机数量：2</p>
```
##小结
这里添加了一个静态HTML来显示一个列表，让我们到步骤2去学习如何利用AngularJS来动态生成相同的列表。

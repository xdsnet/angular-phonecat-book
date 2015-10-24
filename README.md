# PhoneCat 入门教程 App 导言
（基于AngularJS1.3.0-rc.1 由xdsnet at gmail 翻译）

本教程是一个介绍利用AngularJS开发的极好入门材料，它给出了一个完整的AngularJS web app开发的过程。这个app会构建一个Android设备的目录显示服务，可以通过过滤来定位显示个别关注的设备以显示更多细节信息。
![catalog_screen.png](./image/catalog_screen.png)
下面这个教程会展示Angular是如何在没有扩展程序或插件的支持下使得浏览器应用更智能，你将：

* 通过例子了解如何使用客户端数据绑定技术实现能快速响应用户活动操作的动态视图应用。
* 了解Angular是如何在没有专门的DOM操作中实现视图与数据同步的。
* 学习如何利用Karma和Protractor技术来更好、更方便的测试应用。
* 学习如何使用依赖注入和服务来使常见的web任务更容易：例如在应用中获得数据。


当你完成这个入门教程，你将能够：

* 创建一个能在所有现代浏览器使用的动态应用程序
* 通过数据绑定连接你的数据模型与视图
* 利用Karma技术创建和实施单元测试
* 利用Protractor技术创建和实施 端到端（e2e）的测试
* 把应用逻辑从template(模板)中移出到Controllers(控制器)中
* 利用Angular服务从服务器获取数据
* 利用ngAnimate技术在应用中应用动画
* 知道学习更多的AngularJS需要哪方面资源

这个教程通过完整的开发一个简单应用的过程指导你学习AngularJS，这包括如何写并且运行单元测试和端到端测试。每一步还额外提供了建议，供您了解更多的AngularJS和您正在构建的应用程序。

你可以在几个小时内完成整个教程，当然你也可以深度学习来度过愉快的一天。如果你正在寻找一个更短的AngularJS介绍，请看入门文档。

# 开始
这部门内容展示如何在你本地计算机上设置来进行开发AngularJS相关程序。如果你仅仅想阅读入门教程，你可以直接跳到步骤0——准备

#使用代码
在你的计算机上，你完全可以一直跟随教程使用代码，也可以进行随意改动，实际上改动代码可以让你更深入体验如何撰写AngularJS程序以及利用推荐的测试工具工作。

这个教程依赖于Git版本控制工具，但使用本教程你并不需要知道太多Git使用知识,只需要会用教程中用到的很少几个命令（到时也会给出了完整命令）。

## 安装Git
你可以从 [http://git-scm.com/download](http://git-scm.com/download)下载安装Git。安装后你应该可以访问到git命令行工具，在本教程中你仅需要用到两个git命令:

* `git clone ...` 从远处版本仓库克隆到本地计算机
* `git checkout ...`在本地计算机上查看（取出）特定版本（标签）的代码

## 下载angular-phonecat
从 GitHuB上的[angular-phonecat repository](https://github.com/angular/angular-phonecat)克隆需要用到下面的命令
```cmd
git clone --depth=14 https://github.com/angular/angular-phonecat.git
```

这个命令会在你计算机的当前目录下建立`angular-phonecat`目录：
>`--depth=14` 参数选项是告诉Git获取最近的14次提交，这让下载的东西更少，也就更快了。

改变当前目录到`angular-phonecat`
```cmd
cd angular-phonecat
```
本教程说明：从现在开始，都假定你的所有命令都是在`angular-phonecat`目录下执行。

##安装Node.js
如果你想运行预定义的本地web服务来测试，你需要有 [Node.js v0.10.27+](http://nodejs.org/)。你可以从[http://nodejs.org/download/](http://nodejs.org/download/)下载安装你操作系统合适的版本。确认你的Node.js是符合要求的版本，需要运行：
```cmd
node --version
```
在基于Debian的发行版，因为有另外一个工具命名为`node`，所以提供的解决方案是安装`nodejs-legacy`apt包，但把`node`改名为`nodejs`:
```cmd
apt-get install nodejs-legacy
nodejs --version
```
>如果你需要在你的本地环境中运行不同版本的node.js，可以考虑安装 [Node Version Manager (nvm)](https://github.com/creationix/nvm) 。

一旦在你的计算机中安了Node.js，你就可以考虑安装下面的工具来自动解决依赖问题：
```cmd
npm install
```

这个命令会下载下面的工具到`node_modules`目录：
* [Bower](http://bower.io/) -客户端代码包管理
* [Http-Server](https://github.com/nodeapps/http-server) - 简单的本地静态web服务
* [Karma](https://github.com/karma-runner/karma) -单元测试工具
* [Protractor ](https://github.com/angular/protractor)- 端到端(E2E)测试工具

运行`npm install`会自动下载Angular框架到`app/bower_components`目录
>注意angular-phonecat项目通过npm脚本自动安装运行各个单元，这意味着你不需要单独为了这个教程在系统（全局）中安装相应内容。更多信息请在下面的**安装辅助工具**中了解。
项目中预定义了几个npm辅助脚本来使得你更容易运行常规任务，这些在整个开发中都是有效的：

* `npm start `: 打开本地开发web服务
* `npm test`: 运行Karma单元测试
* `npm run protractor`: 运行Protractor端到端(E2E)测试
* `npm run update-webdriver `: 安装Protractor需要的驱动程序

##安装辅助工具（可选）
Bower, Http-Server, Karma 和 Protractor 模块都是单独可执行的，你可以全局安装，然后在一个终端/命令行中运行。在这个教程中你不一定需要下面的工作，如果你决定立即执行，你可以在全局安装:
```cmd
sudo npm install -g ...
```
例如要安装Bower的命令是:
```cmd
sudo npm install -g bower
```
*(注意在windows上运行时需要省略那个sudo)*
然后你就可以运行bower工具了，例如：
```cmd
bower install
```

##运行开发Web服务
整个Angular应用是纯粹的客户端代码，所以它可以用浏览器在本地文件系统打开，这也可能比通过HTTP服务打开更好。但是，为了安全现在很多浏览器是不允许JavaScript直接从本地文件系统加载文件的。所以 angular-phonecat项目定义了一个简单的静态web服务来支持开发。打开运行这个服务需要输入:
```cmd
npm start
```
这会建立一个本地web服务，它监听者端口8000.你可以用浏览器打开
```
http://localhost:8000/app/index.html
```
来访问
>这个web服务也可以绑定到不同的ip或者端口，你需要编辑package.json中的“start"脚本部分，你可以用-a 来设置ip地址，用-p来设置端口。

##运行单元测试
单元测试可以确认JavaScript脚本会执行正确的操作，单元测试关注程序中局部的功能实现。所有的单元测试都在`test/unit`目录下。

本项目使用Karma来定义单元测试，执行这些测试需要运行
```cmd
npm test
```
这将运行Karma单元测试。 Karma读取`test/karma.conf.js`中的配置,它指示Karma执行:
 * 打开一个Chrome浏览器，并且连接到Karma
 * 在浏览器中执行所有的单元测试
 * 在终端/命令行中报告测试结果
 * 监控项目JavaScript文件的改变并再次执行测试

确保所有测试项都是正确的才算完成相应的编码工作，因为单元测试可以检测你的改动是否符合你编码需要，并及时反馈。

##运行端到端（E2E）测试

我们使用端到端（End to End——E2E） 的测试确保应用的整体操作。端到端测试被设计来测试完整客户端应用，包括显示的视图和操作交互对应的行为是否正确。它模拟真实的用户在真实浏览器中访问应用的场景。

端到端测试放置在`test/e2e`目录下。

angular-phonecat项目配置使用[Protractor](https://github.com/angular/protractor)来执行端到端测试。Protractor依赖一套驱动使它能与浏览器交互。你可以通过下面的命令进行安装:

```cmd
npm run update-webdriver
```

*(你只需要执行它一次.)*

因为Protractor工作需要与程序交互，所以需要先启动我们的web服务：:

```cmd
npm start
```

然后在一个特定的终端/命令行窗口中执行Protractor测试脚本：:

```cmd
npm run protractor
```

Protractor会读取在`test/protractor-conf.js`中的配置，这些配置告诉Protractor执行:

* 打开一个Chrome浏览器并且连接到程序
* 在一个浏览器中执行端到端测试
* 在终端/命令行窗口中报告测试结果
* 关闭/退出浏览器

无论你对程作了何种修改都运行端到端测试来确认修改仍然使得程序的整体操作是正确的，在把修改提交到远程仓库前完整运行端到端测试的行为是很常见的。


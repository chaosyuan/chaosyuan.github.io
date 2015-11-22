---
layout: post
title: "Carthage 包管理工具"
date: 2015-11-15 16:41:29 +0800
comments: true
categories: 
---
说到iOS开发的包管理，大家首先可能会想到Cocoapads,它确实是一个很强大的工具，但是这次主要说一下另外一个包管理工具Carthage.

 CocoaPods 和 Carthage的区别:
 
CocoaPods 是一套整体解决方案，在 Podfile文件中指定好我们需要的第三方库。然后执行 pod update命令，就会进行下载，集成，CocoaPods默认会自动创建并更新你的应用程序和所有依赖的Xcode workspace。CocoaPods的方法非常易于使用。

相比之下，Carthage 就要轻量很多，它也会一个叫做 Cartfile 描述文件，但 Carthage 不会对我们的项目结构进行任何修改，更不多创建 workspace。它只是根据我们描述文件中配置的第三方库，将他们下载到本地，然后使用 xcodebuild 构建成 framework 文件。然后由我们自己将这些库集成到项目中。Carthage 使用的是一种非侵入性的方式，它本身不会对我们的项目结构进行任何改动。

CocoaPods为提高第三方开源库的可见性和参与度，创建一个更中心化的生态系统， CocoaPods 有一个中心服务器(cocoapods.org)，来管理各个包的元信息，依赖于每个第三方库自己的源地址，比如 Github。包管理依赖中心服务器，索引第三方库十分方便。

与之相比，Carthage创建的是去中心化的依赖管理器。它没有总项目的列表，这能够减少维护工作并且避免任何中心化带来的问题。不过，这样也有一些缺点，就是项目的发现将更困难，想查找第三方库的时候，也没有一个中心服务器来帮助我们进行索引，而是必须从网络上自行查找，用户将依赖于Github的趋势页面或者类似的代码库来寻找项目。此外，Carthage是用swift语言编写的，Carthage只正式支持动态框架，动态框架能够在任何版本的OS X上使用，但只能在iOS 8及以上版本使用

创建Carthage的原因是想要一种尽可能简单的工具——一个只关心本职工作的依赖管理器，而不是取代部分Xcode的功能，或者需要让框架作者做一些额外的工作。CocoaPods提供的一些特性很棒，但由于附加的复杂性，它们将不会被包含在Carthage当中。

Carthage 和 CocoaPods 各有优劣，如何选择，就要看自己的需求了。

安装Carthage
Carthage提供OS X平台的pkg安装件，你可以从Github https://github.com/Carthage/Carthage/releases


![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/CarthageSet.png)


Carthage 目前最新版本是 0.10.7，下载 Carthage.pkg 包就可以，下载成功后，双击安装。

安装成功后，我们就可以在项目中配置 Cartfile 文件了，可以在我们项目的根目录中，使用文件编辑器，建立这个文件：

在Cartfile文件中，引入一个第三方库，比如强大的网络库Alamofire:
github "Alamofire/Alamofire" .然后保存文件，输入命令 carthage update
Carthage就开始抓取并使用xcodebuild构建第三方库

![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/CarthageUpdate.png)

update 命令执行完以后，会在项目中生成一个Carthage目录，这个目录包含两个Build目录和Checkouts目录。

Build中存放的是构建好的framework包:
![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/Buildfile.png)


Checkouts中存放的是第三方库项目源文件：
![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/Checkout.ong.png)

接下来，在项目设置中，进入 General 选项卡，在最下方的 Linked Frameworks and Libraries 中，将 Carthage/Build/iOS 中的 framework 文件添加到项目中：

![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/linkFrameworks.png)


然后在 Build Phrases 中，点击左上角的 + 号，添加一个 New Run Script Phrase:

![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/runScript.png)



然后在脚本区域输入：
![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/userlocalBin.png)

然后将 Alamofire 的路径添加到 Input Files 中:
![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/SRCRooT.png)

最终结果如下图：
![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/resultScroot.png)


添加这个 Run Script 的作用是为了让运行时能够找到这个动态库。如果不添加这个 copy-frameworks 脚本，那么项目在运行的时候会因为找不到这个动态库而在启动的时候崩溃。

还可以将 Carthage 所集成的第三方库生成的符号文件添加到项目中，这样在调试的时候，就可以步入第三方库内部的代码了。


具体步骤，还是进入 Build Phrases ，然后在右上角点击 New Copy Files Phrase，然后将 Carthage/Build/iOS 目录中的 Alamofire.framework.dSYM 符号文件拖动进来:
![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/dsymFrameWork.png)

在项目运行后，就可以在断点中步入 SwiftyJSON 内部的代码了。
这样就通过 Carthage 将 Alamofire 库顺利的集成到项目中了
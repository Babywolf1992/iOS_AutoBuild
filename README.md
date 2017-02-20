# iOS_AutoBuild
iOS自动打包1

# iOS 自动打包

使用脚本实现自动打包，网上搜了好多，但是偶比较笨，一直发现不知道怎么使用，看了好久，
终于改了个可以使用的，但是最后发现不能用xcrun导出；宝宝心里苦，但宝宝不说😂；接着改，最后终于被我捣鼓成功了，分享给大家一下

Note: 只是简单的基础版本的，这个支持的是没有使用Cocoapods的工程，，脚本见附件；如果是使用Cocoapods工程的，可以稍微修改一下，鼓励大家自己试试，试好了顺便分享给我，😉

使用方法：
> iOS自动打包-sh的使用：下载压缩包后，解压，里面有后缀为.sh的文件和.plist的文件，将这两个文件放到  
.xcodeproj所在的文件夹下，然后ProjectName和SchemeName，打开terminal，运行这个.sh文件即可。


然后来说一下调试中遇到的问题：
1. 文件路径的问题
2. Scheme的问题，我不知道自己的sheme是什么？或者我的sheme明明就是这个但是提示我找不到
3. 脚本运行完，发现所有的文件都变成Modified状态，整个人顿时就不好了。。。

### 问题一：路径的问题
因为是shell脚本，偶会的本来就不多，周五那天智商爆表，居然啃懂了其中几句，然后还知道去找度娘，所以被我改成功了，
这个脚本路径被设置为.xcodeproj所在的文件夹，  
打包的.xcarchive文件放在这个文件夹下的.build文件夹下，  
导出的ipa文件在桌面

### 问题二：Scheme的问题
不知道自己的Scheme是什么的，可以去Product->Scheme->Edit Scheme下查看  
![Scheme 查看1](https://ooo.0o0.ooo/2017/02/20/58aa93bb6da09.png)

或者直接运行脚本会打印出来  
![Scheme 查看2](https://ooo.0o0.ooo/2017/02/20/58aa955cbc561.png)


写了自己的Scheme但是运行脚本后报这个错的  
![No Schemes img](https://ooo.0o0.ooo/2017/02/20/58aa9312cd66a.png)

要注意一下Edit Scheme界面的shared要勾选  
![Fixed No Schemes](https://ooo.0o0.ooo/2017/02/20/58aa965241867.png)


### 脚本运行完，所有文件变为Modified状态
使用git diff查看了之后，发现是filemode的变化
> 文件chmod后其文件某些位发生了变化，如果严格比较原文件和chmod后的文件，两者是不一样的，但是源代码通常只关心文本内容，因此chmod产生的变化应该忽略

上面的是找度娘之后，找到的博客里介绍的，请原谅，忘记当时怎么搜的了，所有找不到出处，文字是当时截图。。。。。
输入 `git config core.filemode false`，之后就好了

## 代码分析
1. 变量声明
![Implement Var](https://ooo.0o0.ooo/2017/02/20/58aa99c4776a9.png)
project_path 是获取此脚本文件所在的目录，百度搜出来的[Linux-获取当前正在执行脚本的绝对路径](http://www.cnblogs.com/JohnABC/p/5855535.html)  
project_name 是.xcodeproj前面的文字  
scheme_name 是前面说的那个  
build_path 是当前路径下build文件夹  
exportOptionsPlistPath 是plist文件的路径  
exportFilePath 是导出.ipa包的路径

2. 打印scheme、清理工程、编译工程、打包
![Run Code](https://ooo.0o0.ooo/2017/02/20/58aa9b9f7b738.png)
打印scheme，如果不知道怎么获取scheme_name，可以先填上上面的project_name，注释掉下面所有的代码，运行，就会打印出来当前project的scheme  
清理工程，编译之前先clean一下。。。。  
编译工程，编译并生成.xcarchive文件，放在build_path下，名字是project_name.xcarchive，这一步最为耗时  
打包，将生成的.xcarchive文件导出.ipa包到桌面  

> 这里面每一步都可以单独执行，例如想获取shceme就注释掉其他几段代码；想测试编译通过没，就只保留清理、编译这段；编译成功，导出失败，就只保留导出这一段，修改测试

3. 判断导出是否成功
![Is Export Success](https://ooo.0o0.ooo/2017/02/20/58aa9d3e2ac48.png)
判断桌面是否有scheme_name.ipa文件，有的话，就视为打包成功，打开这个文件夹；

## 后记
a. 如果是workspace工程，可按照这里这个链接，修改清理工程、编译工程、打包这几步  

[xcodebuild-developer.apple](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/xcodebuild.1.html/man.1.html)  

这就是上面链接里的内容，xcodebuild的使用都在这里
![xcodebuild](https://ooo.0o0.ooo/2017/02/20/58aa9eb88e322.png)

b. .plist文件里的内容可参照这个链接，需要翻墙
[xcodebuild's new exportOptionsPlist flag](http://www.matrixprojects.net/p/xcodebuild-export-options-plist/#tldr)

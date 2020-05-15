

## 1.新版WebStorm Tab键的缩进问题

以前WebStorm设置好code style中的Tab size 和indent后就会默认是这个设置的值,但是新版的WebStorm出现了一个怪现象,无论如何设置Tab size 和indent, tab的缩进永远都是2个字符,让习惯了4个字符缩进的开发者十分别扭,很不适应,

在这里插入图片描述

即使取消掉indents detection检测,也无济于事,让人感觉很别扭

在这里插入图片描述

把project下运行时产生的缓存.idea目录删除了还是不可以, 而让人更奇怪的事，所有新建的项目以及部分旧的项目所有tabsize都是settings设置的4个长度, 也无济于事。

这时发现有问题的项目目录下有一个.editorconfig文件, 再仔细看看偏好设置中有个EditorConfig选项
取消勾选,或者修改.editorconfig的indent_size值即可

在这里插入图片描述

在这里插入图片描述

indent_size = 2 改 indent_size = 4 以后代码缩进全变回4个空格缩进长度了。。。

在这里插入图片描述
新版WebStorm开始对editorconfig支持
项目是我用老版webstrom时生成的，这个项目带了一个.editorconfig文件，然而之前我一直使用老版WebStorm，所以这个配置文件对我的设置没有任何影响。而当我升级到新版WebStorm的了以后, WebStorm开始支持editorconfig，于是项目下的.editorconfig生效并且优先权利大过我的settings配置，于是就出现了明明在settings里配置tabsize=4但实际还是以tabsize=2的大小格式化代码缩进。

## 2. Ctrl+Alt+L格式化JS代码或格式化Vue代码后，单引号变成双引号，以及添加分号设置问题
解决方法：

编辑器左上角开始：File -> Settings -> Editor -> Code Style -> Javascript

1、在 Punctuation （标点符号）tab下

2、Don't use（不使用） semicolon（分号） to terminate statement in new code

3、Use single quotes（单引号） in new code

## 3. tab空格用2个space 
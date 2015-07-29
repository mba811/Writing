# Pandoc入门

熟悉Markdown的朋友，不由地奇怪，为什么它不支持表格、数学公式以及很多你想要的功能。这一切，与Markdown自身的设计哲学有关系，提供尽可能少的，用于创作的功能，将对它的扩展，交给各路开源爱好者。于是，我们可以看到[github flavored markdown](http://github.github.com/github-flavored-markdown/)、[multimarkdow](http://fletcherpenney.net/multimarkdown/)、[maruku](https://github.com/nex3/maruku)等各路扩展。

然而，这些扩展某种意义上，并不是针对写作的。开源界传说中的高帅富社区，[haskell](http://www.haskell.org/)社区的作品[Pandoc](http://johnmacfarlane.net/pandoc/)给了我们更好的选择，在多个方面完败MMD，参考：[Pandoc vs Multimarkdown](https://github.com/jgm/pandoc/wiki/Pandoc-vs-Multimarkdown)。

[haskell](http://www.haskell.org/) 是什么？一种编程语言。每位资深的开发者学习它，都会有些不知所措，因为，它的编程思路不同于通常意义上类似于C#、Python、Java这些流行的编程语言。它来自一群高智商的开发者的贡献，据说人人有PHD学位，是[函数式编程语言](http://zh.wikipedia.org/wiki/%E5%87%BD%E6%95%B8%E5%BC%8F%E7%B7%A8%E7%A8%8B)的典范。当然，[Pandoc](http://johnmacfarlane.net/pandoc/)作者[John MacFarlane](http://johnmacfarlane.net/)也不例外，他是一位来自美国加州大学伯克利分校的哲学教授，研究的是认识论这类高深议题:D

文本格式转换，每位开发过这方面程序的技术青年，都知道它有多么痛苦；开发出的程序有多么丑陋。甚至可以说，这是个无底黑洞。事实证明，[haskell](http://www.haskell.org/)的确是干这脏活、累活的最恰当选择。[Pandoc](http://johnmacfarlane.net/pandoc/)也的确成功了，并已成功在短期内构建一个完整的生态链，于是，有了我们这篇小文。

## 1. Pandoc安装

## 1.1 最简单的安装办法(推荐办法)

[下载软件](http://code.google.com/p/pandoc/downloads/list)，找到相应操作系统对应的文件，然后按照默认点击即可。如果你不需要学习Haskell，请使用此种办法，好处多多。

## 1.2 更技术的安装办法（学习Haskell者使用）

一个通用的安装方式是,安装Haskell平台.然后再通过Haskell平台安装Pandoc。步骤如下：

### 1.2.1 安装Haskell平台

如果之前，你通过其它方式，安装过Haskell的运行平台，需要卸载，Mac下命令如下：
    
    /Library/Haskell/bin/uninstall-hs
    

然后，系统会找到你之前安装过的版本，如7.4.1、7.0.4，运行下列命令：
    
    sudo uninstall-hs only 7.0.4 --remove
    sudo uninstall-hs only 7.4.1 --remove
    

下载Haskell平台，下载地址是：[http://hackage.haskell.org/platform/](http://hackage.haskell.org/platform/)

选择你相应的操作系统平台即可。

然后下载完之后，一路按照默认安装即可。配置在.bashrc或者修改.zshrc文件。作者使用的是zshrc配置文件。因此，在~/.zshrc文件末尾增加一行：
    
    export PATH="$HOME/Library/Haskell/bin:$PATH"
    

这样可以直接通过shell来控制Haskell。打开终端，如iterm2，输入：
    
    cabal update
    

cabal是类似于Ruby下的rubygems一样的包管理器。cabal程序会链接到服务器[http://hackage.haskell.org/packages/archive/](http://hackage.haskell.org/packages/archive/)上下载相应的包信息。下载时间会较长，请耐心等候，并检查本机与HaskellDB服务器的连接速度。

### 1.2.2 安装pandoc

运行：
    
    cabal install pandoc
    

编译安装时间较漫长，你可以去打个酱油。在安装过程中，我们会发现pandoc调用与使用的一些Haskell扩展包，如：

  * 引文格式：[citeproc-hs](http://hackage.haskell.org/packages/archive/citeproc-hs/)
  * 数学公式：[TextMath](http://hackage.haskell.org/packages/archive/texmath/)

### 1.2.3 ubuntu下的安装

yanping总结了[ubuntu下pandoc的安装步骤](http://chen.yanping.me/cn/blog/2012/03/13/pandoc/)，如下：
    
    sudo apt-get autoremove pandoc      #删掉之前的pandoc安装
    sudo apt-get install cabal-install  #安装Haskell包管理器
    cabal update                        #获取Haskell包信息
    cabal install pandoc                #通过cabal安装pandoc
    

然后再把~/.cabal加到路径中去，在.bashrc里加上一句
    
    export PATH=/home/ypchen/.cabal/bin:$PATH
    

### 1.2.4 安装之后的校验

打开Shell，输入：
    
    pandoc --version
    

如果安装无误，应能看到pandoc相关的作者、版权、序列许可等信息。当然，更令人震撼的是，能看到Pandoc已经支持的语言列表。使用
    
    pandoc --help
    

可以看到pandoc主要支持的输入与输出格式：
    
    Input formats:  native, json, markdown, markdown+lhs, rst, rst+lhs, docbook,
                    textile, html, latex, latex+lhs
    
    Output formats: native, json, html, html5, html+lhs, html5+lhs, s5, slidy,
                    slideous, dzslides, docbook, opendocument, latex, latex+lhs,
                    beamer, beamer+lhs, context, texinfo, man, markdown,
                    markdown+lhs, plain, rst, rst+lhs, mediawiki, textile, rtf, org,
                    asciidoc, odt, docx, epub
    

上述语言的互转关系，可以看看作者制作的[这份壮观的图](http://johnmacfarlane.net/pandoc/diagram.png)，从中可以看出Pandoc的强大。这年头，哲学系教授会写程序，还是Haskell，伤不起啊:D

## 2.Pandoc 编辑器

### 2.1 Pandoc 编辑器

当然，你可以一切操作都在Shell中完成，不过，为了提高效率，总是可以借助于Pandoc社区既有的积累。如：

  * 作者用的是开源的[TextMate](https://github.com/textmate/textmate)，搭配[Pandoc TextMate bundle](https://github.com/dsanson/Pandoc.tmbundle)
  * 多数Pandoc用户使用的是[Pandoc Vim](https://github.com/vim-pandoc/vim-pandoc)
  * [Mou](http://mouapp.com/)已支持引用功能，但尚不支持表格。
  * 作者在努力说服其他编辑器作者默认支持Pandoc，请大家静候。

更多编辑器、插件请参考：[Pandoc-Extras](https://github.com/jgm/pandoc/wiki/Pandoc-Extras)

### 2.2 Pandoc TextMate Bundle

以下以Mac下主要编辑器TextMate为例，说明如何安装。请特别注意，该Textmate Bundle作者并没有全部完成所有工作。仅供参考。TextMate2.0之后的bundle位置已经变动了，现在默认位置是：/Applications/TextMate.app/Contents/SharedSupport/Bundles/
    
    cd /Applications/TextMate.app/Contents/SharedSupport/Bundles/
    cd ~/Library/Application\ Support/TextMate/Managed/Bundles
    git clone git://github.com/dsanson/Pandoc.tmbundle.git "Pandoc.tmbundle"
    

安装完之后，效果如下图所示，

![](http://7q5cfr.com1.z0.glb.clouddn.com/pandoc.png)

## 2.Pandoc Markdown语法特色

详情参见：[Pandoc](http://johnmacfarlane.net/pandoc/)

### 2.1 Pandoc写表格

如下所示：
    
    -----------------------------------------------------------------------------------------------------------------
    测量指标                    低程度中心性             低亲近中心性                      低居间中心性     
    --------------------   -------------------   -----------------------------  -------------------------------------
    高程度中心性                                        “自我”所嵌入的聚类                “自我”的联系人是冗余的，
                                                       远离网络中其他节点                整个世界绕他而行  
    
    
    高亲近中心性               是联系重要他人或                                       在事件中，自我位于一个相互
                             活跃人物的关键人物                                     联系密切、活跃的聚类中，与
                                                                                 很多节点都很接近；其他节点也是如此                                                         
    ------------------------------------------------------------------------------------------------------------------
    : 表3-1 不同中心性测量指标之间的关系[^16]
    

### 2.2 Pandoc写参考文献

使用[Pandoc Markdown]格式的写法是在文中使用：
    
    [^1]、[^2]
    

然后在文末使用：
    
    [^1]: 
    [^2]:
    

如下例所示：

正文中是：

> 当一个人在看社会网络时，首先提出的问题之一就是：在社会网络中谁是更重要的人？或者谁拥有更大的权力？在介绍部分（参考第一章介绍的非正式网络的力量），我们发现社会网络是由一个强大的（但大部分是无形的）的“秘书”统治[^7]，类似的观点亦可从一系列社会网络测量指标（参见 _中心性_ ）中获得。

然后在文章末尾写上：
    
    [^7]:译者注：作者此处秘书指的是第一章中“非正式网络的力量”一节介绍的ACME咨询公司的例子中所提到的秘书处，其中代表人物即ACME咨询公司中的Frida，虽然在正式网络中，看似不是核心人物，但是Frida偏偏是最受整个公司信赖的网络中心人物。
    

## 3. Pandoc文档转换

### 3.1 Markdown转html、word与PDF

如下例所示：

#### 转html格式
    
    pandoc 01-chapter2.markdown -o chapter2.html -c Github.css
    

#### 转word格式
    
    pandoc 01-chapter2.markdown -o chapter2.docx -c Github.css
    

### 3.2 Markdown转PDF

#### 安装LaTex

待补。

#### 转pdf格式
    
    pandoc  01-chapter2.markdown -o 01-chapter2.pdf --latex-engine=xelatex -V mainfont=heiti\ 
    

## 4 Pandoc生成epub、html与在线电子书

### 4.1 安装nodejs与grunt、bower

下载nodejs安装包，网址为：[http://nodejs.org/](http://nodejs.org/)

下载之后，安装默认安装。安装完之后，确认安装是否成功。
    
    npm -v
    

如果如我一样，使用的是zsh。在.zshrc文件中添加nodejs的路径。
    
    mate ~/.zshrc
    

在文件末尾添加：
    
    export PATH=/usr/local/share/npm/bin:$PATH
    

然后，安装grunt。
    
    sudo npm install -g grunt
    

安装bower等。
    
    sudo npm install -g bower
    

### 4.2 使用wbb生成epub、html与在线电子书示范

以著名的progit为例。
    
    git clone https://github.com/PascalPrecht/wbb test2012
    cd test2012
    

然后检查clone下来的项目，是否已经包含了grunt.js等文件。各项生成代码写在那里面。

书稿则写在/test2012/src/chapters 这样的目录下面。

写作书稿时，完全按照正常git流程管理。书稿预览效果，是在test2012的根目录下面，运行：
    
    grunt wbb:html
    

自定义各项参数，请修改grunt.js文件。

生成的epub、html与moblie在线电子书，在builds目录下面。同时可以设置，与leanpub在线出版网站搭配。

## 5 Pandoc的科技写作

### 5.1 Pandoc与bibdesk、APA文献格式的配合

请参考：[Pandoc](http://johnmacfarlane.net/pandoc/)之[citations](http://johnmacfarlane.net/pandoc/README.html#citations)

### 5.2 RStudio默认使用Pandoc

请参考：[Customizing Markdown Rendering](http://www.rstudio.com/ide/docs/authoring/markdown_custom_rendering)

还有个pander的R包。
    
      > library(devtools)  
      > install_github('pander', 'daroczig')  
    

## 小结

我目前的观点是，Pandoc Markdown不是万能的，表格、复杂公式、多国语言、上下标、交叉引用、图表对齐较多的场合，它并不适合。但是需要互动、实时展现、更快输出的场合，Pandoc Markdown等值得大力推荐。未来互联网会逼使写作趋简。需要更快发表、互动输出与交流的场合，也会越来越多。比如课堂作业、企业内部交流、个人博客。用它节省的时间是写作时比较关键的"创作时间"而非"排版时间"。

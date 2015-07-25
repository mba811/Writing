# 制作电子书的工具集

作者在制作[《Ruby on Rails Tutorial》中文版](http://about.ac/rails-tutorial-2nd-cn)电子书的过程中积累了一些经验，记录如下。

这本书用了 5 个多月的时间才翻译完，其中大概三分之一的时间都用在编写生成电子书所需的工具上了。期间上了两个月晚班，有很多空闲时间，所以我在网上到处搜索可用的工具，用来生成电子书。我的要求很简单：

  * 使用 Markdown 撰写文本；
  * 能够生成 PDF，Epub 和 Mobi 格式的电子书；
  * 可以自动生成目录；

就这三个简单的要求，我还是没能找到称心的工具。首先被我否定的是 [Sphinx](http://sphinx.pocoo.org/) 和[Pandoc](http://johnmacfarlane.net/pandoc/)。这两个工具用的人比较多，功能也比较完善，但 Sphinx 只支持 reStructuredText 格式，而且是用 Python 开发的，我不太熟悉这种编程语言，而且我觉得这个工具太复杂。而 Pandoc 很强大，似乎用起来也不复杂，但是还是被否了，原因很简单，Haskell？这是哪个星球的语言？

否了这两个之后，我又接着寻找，这一次集中经历寻找使用 Ruby 开发的工具，结果找到两个，似乎用的人比较多：[kitabu](https://github.com/fnando/kitabu) 和 [bookshop](https://github.com/blueheadpublishing/bookshop)。kitabu 还不错，基本的要求都能满足我的要求，但是看了代码后我才发现自动生成目录大代码对中文完全无用，而且这个 gem 功能又太简单了。bookshop 完全就不支持 Markdown。

又是一阵搜索，最后找到了 [easybook](http://easybook-project.org/)，完全能满足我的要求，只有一点，是用 PHP 开发的。本来我想 PHP 就 PHP，反正代码也能看懂，想定制的话也可以，但是当我使用了十几分钟后就决定放弃了，PHP 毕竟还不能胜任 CLI。

找了很多工具，相互比较，也觉得累了，干脆就直接把生成电子书的任务交给 [Leanpub](http://leanpub.com/)吧。动了这个念头之后，我花了点时间熟悉了 Leanpub 的工作流程，也看了生成的文件，但觉得可定制的内容太少了，我是个外观控，如果不能自己定制生成的文件格式，我宁愿不用。所以最后我还是放弃使用 Leanpub。但是，Leanpub 对电子书做的一些处理被我借鉴了，具体内容参见下面的说明。

我决定不在继续找了，所以就总结了一下这几个工具各自的优缺点。最终我决定以 easybook 为蓝本，参照其他几个工具可取的部分，自己编写一个程序，名字嘛就叫做 bookstrap。

其实 bookstrap 只是个 wrapper，使用 Ruby 编写，是个命令行工具，先把 Markdown 转换成 HTML，然后生成 PDF、Epub 和 Mobi 格式电子书。详情如下。

更新（2013-6-3）：bookstrap 更名为 burr，并开源：[https://github.com/AndorChen/burr](https://github.com/AndorChen/burr)

## 文本

文本肯定要用 Markdown，这是我最熟悉的文本书写语言。决定使用 Markdown 之后就要考虑使用那个工具将其生成 HTML 了，可选的工具太多了，我第一个想到的是[redcarpet](https://github.com/vmg/redcarpet)，我之前使用 Jekyll 开发的网站用的就是这个 gem，而且它还对基本的 Markdown 语法做了一些扩展。不过用起来还是觉得句法太少了，而且这个 gem 是用 C 语言编写的，很难扩展，所以我放弃了。

接下来又找到了 [multimarkdown](http://fletcherpenney.net/multimarkdown/)，这是 Markdown 基本语法的一个扩展集，增加了很多实用的句法。但是似乎还是无法满足我的要求，而且相应的转换程序 [peg-multimarkdown](https://github.com/fletcher/peg-multimarkdown)也是用 C 语言开发的，不易扩展。

最后，我想到了 Leanpub。Leanpub 使用的是 [kramdown](https://github.com/gettalong/kramdown)，所以我决定试一下。kramdown 是使用 Ruby 开发的，很容易扩展语法，试用了一段时间之后，我决定就使用它了。我还开发了 [Leanpub 对 kramdown 语法的扩展](https://leanpub.com/help/manual)，这样 Markdown 转换工具就有了。

## 生成 PDF

PDF 制作工具我第一个想到的就是 [LATEX](http://www.latex-project.org/)，但是它有自己的一套书写语言，而且学习曲线太高。LATEX 其实更适合学术文章，也适合组织或公司使用，个人折腾起来代价太大了。

随后又看了几个生成 PDF 的工具，都不符合我的要求。最后还是选择了 kitabu 等使用的工具：[PrinceXML](http://www.princexml.com/)。PrinceXML 是闭源软件，Java 开发，但是可以免费试用，生成的 PDF 首页会加上一个 LOGO。PrinceXML 真正吸引我的是 HTML+CSS 的组合，学过前端开发的人看到这个组合太亲切了。

决定使用 PrinceXML 后，我以 [boom](http://alistapart.com/article/boom) 的样式为基础，编写了所需的样式表，加之上一步生成的 HTML，就可以生成一个很漂亮的 PDF 了。

### 注意事项

  * 在 PDF 中一定要嵌入中文字体；为了减少文件大小，最好嵌入 TrueType 格式的字体；
  * 不要使用 Mac OS X 自带的预览程序优化 PDF 文件；

## 生成 ePub

ePub 格式的生成我没有选择 GUI 程序，或者其他命令行工具，而是选择了 [Eeepub](https://github.com/jugyo/eeepub) 这个 gem，因为使用 EeePub 我可以自由控制很多东西。

在生成 ePub 格式时我产生了错觉，仿佛又进入 IE、Chrome、Firefox、Safari 这些浏览器兼容问题的深渊。市面上 ePub 阅读器太多了，而它们对规范的支持很有限，甚或各自有不同的实现方式及扩展，你就要考虑如何最大限度的兼容这些阅读器。其实这里我们可以采用的措施和前端开发一样：优雅降级。对主流阅读器（多看，iBooks）一定要保证所有功能都正确表现，而在其他阅读器中，只要内容能正常显示即可。

生成 ePub 文件后，一定要使用 [EpubCheck](http://code.google.com/p/epubcheck/) 检查文件是否有错误。如果有错误一定要按照提示修正，否则可能有些阅读器无法正常显示，或者会导致下一步无法生成 Mobi 格式。

## 生成 Mobi

Mobi 格式其实和 ePub 差不多，只是 Kindle 还没提供对 ePub 格式的原生支持，所以必须要为 Kindle 用户考虑，生成 Mobi 格式。生成 Mobi 文件的工具是由 Amazon 开发的[kindlegen](http://www.amazon.com/gp/feature.html?ie=UTF8&docId=1000765211)。这是一个命令行工具，可以直接使用上一步生成的 ePub 文件作为输入。在转换过程中，可能有很多错误提示，一定要及时修正这些错误，否则可能导致 Mobi 文件无法打开。

生成 Mobi 文件后，还可以在 [Kindle Previewer](http://www.amazon.com/gp/feature.html?ie=UTF8&docId=1000765261) 中预览，看一下在各种 Kindle设备中的表现如何。
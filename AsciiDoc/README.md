# AsciiDoc

Markdown 是现在最流行的轻量级标记语言，Github、Stack Overflow、Smashing Magazine 等网站都使用 Markdown。Markdown 格式简单，纯文本具有很好的可读性。但它对复杂格式（例如表格、图片标题）支持不够，也没有预留扩展语法，不同网站各自扩展产生了很多方言。

社区有不少人意识到问题，其中就有 [Jeff Atwood](http://blog.codinghorror.com/about-me/) 牵头，联合 Stack Overflow、Github、reddit 的一些员工发起了 [Standard Markdown](http://blog.codinghorror.com/standard-flavored-markdown/) 项目，希望将 Markdown 标准化。 没想到项目起步遇到的最大阻力是 Markdown 的创建者 John Gruber，John 很不满意 Jeff 在未充分沟通的情况下宣称自己的项目是“Standard”，经过一番争吵，Jeff 把项目改名为 [Common Markdown](http://blog.codinghorror.com/standard-markdown-is-now-common-markdown/)，John 也没有加入项目。

这场争吵让我不太看好 Markdown 的前景，没有创建者的支持，Common Markdown 可能又成为一个方言，Markdown 的混乱状况还要继续下去。

![](http://7q5cfr.com1.z0.glb.clouddn.com/standards.png)

（图片出处：[http://xkcd.com/927/](http://xkcd.com/927/) ）

最近我在摸索制作电子书制作方案，需要一种更严谨、功能更丰富的标记语言，便开始寻找比 Markdown 更好的选择。 现行的轻量级标记语言并不少，[Wikipedia 上列出了 17 种](http://en.wikipedia.org/wiki/Lightweight_markup_language) （有三种是 Markdown 方言）。经过一些研究之后，我比较看好 AsciiDoc，它的设计初衷就是为了解决写书规模的问题，并且是 O'Reilly 的在线出版平台 [Atlas](http://chimera.labs.oreilly.com/) 的推荐语言。经过一番学习，我觉得 Asciidoc 确实很适合电子书制作。

## 第一印象

AsciiDoc 基本语法跟 Markdown 类似，例如以 = 号开头作为标题、以 * 号开头作为列表项、以空行分割段落等，下面是一段 AsciiDoc 文本：
    
    = Hello, AsciiDoc!
    Doc Writer <doc@example.com>
    
    An introduction to http://asciidoc.org[AsciiDoc].
    
    == First Section
    
    * item 1
    * item 2
    
    [source,ruby]
    puts "Hello, World!"
    

如果学会了 Markdown，再学 AsciiDoc 应该不难。详细的语法说明可以看这份文档：[http://asciidoctor.org/docs/asciidoc-syntax-quick-reference/](http://asciidoctor.org/docs/asciidoc-syntax-quick-reference/) 。

## 内置更多特性

AsciiDoc 相比 Markdown 支持更多的格式，包括而不限于：

  * 文档属性，设置作者、版本信息等。
  * 语法高亮。
  * 表格。
  * Include 功能，将大文档拆分几个文件。
  * 自定义块语法，可扩展性。

Markdown 通过自行扩展语法或者使用 HTML 可以实现这些格式，但前者造成文档不通用的问题，后者则直接把展示结构硬编码到了文档中，将来修改会很麻烦。

当然， 更多的特性带来更多的学习成本，对于博客等简单文档，这些特性并不是必须的，但对于电子书等大型文档，标准内提供丰富的特性就很有必要，否则就需要自己实现不成熟、不兼容的扩展。

## AsciiDoctor

[AsciiDoctor](http://asciidoctor.org/) 是 AsciiDoc 的 Ruby 实现，也是一个工具链，实现了 AsciiDoc 对 HTML5/DocBook/EPUB/PDF/MOBI 的转换（有的需要借助第三方工具例如 Kindlegen）。相比原版 Python 实现，AsciiDoctor 作了以下改进：

  * 添加模板引擎，可以用 Ruby 的模板语言（例如 erb）自定义输出格式。
  * 处理过程分成解析和生成两步，在解析之后文档转化为 Ruby 对象，可供编程处理。
  * 性能和安全性提升。
  * 通过 JRuby 提供 Java 版本；通过转编译提供 JavaScript 版本。

AsciiDoctor 是一个很宏伟的项目，还有很多子项目在开发中，例如我比较关注的 Asciidoctor PDF 和 Asciidoctor EPUB3，这两个项目用于去掉现有工具链中对 DocBook 的依赖，由原文档直译目标文档。工具链层次的减少可以增加定制便利性，也更方便用户安装。

因为有 AsciiDoctor 这样设计精良的实现，我才决定把精力放到 AsciiDoc 上。

## 编辑器

轻量级标记语言不依赖编辑器，但好的编辑器会让写作过程更愉快。现在 AsciiDoc 的专用编辑器还不多，我推荐的编辑器是 [Atom](http://atom.io/)，并且加装以下插件：

  * zen，全屏居中的写作布局。
  * language-asciidoc，语法高亮。
  * asciidoc-preview，实时预览。

打开预览窗口的效果图如下：

![](http://7q5cfr.com1.z0.glb.clouddn.com/bjq-a.png)

配色之类可以根据个人喜好配置，Atom 是一个可塑性很强的编辑器。

## 为什么不用……

我可以估计到有人会向我发问，为什么不用某某标记语言？这里简要回答一下：

  * LaTex：语法太复杂，导致写作没有乐趣可言。LaTex 适合用于数学公式这类专门场合。
  * Org mode：基本上是 Emacs 用户在用。
  * reStructuredText：基本上是 Python 开发者在用。
  * 其他类似。

综合功能、易用性、社区氛围、配套工具来看，目前我觉得 AsciiDoc 最好。

## 总结

AsciiDoc 是一个成熟的、为大型文档设计的标记语言。如果你在搭建复杂的文档环境，并且认为 Markdown 已经不能满足需求，那么可以试试 AsciiDoc。
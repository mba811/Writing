# 写作工具的取经之路

![](http://7q5cfr.com1.z0.glb.clouddn.com/xzgj.png)

写公众号写了一年，穿插着写了一本书，在工具的使用上，也渐渐有了些心得，不敢独专，跟大家分享一下。

最早我使用的是reST [1] ，使用sphinx [2] 来撰写文章。使用reST/sphinx最大的好处是格式简单，扩展性强，便于生成PDF和多种文档结构。

然而我的博客在用octopress等工具生成的 [3] ，使用的是markdown。做类似的事，却用两种格式，这让人十分抓狂。再三考虑后，我决定也使用markdown来写公众号。

使用markdown恼人的是格式太简单，有时候需要一些变化，便只好直接上html。不过简单有简单的好处，它让我更专注于内容，而非作为点缀的形式。

为了便于版本管理，我所有的稿子都放在一个git repo下，同步到github（private repo，嘻嘻）。这样做的好处是我不必担心丢稿子，同时github自动为markdown生成的展示页面让我可以copy & paste到公众号上。

最初公众号对github的格式的支持是良好的，但七八月间的某次调整，公众号突然对各种外部粘入的内容不友好了，格式丢得一塌糊涂，恼人得很。后来从知乎上，我了解到markdown here这个chrome神器，你只需把原始的markdown文稿粘入公众号的输入框，按下一个快捷键（记得是ctrl + alt + M），markdown here就会自动帮你转换成html，还保留所有格式。稍稍研究了一下markdown here，我才明白但凡公众号上发布的文章，格式都需要是inline的，就跟html email一样。markdown here自带一个基础样式表，然后在生成html的时候，把这个样式表inline到每一行html代码中。

了解了这一点后，便再也没人能阻挡我对公众号文章的「美」的追求。在不同的文章里，我不断尝试，一点点修改样式表，使其至少符合我的审美。

在此期间，我还用node-webkit + ace + marked做了个简单的osx下的编辑器，但因为没有比sublime text + markdown here更好用，我自己都没在用，所以便罢了。

随着「途客圈创业记」连载的不断深入，我发现自己需要一个好的PDF生成工具。最初我在LaTeX [4] 上艰难前行，生成了也还算过得去的PDF，后来发现了kitabu [5] + princexml [6] 的解决方案，便全面转换到这个方案，「途客圈创业记」便是用其生成的。

后来读者朋友先后给我推荐了gitbook [7] 和asciidoctor [8]。gitbook的能力自不必说，不少出版社都在使用，而且其生成的html可以用做交互式教学，很适合技术类的电子书。但gitbook目前只支持markdown格式，使得其扩展成为问题。在未来的版本中，gitbook有可能会加入asciidoc的支持。

相对于大家都很了解的markdown，知道asciidoc的人不多。这也是一个纯文本的写作语言，和markdown一样很好掌握，但格式丰富得多。想了解asciidoc的语法，可以google asciidoctor syntax。

我的新书「奇博士的管理课」便使用asciidoc撰写，之所以这么选择，是因为我喜欢尝鲜，就跟不断变换写作风格一样，我喜欢在未知的世界里探索。

asciidoc的世界里没有kitabu这样的工具，至少我没发现。所以我只好自己写一个，使用了这些工具：

  * ✓ asciidoctor，一个基于ruby的asciidoc → html的命令行工具。

  * ✓ 自己写的cssliner，用于生成公众号友好的网页，使用python的premailer lib

  * ✓ princexml，用于生成PDF。

  * ✓ makefile，把所有工具连接起来。

Makefile如下：
    
    ADOCTOR=asciidoctor
    PRINCE=prince
    CSSLINER=bin/cssliner
    CAT=cat
    BOOK=book.adoc
    SUFFIX=suffix.adoc
    OUT=./out
    ALL_CHAPTERS=$(shell find chapters -name "*.adoc" | grep -v index.adoc)
    ALL_HTML=$(ALL_CHAPTERS:%.adoc=$(OUT)/%.html)
    HTML_CSS=./templates/html/style.css
    WEIXIN_CSS=./templates/weixin/style.css
    HTML_FILE=$(OUT)/book.html
    WEIXIN_FILE=$(OUT)/weixin.html
    PDF_FILE=$(OUT)/book.pdf
    .PHONY: html weixin
    html:
    @$(ADOCTOR) -o $(HTML_FILE) -a stylesheet=$(HTML_CSS) $(BOOK)
    @$(PRINCE) $(HTML_FILE) -o $(PDF_FILE)
    @echo "Done!"
    $(ALL_HTML): $(OUT)/%.html: %.adoc
    @echo "Working on $<..."
    @$(CAT) $< $(SUFFIX) > $(OUT)/tmp.adoc
    @$(ADOCTOR) -o $@ -a stylesheet=$(WEIXIN_CSS) $(OUT)/tmp.adoc
    @rm $(OUT)/tmp.adoc
    @$(CSSLINER) $@ $@
    weixin: $(ALL_HTML)
    @echo "Done!"
    clean:
    @rm -rf $(OUT)/*

稍稍解释一下：

1) 我的所有文章都放在chapters目录下，`$(ALL_CHAPTERS)` 获取所有adoc文档。

2) 每个新的章节单独一个文档，为了公众号发表方便，生成独立的html。由于每篇文章末尾我都要加一段共同的内容，所以：
    
    @$(CAT) $< $(SUFFIX) > $(OUT)/tmp.adoc

用于生成一个临时的，包含 `$(SUFFIX)` 的文档，然后再用 `cssliner` 将css inline化。

其它的不解释。这样，对于「奇博士的管理课」，当我想生成PDF时：
    
    $ make html

想生成用于公众号的内容时：
    
    $ make weixin

就一切OK了。如果大家觉得这工具有用，也许我可以将其做成一个python命令行工具包，一条命令完成所有工作。

如果您觉得这篇文章不错，请点赞。多谢！

* * *

欢迎订阅公众号『程序人生』（搜索微信号 programmer_life）。每篇文章都力求原汁原味，早8点与您相会。

* * *

1. restructured text

2. 一个基于python的文档生成工具

3. 最早是octopress，后来使用wintersmith，最后又迁移到docpad上

4. 一个基于TeX的排版系统，没听过TeX？你还记得那个神一样的the art of computer progrmming的作者Donald Knuth，写书的时候他觉得排版工具不爽，便花了差不多十年时间发明了TeX

5. 一个ruby下的电子书生成工具

6. 最好的将html转换成PDF的工具，没有之一

7. 一个通过markdown生成印刷级质量的电子书的工具

8. 一个使用asciidoc生成精美html和docbook的工具
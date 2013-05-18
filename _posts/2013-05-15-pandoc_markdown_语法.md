---
layout: post
title: "pandoc's markdown"
category: technique
tags: [Pandoc, Markdown]
description: |
  Pandoc's Markdown 语法简体中文版翻译
---

% [Pandoc's Markdown 语法中文翻译][home]
% by John MacFarlane; Translated by [Tzeng Yuxio](http://tzengyuxio.me)

[home]: http://pages.tzengyuxio.me/pandoc/

前言
--------
这份文件是 [Pandoc][] 版本 Markdown 语法的中文翻译。Pandoc 本身是由 [John MacFarlane][] 所开发的文件转换工具，可以在 HTML, Markdown, PDF, TeX...等等格式之间进行转换。有许多喜欢纯文字编辑的人，利用 Pandoc 来进行论文的撰写或投影片製作。但除了转换的功能外，Pandoc 所定义的 Markdown 扩充语法也是这套工具的一大亮点，在 Pandoc 的官方使用说明文件中，光是其针对 Markdown 格式的扩充就佔了整整一半左右的篇幅。 

本文件翻译自 [Pandoc - Pandoc User’s Guide][userguide] 中的 "Pandoc's markdown" 一节。你可以看看[这份文件的原始档][source]、產生文件[所使用的 HTML 范本][template]，以及[转换时的命令参数][script]。

[John MacFarlane]: http://johnmacfarlane.net/
[pandoc]: http://johnmacfarlane.net/pandoc/
[userguide]: http://johnmacfarlane.net/pandoc/README.html#pandocs-markdown
[source]: https://raw.github.com/tzengyuxio/pages/gh-pages/pandoc/pandoc.markdown
[template]: https://github.com/tzengyuxio/pages/blob/gh-pages/pandoc/pm-template.html5
[script]: https://github.com/tzengyuxio/pages/blob/gh-pages/pandoc/convert.sh

以下翻译开始。

----

Pandoc's markdown
=================

与 John Gruber 的 原始 [markdown] 相比，Pandoc 版本的 markdown 在语法上有额外的扩充与些许的修正。这份文件解释了这些语法，并指出其与原始 markdown 的差异所在。除非特别提到，不然这些差异均可藉由使用 `markdown_strict` 而非 `markdown` 的格式来关闭。单独一项扩充也可透过 `+EXTENSION` 或 `-EXTENSION` 的方式来开啟或关闭。例如，`markdown_strict+footnotes` 表示加上脚註扩充的原始 markdown，而 `markdown-footnotes-pipe_tables` 则是拿掉了脚註与管线表格扩充的 pandoc markdown。

哲学
----------

Markdown 是针对易於书写与阅读的目标而设计的，特别是在易於阅读这点上尤為重要：

> 一份 Markdown 格式的文件应该要能以纯文字形式直接发表，并且一眼看过去不存在任何标记用的标籤或格式指令。
> <small>[John Gruber](http://daringfireball.net/projects/markdown/syntax#philosophy)</small>

这项原则同样也是 pandoc 在制订表格、脚註以及其他扩充的语法时，所依循的规范。

然而，pandoc 的目标与原始 markdown 的最初目标有著方向性的不同。在 markdown 原本的设计中，HTML 是其主要输出对象；然而 pandoc 则是针对多种输出格式而设计。因此，虽然 pandoc 同样也允许直接嵌入 HTML 标籤，但并不鼓励这样的作法，取而代之的是 pandoc 提供了许多非 HTML 的方式，来让使用者输入像是定义清单、表格、数学公式以及脚註等诸如此类的重要文件元素。


段落
----------

一个段落指的是一行以上的文字，跟在一行以上的空白行之后。换行字元会被当作是空白处理，因此你可以依自己喜好排列段落文字。如果你需要强制换行，在行尾放上两个以上的空白字元即可。

**Extension: `escaped_line_breaks`**

一个反斜线后跟著一个换行字元，同样也有强制换行的效果。

标题
-------

有两种不同形式的标题语法，Setext 以及 atx。

### Setext 风格标题 ###

Setext 风格的标题是由一行文字底下接著一行 `=` 符号（用於一阶标题）或 `-` 符号（用於二阶标题）所构成：

    A level-one header
    ==================

    A level-two header
    ------------------

标题的文字可以包含行内格式，例如强调（见下方 [行内格式] 一节）。


### Atx 风格标题 ###

Atx 风格的标题是由一到六个 `#` 符号以及一行文字所组成，你可以在文字后面加上任意数量的 `#` 符号。由行首起算的 `#` 符号数量决定了标题的阶层：

    ## A level-two header

    ### A level-three header ###

如同 setext 风格标题，这裡的标题文字同样可包含行内格式：

    # A level-one header with a [link](/url) and *emphasis*

**Extension: `blank_before_header`**

原始 markdown 语法在标题之前并不需要预留空白行。Pandoc 则需要（除非标题位於文件最开始的地方）。这是因為以 `#` 符号开头的情况在一般文字段落中相当常见，这会导致非预期的标题。例如下面的例子：

    I like several of their flavors of ice cream:
    #22, for example, and #5.


### HTML, LaTeX 与 ConTeXt 的标题识别符 ###

**Extension: `header_attributes`**

在标题文字所在行的行尾，可以使用以下语法為标题加上属性：

    {#identifier .class .class key=value key=value}

虽然这个语法也包含加入类别 (class) 以及键／值形式的属性 (attribute)，但目前只有识别符 (identifier/ID) 在输出时有实际作用（且只在部分格式的输出，包括：HTML, LaTeX, ConTeXt, Textile, AsciiDoc）。举例来说，下面是将标题加上 `foo` 识别符的几种方法：

    # My header {#foo}

    ## My header ##    {#foo}

    My other header   {#foo}
    ---------------

（此语法与 [PHP Markdown Extra] 相容。）

具有 `unnumbered` 类别的标题将不会被编号，即使 `--number-sections` 的选项是开啟的。单一连字符号 (`-`) 等同於 `.unnumbered`，且更适用於非英文文件中。因此，

    # My header {-}

与下面这行是等价的

    # My header {.unnumbered}

**Extension: `auto_identifiers`**

没有明确指定 ID（识别符）的标题将会依据其标题文字，自动指派一个独一无二的 ID。由标题文字推导 ID 的规则如下：

  - 移除所有格式，连结等。
  - 移除所有标点符号，除了底线、连字符号与句号。
  - 以连字符号取代所有空白与换行字元。
  - 将所有英文字母转為小写。
  - 移除第一个字元前的所有内容（ID 不能以数字或标点符号开头）。
  - 如果剩下為空字串，则使用 `section` 作為 ID。

以下是一些范例，

  Header                            Identifier
  -------------------------------   ----------------------------
  Header identifiers in HTML        `header-identifiers-in-html`
  *Dogs*?--in *my* house?           `dogs--in-my-house`
  [HTML], [S5], or [RTF]?           `html-s5-or-rtf`
  3. Applications                   `applications`
  33                                `section`

在大多数情况下，这些规则应该让人能够直接从标题文字推导出 ID。唯一的例外是当有多个标题具有同样文字的情况；在这情况下，第一个标题的 ID 仍旧是透过以上规则推导而得；第二个则是在同样 ID 后加上 `-1`；第三个加上 `-2`；以此类推。

在开啟 `--toc|--table-of-contents` 的选项时，这些 ID 是用来產生目录 (Table of Contents) 所需的页面连结。此外，这些 ID 也提供了一个简便的方式来输入跳到指定章节的连结。一个以 ID 產生的连结，其使用的语法看起来就像下面的例子：

    See the section on
    [header identifiers](#header-identifiers-in-html-latex-and-context).

然而要注意的一点是，只有在以 HTML、LaTeX 与 ConTeXt 格式输出时，才能以这种方式產生对应的章节连结。

如果指定了 `--section-divs` 选项，则每一个小节都会以 `div` 标籤包住（或是 `section` 标籤，如果有指定 `--html5` 选项的话），并且 ID 会被附加在用来包住小节的 `<div>`（或是 `<section>`）标籤，而非附加在标题上。这使得整个小节都可以透过 javascript 来操作，或是採用不同的 CSS 设定。

**Extension: `implicit_header_references`**

Pandoc 假设每个标题都定义了其参考连结，因此，相较於以下的连结语法

    [header identifiers](#header-identifiers-in-html)

你也可以单纯只写

    [header identifiers]

或

    [header identifiers][]

或

    [the section on header identifiers][header identifiers]

如果有多个标题具有同样文字，对应的参考只会连结到第一个符合的标题，这时若要连结到其他符合的标题，就必须以先前提到的方式，明确指定连结到该标题的 ID。

与其他一般参考连结不同的是，这些参考连结是大小写有别的。

注意：如果你有明确定义了任何一个标题的标示符，那麼选项 `implicit_header_references` 就没有作用。

区块引言
----------------

Markdown 使用 email 的习惯来建立引言区块。一个引言区块可以由一或多个段落或其他的区块元素（如清单或标题）组成，并且其行首均是由一个 `>` 符号加上一个空白作為开头。（`>` 符号不一定要位在该行最左边，但也不能缩进超过三个空白）。

    > This is a block quote. This
    > paragraph has two lines.
    >
    > 1. This is a list inside a block quote.
    > 2. Second item.

有一个「偷懒」的形式：你只需要在引言区块的第一行行首输入 `>` 即可，后面的行首可以省略符号：

    > This is a block quote. This
    paragraph has two lines.

    > 1. This is a list inside a block quote.
    2. Second item.

由於区块引言可包含其他区块元素，而区块引言本身也是区块元素，所以，引言是可以嵌套入其他引言的。

    > This is a block quote.
    >
    > > A block quote within a block quote.

**Extension: `blank_before_blockquote`**

原始 markdown 语法在区块引言之前并不需要预留空白行。Pandoc 则需要（除非区块引言位於文件最开始的地方）。这是因為以 `>` 符号开头的情况在一般文字段落中相当常见（也许由於断行所致），这会导致非预期的格式。因此，除非是指定為 `markdown_strict` 格式，不然以下的语法在 pandoc 中将不会產生出嵌套区块引言：

    > This is a block quote.
    >> Nested.


字面（代码）区块
----------------------

### 缩进代码区块 ###

一段以四个空白（或一个 tab）缩进的文字区块会被视為字面区块 (Verbatim Block)：换句话说，特殊字元并不会转换為任何格式，单纯只以字面形式呈现，而所有的空白与换行也都会被保留。例如，

        if (a > 3) {
          moveShip(5 * gravity, DOWN);
        }

位於行首的缩排（四个空白或一个 tab）并不会被视為字面区块的一部分，因此在输出时会被移除掉。

注意：在字面文字之间的空白行并不需要也以四个空白字元做开头。


### 围栏代码区块 ###

**Extension: `fenced_code_blocks`**

除了标準的缩进代码区块外，Pandoc 也支援了**围栏** (*fenced*) 代码区块的语法。这区块需以包含三个以上波浪线 (`~`) 或反引号 (`` ` ``) 的一行作為开始，并以同样符号且至少同样长度的一行作為结束。所有介於开始与结束之间的文字行都会视為代码。不需要额外的缩进：

    ~~~~~~~
    if (a > 3) {
      moveShip(5 * gravity, DOWN);
    }
    ~~~~~~~

如同一般的代码区块，围栏代码区块与其前后的文字之间必须以空白行作间隔。

如果代码本身也包含了一整行的波浪线或反引号，那麼只要在区块首尾处使用更长的波浪线或反引号即可：

    ~~~~~~~~~~~~~~~~
    ~~~~~~~~~~
    code including tildes
    ~~~~~~~~~~
    ~~~~~~~~~~~~~~~~

你也可以选择性地使用以下语法附加属性到代码区块上：

    ~~~~ {#mycode .haskell .numberLines startFrom="100"}
    qsort []     = []
    qsort (x:xs) = qsort (filter (< x) xs) ++ [x] ++
                   qsort (filter (>= x) xs)
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

这裡的 `mycode` 為 ID，`haskell` 与 `numberLines` 是类别，而 `startsFrom` 则是值為 `100` 的属性。有些输出格式可以利用这些资讯来作语法高亮。目前有使用到这些资讯的输出格式仅有 HTML 与 LaTeX。如果指定的输出格式及语言类别有支援语法高亮，那麼上面那段代码区块将会以高亮并带有行号的方式呈现。（要查询支援的程式语言清单，可在命令列输入 `pandoc --version`。）反之若无支援，则上面那段代码区块则会以下面的形式呈现：

    <pre id="mycode" class="haskell numberLines" startFrom="100">
      <code>
      ...
      </code>
    </pre>

下面这个是针对代码区块只有指定程式语言属性的简便形式：

    ```haskell
    qsort [] = []
    ```

这与下面这行的效果是相同的：

    ``` {.haskell}
    qsort [] = []
    ```

要取消所有语法高亮，使用 `--no-highlight` 选项。要设定语法高亮的配色，则使用 `--highlight-style`。

行区块
-----------

**Extension: `line_blocks`**

行区块是一连串以竖线 (`|`) 加上一个空格所构成的连续行。行与行间的区隔在输出时将会以原样保留，行首的空白字元数目也一样会被保留；反之，这些行将会以 markdown 的格式处理。这个语法在输入诗句或地址时很有帮助。

    | The limerick packs laughs anatomical
    | In space that is quite economical.
    |    But the good ones I've seen
    |    So seldom are clean
    | And the clean ones so seldom are comical

    | 200 Main St.
    | Berkeley, CA 94718

如果有需要的话，书写时也可以将完整一行拆成多行，但后续行必须以空白作為开始。下面范例的前两行在输出时会被视為一整行：

    | The Right Honorable Most Venerable and Righteous Samuel L.
      Constable, Jr.
    | 200 Main St.
    | Berkeley, CA 94718

这是从 [reStructuredText] 借来的语法。

清单
-----

### 无序清单 ###

无序清单是以项目符号作列举的清单。每条项目都以项目符号 (`*`, `+` 或 `-`) 作开头。下面是个简单的例子：

    * one
    * two
    * three

这会產生一个「紧凑」清单。如果你想要一个「宽鬆」清单，也就是说以段落格式处理每个项目内的文字内容，那麼只要在每个项目间加上空白行即可：

    * one

    * two

    * three

项目符号不能直接从行首最左边处输入，而必须以一至三个空白字元作缩进。项目符号后必须跟著一个空白字元。

清单项目中的接续行，若与该项目的第一行文字对齐（在项目符号之后），看上去会较為美观：

    * here is my first
      list item.
    * and my second.

但 markdown 也允许以下「偷懒」的格式：

    * here is my first
    list item.
    * and my second.

### 四个空白规则 ###

一个清单项目可以包含多个段落以及其他区块等级的内容。然而，后续的段落必须接在空白行之后，并且以四个空白或一个 tab 作缩进。因此，如果项目裡第一个段落与后面段落对齐的话（也就是项目符号前置入两个空白），看上去会比较整齐美观：

      * First paragraph.

        Continued.

      * Second paragraph. With a code block, which must be indented
        eight spaces:

            { code }

清单项目也可以包含其他清单。在这情况下前置的空白行是可有可无的。嵌套清单必须以四个空白或一个 tab 作缩进：

    * fruits
        + apples
            - macintosh
            - red delicious
        + pears
        + peaches
    * vegetables
        + brocolli
        + chard

上一节提到，markdown 允许你以「偷懒」的方式书写，项目的接续行可以不和第一行对齐。不过，如果一个清单项目中包含了多个段落或是其他区块元素，那麼每个元素的第一行都必须缩进对齐。

    + A lazy, lazy, list
    item.

    + Another one; this looks
    bad but is legal.

        Second paragraph of second
    list item.

**注意：**儘管针对接续段落的「四个空白规则」是出自於官方的 [markdown syntax guide]，但是作為对应参考用的 `Markdown.pl` 实作版本中并未遵循此一规则。所以当输入时若接续段落的缩进少於四个空白时，pandoc 所输出的结果会与 `Markdown.pl` 的输出有所出入。

在 [markdown syntax guide] 中并未明确表示「四个空白规则」是否一体适用於 **所有** 位於清单项目裡的区块元素上；规范文件中只提及了段落与代码区块。但文件暗示了此规则适用於所有区块等级的内容（包含嵌套清单），并且 pandoc 以此方向进行解读与实作。

  [markdown syntax guide]:
    http://daringfireball.net/projects/markdown/syntax#list

### 有序清单 ###

有序清单与无序清单相类似，唯一的差别在於清单项目是以列举编号作开头，而不是项目符号。

在原始 markdown 中，列举编号是阿拉伯数字后面接著一个句点与空白。数字本身代表的数值会被忽略，因此下面两个清单并无差别：

    1.  one
    2.  two
    3.  three

上下两个清单的输出是相同的。

    5.  one
    7.  two
    1.  three

**Extension: `fancy_lists`**

与原始 markdown 不同的是，Pandoc 除了使用阿拉伯数字作為有序清单的编号外，也可以使用大写或小写的英文字母，以及罗马数字。清单标记可以用括号包住，也可以单独一个右括号，抑或是句号。如果清单标记是大写字母接著一个句号，句号后请使用至少两个空白字元。[^2]

[^2]:  之所以有这条规则，主要是要避免以人名头文字缩写作為开头的段落所带来的混淆，像是

        B. Russell was an English philosopher.

    这样就不会被当作清单项目了。

    这条规则并不会避免以下

        (C) 2007 Joe Smith

    这样的叙述被解释成清单项目。在这情形下，可以使用反斜线：
    
        (C\) 2007 Joe Smith

**Extension: `startnum`**

除了清单标记外，Pandoc 也能判读清单的起始编号，这两项资讯都会保留於输出格式中。举例来说，下面的输入可以產生一个从编号 9 开始，以单括号為编号标记的清单，底下还跟著一个小写罗马数字的子清单：

     9)  Ninth
    10)  Tenth
    11)  Eleventh
           i. subone
          ii. subtwo
         iii. subthree

当遇到不同形式的清单标记时，Pandoc 会重新开始一个新的清单。所以，以下的输入会產生三份清单：

    (2) Two
    (5) Three
    1.  Four
    *   Five

如果需要预设的有序清单标记符号，可以使用 `#.`：

    #.  one
    #.  two
    #.  three


### 定义清单 ###

**Extension: `definition_lists`**

Pandoc 支援定义清单，其语法的灵感来自於 [PHP Markdown Extra] 以及 [reStructuredText]：[^3]

    Term 1

    :   Definition 1

    Term 2 with *inline markup*

    :   Definition 2

            { some code, part of Definition 2 }

        Third paragraph of definition 2.

每个专有名词 (term) 都必须单独存在於一行，后面可以接著一个空白行，也可以省略，但一定要接上一或多笔定义内容。一笔定义需由一个冒号或波浪线作开头，可以接上一或两个空白作為缩进。定义本身的内容主体（包括接在冒号或波浪线后的第一行）应该以四个空白缩进。一个专有名词可以有多个定义，而每个定义可以包含一或多个区块元素（段落、代码区块、清单等），每个区块元素都要缩进四个空白或一个 tab。

如果你在定义内容后面留下空白行（如同上面的范例），那麼该段定义会被当作段落处理。在某些输出格式中，这意谓著成对的专有名词与定义内容间会有较大的空白间距。在定义与定义之间，以及定义与下个专有名词间不要留空白行，即可產生一个比较紧凑的定义清单：

    Term 1
      ~ Definition 1
    Term 2
      ~ Definition 2a
      ~ Definition 2b

[^3]:  [David Wheeler](http://www.justatheory.com/computers/markup/modest-markdown-proposal.html) 对於 markdown 的建议也同时影响了我。

[PHP Markdown Extra]: http://www.michelf.com/projects/php-markdown/extra/


### 编号范例清单 ###

**Extension: `example_lists`**

这个特别的清单标记 `@` 可以用来產生连续编号的范例清单。清单中第一个以 `@` 标记的项目会被编号為 '1'，接著编号為 '2'，依此类推，直到文件结束。范例项目的编号不会侷限於单一清单中，而是文件中所有以 `@` 為标记的项目均会次序递增其编号，直到最后一个。举例如下：

    (@)  My first example will be numbered (1).
    (@)  My second example will be numbered (2).

    Explanation of examples.

    (@)  My third example will be numbered (3).

编号范例可以加上标籤，并且在文件的其他地方作参照：

    (@good)  This is a good example.

    As (@good) illustrates, ...

标籤可以是由任何英文字母、底线或是连字符号所组成的字串。


### 紧凑与宽鬆清单 ###

在与清单相关的「边界处理」上，Pandoc 与 `Markdown.pl` 有著不同的处理结果。考虑如下代码：

    +   First
    +   Second:
        -   Fee
    	-   Fie
    	-   Foe

    +   Third

Pandoc 会将以上清单转换為「紧凑清单」（在 "First", "Second" 或 "Third" 之中没有 `<p>` 标籤），而 markdown 则会在 "Second" 与 "Third" （但不包含 "First"）裡面置入 `<p>` 标籤，这是因為 "Third" 之前的空白行而造成的结果。Pandoc 依循著一个简单规则：如果文字后面跟著空白行，那麼就会被视為段落。既然 "Second" 后面是跟著一个清单，而非空白行，那麼就不会被视為段落了。至於子清单的后面是不是跟著空白行，那就无关紧要了。（注意：即使是设定為 `markdown_strict` 格式，Pandoc 仍是依以上方式处理清单项目是否為段落的判定。这个处理方式与 markdown 官方语法规范裡的描述一致，然而却与 `Markdown.pl` 的处理不同。）


### 结束一个清单 ###

如果你在清单之后放入一个缩排的代码区块，会有什麼结果？

    -   item one
    -   item two

        { my code block }

问题大了！这边 pandoc（其他的 markdown 实作也是如此）会将 `{ my code block }` 视為 `item two` 这个清单项目的第二个段落来处理，而不会将其视為一个代码区块。

要在 `item two` 之后「切断」清单，你可以插入一些没有缩排、输出时也不可见的内容，例如 HTML 的註解：

    -   item one
    -   item two

    <!-- end of list -->

        { my code block }

当你想要两个各自独立的清单，而非一个大且连续的清单时，也可以运用同样的技巧：

    1.  one
    2.  two
    3.  three

    <!-- -->

    1.  uno
    2.  dos
    3.  tres

分隔线
----------------

一行中若包含三个以上的 `*`, `-` 或 `_` 符号（中间可以以空白字元分隔），则会產生一条分隔线：

    *  *  *  *

    ---------------


表格
------

有四种表格的形式可以使用。前三种适用於等宽字型的编辑环境，例如 Courier。第四种则不需要直行的对齐，因此可以在比例字型的环境下使用。

### 简单表格

**Extension: `simple_tables`, `table_captions`**

简单表格看起来像这样子：

      Right     Left     Center     Default
    -------     ------ ----------   -------
         12     12        12            12
        123     123       123          123
          1     1          1             1

    Table:  Demonstration of simple table syntax.

表头与资料列分别以一行為单位。直行的对齐则依照表头的文字和其底下虚线的相对位置来决定：[^4]

  - 如果虚线与表头文字的右侧有切齐，而左侧比表头文字还长，则该直行為靠右对齐。
  - 如果虚线与表头文字的左侧有切齐，而右侧比表头文字还长，则该直行為靠左对齐。
  - 如果虚线的两侧都比表头文字长，则该直行為置中对齐。
  - 如果虚线与表头文字的两侧都有切齐，则会套用预设的对齐方式（在大多数情况下，这将会是靠左对齐）。

[^4]:  这个方案是由 Michel Fortin 在 [Markdown discussion list](http://six.pairlist.net/pipermail/markdown-discuss/2005-March/001097.html) 的讨论中所提出。

表格底下必须接著一个空白行，或是一行虚线后再一个空白行。表格标题為可选的（上面的范例中有出现）。标题需是一个以 `Table:` （或单纯只有 `:`）开头作為前缀的段落，输出时前缀的这部份会被去除掉。表格标题可以放在表格之前或之后。

表头也可以省略，在省略表头的情况下，表格下方必须加上一行虚线以清楚标明表格的范围。例如：

    -------     ------ ----------   -------
         12     12        12             12
        123     123       123           123
          1     1          1              1
    -------     ------ ----------   -------

当省略表头时，直行的对齐会以表格内容的第一行资料列决定。所以，以上面的表格為例，各直行的对齐依序会是靠右、靠左、置中以及靠右对齐。

### 多行表格

**Extension: `multiline_tables`, `table_captions`**

多行表格允许表头与表格资料格的文字能以复数行呈现（但不支援横跨多栏或纵跨多列的资料格）。以下為范例：

    -------------------------------------------------------------
     Centered   Default           Right Left
      Header    Aligned         Aligned Aligned
    ----------- ------- --------------- -------------------------
       First    row                12.0 Example of a row that
                                        spans multiple lines.

      Second    row                 5.0 Here's another one. Note
                                        the blank line between
                                        rows.
    -------------------------------------------------------------

    Table: Here's the caption. It, too, may span
    multiple lines.

看起来很像简单表格，但两者间有以下差别：

  - 在表头文字之前，必须以一列虚线作為开头（除非有省略表头）。
  - 必须以一列虚线作為表格结尾，之后接一个空白行。
  - 资料列与资料列之间以空白行隔开。

在多行表格中，表格分析器会计算各直行的栏宽，并在输出时尽可能维持各直行在原始文件中的相对比例。因此，要是你觉得某些栏位在输出时不够宽，你可以在 markdown 的原始档中加宽一点。

和简单表格一样，表头在多行表格中也是可以省略的：

    ----------- ------- --------------- -------------------------
       First    row                12.0 Example of a row that
                                        spans multiple lines.

      Second    row                 5.0 Here's another one. Note
                                        the blank line between
                                        rows.
    ----------- ------- --------------- -------------------------

    : Here's a multiline table without headers.

多行表格中可以单只包含一个资料列，但该资料列之后必须接著一个空白行（然后才是标示表格结尾的一行虚线）。如果没有此空白行，此表格将会被解读成简单表格。

### 格框表格

**Extension: `grid_tables`, `table_captions`**

格框表格看起来像这样：

    : Sample grid table.

    +---------------+---------------+--------------------+
    | Fruit         | Price         | Advantages         |
    +===============+===============+====================+
    | Bananas       | $1.34         | - built-in wrapper |
    |               |               | - bright color     |
    +---------------+---------------+--------------------+
    | Oranges       | $2.10         | - cures scurvy     |
    |               |               | - tasty            |
    +---------------+---------------+--------------------+

以 `=` 串成的一行区分了表头与表格本体，这在没有表头的表格中也是可以省略的。在格框表格中的资料格可以包含任意的区块元素（复数段落、代码区块、清单等等）。不支援对齐，也不支援横跨多栏或纵跨多列的资料格。格框表格可以在 [Emacs table mode] 下轻鬆建立。

  [Emacs table mode]: http://table.sourceforge.net/

### 管线表格

**Extension: `pipe_tables`, `table_captions`**

管线表格看起来像这样：

    | Right | Left | Default | Center |
    |------:|:-----|---------|:------:|
    |   12  |  12  |    12   |    12  |
    |  123  |  123 |   123   |   123  |
    |    1  |    1 |     1   |     1  |

      : Demonstration of simple table syntax.

这个语法与 [PHP markdown extra 中的表格语法][the same as in PHP markdown extra] 相同。开始与结尾的管线字元是可选的，但各直行间则必须以管线区隔。上面范例中的冒号表明了对齐方式。表头可以省略，但表头下的水平虚线必须保留，因為虚线上定义了资料栏的对齐方式。

因為管线界定了各栏之间的边界，表格的原始码并不需要像上面例子中各栏之间保持直行对齐。所以，底下一样是个完全合法（虽然丑陋）的管线表格：

    fruit| price
    -----|-----:
    apple|2.05
    pear|1.37
    orange|3.09

管线表格的资料格不能包含如段落、清单之类的区块元素，也不能包含复数行文字。

  [the same as in PHP markdown extra]:
    http://michelf.ca/projects/php-markdown/extra/#table

注意：Pandoc 也可以看得懂以下形式的管线表格，这是由 Emacs 的 orgtbl-mod 所绘製：

    | One | Two   |
    |-----+-------|
    | my  | table |
    | is  | nice  |

主要的差别在於以 `+` 取代了部分的 `|`。其他的 orgtbl 功能并未支援。如果要指定非预设的直行对齐形式，你仍然需要在上面的表格中自行加入冒号。

文件标题区块
-----------

（译註：本节中提到的「标题」均指 Title，而非 Headers）

**Extension: `pandoc_title_block`**

如果档案以文件标题（Title）区块开头

    % title
    % author(s) (separated by semicolons)
    % date

这部份将不会作為一般文字处理，而会以书目资讯的方式解析。（这可用在像是单一 LaTeX 或是 HTML 输出文件的书名上。）这个区块仅能包含标题，或是标题与作者，或是标题、作者与日期。如果你只想包含作者却不想包含标题，或是只有标题与日期而没有作者，你得利用空白行：

    %
    % Author

    % My title
    %
    % June 15, 2006

标题可以包含多行文字，但接续行必须以空白字元开头，像是：

    % My title
      on multiple lines

如果文件有多个作者，作者也可以分列在不同行并以空白字元作开头，或是以分号间隔，或是两者并行。所以，下列各种写法得到的结果都是相同的：

    % Author One
      Author Two

    % Author One; Author Two

    % Author One;
      Author Two

日期就只能写在一行之内。

所有这三个 metadata 栏位都可以包含标準的行内格式（斜体、连结、脚註等等）。

文件标题区块一定会被分析处理，但只有在 `--standaline` (`-s`) 选项被设定时才会影响输出内容。在输出 HTML 时，文件标题会出现的地方有两个：一个是在文件的 `<head>` 区块裡－－这会显示在瀏览器的视窗标题上－－另外一个是文件的 `<body>` 区块最前面。位於 `<head>` 裡的文件标题可以选择性地加上前缀文字（透过 `--title-prefix` 或 `-T` 选项）。而在 `<body>` 裡的文件标题会以 H1 元素呈现，并附带 "title" 类别 (class)，这样就能藉由 CSS 来隐藏显示或重新定义格式。如果以 `-T` 选项指定了标题前缀文字，却没有设定文件标题区块裡的标题，那麼前缀文字本身就会被当作是 HTML 的文件标题。

而 man page 的输出器会分析文件标题区块的标题行，以解出标题、man page section number，以及其他页眉 (header) 页脚 (footer) 所需要的资讯。一般会假设标题行的第一个单字為标题，标题后也许会紧接著一个以括号包住的单一数字，代表 section number（标题与括号之间没有空白）。在此之后的其他文字则為页脚与页眉文字。页脚与页眉文字之间是以单独的一个管线符号 (`|`) 作為区隔。所以，

    % PANDOC(1)

将会產生一份标题為 `PANDOC` 且 section 為 1 的 man page。

    % PANDOC(1) Pandoc User Manuals

產生的 man page 会再加上 "Pandoc User Manuals" 在页脚处。

    % PANDOC(1) Pandoc User Manuals | Version 4.0

產生的 man page 会再加上 "Version 4.0" 在页眉处。


反斜线跳脱字元
-----------------

**Extension: `all_symbols_escapable`**

除了在代码区块或行内代码之外，任何标点符号或空白字元前面只要加上一个反斜线，都能使其保留字面原义，而不会进行格式的转义解读。因此，举例来说，下面的写法

    *\*hello\**

输出后会得到

    <em>*hello*</em>

而不是

    <strong>hello</strong>

这条规则比原始的 markdown 规则来得好记许多，原始规则中，只有以下字元才有支援反斜线跳脱，不作进一步转义：

    \`*_{}[]()>#+-.!

（然而，如果使用了 `markdown_strict` 格式，那麼就会採用原始的 markdown 规则）

一个反斜线之后的空白字元会被解释為不断行的空白 (nonbreaking space)。这在 TeX 的输出中会显示為 `~`，而在 HTML 与 XML 则是显示為 `\&#160;` 或 `\&nbsp;`。

一个反斜线之后的换行字元（例如反斜线符号出现在一行的最尾端）则会被解释為强制换行。这在 TeX 的输出中会显示為 `\\`，而在 HTML 裡则是 `<br />`。相对於原始 markdown 是以在行尾加上两个空白字元这种「看不见」的方式进行强制换行，反斜线接换行字元会是比较好的替代方案。

反斜线跳脱字元在代码上下文中不起任何作用。

智慧型标点符号
-----------------

**Extension**

如果指定了 `--smart` 选项，pandoc 将会输出正式印刷用的标点符号，像是将 straight quotes 转换為 curly quotes[^T1]、`---` 转為破折号 (em-dashes)，`--` 转為连接号 (en-dashes)，以及将 `...` 转為删节号。不断行空格 (Nonbreaking spaces) 将会插入某些缩写词之后，例如 "Mr."。

注意：如果你的 LaTeX template 使用了 `csquotes` 套件，pandoc 会自动侦测并且使用 `\enquote{...}` 在引言文字上。

[^T1]: 译註：straight quotes 指的是左右两侧都长得一样的引号，例如我们直接在键盘上打出来的单引号或双引号；curly quotes 则是左右两侧不同，有从两侧向内包夹视觉效果的引号。

行内格式
-----------------

### 强调 ###

要 *强调* 某些文字，只要以 `*` 或 `_` 符号前后包住即可，像这样：

    This text is _emphasized with underscores_, and this
    is *emphasized with asterisks*.

重复两个 `*` 或 `_` 符号以產生 **更强烈的强调**：

    This is **strong emphasis** and __with underscores__.

一个前后以空白字元包住，或是前面加上反斜线的 `*` 或 `_` 符号，都不会转换為强调格式：

    This is * not emphasized *, and \*neither is this\*.

**Extension: `intraword_underscores`**

因為 `_` 字元有时会使用在单字或是 ID 之中，所以 pandoc 不会把被字母包住的 `_` 解读為强调标记。如果有需要特别强调单字中的一部分，就用 `*`：

    feas*ible*, not feas*able*.


### 删除线 ###

**Extension:  `strikeout`**

要将一段文字加上水平线作為删除效果，将该段文字前后以 `~~` 包住即可。例如，

    This ~~is deleted text.~~


### 上标与下标 ###

**Extension: `superscript`, `subscript`**

要输入上标可以用 `^` 字元将要上标的文字包起来；要输入下标可以用 `~` 字元将要下标的文字包起来。直接看范例，

    H~2~O is a liquid.  2^10^ is 1024.

如果要上标或下标的文字中包含了空白，那麼这个空白字元之前必须加上反斜线。（这是為了避免一般使用下的 `~` 和 `^` 在非预期的情况下產生出意外的上标或下标。）所以，如果你想要让字母 P 后面跟著下标文字 'a cat'，那麼就要输入 `P~a\ cat~`，而不是 `P~a cat~`。


### 字面文字 ###

要让一小段文字直接以其字面形式呈现，可以用反引号将其包住：

    What is the difference between `>>=` and `>>`?

如果字面文字中也包含了反引号，那就使用双重反引号包住：

    Here is a literal backtick `` ` ``.

（在起始反引号后的空白以及结束反引号前的空白都会被忽略。）

一般性的规则如下，字面文字区段是以连续的反引号字元作為开始（反引号后的空白字元為可选），一直到同样数目的反引号字元出现才结束（反引号前的空白字元也為可选）。

要注意的是，反斜线跳脱字元（以及其他 markdown 结构）在字面文字的上下文中是没有效果的：

    This is a backslash followed by an asterisk: `\*`.

**Extension: `inline_code_attributes`**

与[围栏代码区块]一样，字面文字也可以附加属性：

    `<$>`{.haskell}

数学
----

**Extension: `tex_math_dollars`**

所有介於两个 `$` 字元之间的内容将会被视為 TeX 数学公式处理。开头的 `$` 右侧必须立刻接上任意文字，而结尾 `$` 的左侧同样也必须紧挨著文字。这样一来，`$20,000 and $30,000` 就不会被当作数学公式处理了。如果基於某些原因，有必须使用 `$` 符号将其他文字括住的需求时，那麼可以在 `$` 前使用反斜线跳脱字元，这样 `$` 就不会被当作数学公式的分隔符。

TeX 数学公式会在所有输出格式中印出。至於会以什麼方式演算编排 (render) 则取决於输出的格式：

Markdown, LaTeX, Org-Mode, ConTeXt
  ~ 公式会以字面文字呈现在两个 `$` 符号之间。

reStructuredText
  ~ 公式会使用 [此处](http://www.american.edu/econ/itex2mml/mathhack.rst) 所描述的 `:math:` 这个 "interpreted text role" 来进行演算编排。
  
AsciiDoc
  ~ 公式会以 `latexmath:[...]` 演算编排。

Texinfo
  ~ 公式会在 `@math` 指令中演算编排。
  
groff man
  ~ 公式会以去掉 `$` 后的字面文字演算编排。

MediaWiki
  ~ 公式会在 `<math>` 标籤中演算编排。
  
Textile
  ~ 公式会在 `<span class="math">` 标籤中演算编排。

RTF, OpenDocument, ODT
  ~ 如果可以的话，公式会以 unicode 字元演算编排，不然就直接使用字面字元。

Docbook
  ~ 如果使用了 `--mathml` 旗标，公式就会在 `inlineequation` 或 `informalequation` 标籤中使用 mathml 演算编排。否则就会尽可能使用 unicode 字元演算编排。

Docx
  ~ 公式会以 OMML 数学标记的方式演算编排。
  
FictionBook2
  ~ 如果有使用 `--webtex` 选项，公式会以 Google Charts 或其他相容的网路服务演算编排為图片，并下载嵌入於电子书中。否则就会以字面文字显示。
  
HTML, Slidy, DZSlides, S5, EPUB
  ~ 公式会依照以下命令列选项的设置，以不同的方法演算编排為 HTML 代码。

    1.  预设方式是将 TeX 数学公式尽可能地以 unicode 字元演算编排，如同 RTF、DocBook 以及 OpenDocument 的输出。公式会被放在附有属性 `class="math"` 的 `span` 标籤内，所以可以在需要时给予不同的样式，使其突出於周遭的文字内容。
    
    2.  如果使用了 `--latexmathml` 选项，TeX 数学公式会被显示於 `$` 或 `$$` 字元中，并放在附带 `LaTeX` 类别的 `<span>` 标籤裡。这段内容会用 [LaTeXMathML] script 演算编排為数学公式。（这个方法无法适用於所有瀏览器，但在 Firefox 中是有效的。在不支援 LaTeXMathML 的瀏览器中，TeX 数学公式会单纯的以两个 `$` 字元间的字面文字呈现。）

    3.  如果使用了 `--jsmath` 选项，TeX数学公式会放在 `<span>` 标籤（用於行内数学公式）或 `<div>` 标籤（用於区块数学公式）中，并附带类别属性 `math`。这段内容会使用 [jsMath] script 来演算编排。

    4.  如果使用了 `--mimetex` 选项，[mimeTeX] CGI script 会被呼叫来產生每个 TeX 数学公式的图片。这适用於所有瀏览器。`--mimetex` 选项有一个可选的 URL 参数。如果没有指定 URL，它会假设 mimeTeX CGI script 的位置在 `/cgi-bin/mimetex.cig`。

    5.  如果使用了 `--gladtex` 选项，TeX 数学公式在 HTML 的输出中会被 `<eq>` 标籤包住。產生的 `htex` 档案之后可以透过 [gladTeX] 处理，这会针对每个数学公式生成图片，并於最后生成一个包含这些图片连结的 `html` 档案。所以，整个处理流程如下：

            pandoc -s --gladtex myfile.txt -o myfile.htex
            gladtex -d myfile-images myfile.htex
            # produces myfile.html and images in myfile-images

    6.  如果使用了 `--webtex` 选项，TeX 数学公式会被转换為 `<img>` 标籤并连结到一个用以转换公式為图片的外部 script。公式将会编码為 URL 可接受格式并且与指定的 URL 参数串接。如果没有指定 URL，那麼将会使用 Google Chart API (`http://chart.apis.google.com/chart?cht=tx&chl=`)。
    
    7.  如果使用了 `--mathjax` 选项，TeX 数学公式将会被包在 `\(...\)`（用於行内数学公式）或 `\[...\]`（用於区块数学公式）之间显示，并且放在附带类别 `math` 的 `<span>` 标籤之中。这段内容会使用 [MathJax] script 演算编排為页面上的数学公式。

[LaTeXMathML]: http://math.etsu.edu/LaTeXMathML/
[jsMath]:  http://www.math.union.edu/~dpvc/jsmath/
[mimeTeX]: http://www.forkosh.com/mimetex.html
[gladTeX]:  http://ans.hsh.no/home/mgg/gladtex/
[MathJax]: http://www.mathjax.org/

Raw HTML
--------

**Extension: `raw_html`**

Markdown 允许你在文件中的任何地方插入原始 HTML（或 DocBook）指令（除了在字面文字上下文处，此时的 `<`, `>` 和 `&` 都会按其字面意义显示）。（技术上而言这不算扩充功能，因為原始 markdown 本身就有提供此功能，但做成扩充形式便可以在有特殊需要的时候关闭此功能。）

输出 HTML, S5, Slidy, Slideous, DZSlides, EPUB, Markdown 以及 Textile 等格式时，原始 HTML 代码会不作修改地保留至输出档案中；而其他格式的输出内容则会将原始 HTML 代码去除掉。

**Extension: `markdown_in_html_blocks`**

原始 markdown 允许你插入 HTML「区块」：所谓的 HTML 区块是指，上下各由一个空白行所隔开，开始与结尾均由所在行最左侧开始的一连串对称均衡的 HTML 标籤。在这个区块中，任何内容都会当作是 HTML 来分析，而不再视為 markdown；所以（举例来说），`*` 符号就不再代表强调。

当指定格式為 `markdown_strict` 时，Pandoc 会以上述方式处理；但预设情况下，Pandoc 能够以 markdown 语法解读 HTML 区块标籤中的内容。举例说明，Pandoc 能够将底下这段

    <table>
    	<tr>
    		<td>*one*</td>
    		<td>[a link](http://google.com)</td>
    	</tr>
    </table>

转换為

    <table>
    	<tr>
    		<td><em>one</em></td>
    		<td><a href="http://google.com">a link</a></td>
    	</tr>
    </table>

而 `Markdown.pl` 则是保留该段原样。

这个规则只有一个例外：那就是介於 `<script>` 与 `<style>` 之间的文字都不会被拿来当作 markdown 解读。

这边与原始 markdown 的分歧，主要是為了让 markdown 能够更便利地混入 HTML 区块元素。比方说，一段 markdown 文字可以用 `<div>` 标籤将其前后包住来进行样式指定，而不用担心裡面的 markdown 不会被解译到。

Raw TeX
-------

**Extension: `raw_tex`**

除了 HTML 之外，pandoc 也接受文件中嵌入原始 LaTeX, TeX 以及 ConTeXt 代码。行内 TeX 指令会被保留并不作修改地输出至 LaTeX 与 ConTeXt 格式中。所以，举例来说，你可以使用 LaTeX 来导入 BibTeX 的引用文献：

    This result was proved in \cite{jones.1967}.

请注意在 LaTeX 环境下时，像是底下

    \begin{tabular}{|l|l|}\hline
    Age & Frequency \\ \hline
    18--25  & 15 \\
    26--35  & 33 \\
    36--45  & 22 \\ \hline
    \end{tabular}

位在 `begin` 与 `end` 标籤之间的内容，都会被当作是原始 LaTeX 资料解读，而不会视為 markdown。

行内 LaTeX 在输出至 Markdown, LaTeX 及 ConTeXt 之外的格式时会被忽略掉。

LaTeX 巨集
------------

**Extension: `latex_macros`**

当输出格式不是 LaTeX 时，pandoc 会分析 LaTeX 的 `\newcommand` 和 `\renewcommand` 定义，并套用其產生的巨集到所有 LaTeX 数学公式中。所以，举例来说，下列指令对於所有的输出格式均有作用，而非仅仅作用於 LaTeX 格式： 

    \newcommand{\tuple}[1]{\langle #1 \rangle}

    $\tuple{a, b, c}$

在 LaTeX 的输出中，`\newcommand` 定义会单纯不作修改地保留至输出结果。


连结
-----

Markdown 接受以下数种指定连结的方式。

### 自动连结 ###

如果你用角括号将一段 URL 或是 email 位址包起来，它会自动转换成连结：

    <http://google.com>
    <sam@green.eggs.ham>

### 行内连结 ###

一个行内连结包含了位在方括号中的连结文字，以及方括号后以圆括号包起来的 URL。（你可以选择性地在 URL 后面加入连结标题，标题文字要放在引号之中。）

    This is an [inline link](/url), and here's [one with
    a title](http://fsf.org "click here for a good time!").

方括号与圆括号之间不能有空白。连结文字可以包含格式（例如强调），但连结标题则否。


### 参考连结 ###

一个 **明确** 的参考连结包含两个部分，连结本身以及连结定义，其中连结定义可以放在文件的任何地方（不论是放在连结所在处之前或之后）。

连结本身是由两组方括号所组成，第一组方括号中為连结文字，第二组為连结标籤。（在两个方括号间可以有空白。）连结定义则是以方括号框住的连结标籤作开头，后面跟著一个冒号一个空白，再接著一个 URL，最后可以选择性地（在一个空白之后）加入由引号或是圆括号包住的连结标题。

以下是一些范例：

    [my label 1]: /foo/bar.html  "My title, optional"
    [my label 2]: /foo
    [my label 3]: http://fsf.org (The free software foundation)
    [my label 4]: /bar#special  'A title in single quotes'

连结的 URL 也可以选择性地以角括号包住：

    [my label 5]: <http://foo.bar.baz>

连结标题可以放在第二行：

    [my label 3]: http://fsf.org
      "The free software foundation"

需注意连结标籤并不区分大小写。所以下面的例子会建立合法的连结：

    Here is [my link][FOO]

    [Foo]: /bar/baz

在一个 **隐性** 参考连结中，第二组方括号的内容是空的，甚至可以完全地略去：

    See [my website][], or [my website].

    [my website]: http://foo.bar.baz

注意：在 `Markdown.pl` 以及大多数其他 markdown 实作中，参考连结的定义不能存在於嵌套结构中，例如清单项目或是区块引言。Pandoc lifts this arbitrary seeming restriction。所以虽然下面的语法在几乎所有其他实作中都是错误的，但在 pandoc 中可以正确处理：

    > My block [quote].
    >
    > [quote]: /foo

### 内部连结

要连结到同一份文件的其他章节，可使用自动產生的 ID（参见 [HTML, LaTeX 与 ConTeXt 的标题识别符] 一节后半）。例如：

    See the [Introduction](#introduction).

或是

    See the [Introduction].

    [Introduction]: #introduction

内部连结目前支援的格式有 HTML（包括 HTML slide shows 与 EPUB）、LaTeX 以及 ConTeXt。

图片
------

在连结语法的前面加上一个 `!` 就是图片的语法了。连结文字将会作為图片的替代文字（alt text）：

    ![la lune](lalune.jpg "Voyage to the moon")

    ![movie reel]

    [movie reel]: movie.gif

### 附上说明的图片 ###

**Extension: `implicit_figures`**

一个图片若自身单独存在一个段落中，那麼将会以附上图片说明 (caption) 的图表 (figure) 形式呈现。[^5]（在 LaTeX 中，会使用图表环境；在 HTML 中，图片会被放在具有 `figure` 类别的 `div` 元素中，并会附上一个具有 `caption` 类别的 `p` 元素。）图片的替代文字同时也会用来作為图片说明。

    ![This is the caption](/url/of/image.png)

[^5]: 这项功能尚未在 RTF, OpenDocument 或 ODT 格式上实现。在这些格式中，你会得到一个在段落中只包含自己的图片，而无图片说明。

如果你只是想要个一般的行内图片，那麼只要让图片不是段落裡唯一的元素即可。一个简单的方法是在图片后面插入一个不断行空格：

    ![This image won't be a figure](/url/of/image.png)\


脚註
---------

**Extension: `footnotes`**

Pandoc's markdown 支援脚註功能，使用以下的语法：

    Here is a footnote reference,[^1] and another.[^longnote]

    [^1]: Here is the footnote.

    [^longnote]: Here's one with multiple blocks.

        Subsequent paragraphs are indented to show that they
    belong to the previous footnote.

            { some.code }

        The whole paragraph can be indented, or just the first
        line.  In this way, multi-paragraph footnotes work like
        multi-paragraph list items.

    This paragraph won't be part of the note, because it
    isn't indented.

脚註参考用的 ID 不得包含空白、tabs 或换行字元。这些 ID 只会用来建立脚註位置与脚註文字的对应关连；在输出时，脚註将会依序递增编号。

脚註本身不需要放在文件的最后面。它们可以放在文件裡的任何地方，但不能被放入区块元素（清单、区块引言、表格等）之中。

**Extension: `inline_notes`**

Pandoc 也支援了行内脚註（儘管，与一般脚註不同，行内脚註不能包含多个段落）。其语法如下：

    Here is an inline note.^[Inlines notes are easier to write, since
    you don't have to pick an identifier and move down to type the
    note.]

行内与一般脚註可以自由交错使用。


引用
---------

**Extension: `citations`**

Pandoc 能够以数种形式自动產生引用与参考书目（使用 Andrea Rossato 的 `hs-citeproc`）。為了使用这项功能，你需要一个下列其中一种格式的参考书目资料库：

  Format            File extension
  ------------      --------------
  MODS              .mods
  BibLaTeX          .bib
  BibTeX            .bibtex
  RIS               .ris
  EndNote           .enl
  EndNote XML       .xml
  ISI               .wos
  MEDLINE           .medline
  Copac             .copac
  JSON citeproc     .json

需注意的是副档名 `.bib` 一般而言同时适用於 BibTeX 与 BibLaTeX 的档案，不过你可以使用 `.bibtex` 来强制指定 BibTeX。

你需要使用命令列选项 `--bibliography` 来指定参考书目档案（如果有多个书目档就得反覆指定）。

预设情况下，pandoc 会在引用文献与参考书目中使用芝加哥「作者－日期」格式。要使用其他的格式，你需要用 `--csl` 选项来指定一个 [CSL] 1.0 格式的档案。关於建立与修改 CSL 格式的入门可以在 <http://citationstyles.org/downloads/primer.html> 这边找到。<https://github.com/citation-style-language/styles> 是 CSL 格式的档案库。也可以在 <http://zotero.org/styles> 以简单的方式瀏览。

[CSL]: http://CitationStyles.org

引用资讯放在方括号中，以分号区隔。每一条引用都会有个 key，由 `@` 加上资料库中的引用 ID 组成，并且可以选择性地包含前缀、定位以及后缀。以下是一些范例：

    Blah blah [see @doe99, pp. 33-35; also @smith04, ch. 1].

    Blah blah [@doe99, pp. 33-35, 38-39 and *passim*].

    Blah blah [@smith04; @doe99].

在 `@` 前面的减号 (`-`) 将会避免作者名字在引用中出现。这可以用在已经提及作者的文章场合中：

    Smith says blah [-@smith04].

你也可以在文字中直接插入引用资讯，方式如下：

    @smith04 says blah.

    @smith04 [p. 33] says blah.

如果引用格式档需要產生一份引用作品的清单，这份清单会被放在文件的最后面。一般而言，你需要以一个适当的标题结束你的文件：

    last paragraph...

    # References

如此一来参考书目就会被放在这个标题后面了。

[markdown]: http://daringfireball.net/projects/markdown/
[reStructuredText]: http://docutils.sourceforge.net/docs/ref/rst/introduction.html

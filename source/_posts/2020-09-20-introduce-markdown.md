---
title:  "markdown基礎使用"
date: 2020-09-20 00:00:00
description: "markdown的基礎使用與介紹"
tags: ["basic_introduction"]
categories: other
---

## Introduction

Markdown是一種輕量級的標記式語言(用簡單句法描述簡單格式的文本語言)，使人可編寫簡單的文件，並轉成有效的HTML文件

Markdown語法的主要目的是用來作為一種web寫作的一種格式

其與HTML的部分有關，並非用來取代HTML的而是讓文件容易閱讀、編寫，也可在文件內編寫HTML，但要注意：markdown語法無法在HTML tag內被處理，以下介紹markdown的語法

## Basic syntax

### Block elements

#### Paragraphs and Line Breaks

一個段落是由一個以上相連接的行句組成，而一個以上的空行則會切分出不同的段落（空行的定義是顯示上看起來像是空行，便會被視為空行。比方說，若某一行只包含空白和tab，則該行也會被視為空行），一般的段落不需要用空白或斷行縮排。

如果想插入`<br />`標籤的話，在行尾加上兩個以上的空白，然後按enter。但這在Markdown中並不適合， Markdown中email式的blockquote(區塊引言)和多段落的list item在使用換行來排版的時候，不但更好用，還更好閱讀。

#### Headers

Markdown支援兩種header，Setext跟atx

Setex-style的header是用底線(underlined)的形式，以下為範例

```markdown
This is an H1
=============

This is an H2
-------------
```

Atx-style的header則是用1~6個`#`，例如

```markdown
# This is an H1

## This is an H2
```

#### Blockquotes

Blockquotes類似e-mail的區塊引言，只要在每一行的最前面加上`>`即可

```markdown
> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
> consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
> Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.
> 
> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
> id sem consectetuer libero luctus adipiscing.
```

也可在段落的第一行加上`>`

```markdown
> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.

> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
id sem consectetuer libero luctus adipiscing.
```

####List

Markdown支援有序(order)與無序(unordered)清單

#####Unorder(bulleted) list

可使用 `*` 或 `+` 或 `-` 作為清單標記

以下

```markdown
* red
* green
```

等同於

```markdown
+ red
+ green
```

以及

```markdown
- red
- green
```

#####Order(numbered) List

使用數字和點作為清單標記

```markdown
1.  Bird
2.  McHale
```

要注意的是前面的數字不影響HTML的輸出結果，上面的清單轉換成HTML會變成

```html
<ol>
	<li>Bird</li>
	<li>McHale</li>
</ol>
```

若是數字亂改，輸出結果也不變，但是不建議

如果清單項目間用空行分開， Markdown 會把項目的內容在輸出時用 `<p>` 標籤包起來，舉例來說：

```markdown
*   Bird
*   Magic
```

會轉換成

```html
<ul>
  <li>Bird</li>
  <li>Magic</li>
</ul>
```

但是

```markdown
* Bird

* Magic
```

則會變成

```html
<ul>
  <li><p>Bird</p></li>
  <li><p>Magic</p></li>
</ul>
```



list也可以包含多個段落，段落必須用四個空白或是tab來顯示如下

```markdown
1.  This is a list item with two paragraphs. Lorem ipsum dolor
    sit amet, consectetuer adipiscing elit. Aliquam hendrerit
    mi posuere lectus.

    Vestibulum enim wisi, viverra nec, fringilla in, laoreet
    vitae, risus. Donec sit amet nisl. Aliquam semper ipsum
    sit amet velit.

2.  Suspendisse id sem consectetuer libero luctus adipiscing.
```



在list裡使用引言(blockquote)，那`>`就必須使用縮排(intent)

```markdown
*   A list item with a blockquote:

    > This is a blockquote
    > inside a list item.
```



在list裡放入程式碼區塊(code block)，則要放入兩次縮排(八個空白或是兩個tab)

```markdown
*   A list item with a code block:
        <code goes here>
```



另外，有時可能不小心變成list，例如以下情況

```markdown
1986. What a great season.
```

要避免誤變成list，可加上反斜線`/`

```markdown
1986\. What a great season.
```

#### Code block

Markdown可用 `<pre>` 和 `<code>` 標籤來把程式碼區塊包起來。

建立程式碼只要縮排 4 個空白或是 1 個 tab 就即可

例如以下

```markdown
This is a normal paragraph:

    This is a code block.
```

將會轉換成

```html
<p>This is a normal paragraph:</p>

<pre><code>This is a code block.
</code></pre>
```

#### Horizontal rules

可使用以下方式產生分隔線`<hr />`

```markdown
* * *

***

*****

- - -

---------------------------------------
```



### SPAN ELEMENTS

#### Link

Markdown支援兩種形式的link：`inline` and `reference`，兩種形式的文字皆用square brackets `[]`表示

##### inline link

要產生inline link，需要在square brackets `[]`後加上regular parentheses `()`，其中regular parentheses內需要放入URL，若要加上link的title，則要在URL後面加上quotes，並將title文字放入quote內。

例如以下：

```markdown
This is [an example](http://example.com/ "Title") inline link.

[This link](http://example.net/) has no title attribute.
```

將產生

```html
<p>This is <a href="http://example.com/" title="Title">
an example</a> inline link.</p>

<p><a href="http://example.net/">This link</a> has no
title attribute.</p>
```

若要連接至同樣server的其他位置，也可使用relative paths

```markdown
See my [About](/about/) page for details.   
```

##### reference link

reference link是使用兩個square bracket組成，第一個中括號放入連結文字，第二個中括號則是一個標籤

```markdown
This is [an example][id] reference-style link.
```

兩個中括號間也可加上空白

```markdown
This is [an example] [id] reference-style link.
```

最後，在此文件的任意的一個地方則可以定義標籤連結，此連結不會出現在文件中，只有產生連結時會用到

```markdown
[id]: http://example.com/  "Optional Title Here"
```

連結的定義格式與順序：

1. 中括號，且裡面放入連結標籤((link identifier)
2. 放入colon，放入一或多個空白(tab也可)
3. 放入連結網址(URL)
4. (optional)放入空格，以及title，title可用single或double quotes，或者使用parentheses也可(Markdown.pl 1.0.1可能忽略單引號的title)

以下三種格式皆可

```markdown
[foo]: http://example.com/  "Optional Title Here"
[foo]: http://example.com/  'Optional Title Here'
[foo]: http://example.com/  (Optional Title Here)
```

URL也可用angle brackets包起來

```markdown
[id]: <http://example.com/>  "Optional Title Here"
```

title也可放在下一行，增加tab或space

```markdown
[id]: http://example.com/longish/path/to/resource/here
    "Optional Title Here"
```

link identifier可以使用letters, numbers, spaces, and punctuation(標點符號)，但連結不是case sensitive，所以以下兩個是相同的

```markdown
[link text][a]
[link text][A]
```

implicit link可以讓我們忽略link名稱，此種情況下，link文字與link名稱會視為相同，要使用implicit link，只需要在link文字後加一個square brackets，範例如下

```markdown
[Google][]
```

標籤連結則可定義如下

```markdown
[Google]: http://google.com/
```

標籤連結定義可以放在文件中的任一地方，建議可以放在段落後或文件後，類似註解

```markdown
I get 10 times more traffic from [Google] [1] than from
[Yahoo] [2] or [MSN] [3].

  [1]: http://google.com/        "Google"
  [2]: http://search.yahoo.com/  "Yahoo Search"
  [3]: http://search.msn.com/    "MSN Search"
```

若改用implicit link則是

```markdown
I get 10 times more traffic from [Google][] than from
[Yahoo][] or [MSN][].

  [google]: http://google.com/        "Google"
  [yahoo]:  http://search.yahoo.com/  "Yahoo Search"
  [msn]:    http://search.msn.com/    "MSN Search"
```

若使用inline link則是

```markdown
I get 10 times more traffic from [Google](http://google.com/ "Google")
than from [Yahoo](http://search.yahoo.com/ "Yahoo Search") or
[MSN](http://search.msn.com/ "MSN Search").
```

以上轉變成html：

```html
<p>I get 10 times more traffic from <a href="http://google.com/"
title="Google">Google</a> than from
<a href="http://search.yahoo.com/" title="Yahoo Search">Yahoo</a>
or <a href="http://search.msn.com/" title="MSN Search">MSN</a>.</p>
```

reference-style links的重點不在於好寫，而是較易讀，將標記資訊移到段落外來增加文章閱讀性。

#### Emphasis

Markdown使用asterisks `*`與 underscores `_`當作強調的符號。當文字使用一個`*`或`_`，將會轉成`<em>`，兩個`*`或`_`時，則會轉成`<strong>`，例如

```markdown
*single asterisks*

_single underscores_

**double asterisks**

__double underscores__
```

會轉成

```html
<em>single asterisks</em>

<em>single underscores</em>

<strong>double asterisks</strong>

<strong>double underscores</strong>
```

你可以使用各個符號，唯一限制是，若你用什麼符號開啟，就要用那個符號結束，另外若`*`與`_`兩邊都有空白時，只會被當成普通符號

另外若只是要插入普通的`*`或`_`，則可使用backslash

```markdown
\*this text is surrounded by literal asterisks\*
```

#### Code

若要加入一小段程式碼，可以使用反引號(backtick quotes (`` ` ``)將其包起來，例如

```markdown
Use the `printf()` function.
```

將產生

```html
<p>Use the <code>printf()</code> function.</p>
```

如果要在程式碼區段內插入反引號，你可以用多個反引號來開啟和結束程式碼區段：

```markdown
``There is a literal backtick (`) here.``
```

這段語法會產生：

```html
<p><code>There is a literal backtick (`) here.</code></p>
```

在程式碼的起始與結束端皆加上一個空白，即可加入backtick delimiters`` ` ``

```markdown
A single backtick in a code span: `` ` ``

A backtick-delimited string in a code span: `` `foo` ``
```

會產生：

```html
<p>A single backtick in a code span: <code>`</code></p>

<p>A backtick-delimited string in a code span: <code>`foo`</code></p>
```

在code的區段內，ampersands(`&`) and angle brackets`<>`會被自動轉為HTML實體(HTML entities)，例如

```markdown
Please don't use any `<blink>` tags.
```

會轉為

```html
<p>Please don't use any <code>&lt;blink&gt;</code> tags.</p>
```

也可如此

```markdown
`&#8212;` is the decimal-encoded equivalent of `&mdash;`.
```

轉為

```html
<p><code>&amp;#8212;</code> is the decimal-encoded
equivalent of <code>&amp;mdash;</code>.</p>
```

#### Images

Markdown可使用兩種格式去標記圖片：*inline*和_reference_

##### inline

inline-style的語法如下

```markdown
![Alt text](/path/to/img.jpg)

![Alt text](/path/to/img.jpg "Optional title")
```

格式與順序：

1. `!`起始
2. 接著放入square brackets`[]`，裡面放入`alt` attribute(文字的替代文字)
3. 放入一組parentheses`()`，裡面放入圖片的URL
4. (optional)放入title，可使用double或single quotes將其包起來

##### reference

reference-style的語法如下

```markdown
![Alt text][id]
```

`id`是圖片的reference，使用方式和link一樣

```markdown
[id]: url/to/image  "Optional title attribute"
```

目前markdown尚無語法可設定圖片的寬高，若有需要也可使用HTML的`<img>` tags.

### Other

#### Automatic links

Markdow支援簡短的形式產生URL和email，只要使用angle brackets將其包起來，則會變成link，例如：

```markdown
<http://example.com/>
```

將會轉成

```html
<a href="http://example.com/">http://example.com/</a>
```

email也類似，只是markdown會先進行部分隨機編碼，將文字轉成16進位，來混淆收集email的機器人(address-harvesting spambots)

```markdown
<address@example.com>
```

會轉成

```html
<a href="&#x6D;&#x61;i&#x6C;&#x74;&#x6F;:&#x61;&#x64;&#x64;&#x72;&#x65;
&#115;&#115;&#64;&#101;&#120;&#x61;&#109;&#x70;&#x6C;e&#x2E;&#99;&#111;
&#109;">&#x61;&#x64;&#x64;&#x72;&#x65;&#115;&#115;&#64;&#101;&#120;&#x61;
&#109;&#x70;&#x6C;e&#x2E;&#99;&#111;&#109;</a>
```

上面將轉換成可點擊的`address@example.com`連結。

(This sort of entity-encoding trick will indeed fool many, if not most, address-harvesting bots, but it definitely won’t fool all of them. It’s better than nothing, but an address published in this way will probably eventually start receiving spam.)

---

#### Backslash escapes

Markdown允許使用backslash`\`來產生一些語法中有意義的符號，對於下面符號提供backslash escapes功能

```
\   backslash
`   backtick
*   asterisk
_   underscore
{}  curly braces
[]  square brackets
()  parentheses
#   hash mark
+   plus sign
-   minus sign (hyphen)
.   dot
!   exclamation mark
```



## Extended Syntax

### Tables

新增表格，可以使用三個或以上的hyphens`---`來產生各個欄位的標頭，並使用pipes`|`來分隔各個欄位，最後也可以增添pipes在表格的最後面，如下範例：

```markdown
| Syntax      | Description |
| ----------- | ----------- |
| Header      | Title       |
| Paragraph   | Text        |
```

將會產生以下表格

| Syntax    | Description |
| --------- | :---------- |
| Header    | Title       |
| Paragraph | Text        |

#### Alignment

也可使用align，將colon`:`放在hyphen`-`的左、右、或者兩邊，如下範例

```markdown
| Syntax      | Description | Test Text     |
| :---        |    :----:   |          ---: |
| Header      | Title       | Here's this   |
| Paragraph   | Text        | And more      |
```

將產生以下表格

| Syntax    | Description |   Test Text |
| :-------- | :---------: | ----------: |
| Header    |    Title    | Here's this |
| Paragraph |    Text     |    And more |

### Fenced Code Blocks

可以使用三個backticks `` ``` ``，或者三個tildes `~~~`來產生code blocks

使用方式如下

```markdown
​```
{
  "firstName": "John",
  "lastName": "Smith",
  "age": 25
}
​```
```

此會產生

```
​```
{
  "firstName": "John",
  "lastName": "Smith",
  "age": 25
}
​```
```

若要加上syntax highlighting，只要在backticks之後加上使用的語言即可

使用方式如下

```
​```json
{
  "firstName": "John",
  "lastName": "Smith",
  "age": 25
}
​```
```

此會產生

```json
{
  "firstName": "John",
  "lastName": "Smith",
  "age": 25
}
```

### Footnotes

註解可使你添加note或reference，當產生一個註解時，會有一個上標(superscript)以及連結，觀看者可以點這個連結，並跳到註解。

要產生一個註解，需要在方括號(brackets)內添加插入符號(caret)和標識符(identifier)，例如`[^1]`，Identifiers可以是數字或文字，但不可以包含空格或tab

範例如下：

```markdown
Here's a simple footnote,[^1] and here's a longer one.[^bignote]

[^1]: This is the first footnote.

[^bignote]: Here's one with multiple paragraphs and code.

    Indent paragraphs to include them in the footnote.

    `{ my code }`

    Add as many paragraphs as you like.
```

會產生

Here's a simple footnote,[^1] and here's a longer one.[^bignote]

[^1]: This is the first footnote.

[^bignote]: Here's one with multiple paragraphs and code.

    Indent paragraphs to include them in the footnote.
    
    `{ my code }`
    
    Add as many paragraphs as you like.
### Definition Lists

產生定義清單的方式，首先先寫下一行字段，並在下一行輸入colon`:`，以及一個空白，並輸入解釋

使用方式如下：

```markdown
First Term
: This is the definition of the first term.

Second Term
: This is one definition of the second term.
: This is another definition of the second term.
```

其轉換成HTML如下

```html
<dl>
  <dt>First Term</dt>
  <dd>This is the definition of the first term.</dd>
  <dt>Second Term</dt>
  <dd>This is one definition of the second term. </dd>
  <dd>This is another definition of the second term.</dd>
</dl>
```

最後會轉換成

First Term
: This is the definition of the first term.

Second Term
: This is one definition of the second term.
: This is another definition of the second term.

### Strikethrough

可以使用horizontal line來做出刪除文字功能，只要使用兩個tilde symbols`~~`分別放在字段的左和右即可

```markdown
~~The world is flat~~. We now know that the world is round.
```

呈現結果如下

~~The world is flat~~. We now know that the world is round.

### Task Lists

任務清單(task lists)使你可產生一個列表的複選框(checkboxes)，只要加上dash`-`以及一個內部有空格的brackets`[ ]`即可，若要勾選只要在brackets中間加入`x`即可(`[x]`)

使用方法如下：

```markdown
- [x] Write the press release
- [ ] Update the website
- [ ] Contact the media
```

呈現結果如下

- [x] Write the press release
- [ ] Update the website
- [ ] Contact the media

## Reference

* [Basic Syntax Markdown Guide](https://www.markdownguide.org/basic-syntax/)
* [Extended Syntax](https://www.markdownguide.org/extended-syntax/)
* [Markdown - 維基百科](https://zh.wikipedia.org/wiki/Markdown)
* [Daring Fireball: Markdown Syntax Documentation](https://daringfireball.net/projects/markdown/syntax)
* [Markdown語法說明](https://markdown.tw/)


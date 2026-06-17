# Markdown 語法摘要
#### https://markdown.tw/
#### Markdown是一種輕量級標記式語言，創始人為約翰·格魯伯。它允許人們使用易讀易寫的純文字格式編寫文件，然後轉換成有效的XHTML文件。
#### Markdown的目標是實現「易讀易寫」。


## 標題

### Setext形式是用底線的形式，任何數量的=和-都可以有效果。

This is an H1 標題一
=============

This is an H2 標題二
-------------

### Atx形式則是在行首插入1到6個 # ，各對應到標題1到6階，例如：
# This is an H1 #

## This is an H2 ##

### This is an H3 ######

## 區塊引言

* Markdown使用email形式的區塊引言
 
> 這是區塊引言
> 這是第二行
> 第三行
> > 區塊引言可以有階層（例如：引言內的引言），只要根據層數加上不同數量的>

## 清單

> Markdown支援有序清單和無序清單。

> 無序清單使用星號、加號或是減號作為清單標記：

*   Red
+   Green
-   Blue

>有序清單則使用數字接著一個英文句點：(在清單標記上使用的數字並不會影響輸出的HTML結果)
3. 第一
2. 第二
5. 第三

>若使用**「階層式」的項目符號/編號**，只要記得空的格數要整齊即可。
+ 益者三友
  + 友直
  + 友諒
  + 友多聞
     + 益矣
+ 損者三友
  + 友便辟
  + 友善柔
  + 友便佞
     + 損矣

>要讓清單看起來更漂亮，你可以把內容用固定的縮排整理好：
*   Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
    Aliquam hendrerit mi posuere lectus. Vestibulum enim wisi,
    viverra nec, fringilla in, laoreet vitae, risus.
*   Donec sit amet nisl. Aliquam semper ipsum sit amet velit.
    Suspendisse id sem consectetuer libero luctus adipiscing.

>清單項目可以包含多個段落，每個項目下的段落都必須縮排4個空白或是一個tab：
1.  This is a list item with two paragraphs. Lorem ipsum dolor
    sit amet, consectetuer adipiscing elit. Aliquam hendrerit
    mi posuere lectus.

    Vestibulum enim wisi, viverra nec, fringilla in, laoreet
    vitae, risus. Donec sit amet nisl. Aliquam semper ipsum
    sit amet velit.

2.  Suspendisse id sem consectetuer libero luctus adipiscing.


>如果要在清單項目內放進引言，那>就需要縮排：
*   A list item with a blockquote:

    > This is a blockquote
    > inside a list item.


## 代辦事項
>代辦事項的語法如下，若要在框框中打勾，則需在 [ ] 中輸入 x。
- [ ] 高鐵
- [x] 火車
- [x] 捷運
- [ ] 公車



## 程式碼區塊
>縮排4個空白或是1個tab就可以
This is a normal paragraph:

    This is a code block.
    
## 程式碼段落
Use the \`printf()\` function.

Use the `printf()` function.

>如果要放程式碼區塊的話，該區塊就需要縮排兩次，也就是8個空白或是兩個tab：
*   A list item with a code block:

        <code goes here>

> 也是程式碼區塊

* 用\`\`\`將code前後包夾。

```
function test() {
  console.log("notice the blank line before this function?");
}
```

> 程式碼語法高亮

* 起頭用\`\`\` python，可替換成ruby python C++等等 
* * https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/creating-and-highlighting-code-blocks

`python`
```python
import numpy as np 
data = np.arange(6).reshape((3, 2))
print(np.average(data, axis=1, keepdims=True))
```
`Shell`
```ShellSession
sudo apt update
sudo apt install python3-dev python3-pip python3-venv
```

## 分隔線
    * * *

    ***

    *****

    - - -

    ---------------------------------------
* * *

***

*****

- - -

---------------------------------------


## 表格

>表格的每欄寬度會自動分配，所以可以忽略一切的空格(也就是每列的 | 沒有對齊也沒關係，且加多少空格也不會影響每欄的寬度)

| Column 1 | Column 2 | Column 3 |
| -------- | -------- | -------- |
| Text1 | Text2 | Text3 |
| Text4    | Text5    | Text6    |


| 預設對齊 | 靠左對齊 | 靠右對齊 | 置中對齊 |
| ----- | :----- | -----: | :-----: |
| Text1 | Text2 | Text3 | Text4 |
| Text5 | Text6 | Text7 | Text8 |
| Text9 | Text10 | Text11 | Text12 |


## 強調
    *single asterisks*
        _single underscores_
            **double asterisks**
                __double underscores__
*single asterisks*

_single underscores_

**double asterisks**

__double underscores__



## 圖片

\!\[Alt text](flow_sample_001.jpg "Optional title")

![Alt text](flow_sample_001.jpg "Optional title")

* github md 預設的[]()不能設定尺寸與對齊，僅能用html設定影像尺寸與對齊位置
* `![img](/temp_imgs/ms-coco-switch-anns.jpg)`
* 
![img](/temp_imgs/ms-coco-switch-anns.jpg) 不能設定尺寸與對齊
 
  
 * 設定影像大小 width="500" height="150" ，此處center無作用。
 * `<img src="/temp_imgs/ms-coco-switch-anns.jpg" width="500" height="150" class="center">`
  <img src="/temp_imgs/ms-coco-switch-anns.jpg" width="500" height="150" class="center">
   
 *  設定對齊中央 width=40% height=40% 
 *  `<p align="center"> <img width=40% height=40% src="/temp_imgs/ms-coco-switch-anns.jpg"> </p>`
  <p align="center">
    <img width=40% height=40% src="/temp_imgs/ms-coco-switch-anns.jpg">
  </p>
 
  
  
## 文字色彩
>這在jupyter notebook可行，但在github仍不支援色彩文字。

```Html
<span style="color:blue"> *Blue Italic.* </span>

<font color='red'> Red HTML </font>

<font color=#FF0000>紅色</font>

<font color=#0000FF>深藍色</font>
```
<span style="color:blue"> *Blue Italic.* </span>

<font color='red'> Red HTML </font>

<font color=#FF0000>紅色</font>

<font color=#0000FF>深藍色</font>

>在github上顯示顏色的其他方法
>https://stackoverflow.com/questions/11509830/how-to-add-color-to-githubs-readme-md-file


```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```

> 支援LaTeX：2022 年 5 月起，Github 可以接受Markdown 上的 LATEX 代碼，因此您可以使用\color{namecolor} inside the $ $ Block，
> https://github.blog/2022-05-19-math-support-in-markdown/

## $\color{#0fb503}{your-text-here}$

$${\color{red}Red}$$

${\color{red}Red}$

$${\color{blue}Blue}$$

${\color{blue}Blue}$

$k = {\color{red}x} \mathbin{\color{blue}-} 2$


## <font color=#0fb503>other color</font>
`<font color=#0fb503>other color</font> only work for ipynb`

## LaTex 
> LaTeX額外的

Press <kbd>W</kbd> to go up, and <kbd>A</kbd> to go down.
If you can find the <kbd>ESC</kbd>, pressing that will fire missiles 

GitHub contribution graph colors: `#C6E48B` `#7AC96F` `#249A3C` `#196127`

> LaTeX公式

When $a \ne 0$, there are two solutions to $(ax^2 + bx + c = 0)$ and they are 

$$ x = {-b \pm \sqrt{b^2-4ac} \over 2a} $$


## markdown 註解 comment

```HTML
<!-- markdown 註解 comment  單行-->

<!-- markdown 註解 comment  多行
markdown 註解 comment 
markdown 註解 comment 
-->
```
<!-- markdown 註解 comment  單行-->

<!-- markdown 註解 comment  多行
markdown 註解 comment 
markdown 註解 comment 
-->

## Emoji 表情符號
🔥🍏⚽🧌🚈🛞9⃣🆑㊙㊗🅰🕖⚡💦⭕️❌⛔️🚫㊙️🈲🚷➡️⬅️⬆️⬇️↗️↘️↙️1️⃣0️⃣🔊🔉🔇🔈📢💬🃏🕐🕘🏁📝🗄️📋📁📂📄📊🧪⚙️🖥️💻⌨️🖱️💿📷🚗🚀🛰️🚁🎲♟️🎰🫗✨☄️💥🔥☀️🌊🌈🌞⛑️💼🧠🗣️🦿🦾🛞

`網頁複製貼上` https://tw.piliapp.com/emoji/list/ 

`MacOS內建 本機就可以查詢 字元檢視器` 

文字區域中點滑鼠右鍵

<img src="https://user-images.githubusercontent.com/18000764/228112230-83b3aca9-7fe2-4d9a-9d03-016861e54fc2.png" width="200" height="150">

<img src="https://user-images.githubusercontent.com/18000764/228112329-073c2975-99cc-4e3f-a7be-08b6d1a93689.png" width="500" height="150">

***
# Jupyter notebook, lab 快速切換markdown與code cell
1. 在選擇到一個cell時：按`M`切換到markdown，按`Y`切換到code。
2. 若已經在一個cell中：按`Ese`+`M`，反之按`Ese`+`Y`
3. `A` insert cell above
4. `B` insert cell below
5. `X` cut selected cells
6. `C` copy selected cells
7. `V` paste cells below
8. `Shift + V` paste cells above
9. `DD` delete selected cells

* `Shift + Space` scroll notebook up ⬆️

* `Space`         scroll notebook down ⬇️

[todo] 一次編輯多行
Alt+mouse selection 然後 ←/→ 產生多行游標
cmd+mouse clicks在想要的數個地方設游標



* * *

在 GitHub 的 Markdown（GFM, GitHub Flavored Markdown）中，**原生 Markdown 不支援直接改變文字顏色或其他樣式特效**（如字體大小、背景色等），因為 GFM 限制了 HTML 和 CSS 的使用，以確保安全性和一致性。然而，有幾種方法可以實現文字顏色或特效，以下是詳細指南：

### 1. 使用 LaTeX 語法（透過 MathJax）
GitHub 支援在 Markdown 中使用 LaTeX（透過 MathJax）來渲染數學表達式，並可利用 LaTeX 的 `\color` 指令為文字上色。這種方法適用於 README.md、Issues 和 Pull Requests。

#### 語法：
使用美元符號 `$` 或雙美元符號 `$$` 包圍 LaTeX 語法：
```markdown
$\color{red}{這是紅色文字}$
```
或作為區塊：
```markdown
$$\color{blue}{這是藍色文字}$$
```

#### 示例：
```markdown
$\color{green}{成功} \space \color{red}{失敗} \space \color{#FF69B4}{粉紅色}$
```
**效果**：文字會以指定顏色顯示（綠色、紅色、粉紅色）。

#### 注意事項：
- **支援顏色**：可以使用顏色名稱（如 `red`、`blue`、`green`）或十六進位色碼（如 `#FF69B4`）。
- **限制**：
  - LaTeX 文字不可被選取或複製，且對螢幕閱讀器不友好（不利於無障礙存取）。[](https://gist.github.com/luigiMinardi/4574708d404cdf4fe0da7ac6fe2314db)
  - 在行動裝置（例如 GitHub Android 應用程式）上可能無法正確渲染。[](https://github.com/orgs/community/discussions/31570)
  - 不支援 h1 到 h6 標題（`#`）內的 LaTeX 顏色渲染（可能因 GitHub 更新而改變）。[](https://gist.github.com/luigiMinardi/4574708d404cdf4fe0da7ac6fe2314db)
- **進階樣式**：可結合 `\textsf` 或 `\textbf` 改變字體樣式，例如：
  ```markdown
  $\color{#58A6FF}\textsf{\large 提示文字}$
  ```
  這會顯示大號無襯線字體的藍色文字。

#### 適用場景：
適合用於簡單的單詞或短語上色，例如標示「成功」或「警告」。

---

### 2. 使用 Diff 語法高亮
GitHub 支援使用 Diff 語法（通常用於程式碼差異比較）來為文字添加紅色或綠色高亮，模擬顏色效果。

#### 語法：
在程式碼區塊中使用 `diff` 語言標記：
```markdown
```diff
- 紅色文字
+ 綠色文字
! 橙色文字
# 灰色文字
@@ 紫色加粗文字 @@
```
```

#### 示例：
```markdown
```diff
- 這是紅色文字
+ 這是綠色文字
! 這是橙色文字
```
```

#### 效果：
- `-`：紅色文字
- `+`：綠色文字
- `!`：橙色文字
- `#`：灰色文字
- `@@`：紫色加粗文字

#### 注意事項：
- 僅限於程式碼區塊，文字會以等寬字體顯示。
- 顏色選擇受限（僅紅、綠、橙、灰、紫）。
- 適合用於程式碼或技術文件中的簡單高亮。[](https://github.com/orgs/community/discussions/31570)[](https://medium.com/analytics-vidhya/writing-github-readme-e593f278a796)

---

### 3. 使用 HTML（有限支援）
GitHub 的 Markdown 對 HTML 支援有限，許多樣式標籤（如 `<span style="color:red">`）會被過濾掉。然而，某些 HTML 標籤在特定情境下可用，尤其是在 GitHub Pages 或非嚴格過濾的環境。

#### 語法：
```markdown
<span style="color:red;">這是紅色文字</span>
```
或使用 `<font>`（已棄用但部分支援）：
```markdown
<font color="blue">這是藍色文字</font>
```

#### 注意事項：
- **GitHub 限制**：GitHub 會移除大多數內聯 CSS 和 `<style>` 標籤，因此 `<span style="color:red;">` 在 README.md 中通常無效。[](https://bobbyhadz.com/blog/markdown-change-color-of-text)[](https://stackoverflow.com/questions/62933261/applying-color-in-a-markdown-file-on-github)
- **適用場景**：在 GitHub Pages 或某些支援 HTML 的 Markdown 解析器（如 Jupyter Notebook）中可能有效。[](https://clemensjarnach.github.io/02-articles/2023-05-02-article.html)[](https://bobbyhadz.com/blog/markdown-change-color-of-text)
- **替代方案**：若 HTML 被過濾，可嘗試嵌入 SVG 圖形來模擬顏色效果（見下方）。

---

### 4. 使用 SVG 圖形（進階）
透過嵌入 SVG 圖形，可以模擬彩色文字或背景效果，因為 SVG 被視為圖片，GitHub 不會過濾。

#### 語法：
```markdown
<img src="https://via.placeholder.com/150x20/FF0000/FFFFFF?text=紅色文字" alt="紅色文字">
```

或內聯 SVG：
```markdown
<svg width="100" height="20"><text x="0" y="15" fill="red">紅色文字</text></svg>
```

#### 示例：
```markdown
<svg width="200" height="30"><rect width="100%" height="100%" fill="#f0f0f0"/><text x="10" y="20" fill="blue">藍色文字</text></svg>
```

#### 注意事項：
- **優點**：可自訂文字顏色、背景色、大小等，且繞過 HTML 過濾。
- **缺點**：
  - 文字不可選取或搜尋，對無障礙存取不友好。[](https://gist.github.com/luigiMinardi/4574708d404cdf4fe0da7ac6fe2314db)
  - SVG 語法較複雜，適合進階使用者。
- **工具**：可使用線上 SVG 編輯器生成程式碼，或透過 placeholder 服務（如 `placehold.co`）快速生成彩色文字圖片。[](https://stackoverflow.com/questions/11509830/how-to-add-color-to-githubs-readme-md-file)

---

### 5. 使用 Emoji 或色塊
GitHub 支援 Emoji 和色塊來間接模擬顏色效果，特別適合簡單標記。

#### 語法：
```markdown
🔴 紅色標記
🟢 綠色標記
🟣 紫色標記
```

#### 示例：
```markdown
- 🔴 錯誤：請檢查設定
- 🟢 成功：已完成
```

#### 注意事項：
- **優點**：簡單、跨平台支援良好，且可複製。
- **缺點**：僅限於少數顏色，無法直接改變文字顏色，只能用於標記。
- **資源**：可參考 Emojipedia 查找更多 Emoji。[](https://www.markdownguide.org/extended-syntax/)

---

### 6. 其他特效
雖然顏色支援有限，GFM 提供其他格式化特效來增強視覺效果：

#### 粗體與斜體
```markdown
**粗體文字**
*斜體文字*
```

#### 刪除線
```markdown
~~刪除線文字~~
```

#### 程式碼區塊（語法高亮）
為程式碼添加語法高亮，模擬視覺區分：
```markdown
```python
print("這是 Python 程式碼")
```
```

#### 鍵盤標籤
使用 `<kbd>` 標籤模擬按鍵效果：
```markdown
按下 <kbd>Ctrl</kbd> + <kbd>C</kbd>
```

#### 摺疊內容
使用 `<details>` 和 `<summary>` 創建可摺疊區塊：
```markdown
<details>
<summary>點擊展開</summary>
這是隱藏內容
</details>
```

#### 警示框（Alerts）
使用引用語法模擬警示框：
```markdown
> **注意**：這是重要訊息
> **警告**：請小心操作
```

#### 表格
使用表格區分內容：
```markdown
| 狀態 | 說明 |
|------|------|
| 🟢  | 成功 |
| 🔴  | 失敗 |
```

---
* * * 



* * *
# Latex
LaTeX 字體大小指令	相對大小百分比	適用情境
\small	約 90%	雙欄排版橫跨兩欄（figure*）且檔名很短時
\footnotesize	約 80%	最推薦！ 標準三欄並排的黃金比例
\script_size (寫作 \scriptsize)	約 70%	檔名極長（例如超過20個字元）或單欄排版強行擠三欄時
<img width="432" height="189" alt="image" src="https://github.com/user-attachments/assets/b9ce42b0-ecb0-4e58-ba9c-1590f3c1e792" />




* * *

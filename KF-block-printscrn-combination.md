---
title: Keyboard Filter で [Windows キー + PrintScrn キー] 及び [Shift キー + PrintScrn キー] をフィルタする方法
date: 2021-04-19 12:00:00
categories:
- Keyboard Filter
tags:
- Keyboard Filter
---
Windows 10 IoT Enterprise (Enterprise もバイナリとして同じ) の Device Lockdown 機能の Keyboard Filter で [Windows キー + PrintScrn キー] 及び [Shift キー + PrintScrn キー] をフィルタする方法をご紹介します。  

<!-- more -->
<br>

***
## 方法
以下のドキュメントの Enable-rules.ps1 の Enable-Scancode を使います。  

- [Add blocked key combinations](https://docs.microsoft.com/en-us/windows-hardware/customize/enterprise/keyboardfilter-add-blocked-key-combinations)

Enable-rules.ps1 の 「# Some example uses of the functions defined above.」以降を、以下の様に置き換えます。
以下の変更を加えた状態で、Enable-rules.ps1 を実行した場合は、[Windows キー + PrintScrn キー] 及び [Shift キー + PrintScrn キー] のフィルターが有効化されます。

### Windows + PrintScrn
>Enable-Scancode -Modifiers "Win" -Code 0xE037 

### Shift + PrintScrn
>Enable-Scancode -Modifiers "Shift" -Code 0xE037 


***
## 補足
上記ドキュメントの Enable-Custom-Key で、以下の様な組み合わせを試した場合、PrintScrn キー単体と、[Alt キー+ PrintScrn キー] は問題なくフィルターできますが、[Windows キー + PrintScrn キー] 及び [Shift キー + PrintScrn キー] をフィルタすることはできません。  

### PrintScrn
>Enable-Custom-Key "PrintScrn"

### Alt + PrintScrn
>Enable-Custom-Key "Alt+PrintScrn"

### Windows + PrintScrn
>Enable-Custom-Key "Win+PrintScrn" 

### Shift + PrintScrn
>Enable-Custom-Key "Shift+PrintScrn"

これは不具合ではなく、想定された動作として取り扱われています。[Windows キー + PrintScrn キー] 及び [Shift キー + PrintScrn キー] のフィルタリングは、冒頭で紹介しました方法をご利用ください。

以上の内容がお役に立てば幸いです。


***
`変更履歴`  
`2021/04/19 created by Tsuda`  

※ 本記事は 「[jpiotblog について](https://jpiotblog.github.io/blog/2020/01/01/about-jpiotblog/)」 の留意事項に準じます。  
※ 併せて 「[ホームページ](https://jpiotblog.github.io/blog/)」 および 「[記事一覧](https://jpiotblog.github.io/blog/archives/)」 もご参照いただければ幸いです。  
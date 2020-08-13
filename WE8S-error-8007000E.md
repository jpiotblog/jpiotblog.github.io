---
title: Windows Embedded 8 Standard で Windows Update がエラー 8007000E で失敗する (関連 KB4571736)
date: 2020-08-13 12:00:00
categories:
- Windows Embedded 8 Standard
tags:
- Windows Update
- 8007000E
---
Windows Embedded 8 Standard で Windows Update がエラー 8007000E で失敗する現象の修正プログラム KB4571736 がリリースされましたのでご紹介します。
<!-- more -->
<br>

***
Windows Embedded 8 Standard で Windows Update がエラー 8007000E で失敗する現象の修正プログラム KB4571736 がリリースされました。

- [August 11, 2020-KB4571736 (Monthly Rollup)](https://support.microsoft.com/en-us/help/4571736/windows-server-2012-update)

上記サイトの修正プログラムは、Windows Update カタログ サイトから手動でダウンロードいただけます。

- [Windows Update カタログ (KB4571736)](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4571736)

修正される現象について、改めて以下にご紹介させていただきます。  

---
### KB4571736 適用によって修正される現象
Windows Embedded 8 Standard で Windows Update の 「更新プログラムの確認」 をクリックすると、以下の様に 8007000E エラー (E_OUTOFMEMORY、メモリ不足) で失敗します。  

![WE8S-error-8007000E.png](https://jpiotblog.github.io/images/WE8S-error-8007000E/WE8S-error-8007000E.png)

この現象は、どの環境でも再現するわけではありません。また、KB4571736 がリリースされるまでに Windows Update で配布される修正プログラムの重複の除外により、配布される容量の削減も行われ、再現率も低下しております。しかしながら、弊社で以下の条件の Hyper-V 仮想マシンで環境構築すれば、x86 でも x64 でも再現することを確認しました。

- 世代の指定で 「第 1 世代」 を選択
- メモリの割り当てで 「2048 MB」 を指定
- Windows Embedded 8 Standard のテンプレートに 「ThinClient」 を選択

---
### 回避方法の補足
KB4571736 を適用せずに回避する方法はございません。また、Windows Update そのものが失敗するエラーのため、本現象が発生する場合は、KB4571736 を手動で適用していただく必要があります。  
<br>

以上の内容がお役に立てば幸いです。  
***
`変更履歴`  
`2020/08/13 created by Tsuda`  
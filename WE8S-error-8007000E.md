---
title: Windows Embedded 8 Standard で Windows Update がエラー 8007000E で失敗する (KB4571736 関連)
date: 2020-08-13 12:00:00
categories:
- Windows Embedded 8 Standard
tags:
- Windows Update
- 8007000E
---
Windows Embedded 8 Standard において、Windows Update がエラー 8007000E で失敗する現象の対処方法をご紹介します。
<!-- more -->
<br>

***
Windows Embedded 8 Standard において、Windows Update がエラー 8007000E で失敗する現象が報告されております。この事象の修正を 2020 年 8 月のロールアップ更新プログラム (KB4571736) でリリースいたしました。  

- [August 11, 2020-KB4571736 (Monthly Rollup)](https://support.microsoft.com/en-us/help/4571736/windows-server-2012-update)

当該現象が確認されている端末にて、KB4571736 (または以降のロールアップ更新プログラム) を適用することで事象の改善が見込めます。すでに現象が発生している環境では、上記更新プログラムを Windows Update カタログ サイトから手動でダウンロードし、適用ください。  

- [Windows Update カタログ (KB4571736)](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4571736)

修正される現象について、改めて以下にご紹介させていただきます。  

---
## KB4571736 適用によって修正される現象
Windows Embedded 8 Standard で Windows Update の 「更新プログラムの確認」 をクリックすると、以下の様に 8007000E エラー (E_OUTOFMEMORY、メモリ不足) で失敗します。  

![WE8S-error-8007000E.png](https://jpiotblog.github.io/images/WE8S-error-8007000E/Error-8007000E.png)

この現象は、どの環境でも再現するわけではありません。また、KB4571736 がリリースされるまでに Windows Update で配布される修正プログラムの重複の除外により、配布される容量の削減も行われ、再現率も低下しております。しかしながら、弊社で以下の条件の Hyper-V 仮想マシンで環境構築すれば、x86 でも x64 でも再現することを確認しました。

- 世代の指定で 「第 1 世代」 を選択
- メモリの割り当てで 「2048 MB」 を指定
- Windows Embedded 8 Standard のテンプレートに 「ThinClient」 を選択

<br>

以上の内容がお役に立てば幸いです。  
***
`変更履歴`  
`2020/08/13 created by Tsuda`  
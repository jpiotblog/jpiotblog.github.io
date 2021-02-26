---
title: WMI を使って UWF を操作する方法
date: 9999-12-31 00:00:00
categories:
- template
tags:
- テンプレ
---
テンプレート
<!-- more -->
<br>

***

https://docs.microsoft.com/en-us/windows-hardware/customize/enterprise/uwf-filter

```powershell
PS C:\Windows\system32> $uwf = Get-WMIObject -class UWF_Filter -namespace "root\standardcimv2\embedded"
PS C:\Windows\system32> $uwf

__GENUS          : 2
__CLASS          : UWF_Filter
__SUPERCLASS     :
__DYNASTY        : UWF_Filter
__RELPATH        : UWF_Filter.Id="UWF_Filter"
__PROPERTY_COUNT : 5
__DERIVATION     : {​}​
__SERVER         : DESKTOP-H44UL9P
__NAMESPACE      : root\standardcimv2\embedded
__PATH           : \\DESKTOP-H44UL9P\root\standardcimv2\embedded:UWF_Filter.Id="UWF_Filter"
CurrentEnabled   : True
HORMEnabled      : False
Id               : UWF_Filter
NextEnabled      : True
ShutdownPending  : False
PSComputerName   : DESKTOP-H44UL9P
PS C:\Windows\system32> $uwf.Disable()

__GENUS          : 1
__CLASS          : __PARAMETERS
__SUPERCLASS     :
__DYNASTY        : __PARAMETERS
__RELPATH        : __PARAMETERS
__PROPERTY_COUNT : 1
__DERIVATION     : {​}​
__SERVER         : DESKTOP-H44UL9P
__NAMESPACE      : ROOT\standardcimv2\Embedded
__PATH           : \\DESKTOP-H44UL9P\ROOT\standardcimv2\Embedded:__PARAMETERS
ReturnValue      : 0
PSComputerName   : DESKTOP-H44UL9P
```


# テンプレート
## テンプレートファイルについて
これはテンプレートファイルです。このファイルはサイトから参照されません。

<img src="https://jpiotblog.github.io/images/template.png" width=300px align="left" border="1"><br clear="left">

***
`変更履歴`  
`9999/12/31 created by ******`  

※ 本記事は 「[jpiotblog について](https://jpiotblog.github.io/blog/2020/01/01/about-jpiotblog/)」 の留意事項に準じます。  
※ 併せて 「[ホームページ](https://jpiotblog.github.io/blog/)」 および 「[記事一覧](https://jpiotblog.github.io/blog/archives/)」 もご参照いただければ幸いです。  
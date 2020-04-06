---
title: UWF の除外を推奨しないパスについて
date: 2020-02-19 00:00:00
categories:
- Unified Write Filter
tags:
- 除外設定
---
UWF の除外設定を実施するにあたり、留意いただきたい内容をお纏めいたします。
<!-- more -->
<br>

***
システムの起動の初期段階でアクセスするフォルダ (C:\Windows 配下など) は、他のデータと一貫性を持っています。一部のフォルダーを除外し、関連するその他の情報と一貫性が崩れた場合、システムがハンドルできない問題が発生し「SYSTEM THREAD EXCEPTION NOT HANDLED」エラーが発生する場合があります。これは、アクセスできる情報としてアクセスしようとしたら失敗 (Access violation の発生) することでシステムの起動が進められなくなるためです。  
一般的に、設定を推奨しない除外パスは、公開情報 [書き込みフィルターの除外](https://docs.microsoft.com/ja-jp/windows-hardware/customize/enterprise/uwfexclusions) を参照ください。なお、現在公開情報への追加リクエスト中の内容となりますが、以下も併せてご確認ください。  

- C:\Windows\WinSXS フォルダーを除外すると OS 起動時に BSOD が発生する  

   Winodws 10 Enterprise 2019 LTSC では、C:\Windows\WinSXS フォルダーを除外するとシステム起動時に 「SYSTEM THREAD EXCEPTION NOT HANDLED」 で BSOD が発生するという報告がございます。C:\Windows\WinSXS フォルダー配下にはシステム起動初期にアクセスする情報が含まれておりますため、除外をお勧めいたしません。  

- Windows Defender のレジストリを除外すると OS 起動時にハングアップする  

   下記レジストリ キーを除外していると、OS 起動時にハングアップして起動できない場合があります。  

   ・ `HKLM\System\CurrentControlSet\Services\WdFilter`  
   ・ `HKLM\SYSTEM\ControlSet001\Control\DeviceClasses\{53f5630d-b6bf-11d0-94f2-00a0c91efb8b}`  

   レースコンディションによるものであり、スペックの低い端末で発生する傾向にあります。OS 側での修正は困難であるため、Wdfilter の除外解除等をご検討ください。  

   Windows 10 2016 LTSB の環境では下記除外が入っていた場合にも同様の問題が発生するという報告がございます。該当する場合にはこちらの除外解除もご検討ください。  

   ・ `HKLM\SOFTWARE\Classes\TypeLib\{8C389764-F036-48F2-9AE2-88C260DCF43B}`  
   ・ `HKLM\SOFTWARE\Classes\CLSID\{A2D75874-6750-4931-94C1-C99D3BC9D0C7}`  
   ・ `HKLM\SOFTWARE\Classes\CLSID\{195B4D07-3DE2-4744-BBF2-D90121AE785B}`  

なお、UWF を有効にした環境で OS 再起動後も情報を維持したい製品がある場合、必要な除外設定につきましては対象製品サポートへお問い合わせいただく必要がございます。除外設定の追加によって何らかの問題が発生した場合にも、除外対象パスを使用するプロセス側にて調査が必要となりますため、対象の製品サポートにて事例の有無や対処方法をお問い合わせください。
***

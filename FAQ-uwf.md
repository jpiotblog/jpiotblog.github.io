---
title: FAQ (Unified Write Filter)
date: 2020-01-12 00:00:00
categories:
- Unified Write Filter
tags:
- FAQ
---

現在更新中です。
---
### “UWF Servicing Mode” で WSUS の設定が参照されない
UWF の不具合として、2019 年 9 月 (Windows 10 1903 のみ 2019 年 10 月) の更新プログラムで修正しております。


### Windows Defender のレジストリを除外すると OS 起動時にハングアップする
“HKLM\System\CurrentControlSet\Services\WdFilter” や、”HKLM\SYSTEM\ControlSet001\Control\DeviceClasses\{53f5630d-b6bf-11d0-94f2-00a0c91efb8b}” を除外設定にしていると、OS 起動時にハングアップして起動できない場合があります。レースコンディションによるものであり、スペックの低い端末で発生する傾向にあります。OS 側での修正は困難であるため、Wdfilter の除外解除等をご検討ください。


### “uwfmgr.exe filter enable” コマンドを実行すると保護対象ボリュームに配置された Pagefile.sys が削除される
初回の ”uwfmgr.exe filter enable” コマンドの実行時に Pagefile に関するレジストリ値を操作する処理が実施される為です。回避策としては、”uwfmgr.exe filter enable” コマンドを実行した後、再起動の直前にもう一度 Pagefile.sys の設定を保護対象ボリュームに対して実施する必要があります。







---
title: FAQ (Unified Write Filter)
date: 2020-01-16 00:00:00
categories:
- Unified Write Filter
tags:
- FAQ
---

よくあるご質問とその回答をお纏めいたします。
<!-- more -->
<br>

***
## 参考となる公開情報について
- [統合書き込みフィルター (UWF) 機能](https://docs.microsoft.com/ja-jp/windows-hardware/customize/enterprise/unified-write-filter)  
> 機能の概要、要件、制限事項、各機能の紹介など  
- [Unified Write Filter (UWF) 機能を使用します。](https://docs.microsoft.com/ja-jp/windows-hardware/customize/enterprise/uwf-turnonuwf)  
> UWF の有効化方法  
- [統合書き込みフィルター (UWF) オーバーレイの位置とサイズ](https://docs.microsoft.com/ja-jp/windows-hardware/customize/enterprise/uwfoverlay)  
> オーバーレイに関する情報  
- [書き込みフィルターの除外](https://docs.microsoft.com/ja-jp/windows-hardware/customize/enterprise/uwfexclusions)  
> UWF 機能の除外設定について  
- [UWF で保護されているデバイスのサービス](https://docs.microsoft.com/ja-jp/windows-hardware/customize/enterprise/service-uwf-protected-devices)  
> UWF を有効化している環境で Windows 更新プロブラムやマルウェア対策ソフトのシグネチャを更新する方法について  
- [Unified Write Filter (UWF) のトラブルシューティング](https://docs.microsoft.com/ja-jp/windows-hardware/customize/enterprise/uwftroubleshooting)  
> トラブルシューティングに関する情報  
> UWF に関する情報採取手順は、[こちら](https://jpiotblog.github.io/files/CollectInfo_UWF.md "") を参照ください。
***
## UWF 適用環境で Windows Update 時に CPU が高くなる時がある  
WmiPrvSE.exe プロセスが uwfwmi.dll にて UWF のオーバーレイ ファイルを取得する処理に CPU を消費する傾向があります。こちらは仕様に基づく動作となります。
***
## “UWF Servicing Mode” 実行時に、WSUS の設定が参照されない  
UWF の不具合として、2019 年 9 月 (Windows 10 1903 のみ 2019 年 10 月) の更新プログラムで修正しております。  
***
## “uwfmgr.exe filter enable” コマンドを実行すると保護対象ボリュームに配置された Pagefile.sys が削除される  
初回の `uwfmgr.exe filter enable` コマンドの実行時に Pagefile に関するレジストリ値を操作する処理が実施される為です。回避策としては、`uwfmgr.exe filter enable` コマンドを実行した後、再起動の直前にもう一度 Pagefile.sys の設定を保護対象ボリュームに対して実施する必要があります。  
***
## 長期間 (例: 24 時間 365 日) 稼働するシステムで UWF を有効化し、オーバーレイ領域を枯渇させない方法はあるか
オーバーレイ領域の消費傾向は、その環境の利用方法に依存いたします。ご利用方法に依存せず、オーバーレイ領域の消費を節約する汎用的な方法はございません。  
UWF 有効化した環境で長期間システムを起動し続ける場合、システムを通常利用した場合にどの程度オーバーレイが消費するか運用テストを最低でも 1 週間単位で実施いただくことをお勧めいたします。ウイルス対策ソフトウェアの定義ファイル更新を含める場合には、最低でも 1 ヶ月以上の運用テストを実施ください。結果を基にオーバーレイ領域が枯渇しないよう容量を確保するか、システムのご利用方法を調整ください。  
***
<br>
※ 現在追加更新中です。

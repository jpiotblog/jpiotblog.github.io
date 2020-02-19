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
## UWF の除外に追加したら OS が正常に起動しなくなる 
システムの起動の初期段階でアクセスするフォルダ (C:\Windows 配下など) は、他のデータと一貫性を持っています。一部のフォルダーを除外し、関連するその他の情報と一貫性が崩れた場合、システムがハンドルできない問題が発生し「SYSTEM THREAD EXCEPTION NOT HANDLED」エラーが発生する場合があります。これは、アクセスできる情報としてアクセスしようとしたら失敗 (Access violation の発生) することでシステムの起動が進められなくなるためです。  
設定を推奨しない除外パスは、公開情報 [書き込みフィルターの除外](https://docs.microsoft.com/ja-jp/windows-hardware/customize/enterprise/uwfexclusions) を参照ください。なお、現在公開情報への追加リクエスト中の情報として、以下も併せてご確認ください。  

- C:\Windows\WinSWX フォルダーを除外すると OS 起動時に BSOD が発生する  

   Winodws 10 Enterprise 2019 LTSC では、C:\Windows\WinSWX フォルダーを除外するとシステム起動時に 「SYSTEM THREAD EXCEPTION NOT HANDLED」 で BSOD なるという報告がございます。  

なお、UWF の除外対象にしたい製品の具体的な除外パスについてお問い合わせいただく場合、各対象製品サポートへお問い合わせくださいますようお願いいたします。Windows Defender については以下のような事例がございますので、併せてご紹介いたします。  

- Windows Defender のレジストリを除外すると OS 起動時にハングアップする  

   レジストリ キー `HKLM\System\CurrentControlSet\Services\WdFilter` や `HKLM\SYSTEM\ControlSet001\Control\DeviceClasses\{53f5630d-b6bf-11d0-94f2-00a0c91efb8b}` を除外設定にしていると、OS 起動時にハングアップして起動できない場合があります。レースコンディションによるものであり、スペックの低い端末で発生する傾向にあります。OS 側での修正は困難であるため、Wdfilter の除外解除等をご検討ください。  
***
## 長期間 (例: 24 時間 365 日) 稼働するシステムで UWF を有効化し、オーバーレイ領域を枯渇させない方法はあるか
オーバーレイ領域の消費傾向は、その環境の利用方法に依存いたします。ご利用方法に依存せず、オーバーレイ領域の消費を節約する汎用的な方法はございません。  
UWF 有効化した環境で長期間システムを起動し続ける場合、システムを通常利用した場合にどの程度オーバーレイが消費するか運用テストを最低でも 1 週間単位で実施いただくことをお勧めいたします。更新プログラムの適用までを含める場合には、最低でも 1 ヶ月以上の運用テストを実施ください。結果を基にオーバーレイ領域が枯渇しないよう容量を確保するか、システムのご利用方法を調整ください。  
***
<br>
※ 現在追加更新中です。

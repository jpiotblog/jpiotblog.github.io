---
title: FAQ (Unified Write Filter)
date: 2020-01-16 00:00:00
categories:
- Unified Write Filter
tags:
- FAQ
---
UWF 機能に関してよくあるご質問とその回答をまとめております。
<!-- more -->
<br>

***
#### 製品を理解するために参考となる公開情報を教えて欲しい
<details><summary style="font-size: 10pt">回答</summary>

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
</details>

***
#### Windows Update 時に CPU が高くならないようにしたい
<details><summary style="font-size: 10pt">回答</summary>

WmiPrvSE.exe プロセスが uwfwmi.dll にて UWF のオーバーレイ ファイルを取得する処理に CPU を消費する傾向があります。こちらは仕様に基づく動作となります。
</details>

***
#### “UWF Servicing Mode” 実行時に WSUS の設定が参照されないのは不具合か
<details><summary style="font-size: 10pt">回答</summary>

UWF の不具合として、2019 年 9 月 (Windows 10 1903 のみ 2019 年 10 月) の更新プログラムで修正しております。  
</details>

***
#### フィルター有効化時にページファイル (Pagefile.sys) が削除されないようにしたい
<details><summary style="font-size: 10pt">回答</summary>

初回の `uwfmgr.exe filter enable` コマンドの実行時に Pagefile に関するレジストリ値を操作する処理が実施される為です。回避策としては、`uwfmgr.exe filter enable` コマンドを実行した後、再起動の直前にもう一度 Pagefile.sys の設定を保護対象外のボリュームに対して実施する必要があります。  
</details>

***
#### UWF を有効化して利用開始する際のベストプラクティスについて
<details><summary style="font-size: 10pt">回答</summary>

以下の実施についてご留意ください。  

- UWF には複数の既知の不具合が存在します。Windows Update を最新まで適用してご利用を開始ください。

- 除外設定によりシステム上同期を取る必要がある情報に矛盾が生じ、何らかの問題を発生させる可能性がございます。除外設定は以下サイトの情報を参考に実施ください。  
   [UWF の除外設定について](https://jpiotblog.github.io/blog/2020/02/19/UWF-exclusions/)

- OS 再起動を定期的に行わないシステムの場合、UWF のオーバーレイ領域の不足によって問題が発生する可能性があります。下記サイトの記事を参考に、オーバーレイの設定をご検討ください。  
   [長期間稼働するシステムでの UWF 有効化について](https://jpiotblog.github.io/blog/2020/02/20/UWF-longtermup/)

</details>

***
<br>
※ 適宜追加更新します。

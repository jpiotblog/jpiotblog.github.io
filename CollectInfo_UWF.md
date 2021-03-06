---
title: UWF 関連トラブル時の情報採取手順について
date: 2020-01-26 00:00:00
categories:
- Unified Write Filter (UWF)
tags:
- 情報採取手順
---
UWF 機能に関連するトラブル発生時の情報採取手順を紹介します。
<!-- more -->
<br>

***
## 対象
事象発生マシン (OS: Windows 10 Enterprise または Windows 10 IoT Core, Windows 10 IoT Enterprise)  

## 事前準備
[こちら](https://jpiotblog.github.io/files/CollectInfo_UWF.txt "CollectInfo_UWF.txt") から CollectInfo_UWF.txt ファイルをダウンロードし、拡張子を .txt から .bat に変更、実施対象の任意の場所に格納します。  

<実施対象に以下が存在する状態>  
・ [任意の場所]\CollectInfo_UWF.bat ファイル

## 影響
ログを採取することで負荷が上がる可能性は考えられますが、基本的に OS リソースや処理への影響はありません。  

## 実行手順
(1) 事象発生マシンに管理者アカウントでログオンします。  
(2) CollectInfo_UWF.bat ファイルを右クリックし、[管理者として実行] を選択します。  
(3) "Please enter the number you want to execute. Enter q to quit tool." メッセージに `1` を入力、リターン キーを押下し、表示に従いメニューに戻ります。  
(4) "Please enter the number you want to execute. Enter q to quit tool." メッセージに `q` を入力、リターン キーを押下してツールを終了します。  
(5) デスクトップ上 `<YYYYMMDD 形式の年月日>_<ホスト名>_info` フォルダーを圧縮し、お問い合わせいただく際にご提供ください。  

***
本記事は 「[jpiotblog について](https://jpiotblog.github.io/blog/2020/01/01/about-jpiotblog/)」 の留意事項に準じます。  

`変更履歴`  
`2020/01/26 created by Mochizuki`
`2020/08/31 modified by Mochizuki`
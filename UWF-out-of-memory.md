---
title: UWF 有効化環境で Windows Update 実行時に Out Of Memory が発生する
date: 2020-08-21 12:00:00
categories:
- Unified Write Filter
tags:
- Out Of Memory
- UWF servicing mode
---
UWF 有効化環境で LCU (累積更新プログラム) の適用時に Out Of Memory が発生する事象について紹介します。
<!-- more -->
<br>

***
### 環境
事象が発生することが確認出来ている環境の条件は、以下全てを満たす場合です。  
- Windows 10 Enterprise 2016 LTSB または Windows 10 IoT Enterprise 2016 LTSB
- 物理メモリ (RAM) が 4 GB 以下
- UWF でシステム使用領域 (通常は C ドライブ) を保護している

---

### 事象
UWF を有効化している環境で Windows Update を適用する方法は、主に以下 2 通りになります。

- uwfmgr.exe filter disable を実行して OS 再起動し、手動で Windows Update を実行する。  
- uwfmgr.exe servicing enable を実行して[UWF サービスモード](https://docs.microsoft.com/ja-jp/windows-hardware/customize/enterprise/service-uwf-protected-devices)を有効化する。  

この時、上述の適用環境に該当するマシンでは Out Of Memory が発生し、更新が失敗する事象が確認されています。 

---
### 原因
Windows 10 Enterprise 2016 LTSB および Windows 10 IoT Enterprise 2016 LTSB は、リリースから長期間が経過していることから、LCU (累積更新プログラム) が持つメタ データのサイズが非常に大きくなっており、処理の過程でメモリ不足 (Out Of Memory) が発生することが知られております。  
このような問題の一般的な対処策として、ページング ファイルを有効化する方法があります。しかし、UWF を有効化したタイミングでページング ファイルは無効化されるため、UWF の利用開始がこの問題を発生させるトリガーとなる場合がございます。  

---
### 対処方法
以下いずれかの方法を実施することで、事象を回避できる可能性があります。  

- RAM を増設する
- ドライブを追加しページング ファイルの出力先に設定する
- UWF フィルターの無効化後、ページング ファイルを設定してから Windows Update を実行する

3 点目の運用回避方法につきまして、以下に具体的な手順を紹介させていただきます。  

</br>
<b><u>運用手順</u></b>

(1) 以下のコマンドを実行して、UWF フィルターを無効化します (※ コマンド実行後、OS が再起動します)。
```
uwfmgr.exe filter disable
shutdown /r /t 0
```

(2) 以下のコマンドを実行して、ページング ファイルを設定します (コマンド実行後、OS が再起動します)。  
※ GUI で実行する場合は [こちら](https://jpiotblog.github.io/blog/2020/08/21/UWF-out-of-memory/#2-%E3%81%AE%E6%89%8B%E9%A0%86%E3%82%92-GUI-%E3%81%A7%E5%AE%9F%E8%A1%8C%E3%81%99%E3%82%8B%E6%96%B9%E6%B3%95) を参照ください。  
```
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Session Manager\Memory Management" /v PagingFiles /t REG_MULTI_SZ /d "c:\pagefile.sys 4396 4396" /f
shutdown /r /t 0
```

(3) 手動での Windows Update を実行します。

(4) 以下のコマンドを実行して、ページング ファイルの設定を削除します (コマンド実行後、OS が再起動します)。  
※ GUI で実行する場合は [こちら](https://jpiotblog.github.io/blog/2020/08/21/UWF-out-of-memory/#4-%E3%81%AE%E6%89%8B%E9%A0%86%E3%82%92-GUI-%E3%81%A7%E5%AE%9F%E8%A1%8C%E3%81%99%E3%82%8B%E6%96%B9%E6%B3%95) を参照ください。  
```
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Session Manager\Memory Management" /v PagingFiles /t REG_MULTI_SZ /d "" /f
shutdown /r /t 0
```

(5) 以下のコマンドを実行して、UWF フィルターを有効化します (コマンド実行後、OS が再起動します)。  
```
uwfmgr.exe filter enable
shutdown /r /t 0
```

---
### 補足情報 
上述の手順 (2) および (4) は GUI からも実行頂けます。レジストリを直接操作することに制限がございます場合、以下の手順を参考に実施ください。  

#### (2) の手順を GUI で実行する方法
(2-1) Ctrl + R キーを押下し、[名前] 欄に sysdm.cpl と入力して [OK] ボタンをクリックします。  
(2-2) [システムのプロパティ] ダイアログの [詳細設定] タブ - [設定] ボタンをクリックします。  
(2-3) [パフォーマンス オプション] ダイアログの [詳細設定] タブ - [変更] ボタンをクリックします。  
(2-4) [仮想メモリ] ダイアログの [すべてのドライブのページング ファイルのサイズを自動的に管理する] のチェックを外し、[カスタム サイズ] にチェックを入れ、初期サイズと最大サイズに 4396 を入力、[設定] ボタンをクリックし、[OK] ボタンをクリックします。

<img src="https://jpiotblog.github.io/images/UWF-out-of-memory/Virtual-memory-2-4.png" width=300px>  

(2-5) OS の再起動を促すダイアログがポップアップした場合、指示に従い再起動します。表示されなかった場合には、手動で OS を再起動します。  

#### (4) の手順を GUI で実行する方法
(4-1) Ctrl + R キーを押下し、[名前] 欄に sysdm.cpl と入力して [OK] ボタンをクリックします。  
(4-2) [システムのプロパティ] ダイアログの [詳細設定] タブ - [設定] ボタンをクリックします。  
(4-3) [パフォーマンス オプション] ダイアログの [詳細設定] タブ - [変更] ボタンをクリックします。  
(4-4) [仮想メモリ] ダイアログの [ページング ファイルなし] にチェックを入れ、[設定] ボタンをクリックし、[OK] ボタンをクリックします。

<img src="https://jpiotblog.github.io/images/UWF-out-of-memory/Virtual-memory-4-4.png" width=300px>  

(4-5) 続行するか確認のダイアログがポップアップした場合、[はい] を選択します。
(4-6) OS の再起動を促すダイアログがポップアップした場合、指示に従い再起動します。表示されなかった場合には、手動で OS を再起動します。  

***
`変更履歴`  
`2020/08/01 created by Mochizuki`  
`2020/08/21 modified by Mochizuki`  
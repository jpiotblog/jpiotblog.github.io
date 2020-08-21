---
title: UWF 有効化環境で Windows Update 実行時に Out Of Memory が発生する
date: 2020-08-21 12:00:00
categories:
- Unified Write Filter
tags:
- Out Of Memory
- UWF servicing mode
---
UWF 有効化環境で LCU (累積更新プログラム) の適用時に Out Of Memory で発生する事象について紹介します。
<!-- more -->
<br>

***
### 適用環境
- Windows 10 Enterprise 2016 LTSB
- Windows 10 IoT Enterprise 2016 LTSB
- 物理メモリが 4 GB
- UWF でシステム使用領域を保護している

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

(2) 以下のコマンドを実行して、ページング ファイルを設定します (※ コマンド実行後、OS が再起動します)。
```
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Session Manager\Memory Management" /v PagingFiles /t REG_MULTI_SZ /d "c:\pagefile.sys 4396 4396" /f
shutdown /r /t 0
```

(3) 手動での Windows Update を実行します。

(4) 以下のコマンドを実行して、ページング ファイルの設定を削除します (※ コマンド実行後、OS が再起動します)。  
```
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Session Manager\Memory Management" /v PagingFiles /t REG_MULTI_SZ /d "" /f
shutdown /r /t 0
```

(5) 以下のコマンドを実行して、UWF フィルターを有効化します (※ コマンド実行後、OS が再起動します)。  
```
uwfmgr.exe filter enable
shutdown /r /t 0
```

***
`変更履歴`  
`2020/08/01 created by Mochizuki`  
`2020/08/21 modified by Mochizuki`  
---
title: UWF 有効化環境で HORM を使用する際の留意点について
date: 2020-12-1 12:00:00
categories:
- Unified Write Filter (UWF)
tags:
- uwfmgr.exe
- Hibernate Once/Resume Many (HORM)
---
Unified Write Filter (UWF) と共に Hibernate Once/Resume Many (HORM) 機能を使用する際の留意点を紹介いたします。  

<!-- more -->
<br>
統合書き込みフィルター (UWF) と共に Hibernate Once/Resume Many (HORM) 機能を使用することで、事前に構成された状態でデバイスを起動することが可能になります。HORM が有効になると、システムは常に最後に保存された休止状態ファイル (hiberfil.sys) から再開および再起動されます。機能の有効化など具体的な手順は、下記サイトを参照ください。  

- [Hibernate Once/Resume Many (HORM)](https://docs.microsoft.com/ja-jp/windows-hardware/customize/enterprise/hibernate-once-resume-many-horm-)

本記事では、HORM を有効化する際の留意点をお纏めいたします。  

***
## 要件等
具体的な要件は以下サイトに纏めておりますので、ご参照ください。  

- [要件](https://docs.microsoft.com/ja-jp/windows-hardware/customize/enterprise/hibernate-once-resume-many-horm-#requirements)
  >Windows 10 Enterprise、Windows 10 の教育、または Windows IoT Core (IoT Core)。 X86 ベースおよび x64 ベースのデバイスでサポートされています。  

- [UWF 構成](https://docs.microsoft.com/ja-jp/windows-hardware/customize/enterprise/hibernate-once-resume-many-horm-#uwf-configuration)
  >HORM を有効または無効にするには、その前に UWF を有効にする必要があります。 UWF は、休止ファイルが無効にならないように保護するために、次の方法で構成する必要があります。  
  >- システムにマウントされているすべての固定ボリュームは、UWF によって保護されている必要があります。  
  >- システムで、UWF 用に構成されたファイル、フォルダー、またはレジストリの除外を使用することはできません。  
  >- UWF オーバーレイは RAM モードを使用するように構成されている必要があります。 HORM では、ディスクを利用するオーバーレイはサポートされていません。

***
## 状態確認方法
HORM の有効/無効状態は uwfmgr.exe get-config コマンドの結果から確認することが出来ません。そのため、下記レジストリ値などから確認を行うことをお勧めしております。  

`キー: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\uwfvol\Parameters\Dynamic`  
`名前: HormEnabled`  
`種類: DWORD`  
`値: (0: 無効 / 1 有効)`  

コマンドで確認する場合は、以下の通り実行ください。  

```
reg query "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\uwfvol\Parameters\Dynamic" /v "HormEnabled"
```

なお、ufgmgr ツールで HORM 有効/無効化する際のエラーメッセージからも状態を確認することが可能です。例えば、uwfmgr.exe filter enable-horm コマンドを実行時に、既に有効になっている場合以下の通りメッセージが出力します。  
>Unified Write Filter already disabled - no action taken.  

uwfmgr.exe filter disable-horm コマンドを実行時に、既に無効になっている場合以下の通りメッセージが出力します。  
>Unified Write Filter already enabled - no action taken.  

***
## 特定のドライブを保護せずに HORM を有効化する方法
HORM 機能を有効化する際、全てのボリュームを保護しておくことが前提となりますが、一部のボリュームを保護せずに使用したい場合がありす。UWF の前身である EWF では、HORM を有効化する際に保護対象外のボリュームを dismount することで対応可能といった記事がございますが、UWF でも同様の方法が利用いただけます。  

- [Use an Unprotected Volume in a Hibernate Once/Resume Many Environment (Standard 7 SP1)](https://docs.microsoft.com/en-us/previous-versions/windows/embedded/ff794826(v=winembedded.60))  

具体的には、uwfmgr volume protect コマンドで特定のボリュームを保護した後、保護対象としていないボリュームのディスクを diskpart コマンドなどでオフラインにしておくことで実現可能です。以下は、ディスク 1 が D ドライブである場合の実行例です。  
※ diskpart コマンドのリファレンスは [こちら](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/diskpart)。

```
C:\>diskpart
 
Microsoft DiskPart バージョン 10.0.17763.1
 
Copyright (C) Microsoft Corporation.
コンピューター: DESKTOP-UQTBV36
 
DISKPART> list disk
 
  ディスク      状態           サイズ   空き   ダイナ GPT
  ###                                          ミック
  ------------  -------------  -------  -------  ---  ---
  ディスク 0    オンライン           127 GB      0 B        *
  ディスク 1    オンライン            10 GB  1024 KB        *
 
DISKPART> select disk 1
 
ディスク 1 が選択されました。
 
DISKPART> offline disk
 
DiskPart は選択されたディスクをオフラインにしました。
 
DISKPART> list disk
 
  ディスク      状態           サイズ   空き   ダイナ GPT
  ###                                          ミック
  ------------  -------------  -------  -------  ---  ---
  ディスク 0    オンライン           127 GB      0 B        *
* ディスク 1    オフライン            10 GB  1024 KB        *
 
DISKPART> exit
 
DiskPart を終了しています...
```

COMPMGMT.MSC または DISKMGMT.MSC コマンドで "ディスクの管理" コンソールを起動し、UI 上でオフラインにしても問題ありません。  

<img src="https://jpiotblog.github.io/images/UWF-HORM/UWF-disk-management-offline.png" align="left" border="1"><br clear="left">  


***
`変更履歴`  
`2020/12/1 created by Mochizuki`  

※ 本記事は 「[jpiotblog について](https://jpiotblog.github.io/blog/2020/01/01/about-jpiotblog/)」 の留意事項に準じます。  
※ 併せて 「[ホームページ](https://jpiotblog.github.io/blog/)」 および 「[記事一覧](https://jpiotblog.github.io/blog/archives/)」 もご参照いただければ幸いです。  
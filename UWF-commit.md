---
title: uwfmgr.exe コマンドの Commit/Cummit-Delete サブ オプションについて
date: 2020-06-29 17:00:00
categories:
- Unified Write Filter
tags:
- uwfmgr.exe
---
UWF オーバーレイの保護領域に対して、ファイルやレジストリの変更を反映させる方法についてお纏めします。
<!-- more -->
<br>

***
※ uwfmgr.exe ツールの利用法は、[こちら](https://docs.microsoft.com/en-us/windows-hardware/customize/enterprise/uwfmgrexe) を参照ください。  

***
## uwfmgr.exe File Commit/Cummit-Delete について

UWF オーバーレイの保護領域に対してファイル操作を行った場合、OS 再起動後にこれらの操作内容がリセットされます。この時、uwfmgr.exe File Commit/Cummit-Delete によって、変更内容がリセットされないよう "コミット" することが可能です。  

ファイルの作成、変更を反映する場合、uwfmgr.exe file commit を使用します。実行はファイル単位で、正規表現は使用できません。  

- 例 (c:\temp\test.txt の変更反映)
   ```
   uwfmgr.exe file commit "c:\temp\test.txt"
   ```
<br>
ファイルの削除を反映する場合、uwfmgr.exe file commit-delete を使用します。実行はファイル単位ですが、空のフォルダーに対しても使用可能です。正規表現は使用できません。  
ファイル削除後に実行するのではなく、コマンドを実行によってファイルを削除します。  

- 例 (c:\temp\test.txt の削除反映)
   ```
   uwfmgr.exe file commit-delete "c:\temp\test.txt"
   ```
<br>
> 補足:  
新しくフォルダーを作成し、その配下に作成したファイルに対して uwfmgr file commit を実行した場合、作成したフォルダーを含めてコミットされます。逆に、フォルダー内のファイルを全て uwfmgr file commit-delete で削除後、空のフォルダーを削除する場合には uwfmgr file commit-delete をそのフォルダーに対して実行することでフォルダーの削除がコミットされます。

***
## uwfmgr.exe Registry Commit/Cummit-Delete について

UWF オーバーレイによってシステムの利用領域を保護している状態でレジストリ キーや値の変更を行った場合、OS 再起動後に変更内容はリセットされます。この時、uwfmgr.exe Registry Commit/Cummit-Delete によって、変更内容がリセットされないよう "コミット" することが可能です。  

レジストリ キー、値の作成、変更を反映する場合、uwfmgr.exe registry commit を使用します。実行は値単位で、正規表現は使用できません。    

- 例 (HKLM\SOFTWARE\test キー配下の値 test の変更反映)
   ```
   uwfmgr.exe registry commit "HKLM\SOFTWARE\test" test
   ```
<br>
レジストリ キー、値の削除を反映する場合、uwfmgr.exe registry commit-delete を使用します。キーのみを指定して削除を実行することが可能です。  
レジストリ キー、値を削除後に実行するのではなく、コマンドを実行によって削除します。  

- 例 (HKLM\Software\test キー配下の値 test の削除反映)
   ```
   uwfmgr.exe registry commit-delete "HKLM\Software\test" test
   ```

- 例 (HKLM\Software\test キーの削除反映)
   ```
   uwfmgr.exe registry commit-delete "HKLM\Software\test"
   ```
<br>
> 補足:  
HKCU レジストリ ハイブのキー、サブ キー、値に対する変更や削除、レジストリ ハイブ ((HKLM\Software, HKLM\System, HKLM\SAM, HKLM\Security 等) を指定した削除の実行は拒否されます。  

***
`変更履歴`  
`2020/06/29 created by Mochizuki`  
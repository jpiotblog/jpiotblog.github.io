---
title: uwfmgr.exe ツールの Commit/Commit-Delete コマンドについて
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
## uwfmgr.exe File Commit/Commit-Delete について

UWF オーバーレイの保護領域に対してファイル操作を行った場合、OS 再起動後にこれらの操作内容がリセットされます。この時、uwfmgr.exe File Commit/Commit-Delete によって、変更内容がリセットされないよう "コミット" することが可能です。コミットしたタイミングの変更内容が実際のシステムに反映されます。  

ファイルの作成、変更を反映する場合、uwfmgr.exe file commit を使用します。実行はファイル単位で、正規表現は使用できません。  

- 例 (c:\temp\test.txt の変更反映)
   ```
   uwfmgr.exe file commit "c:\temp\test.txt"
   ```

ファイルの削除を反映する場合、uwfmgr.exe file commit-delete を使用します。実行はファイル単位ですが、空のフォルダーに対しても使用可能です。正規表現は使用できません。  
ファイル削除後に実行するのではなく、コマンドを実行によってファイルを削除します。  

- 例 (c:\temp\test.txt の削除反映):
   ```
   uwfmgr.exe file commit-delete "c:\temp\test.txt"
   ```

- 例 (c:\temp の削除反映 ※ 空のフォルダーである必要があります):
   ```
   uwfmgr.exe file commit-delete "c:\temp"
   ```

> 補足 1:  
新しくフォルダーを作成し、その配下に作成したファイルに対して uwfmgr file commit を実行した場合、作成したフォルダーを含めてコミットされます。逆に、フォルダー内のファイルを全て uwfmgr file commit-delete で削除後、空のフォルダーを削除する場合には uwfmgr file commit-delete をそのフォルダーに対して実行することでフォルダーの削除がコミットされます。  

> 補足 2:  
現状、新しく作成したフォルダーのみ (空の状態) をコミットする方法がありません。代替策として、フォルダーの配下に適当なファイルを作成し Commit 実行後、Comite-Delete で削除することでフォルダーのみをコミットしたことと同等の結果を得ることが可能です。
>
> - 例 (c:\temp フォルダーのみコミットする場合):
>   ```
>   mkdir c:\temp
>   echo commit > c:\temp\test.txt
>   uwfmgr file commit c:\temp\test.txt
>   uwfmgr file commit-delete c:\temp\test.txt
>   ```
>   2 行目の echo コマンドで commit という文字列を c:\temp\test.txt へ標準出力し、ファイルを作成します。  
>   3 行目で作成されたファイルをコミットし、4 行目で削除します。

***
## uwfmgr.exe Registry Commit/Commit-Delete について

UWF オーバーレイによってシステムの利用領域を保護している状態でレジストリ キーや値の変更を行った場合、OS 再起動後に変更内容はリセットされます。この時、uwfmgr.exe Registry Commit/Commit-Delete によって、変更内容がリセットされないよう "コミット" することが可能です。コミットしたタイミングの変更内容がシステムに反映されます。  

レジストリ キー、値の作成、変更を反映する場合、uwfmgr.exe registry commit を使用します。実行は値単位で、正規表現は使用できません。    

- 例 (HKLM\SOFTWARE\test キー配下の値 test の変更反映):
   ```
   uwfmgr.exe registry commit "HKLM\SOFTWARE\test" test
   ```

レジストリ キー、値の削除を反映する場合、uwfmgr.exe registry commit-delete を使用します。キーのみを指定して削除を実行することが可能です。  
レジストリ キー、値を削除後に実行するのではなく、コマンドを実行によって削除します。  

- 例 (HKLM\Software\test キー配下の値 test の削除反映):
   ```
   uwfmgr.exe registry commit-delete "HKLM\Software\test" test
   ```

- 例 (HKLM\Software\test キーの削除反映):
   ```
   uwfmgr.exe registry commit-delete "HKLM\Software\test"
   ```

> 補足:  
HKLM 以外のレジストリ ハイブ (例えば、HKCR、HKCU など) のキー、サブ キー、値の作成や変更、削除は行えません。また、HKLM 自体や、HKLM\Software, HKLM\System, HKLM\SAM, HKLM\Security, HKLM\Components キーを変更対象とすることはできません。これらは仕様上、実行が拒否されます。

***
`変更履歴`  
`2020/06/29 created by Mochizuki`  
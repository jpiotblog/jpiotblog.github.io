---
title: UWF を有効化した環境でメモリ ダンプを取得する方法
date: 2020-02-12 00:00:00
categories:
- Unified Write Filter
tags:
- メモリ ダンプの設定手順
---
UWF を有効化した環境で、メモリ ダンプを取得する方法についてご紹介します。
<!-- more -->
<br>

***
## はじめに

- 概要  

   強制メモリ ダンプを取得することで、現象発生時のメモリの状態を Memory.dmp ファイルとして保存し、そこから現象発生時のオペレーティング システムのメモリの状態や、行われていた処理などの詳細を確認することができます。  

- 影響  

   メモリ ダンプ取得のためのシャットダウン操作は、正常にシステムを停止させる方法ではないことを予めご留意ください。システムが正常に動作しない問題の調査方法として、他に方法が無い場合にご案内させていただいております。運用環境で、且つ回避策がある場合には、極力実施をお勧めいたしません。  

- 再起動の必要性  

   事前準備時、メモリダンプ採取時に再起動が必要です。  

-  採取ファイル  

   本手順では "D:\Dumps\Memory.dmp" として進めさせていただきます。  

***
## 事前準備
- システム ドライブ以外に、メモリ ダンプを保存するボリュームを追加し、いずれかのドライブとしてマウントします。  
※ この手順では D: ドライブとします。  

- 事前に UWF フィルターとボリュームの保護を実行します。この過程で PageFile の設定が一旦初期化されます。  

   実行例:  
   ※ この手順ではシステム ドライブを C: ドライブとします。 
   ```Console
   uwfmgr.exe volume protect c:  
   uwfmgr.exe filter enable  
   shutdown -r -t 0  
   ```

***
## Dump 出力設定手順

1. UWF フィルターを無効化する。  

   実行例:  
   ```
   uwfmgr.exe filter disable  
   ```
  
1. PageFile の大きさを 物理メモリ + 300 Mbyte 以上の大きさに設定する。

   実行例:  
   a) [エクスプローラ] - [コンピュータ] を右クリックし [プロパティ(R)] をクリックします。  
   b) [システムの詳細設定] をクリックします。  
   c) [詳細設定] タブの [パフォーマンス] にある [設定(S)] をクリックします。  
   d) [詳細設定] タブの [仮想メモリ] の項目にある [変更(C)] ボタンをクリックします。  
   e) この画面にて、[すべてのドライブのページング ファイルのサイズを自動的に管理する(A)] オプションを外します。  
   f) 手順 1) で作成したシステムドライブ以外のドライブ (ここでは D:\) をクリックします。  
   g) [カスタムサイズ] にチェックを付け、[初期サイズ]、[最大サイズ] の両方に物理メモリ + 300 Mbyte 以上の値を入力します。 (例えば 4096MB メモリの場合、4396MB)  
   h) その後 [設定] ボタンをクリックし設定を反映させ [OK] ボタンをクリックします。  
   i) "変更結果はコンピューターを再起動しなければ有効になりません。" というポップアップが表示されますので、[OK] ボタンをクリックします。  
   j) "パフォーマンス オプション" のウィンドウも [OK] ボタンにて閉じます。  

   ※ 補足  
   この設定値は、次のレジストリに反映されます。なお、直接レジストリ値を編集することでも、ページング ファイル サイズを設定することができます。  

   キー: `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SessionManager\MemoryManagement`  
   名前: `PagingFiles`  
   種類: `REG_MULTI_SZ`  
   データ: <ページ ファイル保存先> <初期サイズ (MB)> <最大サイズ (MB)> (設定例: `d:\pagefile.sys 4396 4396`)

1. 完全メモリ ダンプ (Full Dump) が生成されるよう設定する。  

   a) [エクスプローラ] - [コンピュータ] を右クリックし、[プロパティ] をクリックします。  
   b) 左ペインにある [システムの詳細設定] をクリックします。  
   c) "システムのプロパティ" の [詳細設定] タブの [起動と回復] 枠内にある [設定] ボタンをクリックします。  
   d) "起動と回復" の [デバッグ情報の書き込み] 枠内にあるプルダウン メニューから [完全メモリ ダンプ] を選択し、[OK] ボタンを 2 回クリックします。  

1. [ダンプ ファイル] のパスを変更する。  

   ※ 注意  
   GUI から [完全メモリ ダンプ] を選択する事と併せて、以下のレジストリ エディタで、次のレジストリの値が設定されている事をご確認ください。  

   キー: `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\CrashControl`  
   名前: `CrashDumpEnabled`  
   種類: `REG_DWORD`  
   データ: `1`  

   メモリ ダンプの出力先は、次のレジストリ値で確認できます。  

   キー: `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\CrashControl`  
   名前: `DumpFile`  
   種類: `REG_EXPAND_SZ`  
   データ(既定値): `D:\Dumps\MEMORY.DMP`  

1. DedicatedDumpFile.sys を設定する。  

   レジストリ エディタで、次のレジストリの値を設定してください。  

   キー: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\CrashControl`  
   名前: `DedicatedDumpFile`  
   種類: `REG_SZ`  
   データ: `D:\dedicateddumpfile.sys`  

   キー: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\CrashControl`  
   名前: `DumpFileSize`  
   種類: `REG_DWORD`  
   データ: <10 進数でメモリサイズ + 300 MB を設定ください> (設定例: `4396`)  

1. (オプション) AlwaysKeepMemoryDump を設定する。  

   Client OS におけるメモリ ダンプでは WorkGroup 環境の場合、ダンプ ファイル格納ボリュームの空き容量が25 GB 未満の場合、ダンプ ファイルが削除される動作となります (Domain 環境の場合、25 GB の制限には合致いたしません)。  
   そのため、対象の端末が Workgroup 環境の場合には、以下のレジストリ値を設定いただき、ダンプ ファイルが上記制限に合致しないよう設定いただく事をご検討いただければと存じます。  

   キー: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\CrashControl`  
   名前: `AlwaysKeepMemoryDump`  
   種類: `REG_DWOD`  
   データ : `1`  

   公開情報:  
   [Kernel dump storage and clean up behavior in Windows 7](https://blogs.msdn.microsoft.com/wer/2009/02/09/kernel-dump-storage-and-clean-up-behavior-in-windows-7/)  

1. (オプション) キーボードから STOP エラーを発生できるように設定する。  

   事象発生時にメモリ ダンプの生成を行えるよう、トリガーの設定を行います。レジストリ エディタで、次のレジストリの値を設定してください。  

   <PS/2 キーボードの場合>  
   キー: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\i8042prt\Parameters`  
   名前: `CrashOnCtrlScroll`  
   種類: `REG_DWORD`  
   データ: `1`  

   <USB キーボードの場合>  
   キー: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\kbdhid\Parameters`  
   名前: `CrashOnCtrlScroll`  
   種類: `REG_DWORD`  
   データ: `1`  

1. (オプション) NMI スイッチを使用できるように設定する。  

   Windows の機能ではなく、ご使用のハードウェアにメモリ ダンプ取得のための NMI (Non-Maskable Interrupt) スイッチが備わっている場合、以下にご案内するキーボードでクラッシュさせる方法よりも割り込みレベルが高いため、ご使用いただくことをお勧めします。  
   ご使用のハードウェアに当該機能が備わっているか、また、使用方法につきましては、ハードウェアの取扱説明書、あるいはハードウェアご提供元様にご確認ください。  

   また、Windows 側の設定として、レジストリ エディタで、次のレジストリの値を設定ください。  

   キー: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\CrashControl`  
   名前: `NMICrashDump`  
   種類: `REG_DWORD`  
   データ: `1`  

1. UWF を有効化し、OS を再起動する。  

   実行例:  
   ```dos
   uwfmgr.exe filter enable  
   shutdown -r -t 0  
   ```

***
## Dump の出力手順
調査対象の事象が発生した際に、NMI もしくはキーボード操作によってメモリ ダンプを出力させます。  

- 取得対象: `D:\Dumps\Memory.dmp`  

***
## ※ 参考情報
- [Windows Server 2008 および Windows Server 2008 R2 でカーネルまたは完全メモリ ダンプ ファイルを生成する方法](http://support.microsoft.com/kb/969028)  

- [Overview of memory dump file options for Windows 2000, Windows XP, Windows Server 2003, Windows Vista, Windows Server 2008, Windows 7 and Windows Server 2008 R2](http://support.microsoft.com/kb/254649/en-us)  

- [メモリ ダンプ ファイルのオプションは、Windows 2000、Windows XP、Windows Server 2003、Windows Vista、Windows Server 2008、Windows 7 と Windows サーバー 2008 R2 の概要 (機械翻訳)](http://support.microsoft.com/kb/254649/ja)  

- [Windows 8 and Windows Server 2012: Automatic Memory Dump (英文のみ)](http://blogs.technet.com/b/askcore/archive/2012/09/12/windows-8-and-windows-server-2012-automatic-memory-dump.aspx)

- [NMI_HARDWARE_FAILURE error when an NMI is triggered on Windows 8 and Windows Server 2012 (英文のみ)](http://support.microsoft.com/kb/2750146)  

- [Windows の機能により、キーボード操作で Memory.dmp ファイルを作成できる](http://support.microsoft.com/kb/244139)  

- [クラッシュ後 Windows でメモリ ダンプ ファイルが保存されない](http://support.microsoft.com/kb/130536)
---
title: UWF の除外設定について
date: 2020-02-19 00:00:00
categories:
- Unified Write Filter
tags:
- 除外設定
---
UWF の除外設定を実施するにあたり、留意いただきたい内容をお纏めいたします。
<!-- more -->
<br>

***
## 除外設定に関するお問い合わせについて

UWF は、保護対象領域に対する書き込みをオーバーレイ領域でキャッシュし、OS 再起動をトリガーにキャッシュした情報をリセットして、UWF を有効化した時点に戻します。ある製品を動作させようとしたときに、OS 再起動後に情報がリセットされると不都合が発生する場合、除外設定を追加することで期待通りに動作させることができる可能性があります。  
この時、各製品で必要な除外設定につきましては、各製品サポートよりご案内させていただいております。下記例を参照ください。  

- 質問例 1. Windows Defender に必要な除外設定を教えてほしい  
   -> Windows Defender の製品サポートへお問い合わせください

- 質問例 2. Wi-Fi の接続情報を OS 起動後も維持したい  
   -> Windows OS の製品サポート (ネットワーク担当) へお問い合わせください

- 質問例 3. A 社の製品を UWF 有効化した状態で動作させることは可能か  
   -> A 社製品ベンダーへお問い合わせください

除外設定の追加によって何らかの問題が発生した場合、除外対象パスを使用するプロセス側にて調査が必要となる場合がございます。対象の製品サポートへ事例の有無や対処方法についてお問い合わせください。  

***

## 除外を推奨しないパスについて

システムの起動の初期段階でアクセスするフォルダ (C:\Windows 配下など) は、他のデータと一貫性を持っているため、一部のフォルダーのみを除外することで問題が発生する場合があります。この一貫性が崩れた場合、システムがハンドルできない問題が発生し 「SYSTEM THREAD EXCEPTION NOT HANDLED」 エラー表示されます。これは、システム内部にて Access violation が発生 (アクセスできる情報としてアクセスしたが、情報が無いか権限が無く失敗) することでシステムの起動が進められなくなるためです。  

なお、レジストリの除外については下記公開情報のとおり、HKLM レジストリ ハイブ配下の幾つかのキーおよびその配下のサブキーでのみ実行いただけます。こちらに該当しないレジストリを除外設定しようとすると、アクセス拒否のエラーと共に実行が失敗します。仮に設定時にエラーが出なかった場合でも、HKCU や HKU 配下など、除外が許可されていない (マイクロソフト側で正常動作を保証しない) パスにつきましては、書き込み結果の不整合による予期しない動作の原因になります。    

- [UWF_RegistryFilter.AddExclusion](https://docs.microsoft.com/ja-jp/windows-hardware/customize/enterprise/uwf-registryfilteraddexclusion)
   > 除外リストには、次のレジストリ キーの下のレジストリ サブキーのみを追加できます。
   >- HKEY_LOCAL_MACHINE\BCD00000000
   >- HKEY_LOCAL_MACHINE\SYSTEM
   >- HKEY_LOCAL_MACHINE\SOFTWARE
   >- HKEY_LOCAL_MACHINE\SAM
   >- HKEY_LOCAL_MACHINE\SECURITY
   >- HKEY_LOCAL_MACHINE\COMPONENTS

<br>
下記公開情報には、一般的な除外設定に加えて、除外を推奨しないパスの情報も含まれております。主にファイルのパスですが、一部レジストリ パスもございます。除外設定の際にご参照ください。  

- [書き込みフィルターの除外](https://docs.microsoft.com/ja-jp/windows-hardware/customize/enterprise/uwfexclusions)  
   >! 重要  
   >次の除外を追加しないでください。
   >- \Windows\System32\config\DEFAULT  
   >- \Windows\System32\config\SAM  
   >- \Windows\System32\config\SECURITY  
   >- \Windows\System32\config\SOFTWARE  
   >- \Windows\System32\config\SYSTEM  
   >- \Users\<User Name>\NTUSER.DAT  
   >- \Windows\BOOTSTAT.DAT  
   >- <System Drive>\EFI\Microsoft\Boot\BOOTSTAT.DAT  
   >- <System Drive>\Boot\BOOTSTAT.DAT  
   >
   >また、次の項目の除外を追加しないでください。  
   >- ボリュームルート。 例: C: または d:  
   >- システムボリューム上のフォルダー。\Windows  
   >- システムボリューム上のフォルダー。\Windows\System32  
   >- システムボリューム上のフォルダー。\Windows\System32\Drivers  
   >- ページングファイル。  
   >
   >これらのアイテムの除外の追加はサポートされていないため、予期しない結果が生じる可能性があります。 これらの場所にあるサブディレクトリとファイルを除外するのは問題ありません。

   >! 重要  
   >次の除外を追加しないでください。
   >- HKLM\SECURITY\Policy\Secrets\$MACHINE.ACC

- [統合書き込みフィルター (UWF) 機能](https://docs.microsoft.com/ja-jp/windows-hardware/customize/enterprise/unified-write-filter) 
   >! 注意  
   >この問題を回避するには、日付と時刻の設定 ("%windir%\bootstat.dat") を保持するファイルを書き込みフィルターの除外に追加しないでください。 これにより、Stop エラー 0x7E (SYSTEM_THREAD_EXCEPTION_NOT_HANDLED) が発生します。

<br>
また、公開情報へ追加リクエスト中の内容となりますが、下記についても除外を設定しないようお願いいたします。  

- C:\Windows\WinSXS フォルダー

   > Winodws 10 Enterprise 2019 LTSC では、C:\Windows\WinSXS フォルダーを除外するとシステム起動時に 「SYSTEM THREAD EXCEPTION NOT HANDLED」 で BSOD が発生するという報告がございます。C:\Windows\WinSXS フォルダー配下にはシステム起動初期にアクセスする情報が含まれておりますため、除外をお勧めいたしません。  

***
## Windows Defender 用の除外設定について

弊社より提供させていただいておりますマルウェア対策ソフトウェア (Windows Defender および System Center Endpoint Protection) につきましては、除外設定を下記サイトにて公開しております。  

[UWF で保護されているデバイスでマルウェア対策のサポート](https://docs.microsoft.com/ja-jp/windows-hardware/customize/enterprise/uwf-antimalware-support)  

また、これらの除外設定に伴い発生する問題の事例をご紹介します。比較的新しいバージョンの OS (Windows 10 バージョン 1909 以降) におきましては、2020/6/17 現在にて発生報告は確認されておりません。関連すると思われる問題が確認された場合には、Windows Defender 製品サポートへお問い合わせください。  

- Windows Defender のレジストリを除外すると OS 起動時にハングアップする  

   > 下記レジストリ キーを除外していると、OS 起動時にハングアップして起動できない場合があります。レースコンディションによるものであり、スペックの低い端末で発生する傾向にあります。OS 側での修正は困難であるため、Wdfilter の除外解除等をご検討ください。  
   > 
   >- HKLM\System\CurrentControlSet\Services\WdFilter  
   >- HKLM\SYSTEM\ControlSet001\Control\DeviceClasses\{53f5630d-b6bf-11d0-94f2-00a0c91efb8b}  
    
   >Windows 10 Enterprise 2016 LTSB の環境では下記除外が入っていた場合にも同様の問題が発生するという報告があります。該当する場合にはこちらの除外解除もご検討ください。  
   >
   >- HKLM\SOFTWARE\Classes\TypeLib\{8C389764-F036-48F2-9AE2-88C260DCF43B}  
   >- HKLM\SOFTWARE\Classes\CLSID\{A2D75874-6750-4931-94C1-C99D3BC9D0C7}  
   >- HKLM\SOFTWARE\Classes\CLSID\{195B4D07-3DE2-4744-BBF2-D90121AE785B}  

***
`変更履歴`  
`2020/02/19 created by Mochizuki`  
`2020/06/16 modified by Mochizuki` 
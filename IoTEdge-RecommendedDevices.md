---
title: IoT Edge が動作するハードウェアについて
date: 2021-05-06 14:00:00
categories:
- IoT Edge
tags:
- IoT Edge
---
IoT Edgeが動作するハードウェアについてご説明いたします。 
<!-- more -->
<br>

***
# IoT Edge が動作するハードウェア 
IoT Edge の動作には IoT Edge ランタイム本体の他に Moby-engine など依存関係があるソフトウェアを必要とします。 

これらのソフトウェアの動作がサポートされるオペレーティングシステム (OS) などの要件は「[IoT Edge がサポートされているプラットフォーム](https://learn.microsoft.com/ja-jp/azure/iot-edge/support?view=iotedge-1.4)」のドキュメントをご確認ください。 

上記ドキュメントの要件を満たすソフトウェアを稼働できるハードウェアであれば IoT Edge も動作することが期待されます。  
ただし、CPU の種類やメモリ量、その他にインストールされているソフトウェアなどの要因により、全てのハードウェア・環境で動作することは保証されません。  
実際に期待する動作が得られるかどうかはユーザーによる検証が必要です。

## IoT Edge との互換性が期待できるデバイスを選択するためには 
Azure サービスに接続する IoT デバイスを選択いただく際の参考として、マイクロソフトでは[Azure Certified Devices プログラム](https://learn.microsoft.com/ja-jp/azure/certification/overview)および、[認定されたデバイスのカタログ](https://devicecatalog.azure.com/devices)を提供しています。  

IoT デバイスが特定の水準を満たしている場合、IoT デバイスの開発ベンダーは認定を受けた自社のデバイスをカタログへ登録することができます。  
また上記プログラムには、IoT Edge との互換性が認定された「[Edge Managed デバイス](https://learn.microsoft.com/ja-jp/azure/certification/program-requirements-edge-managed)」があります。   
そのため、IoT ソリューションの開発者は、カタログから IoT Edge との互換性が期待できるデバイスを選択することにより、IoT Edge が動作しないというリスクを下げることができます。 

カタログに公開されている各デバイスが準拠している認定の内容はフィルタリングを行うことで確認できます。   
[IoT デバイスと IoT ハードウェアの購入 | Azure Certified Device カタログ](https://devicecatalog.azure.com/devices)  
例として Edge Managed デバイスのみ表示するフィルタリングが含まれています。 

***
`変更履歴`  
`2023/08/24 created by Kudou`  

※ 本記事は 「[jpiotblog について](https://jpiotblog.github.io/blog/2020/01/01/about-jpiotblog/)」 の留意事項に準じます。  
※ 併せて 「[ホームページ](https://jpiotblog.github.io/blog/)」 および 「[記事一覧](https://jpiotblog.github.io/blog/archives/)」 もご参照いただければ幸いです。  

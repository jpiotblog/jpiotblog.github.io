---
title: IoT Hub に MQTT でメッセージを Publish する際のトピック名について
date: 2021-04-28 17:00:00
categories:
- IoT Hub
tags:
- IoT Hub
---
IoT Hub に MQTT でメッセージを Publish する際のトピック名に関する注意点をご案内します。
<!-- more -->
<br>

***
## 本記事の背景
IoT Hub は [MQTT での接続をサポート](https://docs.microsoft.com/ja-jp/azure/iot-hub/iot-hub-mqtt-support)しており、デバイスは Azure IoT device SDK の代わりに汎用的な MQTT ライブラリを利用して通信することが可能です。

ここで、デバイス ID が Device01 の場合に、IoT Hub に MQTT で接続して D2C (テレメトリ) メッセージを送信するまでの一般的な流れは以下の通りです。

(1) デバイス ID (Device01) と IoT Hub のホスト名、対称キーの情報を元に SAS トークンを生成します。

- SAS トークンの例: SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2F**Device01**%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802

(2) MQTT で接続 (CONNECT パケットの送信) を行います。

- Usename: {your hub name}.azure-devices.net/**Device01**/?api-version=2018-06-30
- Password: ステップ 1 で生成した SAS トークン

(3) D2C メッセージを送信するため Publish を行います。
- Publish 先のトピック名: devices/**Device01**/messages/events/

## MQTT を直接利用する場合に発生する現象
通常は上記のようにステップ 1 - 3 で使用されるデバイス ID は全て同じ (Device01) となります。Azure IoT device SDK を利用している場合は MQTT レベルの操作を意識する必要がなく、トピック名などのパラメーターは適切に設定されます。

一方で、MQTT を直接使用している場合、ステップ 3 で ステップ 1, 2 とは異なるデバイス ID (例. Device02) をクライアント側で指定することが技術的には可能です。

- Publish 先のトピック名: devices/**Device02**/messages/events/

現在の IoT Hub はこのようなケースについて、公開ドキュメントの仕様では明記されていませんが、実際の動作としては、デバイス ID の文字列によってエラーになるパターンと、エラーとならずに Publish に成功するパターンが存在しています。

## 今後の対応予定
ステップ 3 で Publish する際のトピック名に含まれるデバイス ID を、ステップ 1, 2 と異なるものにする必要は通常無いと考えられるため、今後はトピック名を厳密にチェックして、接続時と異なるデバイス ID を宛先とした Publish は全てエラーとして扱われる予定です。併せて、既に本動作を前提として運用しているユーザーに対する周知や、仕様として公開ドキュメントに記載する準備も進めています。

***
`変更履歴`  
`2021/04/28 created by Nakagami`  

※ 本記事は 「[jpiotblog について](https://jpiotblog.github.io/blog/2020/01/01/about-jpiotblog/)」 の留意事項に準じます。  
※ 併せて 「[ホームページ](https://jpiotblog.github.io/blog/)」 および 「[記事一覧](https://jpiotblog.github.io/blog/archives/)」 もご参照いただければ幸いです。  
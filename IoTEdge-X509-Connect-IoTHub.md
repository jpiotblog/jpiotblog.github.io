---
title: IoT Edge デバイスを X.509 証明書を使って IoT Hub に接続する方法について
date: 2021-05-06 14:00:00
categories:
- IoT Hub
- IoT Edge
tags:
- IoT Hub
- IoT Edge
- X.509
---
IoT Edge デバイスを IoT Hub に接続する方法について、X.509 証明書を使用する場合の具体的な手順例をご紹介します。
<!-- more -->
<br>

***
# IoT Edge デバイスを IoT Hub に接続する方法 (X.509 証明書)
IoT Hub に IoT Edge デバイスを接続する際の認証情報としては対称キー (SAS トークン) の他に X.509 証明書がサポートされています。以下は X.509 証明書の拇印を利用して IoT Edge デバイスを IoT Hub に接続するための具体的な手順となります。

## 検証環境
- ホスト OS: Ubuntu 18.04 LTS
- IoT Edge バージョン: 1.0.10.4

## 手順

###  (1) IoT Edge をインストールする
下記の手順に従って「IoT Edge をインストールする」の項目まで作業を進めます。

- [Azure IoT Edge for Linux をインストールまたはアンインストールする](https://docs.microsoft.com/ja-jp/azure/iot-edge/how-to-install-iot-edge?view=iotedge-2018-06)


### (2) テスト用の証明書を生成する
下記ドキュメントの Bash 向けの手順でテスト用の証明書を生成します。これは IoT Edge をインストールした Ubuntu 上で実行しても問題ありません。

- [IoT Edge デバイスの機能をテストするためのデモ用の証明書を作成する](https://docs.microsoft.com/ja-jp/azure/iot-edge/how-to-create-test-certificates?view=iotedge-2018-06)

ここでは IoT Edge デバイスのデバイス ID を "u1804serveredge01" とします。

コマンド例:
```console
git clone https://github.com/Azure/iotedge.git  
    
mkdir certgen  
    
cp iotedge/tools/CACertificates/*.cnf certgen/  
    
cp iotedge/tools/CACertificates/certGen.sh certgen/  
    
cd certgen  
    
./certGen.sh create_root_and_intermediate  

./certGen.sh create_edge_device_identity_certificate "u1804serveredge01"  
```

### (3) デバイス証明書の拇印を確認する

コマンド例:
```console
openssl x509 -in certs/iot-edge-device-identity-u1804serveredge01.cert.pem -fingerprint -noout
```

出力例:
```console
SHA1 Fingerprint=07:49:D2:99:AD:39:88:AD:1E:CA:36:BD:5B:33:B1:55:48:CA:CE:82
```

### (4) IoT Hub に IoT Edge デバイスを登録する
Azure ポータルから IoT Edge デバイスを追加します。

![Register-IoTEdge-Device.png](https://jpiotblog.github.io/images/IoTEdge-X509-Connect-IoTHub/Register-IoTEdge-Device.png)

手順 (2) で指定したデバイス ID (この例では "u1804serveredge01") および手順 (3) で確認した拇印を入力します。

入力例:

![Register-IoTEdge-Device-2.png](https://jpiotblog.github.io/images/IoTEdge-X509-Connect-IoTHub/Register-IoTEdge-Device-2.png)

### (5) IoT Edge の config.yaml を編集してデーモンを再起動する
上記 "Azure IoT Edge for Linux をインストールまたはアンインストールする" の「オプション 2:X.509 証明書を使用した認証」に従って設定を行います。

config.yaml の設定例:
***
    provisioning:

        source: "manual"

        authentication:

            method: "x509"

            iothub_hostname: "<YOUR IOT HUB NAME>.azure-devices.net"

            device_id: "u1804serveredge01"

            identity_cert: "file:///home/user/certgen/certs/iot-edge-device-identity-u1804serveredge01.cert.pem"

            identity_pk: "file:///home/user/certgen/private/iot-edge-device-identity-u1804serveredge01.key.pem"

        dynamic_reprovisioning: false
***

- iothub_hostname: IoT Hub のホスト名
- device_id: デバイス ID, この例では "u1804serveredge01"
- identity_cert: デバイス証明書のパス (フルパスの前に "file://" が必要)
- identity_pk: デバイス証明書の秘密鍵のパス (フルパスの前に "file://" が必要)

config.yaml の変更内容を保存したら IoT Edge デーモンを再起動します。
***
    sudo systemctl restart iotedge
***

### 注意点
IoT Edge デバイスは CA 証明書による複数デバイスの包括的な認証には対応していないため、自己署名証明書ではなく他の CA によって署名された証明書を使用する場合であっても、手順 (4) では「X.509 自己署名済み」を選択して証明書の拇印を個別で登録する必要があります。また、Device Provisioning Service (DPS) で IoT Edge デバイスをグループ登録することは可能ですが、この場合も IoT Hub には証明書の拇印が登録されます。

- [X.509 CA 証明書を使用したデバイス認証](https://docs.microsoft.com/ja-jp/azure/iot-hub/iot-hub-x509ca-overview)
- [X.509 証明書を使用して IoT Edge デバイスを作成およびプロビジョニングする](https://docs.microsoft.com/ja-jp/azure/iot-edge/how-to-auto-provision-x509-certs?view=iotedge-2020-11)

`変更履歴`  
`2021/05/06 created by Nakagami`  

※ 本記事は 「[jpiotblog について](https://jpiotblog.github.io/blog/2020/01/01/about-jpiotblog/)」 の留意事項に準じます。  
※ 併せて 「[ホームページ](https://jpiotblog.github.io/blog/)」 および 「[記事一覧](https://jpiotblog.github.io/blog/archives/)」 もご参照いただければ幸いです。  
<properties
 pageTitle="デバイス管理の概要 | Microsoft Azure"
 description="Azure IoT Hub デバイス管理の概要: デバイス ツイン、デバイス クエリ、デバイス ジョブ"
 services="iot-hub"
 documentationCenter=""
 authors="ellenfosborne"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# Azure IoT Hub デバイス管理の概要 (プレビュー)

Azure IoT Hub デバイス管理では、標準ベースの IoT デバイス管理を使用して、デバイスをリモートで管理、構成、および更新できます。

Azure IoT のデバイス管理には 3 つの主な概念があります。

1.  **デバイス ツイン:** IoT Hub の物理デバイスの表現。

2.  **デバイス クエリ**: デバイス ツインを検索し、デバイス ツインの総合的な理解を生み出すことができます。たとえば、1.0 のファームウェア バージョンですべてのデバイス ツインを検索できます。

3.  **デバイス ジョブ**: ファームウェアの更新、再起動、および出荷時の設定へのリセットなど、1 つ以上の物理デバイス上で実行するアクション。

## デバイス ツイン

デバイス ツインは、Azure IoT の物理デバイスの表現です。**Microsoft.Azure.Devices.Device** オブジェクトを使用すると、デバイス ツインを表現できます。

![][img-twin]

デバイス ツインには、次のコンポーネントがあります。

1.  **デバイス フィールド:** デバイス フィールドは、IoT Hub メッセージングとデバイス管理の両方に使用される定義済みのプロパティです。これにより、IoT Hub は物理デバイスを識別して接続します。デバイス フィールドは、デバイスに同期されないため、デバイス ツインに排他的に格納されます。デバイス フィールドには、デバイス ID と認証情報が含まれます。

2.  **デバイス プロパティ:** デバイス プロパティは、物理デバイスを表すプロパティの定義済みの辞書です。物理デバイスは、各デバイス プロパティのマスターであり、対応する各値の権限のあるストアです。このプロパティの最終的に一貫性のある表現は、クラウド内のデバイス ツインに格納されます。一貫性と更新は、「[Tutorial: how to use the device twin (チュートリアル: デバイス ツインの使用方法)][lnk-tutorial-twin]」に記載されている同期設定の影響を受けます。デバイス プロパティのいくつかの例には、ファームウェア バージョン、バッテリ レベル、および製造元の名前が含まれます。

3.  **サービス プロパティ:** サービス プロパティは、開発者がサービス プロパティの辞書に追加する **& lt;key,value& gt;** ペアです。 このプロパティは、デバイス ツインのデータ モデルを拡張することにより、デバイスをさらに特徴付けることができます。サービス プロパティは、デバイスに同期されないため、クラウド内のデバイス ツインに排他的に格納されます。サービス プロパティの 1 つの例が **&lt;NextServiceDate, 11/12/2017&gt;** であり、これを使用すると、サービスの次の日付でデバイスを検索できます。

4.  **タグ:** タグは、辞書プロパティではなく、任意の文字列であるサービス プロパティのサブセットです。これを使用すると、デバイス ツインに注釈を付けるか、デバイスをグループ化することができます。タグは、デバイスに同期されないため、デバイス ツインに排他的に格納されます。たとえば、デバイス ツインが物理トラックを表現する場合は、トラックの貨物の各種類に**りんご**、**オレンジ**、および**バナナ**というタグを追加できます。

## デバイス クエリ

前のセクションでは、デバイス ツインのさまざまなコンポーネントについて説明しました。ここでは、デバイス プロパティ、サービス プロパティまたはタグに基づいた IoT Hub デバイス レジストリのデバイス ツインを検索する方法について説明します。クエリを使用する場合の例に、更新する必要があるデバイスの検索があります。指定されたファームウェア バージョンですべてのデバイスにクエリを実行し、その結果を特定のアクション (IoT Hub ではデバイス ジョブと呼びますが、これは次のセクションで説明します) に送ることができます。

タグやプロパティを使用してクエリを実行できます。

-   タグを使用してデバイス ツインのクエリを実行するには、文字列の配列を渡します。クエリは、これらの文字列のすべてにタグ付けされているデバイスのセットを返します。

-   サービス プロパティまたはデバイス プロパティを使用してデバイス ツインのクエリを実行するには、JSON クエリ式を使用します。次の例は、キー **FirmwareVersion** および値 **1.0** を持つデバイス プロパティを使用して、すべてのデバイスのクエリを実行する方法を示しています。プロパティの **type** は **device** であり、サービス プロパティではなく、デバイス プロパティに基づいたクエリの実行を示していることが確認できます。

  ```
  {                           
      "filter": {                  
        "property": {                
          "name": "FirmwareVersion",   
          "type": "device"             
        },                           
        "value": "1.0",              
        "comparisonOperator": "eq",  
        "type": "comparison"         
      },                           
      "project": null,             
      "aggregate": null,           
      "sort": null                 
  }
  ```

## デバイス ジョブ

デバイス管理の次の概念は、デバイス ジョブです。デバイス ジョブは、複数のデバイス上の複数手順のオーケストレーションの調整を有効にします。

現時点では、Azure IoT Hub デバイス管理によって用意されている 6 種類のデバイスジョブがあります (顧客の必要に応じてジョブは今後追加されます)。

- **ファームウェアの更新**: 物理デバイスのファームウェア (または OS イメージ) を更新します。
- **再起動**: 物理デバイスを再起動します。
- **工場出荷時の設定へのリセット**: 物理デバイスのファームウェア (または OS イメージ)を、デバイスに格納されている工場出荷時のバックアップ イメージに戻します。
- **構成の更新**: 物理デバイスで実行されている IoT Hub クライアント エージェントを構成します。
- **デバイス プロパティの読み取り**: 物理デバイスのデバイス プロパティの最新の値を取得します。
- **デバイス プロパティの書き込み**: 物理デバイスのデバイス プロパティを変更します。

これらの各ジョブの使用方法の詳細については、「[API documentation for C# and node.js (c# および node.js 用の API ドキュメント)][lnk-apidocs]」を参照してください。

ジョブは、複数のデバイスに対して動作できます。ジョブを開始する場合、それらの各デバイスに関連付けられている子ジョブが作成されます。子ジョブは、1 つのデバイスで動作します。子ジョブごとに、その親ジョブへのポインターがあります。親ジョブは、子ジョブのコンテナー専用であり、デバイスの種類を区別するロジック (Intel Edison の更新と Raspberry Pi の更新など) を実装しません。次の図は、親ジョブ、その子、関連付けられている物理デバイス間のリレーションシップを示しています。

![][img-jobs]

ジョブ履歴のクエリを実行して、開始したジョブの状態を理解できます。いくつかのサンプル クエリについては、「[our query library (クエリ ライブラリ)][lnk-query-samples]」を参照してください。

## デバイスの実装

ここまではサービス側の概念について説明しましたが、ここからは管理対象の物理デバイスを作成する方法について説明します。Azure IoT Hub DM クライアント ライブラリを使用すると、Azure IoT Hub で IoT デバイスを管理できます。"管理" には、再起動、出荷時の設定へのリセット、ファームウェアの更新などのアクションが含まれます。現在は、プラットフォームに依存しない C ライブラリを提供していますが、近日中に他の言語のサポートも追加する予定です。

DM クライアント ライブラリは、デバイス管理において主な役割が 2 つあります。

- 物理デバイスのプロパティを、IoT Hub でそれに対応するデバイス ツインと同期する
- IoT Hub からデバイスに送信されるデバイス ジョブを構成する

物理デバイスでの実装の詳細については、「[Azure IoT Hub デバイス管理 (DM) クライアント ライブラリの概要][lnk-library-c]」を参照してください。

## 次のステップ

Azure IoT Hub デバイス管理機能の詳細については、次のチュートリアルに進んでください。

- [Azure IoT Hub デバイス管理の使用][lnk-get-started]

- [デバイス ツインの使用方法][lnk-tutorial-twin]

- [クエリを使用したデバイス ツインの検索方法][lnk-tutorial-queries]

- [デバイス ジョブを使用して、デバイスのファームウェアを更新する方法][lnk-tutorial-jobs]

<!-- Images and links -->
[img-twin]: media/iot-hub-device-management-overview/image1.png
[img-jobs]: media/iot-hub-device-management-overview/image2.png
[img-client]: media/iot-hub-device-management-overview/image3.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-library-c]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-apidocs]: http://azure.github.io/azure-iot-sdks/
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md

<!---HONumber=AcomDC_0518_2016-->
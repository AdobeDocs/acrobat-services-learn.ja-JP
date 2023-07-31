---
title: Acrobat Sign API入門
description: Acrobat Sign APIをアプリケーションに含めて、署名やその他の情報を収集する方法を説明します
role: Developer
level: Intermediate
type: Tutorial
feature: Acrobat Sign API
thumbnail: KT-8089.jpg
jira: KT-8089
exl-id: ae1cd9db-9f00-4129-a2a1-ceff1c899a83
source-git-commit: b65ffa3efa3978587564eb0be0c0e7381c8c83ab
workflow-type: tm+mt
source-wordcount: '2058'
ht-degree: 2%

---

# Adobe Sign APIの概要

![ユースケースの英雄バナー](assets/UseCaseStartedHero.jpg)

[ACROBAT SIGN API](https://www.adobe.io/apis/documentcloud/sign.html) は、署名済み契約書の管理方法を強化するのに最適な方法です。 開発者はSign APIを使用してシステムを簡単に統合できます。この機能により、信頼性が高く簡単な方法で、文書のアップロード、署名用の送信、リマインダーの送信、電子サインの収集を行うことができます。

## 学習内容

この実践チュートリアルでは、開発者がSign APIを使用して、で作成したアプリケーションとワークフローを強化する方法について説明します [!DNL Adobe Acrobat Services]. [!DNL Acrobat Services] includes [Adobe PDF Services API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-tools.html), [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/viesdk) （無料）、 [Adobe文書生成API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html).

詳しくは、Acrobat Sign APIをアプリケーションに組み込んで、署名や、保険フォームの社員情報などの他の情報を収集する方法を確認してください。 簡略化されたHTTPリクエストとレスポンスを含む一般的な手順が使用されます。 これらのリクエストは、お気に入りの言語で実装できます。 次の組み合わせを使用してPDFを作成できます [[!DNL Acrobat Services] API](https://www.adobe.io/apis/documentcloud/dcsdk/)を使用して、Sign APIに [一過性](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/overview/terminology.md) 契約書を使用して文書を作成し、エンドユーザーの署名を依頼するか、 [widget](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/overview/terminology.md) ワークフロー。

## PDF文書の作成

まず、Microsoft Wordテンプレートを作成し、PDFとして保存します。 または、Document Generation APIを使用してパイプラインを自動化し、Wordで作成されたテンプレートをアップロードしてPDF文書を生成できます。 Document Generation APIは [!DNL Acrobat Services], [6か月間無料で、その後は従量課金制で、ドキュメントのトランザクションあたり0.05ドルまたは0.05ドルです](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html).

この例のテンプレートは、いくつかの署名者フィールドを入力する単純な文書です。 フィールドに今すぐ名前を付け、後でこのチュートリアルの実際のフィールドを挿入します。

![いくつかのフィールドがある保険フォームのスクリーンショット](assets/GSASAPI_1.png)

## 有効なAPIアクセスポイントの検出

Sign APIを使用する前に、 [無料のデベロッパーアカウントを作成](https://acrobat.adobe.com/ca/en/sign/developer-form.html) apiにアクセスするには、文書の交換と実行をテストし、電子メール機能をテストします。

Adobeは、「シャード」と呼ばれる多くのデプロイメント単位で、世界中にAcrobat Sign APIを配布しています。 各シャードは、NA1、NA2、NA3、EU1、JP1、AU1、IN1などのお客様のアカウントに対応しています。 シャード名は地理的位置に対応します。 これらのシャードは、APIエンドポイントのベースURI（アクセスポイント）を構成します。

Sign APIにアクセスするには、最初にアカウントの正しいアクセスポイントを見つける必要があります。アクセスポイントには、api.na1.adobesign.com、api.na4.adobesign.com、api.eu1.adobesign.com、その他があり、お住まいの地域によって異なります。

```
  GET /api/rest/v6/baseUris HTTP/1.1
  Host: https://api.adobesign.com
  Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
  Accept: application/json

  Response Body (example):

  {
    "apiAccessPoint": "https://api.na4.adobesign.com/", 
    "webAccessPoint": "https://secure.na4.adobesign.com/" 
  }
```

上記の例では、は値をアクセスポイントとする応答です。

>[!IMPORTANT]
>
>この場合、以降にSign APIに対して行うすべてのリクエストは、そのアクセスポイントを使用する必要があります。 自分の地域にサービスを提供していないアクセスポイントを使用すると、エラーが発生します。

## 一時的なドキュメントのアップロード

Adobe Signを使用すると、様々なフローを作成して、文書の署名やデータ収集を行うことができます。 アプリケーションのフローに関係なく、最初に文書をアップロードする必要があります。この文書は、7日間だけ使用可能です。 その後のAPI呼び出しでは、この一時的な文書を参照する必要があります。

文書は、POSTリクエストを使用して `/transientDocuments` 端点。 マルチパート要求は、ファイル名、ファイルストリーム、および文書ファイルのMIME（メディア）タイプで構成されます。 エンドポイント応答には、文書を識別するIDが含まれます。

また、Acrobat SignのコールバックURLを指定してpingを実行し、署名プロセスが完了したらアプリに通知することができます。


```
  POST /api/rest/v6/transientDocuments HTTP/1.1
  Host: {YOUR-API-ACCESS-POINT}
  Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
  x-api-user: email:your-api-user@your-domain.com
  Content-Type: multipart/form-data
  File-Name: "Insurance Form.pdf"
  File: "[path]\Insurance Form.pdf"
  Accept: application/json

  Response Body (example):

  {
     "transientDocumentId": "3AAA...BRZuM"
  }
```

## Webフォームの作成

Webフォーム（以前の署名ウィジェット）は、アクセス権を持つユーザーが署名できるホストされた文書です。 Webフォームの例には、サインアップシート、免責条項など、多くの人がオンラインでアクセスおよび署名する文書が含まれます。

Sign APIを使用して新しいwebフォームを作成するには、まず一時的なドキュメントをアップロードする必要があります。 POSTリクエストの送信先 `/widgets` endpointは、返された `transientDocumentId` .

この例では、Webフォームは `ACTIVE`を選択します。ただし、以下の3つの異なるステートのいずれかで作成できます。

* DRAFT — Webフォームを段階的に構築します。

* AUTHORING — Webフォームのフォームフィールドを追加または編集

* ACTIVE — Webフォームをすぐにホストします。

フォームの参加者に関する情報も定義する必要があります。 この `memberInfos` プロパティには、電子メールなど、参加者に関するデータが含まれています。 現在、このセットは複数のメンバをサポートしていません。 ただし、Webフォームの作成時にWebフォーム署名者の電子メールが不明なため、次の例のように電子メールは空にしておく必要があります。 この `role` プロパティは、のメンバーが想定する役割を定義します `memberInfos` （署名者、承認者など）。

```
  POST /api/rest/v6/widgets HTTP/1.1
  Host: {YOUR-API-ACCESS-POINT}
  Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
  x-api-user: email:your-api-user@your-domain.com
  Content-Type: application/json

  Request Body:

  {
    "fileInfos": [
      {
      "transientDocumentId": "YOUR-TRANSIENT-DOCUMENT-ID"
      }
     ],
    "name": "Insurance Form",
      "widgetParticipantSetInfo": {
          "memberInfos": [{
              "email": ""
          }],
      "role": "SIGNER"
      },
      "state": "ACTIVE"
  }

  Response Body (example):

  {
     "id": "CBJ...PXoK2o"
  }
```

Webフォームは次のように作成できます `DRAFT` または `AUTHORING`フォームがアプリケーションパイプラインを通過するときに状態を変更します。 Webフォームの状態を変更するには、 [PUT /widgets/{widgetId}/state](https://secure.na4.adobesign.com/public/docs/restapi/v6#!/widgets/updateWidgetState) 端点。

## WebフォームホスティングURLの読み取り

次の手順は、WebフォームをホストしているURLを見つけることです。 /widgetsエンドポイントは、署名やその他のフォームデータを収集するために、ユーザーに転送するWebフォームのホストURLを含むWebフォームデータのリストを取得します。

このエンドポイントはリストを返すので、特定のフォームをIDで検索できます。 `userWidgetList` webフォームをホストするURLを取得する前に、次の操作を実行します。

```
  GET /api/rest/v6/widgets HTTP/1.1
  Host: {YOUR-API-ACCESS-POINT}
  Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
  Accept: application/json

  Response Body:

  {
    "userWidgetList": [
      {
        "id": "CBJCHB...FGf",
        "name": "Insurance Form",
        "groupId": "CBJCHB...W86",
        "javascript": "<script type='text/javascript' ...
        "modifiedDate": "2021-03-13T15:52:41Z",
        "status": "ACTIVE",
        "Url":
        "https://secure.na4.adobesign.com/public/esignWidget?wid=CBFCIB...Rag*",
        "hidden": false
      },
      {
        "id": "CBJCHB...I8_",
        "name": "Insurance Form",
        "groupId": "CBJCHBCAABAAyhgaehdJ9GTzvNRchxQEGH_H1ya0xW86",
        "javascript": "<script type='text/javascript' language='JavaScript'
        src='https://sec
        "modifiedDate": "2021-03-13T02:47:32Z",
        "status": "ACTIVE",
        "Url":
        "https://secure.na4.adobesign.com/public/esignWidget?wid=CBFCIB...AAB",
        "hidden": false
      },
      {
        "id": "CBJCHB...Wmc",
```

## Webフォームの管理

このフォームは、ユーザーが入力するためのPDF文書です。 ただし、ユーザーが入力する必要があるフィールドと、文書内のどこに配置されているかをフォームのエディターに伝える必要があります。

![いくつかのフィールドがある保険フォームのスクリーンショット](assets/GSASAPI_1.png)

上の文書には、まだフィールドが表示されていません。 署名者の情報を収集するフィールド、サイズ、位置を定義する際に追加されます。

次に [Webフォーム](https://secure.na4.adobesign.com/public/agreements/#agreement_type=webform) 「契約書」ページをタブでクリックし、作成したフォームを見つけます。

![「Acrobat Signの管理」タブのスクリーンショット](assets/GSASAPI_2.png)

![「Webフォーム」が選択された「Acrobat Signの管理」タブのスクリーンショット](assets/GSASAPI_3.png)

クリック **編集** をクリックして、ドキュメント編集ページを開きます。 使用可能な定義済みフィールドが右側のパネルに表示されます。

![Acrobat Signフォームオーサリング環境のスクリーンショット](assets/GSASAPI_4.png)

このエディターでは、テキストおよび署名フィールドをドラッグ&amp;ドロップできます。 必要なフィールドをすべて追加したら、フィールドのサイズを変更して配置し、フォームに磨きをかけることができます。 最後に、 **保存** をクリックしてフォームを作成します。

![フォームフィールドが追加されたAcrobat Signフォームオーサリング環境のスクリーンショット](assets/GSASAPI_5.png)

## 署名用のWebフォームの送信

Webフォームを完了したら、ユーザーが入力および署名できるように送信する必要があります。 フォームを保存したら、URLと埋め込みコードを表示してコピーできます。

**WebフォームURLをコピー**：このURLを使用して、レビューおよび署名のためにホストされている本契約書のバージョンにユーザーを送信します。 次に例を示します。

[https://secure.na4.adobesign.com/public/esignWidget?wid=CBFCIBAA3...babw\*](https://secure.na4.adobesign.com/public/esignWidget?wid=CBFCIBAA3AAABLblqZhCndYscuKcDMPiVfQlpaGPb-5D7ebE9NUTQ6x6jK7PIs8HCtTzr3HOx8U6D5qqbabw*)

**Webフォーム埋め込みコードをコピー**：このコードをコピーしてHTMLーに貼り付け、webサイトに契約書を追加します。

次に例を示します。

```
<iframe
src="https://secure.na4.adobesign.com/public/esignWidget?wid=CBFC
...yx8*&hosted=false" width="100%" height="100%" frameborder="0"
style="border: 0;
overflow: hidden; min-height: 500px; min-width: 600px;"></iframe>
```

![最終Webフォームのスクリーンショット](assets/GSASAPI_6.png)

ユーザーがフォームのホストされたバージョンにアクセスすると、最初にアップロードされた一時的な文書を確認し、指定された位置にフィールドを配置します。

![最終Webフォームのスクリーンショット](assets/GSASAPI_7.png)

ユーザーがフィールドに入力し、フォームに署名します。

![ユーザーが「署名」フィールドを選択したスクリーンショット](assets/GSASAPI_8.png)

次に、ユーザーは以前に保存された署名を使用するか、新しい署名を使用して、文書に署名します。

![署名エクスペリエンスのスクリーンショット](assets/GSASAPI_9.png)

![署名のスクリーンショット](assets/GSASAPI_10.png)

ユーザーがクリックしたとき **適用**、Adobeは電子メールを開いて署名を確認するように指示します。 署名は確認が届くまで保留中です。

![あと1ステップのスクリーンショット](assets/GSASAPI_11.png)

この認証により、多要素認証が追加され、署名プロセスのセキュリティが強化されます。

![確認メッセージのスクリーンショット](assets/GSASAPI_12.png)

![完了メッセージのスクリーンショット](assets/GSASAPI_13.png)

## 入力済みのWebフォームを読み取り中

次に、ユーザーが入力したフォームデータを取得します。 この `/widgets/{widgetId}/formData` endpointは、ユーザーがフォームに署名したときに、ユーザーが入力したデータを対話型フォームに取得します。

```
GET /api/rest/v6/widgets/{widgetId}/formData HTTP/1.1
Host: {YOUR-API-ACCESS-POINT}
Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
Accept: text/csv
```

生成されるCSVファイルストリームには、フォームデータが含まれます。

```
Response Body:
"Agreement
name","completed","email","role","first","last","title","company","agreementId",
"email verified","web form signed/approved"
"Insurance Form","","myemail@email.com","SIGNER","John","Doe","My Job Title","My
Company Name","","","2021-03-07 19:32:59"
```

## 契約書の作成

Webフォームの代わりに、契約書を作成することもできます。 次のセクションでは、Sign APIを使用して契約書を管理するための簡単な手順を示します。

署名または承認のために、指定した受信者に文書を送信すると、契約書が作成されます。 APIを使用して、契約書のステータスと完了を追跡できます。

契約書は、 [一時的文書](https://helpx.adobe.com/sign/kb/how-to-send-an-agreement-through-REST-API.html), [ライブラリ文書](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/samples/send_using_library_doc.md)、またはURL。 この例では、契約書は `transientDocumentId`前に作成したWebフォームと同様です。

```
POST /api/rest/v6/agreements HTTP/1.1
Host: {YOUR-API-ACCESS-POINT}
Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
x-api-user: email:your-api-user@your-domain.com
Content-Type: application/json
Accept: application/json
Request Body:
{
    "fileInfos": [
      {
      "transientDocumentId": "{transientDocumentId}"
      }
     ],
    "name": "{agreementName}",
    "participantSetsInfo": [
      {
      "memberInfos": [
          {
          "email": "{signerEmail}"
          }
        ],
        "order": 1,
        "role": "SIGNER"
      }
    ],
    "signatureType": "ESIGN",
    "state": "IN_PROCESS"
  }
```

この例では、契約書はIN_PROCESSとして作成されますが、次の3つの異なる状態のいずれかで作成できます。

* DRAFT – 契約書を送信する前に段階的に作成します。

* AUTHORING – 契約書のフォームフィールドを追加または編集します。

* IN_PROCESS – 契約書をすぐに送信します。

契約書の状態を変更するには、 `PUT /agreements/{agreementId}/state` エンドポイント：次のいずれかの許可された状態遷移を実行します。

* オーサリングへのドラフト

* IN_PROCESSへのオーサリング

* IN_PROCESSをCANCELLEDに

この `participantSetsInfo` 上記のプロパティにより、契約書への参加を期待されるユーザーの電子メールと、そのユーザーが実行するアクション（署名、承認、確認など）が提供されます。 上記の例では、参加者は1人だけです。署名者です。 手書き署名は1つの文書につき4つまでに制限されています。

Webフォームとは異なり、契約書を作成すると、Adobeによって署名用に自動送信されます。 エンドポイントは、契約書の一意のIDを返します。


```
  Response Body:

  {
     id (string): The unique identifier of the agreement
  }
```

## 契約書メンバーに関する情報の取得

契約書を作成したら、 `/agreements/{agreementId}/members` エンドポイントを使用して、契約書のメンバーに関する情報を取得します。 例えば、参加者が契約書に署名したかどうかを確認できます。

```
GET /api/rest/v6/agreements/{agreementId}/members HTTP/1.1
Host: {YOUR-API-ACCESS-POINT}
Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
Accept: application/json
```

生成されるJSON応答の本文には、参加者に関する情報が含まれています。

```
  Response Body:

  {
     "participantSets":[
        {
           "memberInfos":[
              {
                 "id":"CBJ...xvM",
                 "email":"participant@email.com",
                 "self":false,
                 "securityOption":{
                    "authenticationMethod":"NONE"
                 },
                 "name":"John Doe",
                 "status":"ACTIVE",
                 "createdDate":"2021-03-16T03:48:39Z",
                 "userId":"CBJ...vPv"
              }
           ],
           "id":"CBJ...81x",
           "role":"SIGNER",
           "status":"WAITING_FOR_MY_SIGNATURE",
           "order":1
        }
     ],
```

## 契約書のリマインダーの送信

ビジネス規則によっては、期限によって、特定の日付以降に参加者が契約書に署名できない場合があります。 契約書に有効期限がある場合、その日付が近づくと参加者に通知できます。

への通話後に受信した契約書メンバーの情報に基づきます。 `/agreements/{agreementId}/members` エンドポイント最後のセクションでは、契約書にまだ署名していないすべての参加者に対して、電子メールのリマインダーを発行できます。

へのPOSTリクエスト `/agreements/{agreementId}/reminders` 「エンドポイント」を選択すると、 `agreementId` パラメータで指定します。

```
POST /agreements/{agreementId}/reminders HTTP/1.1
Host: {YOUR-API-ACCESS-POINT}
Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
x-api-user: email:your-api-user@your-domain.com
Content-Type: application/json
Accept: application/json
  Request Body:

  {
    "recipientParticipantIds": [{agreementMemberIdList}],
    "agreementId": "{agreementId}",
    "note": "This is a reminder that you haven't signed the agreement yet.",
    "status": "ACTIVE"
  }

  Response Body:

  {
     id (string, optional): An identifier of the reminder resource created on the
     server. If provided in POST or PUT, it will be ignored
  }
```

リマインダーを投稿すると、ユーザーは、契約書の詳細と契約書へのリンクが記載された電子メールを受信します。

![リマインダーメッセージのスクリーンショット](assets/GSASAPI_14.png)

## 完了した契約書を読み取り中

Webフォームと同様に、受信者が署名した契約書の詳細を読むことができます。 この `/agreements/{agreementId}/formData` エンドポイントは、ユーザーがWebフォームに署名したときに入力したデータを取得します。

```
GET /api/rest/v6/agreements/{agreementId}/formData HTTP/1.1
Host: {YOUR-API-ACCESS-POINT}
Authorization: Bearer {YOUR-INTEGRATION-KEY-HERE}
Accept: text/csv
Response Body:
"completed","email","role","first","last","title","company","agreementId"
"2021-03-16 18:11:45","myemail@email.com","SIGNER","John","Doe","My Job Title","My
Company Name","CBJCHBCAABAA5Z84zy69q_Ilpuy5DzUAahVfcNZillDt"
```

## 次の手順

Acrobat Sign APIを使用すると、文書、Webフォーム、契約書を管理できます。 Webフォームと契約書を使用して作成された、シンプルでありながら完全なワークフローは、開発者が任意の言語を使用して実装できる一般的な方法で実行されます。

Sign APIの仕組みについては、 [API使用デベロッパーガイド](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/api_usage.md). このドキュメントには、この記事で行う多くの手順に関する短い記事と、その他の関連トピックが含まれています。

Acrobat Sign APIは、次のいくつかの階層で利用できます [シングルユーザーおよびマルチユーザーの電子サインプラン](https://acrobat.adobe.com/jp/ja/sign/pricing/plans.html)ニーズに最適な価格モデルを選択できます。 これでSign APIをアプリに組み込む簡単さが分かりました。次のような他の機能に興味があるかもしれません。 [Acrobat Sign Webhook](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/webhooks.md)（プッシュベースのプログラミングモデル）。 Webhookでは、アプリケーションでAcrobat Signイベントを頻繁に確認する必要がなくなり、イベントが発生したときにSign APIがPOSTコールバックリクエストを実行するHTTP URLを登録できます。 Webhookを使用すると、アプリケーションにリアルタイムで即座に更新を適用できるため、堅牢なプログラミングが可能になります。

詳細については、 [従量課金制の価格](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)6か月間のAdobe PDF Services API無料体験版が終了する場合と、Adobe PDF Embed API無料版が提供されます。

自動文書作成や文書署名などの魅力的な機能をアプリに追加するには、を開始してください [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html).

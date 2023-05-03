---
title: 従業員オンボーディングの近代化
description: 従業員オンボーディングを [!DNL Adobe Acrobat Services] API
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-10203.jpg
kt: 10203
exl-id: 0186b3ee-4915-4edd-8c05-1cbf65648239
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1514'
ht-degree: 1%

---

# 従業員オンボーディングの近代化

![ユースケースのヒーローバナー](assets/usecaseemployeeonboardinghero.jpg)

大規模な組織では、従業員のオンボーディングは大規模で時間のかかるプロセスになる場合があります。 通常、カスタマイズされた文書と、新入社員が提示して署名する必要があるボイラープレート資料が混在しています。 カスタマイズされたボイラープレートの材料を組み合わせて使用するには、複数のステップが必要です。プロセスに関わる人々から貴重な時間を奪うことになります。 [!DNL Adobe Acrobat Services] Acrobat Signでは、このアプローチを近代化して自動化することで、より重要な業務のために人事をパーソナル化することができます。 次に、その方法を見てみましょう。

## 概要 [!DNL Adobe Acrobat Services]?

[[!DNL Adobe Acrobat Services]](https://developer.adobe.com/document-services/homepage) は、ドキュメントの操作に関連する一連の API です (PDF以外 )。 大まかに言えば、このサービススイートは次の 3 つの主要なカテゴリに分類されます。

* まず、 [PDFサービス](https://developer.adobe.com/document-services/apis/pdf-services/) ツールのセット これらは、アプリケーションやその他のドキュメントを操作するための「PDF」メソッドです。 サービスには、PDFとの相互変換、OCR の実行と最適化、PDFの結合と分割などがあります。 文書処理機能のツールボックスです。
* [PDF抽出 API](https://developer.adobe.com/document-services/apis/pdf-extract/) は、強力な AI/ML 技術を使用してPDFを分析し、コンテンツに関する信じられないほどの詳細を返します。 これには、テキスト、スタイル、位置情報が含まれます。また、CSV/XLS 形式で表データを返したり、画像を取得することもできます。
* 最後に [ドキュメント生成 API](https://developer.adobe.com/document-services/apis/doc-generation/) Microsoft Word を「テンプレート」として使用したり、（任意のソースからの）データと組み合わせたり、動的にパーソナライズされた文書 (PDFおよび Word) を生成したりできます。

開発者は、 [登録](https://documentcloud.adobe.com/dc-integration-creation-app-cdn/main.html) 無料体験版でこれらのサービスをすべて試すことができます。 この [!DNL Acrobat Services] プラットフォームは REST ベースの API を使用しますが、Node、Java、.NET、Python 用の SDK もサポートします（現時点では Extract のみ）。

API ではありませんが、開発者は無料の [PDF埋め込み API](https://developer.adobe.com/document-services/apis/pdf-embed/)を使用すると、Web ページでドキュメントを一貫した柔軟な方法で表示できます。

##  Acrobat Sign とは何ですか。

[Acrobat Sign](https://www.adobe.com/jp/sign.html) は、電子サインサービスの世界的リーダーです。 複数の署名を含む、様々なワークフローを使用して署名用の文書を送信できます。 Acrobat Signは、署名や追加情報を必要とするワークフローもサポートしています。 これらの機能はすべて、柔軟なオーサリングシステムを備えた強力なダッシュボードによってサポートされます。

～と同じように [!DNL Acrobat Services]、Acrobat Signには [無料体験版](https://www.adobe.com/sign.html#sign_free_trial) これにより、開発者はダッシュボードと使いやすい REST ベースの API の両方を使用して署名プロセスをテストできます。

## オンボーディングシナリオ

Adobeのサービスがどのように役立つかを示す実際のシナリオを考えてみましょう。 新入社員が入社する際には、それぞれの役割に合わせてカスタマイズされた情報が必要です。 さらに、企業全体の資料も必要です。 最後に、文書に署名して、企業ポリシーの受諾を示す必要があります。 具体的なステップに分けてみましょう。

* まず、新しい従業員の名前を迎えるカスタマイズされたカバーレターが必要です。 レターには、従業員の名前、ロール、給与、および場所に関する情報を含める必要があります。
* カスタマイズされたレターは、基本的な全社的な情報（様々な人事方針、福利厚生などを考える）を含むPDFと組み合わせる必要があります。
* 従業員の署名と日付を求める最終文書を含める必要があります。
* 上記のすべては、署名のために従業員に送信される 1 つの文書として提示する必要があります。

これを行う方法について詳しく説明します。

## 動的ドキュメントの生成

Adobe [文書生成](https://developer.adobe.com/document-services/apis/doc-generation/) 開発者は、API を使用して、Microsoft Word とシンプルなテンプレート言語を使用し、動的なドキュメントを作成して、PDFや Word ドキュメントを生成できます。 これがどのように機能するかの例を示します。

まず、値がハードコードされた Word 文書から始めましょう。 ドキュメントには、グラフィックや表など、任意のスタイルを設定できます。 これが最初の文書です。

![イニシャルドキュメントのスクリーンショット](assets/onboarding_1.png)

文書の生成は、データに置き換えられる Word 文書に「トークン」を追加することで機能します。 これらのトークンは手動で入力できますが、 [Microsoft Word add-in](https://developer.adobe.com/document-services/docs/overview/document-generation-api/wordaddin/) 簡単にできます。 文書を開くと、文書内で使用できるタグやデータセットを作成者が定義するためのツールが提供されます。

![Document Tagger のスクリーンショット](assets/onboarding_2.png)

ローカルファイルから JSON 情報をアップロードするか、JSON テキストでコピーするか、または初期データを続行するように選択できます。 これにより、特定のニーズに基づいてタグをアドホックに定義できます。 この例では、name、role、salary、および location のタグのみが必要です。 これを行うには、 **タグを作成** ボタン：

![タグ定義のスクリーンショット](assets/onboarding_3.png)

最初のタグを定義した後は、必要な数だけ引き続き定義できます。

![定義されたタグのスクリーンショット](assets/onboarding_4.png)

タグを定義して、文書内のテキストを選択し、必要に応じてタグで置き換えます。 この例では、name、role、salary にタグが追加されています。

![タグのスクリーンショット](assets/onboarding_5.png)

文書生成は、単純なタグだけでなく、論理式もサポートします。 文書の 2 番目の段落には、ルイジアナ州の人々にのみ適用されるテキストが含まれています。 コンディショナル式を追加するには、文書タグの「詳細」タブに移動し、条件を定義します。 ここでは、単純な等価条件を定義する方法を示しますが、数値比較やその他の比較タイプもサポートされています。

![条件のスクリーンショット](assets/onboarding_6.png)

この段落は、次のように挿入して囲むことができます。

![ドキュメント内の条件のスクリーンショット](assets/onboarding_7.png)

この動作をテストするには、 **ドキュメントの生成**&#x200B;を選択します。 これを初めて行う場合は、Adobe IDでログインする必要があります。 ログインすると、デフォルトの JSON が表示され、手動で編集できます。

![データのスクリーンショット](assets/onboarding_8.png)

PDFが生成され、表示またはダウンロードできます。

![生成されたPDF](assets/onboarding_9.png)

Document Tagger を使用すると、迅速なデザインとテストが可能ですが、いったん作成して実稼働中であれば、いずれかの SDK を使用してこのプロセスを自動化できます。 実際のコードは特定のニーズに応じて異なりますが、Node.js ではこのコードがどのように見えるかの例を示します。

```js
 const PDFServicesSdk = require('@adobe/pdfservices-node-sdk');

const credentials =  PDFServicesSdk.Credentials
    .serviceAccountCredentialsBuilder()
    .fromFile("pdfservices-api-credentials.json")
    .build();

// Data would be dynamic...
let data = {
    "name":"Raymond Camden",
    "role":"Lead Developer",
    "salary":9000,
    "location":"Louisiana"
}

// Create an ExecutionContext using credentials.
const executionContext = PDFServicesSdk.ExecutionContext.create(credentials);

// Create a new DocumentMerge options instance.
const documentMerge = PDFServicesSdk.DocumentMerge,
    documentMergeOptions = documentMerge.options,
    options = new documentMergeOptions.DocumentMergeOptions(jsonDataForMerge, documentMergeOptions.OutputFormat.PDF);

// Create a new operation instance using the options instance.
const documentMergeOperation = documentMerge.Operation.createNew(options);

// Set operation input document template from a source file.
const input = PDFServicesSdk.FileRef.createFromLocalFile('documentMergeTemplate.docx');
documentMergeOperation.setInput(input);

// Execute the operation and Save the result to the specified location.
documentMergeOperation.execute(executionContext)
    .then(result => result.saveAsFile('documentOutput.pdf'))
    .catch(err => {
        if(err instanceof PDFServicesSdk.Error.ServiceApiError
            || err instanceof PDFServicesSdk.Error.ServiceUsageError) {
            console.log('Exception encountered while executing operation', err);
        } else {
            console.log('Exception encountered while executing operation', err);
        }
    });
```

簡単に言うと、コードは資格情報を設定し、操作オブジェクトを作成し、入力とオプションを設定してから、操作を呼び出します。 最後に、結果をPDFとして保存します。 （結果は Word 形式でも出力できます）。

ドキュメント生成は、完全に動的なテーブルやイメージを持つ機能など、はるかに複雑なユースケースをサポートします。 詳しくは、 [文書](https://developer.adobe.com/document-services/docs/overview/document-generation-api/) を参照してください。

## PDF操作

この [PDFサービス API](https://developer.adobe.com/document-services/apis/pdf-services/) には、PDFを操作するための「ユーティリティ」操作が多数用意されています。 これらの操作には次のものがあります。

* Office ドキュメントからのPDFの作成
* Office ドキュメントへのPDFのエクスポート
* 結合と分割のPDF
* OCR の適用 (PDF)
* 保護の設定、削除、および変更をPDF
* ページの削除、挿入、並べ替えおよび回転
* 圧縮または線形PDFによる最適化
* PDFの取得

このシナリオでは、ドキュメント生成の呼び出しの結果を標準PDFにマージする必要があります。 SDK を使用すれば、この操作はかなり簡単です。 Node.js の例を次に示します。

```js
const PDFServicesSdk = require('@adobe/pdfservices-node-sdk');
 
// Initial setup, create credentials instance.
const credentials = PDFServicesSdk.Credentials
    .serviceAccountCredentialsBuilder()
    .fromFile("pdfservices-api-credentials.json")
    .build();
 
// Create an ExecutionContext using credentials and create a new operation instance.
const executionContext = PDFServicesSdk.ExecutionContext.create(credentials),
    combineFilesOperation = PDFServicesSdk.CombineFiles.Operation.createNew();
 
// Set operation input from a source file.
const combineSource1 = PDFServicesSdk.FileRef.createFromLocalFile('documentOutput.pdf'),
      combineSource2 = PDFServicesSdk.FileRef.createFromLocalFile('standardCorporate.pdf');

combineFilesOperation.addInput(combineSource1);
combineFilesOperation.addInput(combineSource2);
 
// Execute the operation and Save the result to the specified location.
combineFilesOperation.execute(executionContext)
    .then(result => result.saveAsFile('combineFilesOutput.pdf'))
    .catch(err => {
        if (err instanceof PDFServicesSdk.Error.ServiceApiError
            || err instanceof PDFServicesSdk.Error.ServiceUsageError) {
            console.log('Exception encountered while executing operation', err);
        } else {
            console.log('Exception encountered while executing operation', err);
        }
    });
```

このコードは、2 つのPDFを取得してマージし、結果を新しいPDFに保存します。 シンプルで簡単！ 詳しくは、 [文書](https://developer.adobe.com/document-services/docs/overview/pdf-services-api/) を参照してください。

## 署名プロセス

オンボーディングプロセスの最後の停止では、従業員は契約書に署名し、その中で定義されているすべてのポリシーを読んで同意したことを明記する必要があります。 [Acrobat Sign](https://www.adobe.com/jp/sign.html) では、 [API](https://opensource.adobe.com/acrobat-sign/developer_guide/index.html)を選択します。 大まかに言えば、シナリオの最終部分は次のように完了できます。

まず、署名が必要なフォームを含む文書をデザインします。 これには、Adobe Signのユーザーダッシュボードでデザインしたビジュアルなど、複数の方法があります。 もう 1 つのオプションは、文書生成 Word アドインを使用してタグを挿入することです。 次の使用例は、署名と日付を要求します。

![署名タグ付き文書のスクリーンショット](assets/onboarding_10.png)

この文書はPDFとして保存できます。前述の方法と同じ方法で、すべての文書を結合します。 このプロセスにより、パーソナライズされたグリーティング、標準的な企業ドキュメント、署名に適した最終ページを含む、まとまりのある 1 つのパッケージが作成されます。

テンプレートはAcrobat Signダッシュボードにアップロードして、新しい契約書に使用できます。 REST API を使用すると、この文書を見込み従業員に送信して署名を依頼できます。

![署名済み文書のスクリーンショット](assets/onboarding_11.png)

## 実際に操作する

この記事で説明されているすべての内容は、今すぐテストできます。 この [!DNL Adobe Acrobat Services] API [無料体験版](https://documentcloud.adobe.com/dc-integration-creation-app-cdn/main.html) 現在、6 ヶ月間で 1,000 件の無料リクエストを受け付けています。 Acrobat Sign [無料体験版](https://www.adobe.com/sign.html#sign_free_trial) では、テスト目的で透かし入りの契約書を送信できます。

ご質問がある場合はこの [サポートフォーラム](https://community.adobe.com/t5/document-services-apis/ct-p/ct-Document-Cloud-SDK) は、開発者やサポートAdobeによって毎日監視されています。 多くのインスピレーションを得るには、次の [ペーパークリップ](https://www.youtube.com/playlist?list=PLcVEYUqU7VRe4sT-Bf8flvRz1XXUyGmtF) エピソード ニュース、デモ、顧客との会話を含む定期的なライブ会議があります。

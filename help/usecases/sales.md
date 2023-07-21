---
title: 販売提案および契約の管理
description: 販売提案を自動化および簡素化するための効率的なワークフローを構築する方法について説明します
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8099.jpg
jira: KT-8099
exl-id: 219c70de-fec1-4946-b10e-8ab5812562ef
source-git-commit: 2d1151c17dfcfa67aca05411976f4ef17adf421b
workflow-type: tm+mt
source-wordcount: '1395'
ht-degree: 2%

---

# 販売提案および契約の管理

![ユースケースのヒーローバナー](assets/UseCaseSalesHero.jpg)

販売提案は、顧客獲得に向けたビジネスの第一歩です。 他の物と同様に第一印象は残る。 そのため、顧客との最初のやり取りは、ビジネスに対する期待を設定します。 提案書は、簡潔かつ正確で、便利なものでなければなりません。

契約書や提案書の文書構造内には、様々な種類のデータが含まれています。 動的データ（クライアント名、見積もり額など）と静的データ（企業機能、チーム・プロファイル、標準 SOW 用語などのボイラープレート・テキスト）の両方が含まれます。 販売提案書などのテンプレート文書を作成する場合、多くの場合、ボイラープレートテンプレートのプロジェクトの詳細を手動で置き換えるなど、単調な作業が伴います。 このチュートリアルでは、動的データとワークフローを使用して、 [販売提案の作成](https://www.adobe.io/apis/documentcloud/dcsdk/sales-proposals-and-contracts.html)を選択します。

## 学習内容

この実践チュートリアルでは、複数のツールを使用して動的データとワークフローを実装する方法を学習します。最も重要なツールは次のとおりです [!DNL Adobe Acrobat Services] API これらの API を使用して、お客様とお客様のビジネスに対する販売提案と契約の利便性が高まります。 このチュートリアルでは、PDFドキュメントを自動的に作成、結合、表示する方法を実際に操作しながら説明します。 これらのタスクを手動で実行するのは、時間がかかり、面倒です。 利用して [!DNL Acrobat Services] API を使用すると、これらのタスクにかかる時間を短縮できます。

## 関連する API とリソース

* [Microsoft Word](https://www.office.com/)

* [Node.js](https://nodejs.org/en/)

* [npm](https://www.npmjs.com/get-npm)

* [[!DNL Acrobat Services] API](https://www.adobe.io/apis/documentcloud/dcsdk/)

* [Adobeドキュメント生成 API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Adobe文書生成タグ](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo)

## 問題の解決

ツールをインストールしたら、問題の解決を開始できます。 提案書には、各クライアントに固有の静的コンテンツと動的コンテンツの両方が含まれています。 ボトルネックは、提案を行うたびに両方のタイプのデータが必要になるため発生します。 静止テキストの入力には時間がかかるので、これを自動化し、各クライアントの動的データを手動でのみ処理します。

まず、 [Microsoft Forms](https://www.office.com/launch/forms?auth=1) （または任意のフォームビルダー）を選択します。 このフォームは、顧客からの動的データを販売提案に追加するためのフォームです。 このフォームに、会社名、日付、住所、プロジェクトの範囲、価格、その他のコメントなど、クライアントから必要な情報を取得するための質問を記入してください。 独自に作成するには、これを使用します [form](https://forms.office.com/Pages/ShareFormPage.aspx id=DQSIkWdsW0yxEjajBLZtrQAAAAAAAAAAAAAAN__rtiGj5UNElTR0pCQ09ZNkJRUlowSjVQWDNYUEg2RC4u&amp;sharetoken=1AJeMavBAzzxuISRKmUy)。 潜在的なクライアントは、フォームに入力し、その返答を JSON ファイルとして書き出すことを目標としています。このファイルはワークフローの次の部分に渡されます。

一部のフォームビルダーでは、データを CSV ファイルとしてのみエクスポートできます。 そのため、 [変換](http://csvjson.com/csv2json) 生成された CSV ファイルを JSON ファイルに変換します。

静的データは、すべての販売提案で再利用されます。 そのため、Microsoft Word の販売提案テンプレートを使用して、静的テキストを提供できます。 この [template](https://1drv.ms/w/s!AiqaN2pp7giKkmhVu2_2pId9MiPa?e=oeqoQ2)ただし、独自に作成することも、 [Adobe鋳型](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)を選択します。

次に、クライアントからの動的データを JSON 形式で取り込み、Microsoft Word テンプレートの静的テキストを取り込んで、クライアント向けにユニークな販売提案を作成する機能が必要です。 この [!DNL Acrobat Services] API を使用して 2 つをマージし、署名可能なPDFを生成します。

この機能を使用するには、タグを使用します。 タグは、数字、単語、配列、または複雑なオブジェクトを表すことのできる使いやすいストリングです。 タグは動的データのプレースホルダーとして機能します。この場合、タグはフォームに入力されたクライアントデータです。 テンプレートにタグを挿入したら、フォームフィールドを JSON ファイルから Word テンプレートにマッピングできます。

## タグの使用

販売提案テンプレートを開き、 **挿入** タブを選択します。 」を **アドイン** 」グループで、「 **アドインを入手**&#x200B;を選択します。 次に、 **Adobeドキュメント生成アドイン** を選択します。 追加すると、 **ホーム** タブを **Adobe** を選択します。

」を **ホーム** タブを **Adobe** 」グループで、「 **文書生成** 」をクリックして、ドキュメントのタグ付けを開始します。 ウィンドウの右側にあるパネルに、役立つデモビデオが表示されます。

![Word 内の Document Tagger アドインのスクリーンショット](assets/sales_1.png)

選択 **はじめに**&#x200B;を選択します。 その後、サンプルデータの提供を求めるメッセージが表示されます。 フォームの応答 JSON ファイルを次のように貼り付けるか、アップロードします。

![サンプルコードのペーストのスクリーンショット](assets/sales_2.png)

選択 **タグを生成** を使用して、ペーストまたはアップロードした JSON ファイルからフィールドのリストを取得します。 タグは、右側のサイドバーに次のように表示されます。

![使用可能なタグのスクリーンショット](assets/sales_3.png)

タグを生成した後で、それらを文書に挿入できます。 タグが文書のカーソル位置に追加されます。 上記のように、 **プロジェクト範囲** タグを **プロジェクト範囲** 字幕、 これにより、クライアントがフォームにプロジェクトのスコープを入力すると、その応答は **プロジェクト範囲** 字幕を追加し、先ほど追加したタグを置き換えます。 タグの追加が完了すると、文書の一部が以下のスクリーンキャプチャのように表示されます。

![Word 文書へのタグ追加のスクリーンショット](assets/sales_4.png)

## API の使用

次の [!DNL Acrobat Services] API [ホームページ](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)を選択します。 使用を開始するには [!DNL Acrobat Services] API では、アプリケーションの資格情報が必要です。 一番下にスクロールして、 **無料で始める** 資格情報を作成します。 これらのサービスを使用できます [6 ヶ月間無料で、そのまま利用可能](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 文書のトランザクションあたり 0.05 ドルで、必要な分だけ支払います。

選択 **PDFサービス API** を選択し、以下に示すように、その他の詳細を入力します。

![資格情報の取得のスクリーンショット](assets/sales_5.png)

資格情報を作成すると、いくつかのコードサンプルを取得できます。 使用する言語を選択します（このチュートリアルでは Node.js を使用します）。 API 資格情報は zip ファイルに含まれています。 ファイルを PDFToolsSDK-Node.jsSamples に抽出します。

まず、auto-doc\*\*という名前の空のフォルダを作成します。\*\*フォルダーで次のコマンドを実行して、Node.js プロジェクトを初期化します。 `npm init`を選択します。 プロジェクトに「auto-doc」という名前を付けます。*を選択します。*

フォルダー内。/PDFToolsSDK-Node.jsSamples/adobe-dc-pdf-tools-sdk-node-samples には、pdftools-api-credentials.json というファイルがあります。 private.key とともに auto-doc フォルダーに移動します。 API 資格情報が含まれます。 また、auto-doc フォルダーに、「resources」というサブフォルダーを作成します。 販売提案を生成するたびに、クライアントから受信する JSON 形式のデータが保持されます。 同じフォルダーに、Microsoft Word の販売提案テンプレートを保存します。

今、あなたは魔法を作る準備ができています！ このチュートリアルでは Node.js を使用しているため、Node.js をインストールする必要があります [!DNL Acrobat Services] SDK これを行うには、auto-doc フォルダーで、yarn add @adobe/documentservices-pdftools-node-sdk を実行します。

merge.js というファイルを作成し、次のコードを貼り付けます。

```
javascript
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk'),
fs = require('fs');
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Setup input data for the document merge process
const jsonString = fs.readFileSync('resources/Proposal.json'),
jsonDataForMerge = JSON.parse(jsonString);
// Create an ExecutionContext using credentials
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials);
// Create a new DocumentMerge options instance
const documentMerge = PDFToolsSdk.DocumentMerge,
documentMergeOptions = documentMerge.options,
options = new documentMergeOptions.DocumentMergeOptions(jsonDataForMerge, documentMergeOptions.OutputFormat.PDF);
// Create a new operation instance using the options instance
const documentMergeOperation = documentMerge.Operation.createNew(options)
// Set operation input document template from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile('resources/Proposal.docx');
documentMergeOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
documentMergeOperation.execute(executionContext)
.then(result => result.saveAsFile('output/Proposal.pdf'))
.catch(err => {
if (err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log('Exception encountered while executing operation', err);
} else {
console.log('Exception encountered while executing operation', err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
```

このコードは、 [!DNL Acrobat Services]を選択します。 次に、データをMicrosoft Word で作成した販売提案テンプレートと結合して、まったく新しいPDFを生成します。 PDFは、新しく作成した/output フォルダーに保存されます。

また、コードでは [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html) 生成された販売提案に両社が署名するようにします。 この API の詳細については、このブログの記事を参照してください。

## 次の手順

非効率的で手間のかかるプロセスから始め、自動化が必要でした。 あらゆるクライアントに対して手動で文書を作成する必要がなくなり、自動化と簡素化のための効率的なワークフローを作成できるようになりました [販売提案プロセス](https://www.adobe.io/apis/documentcloud/dcsdk/sales-proposals-and-contracts.html)を選択します。

Microsoft Formsを使用すると、クライアントから得た重要なデータを独自の提案に反映させることができます。 Microsoft Word で販売提案テンプレートを作成して、毎回再作成する必要のない静的テキストを提供しました。 次に、 [!DNL Acrobat Services] フォームとテンプレートのデータを結合する API を使用して、より効率的に顧客の売上提案PDFを作成することができます。

この実践チュートリアルでは、これらの API で何が可能かをほんの一瞬で説明します。 その他のソリューションについては、 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) API ページを参照してください。 これらのツールをすべて 6 ヶ月間無料で使用できます。 その後、 [従量制の](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 予算を計画し、セールスパイプラインに見込み客を追加する場合にのみ料金を支払います。

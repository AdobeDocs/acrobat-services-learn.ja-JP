---
title: 営業提案および契約の管理
description: 販売提案を自動化および簡素化するための効率的なワークフローを構築する方法を説明します
role: Developer
level: Intermediate
type: Tutorial
feature: Use Cases
thumbnail: KT-8099.jpg
jira: KT-8099
exl-id: 219c70de-fec1-4946-b10e-8ab5812562ef
source-git-commit: b65ffa3efa3978587564eb0be0c0e7381c8c83ab
workflow-type: tm+mt
source-wordcount: '1395'
ht-degree: 2%

---

# 販売提案と契約の管理

![ユースケースの英雄バナー](assets/UseCaseSalesHero.jpg)

販売提案は、顧客獲得に向けたビジネスの道のりの最初のステップです。 何事にも第一印象は続く。 そのため、最初に顧客とやり取りすることで、ビジネスに対する期待が高まります。 あなたの提案は簡潔、正確、そして便利でなければならない。

契約書と提案書には、その文書構造内に様々な種類のデータが含まれています。 動的データ（クライアント名、見積書の金額など）と静的データ（会社の能力、チームプロファイル、標準のSOW条件などの定型文）の両方が含まれます。 販売提案などのテンプレート文書を作成する場合は、プロジェクトの詳細をボイラープレートテンプレートに手動で置き換えるなど、単調なタスクが伴うことがよくあります。 このチュートリアルでは、動的データとワークフローを使用して、 [販売提案の作成](https://www.adobe.io/apis/documentcloud/dcsdk/sales-proposals-and-contracts.html).

## 学習内容

この実践チュートリアルでは、複数のツールを使用して動的データとワークフローを実装する方法を学習します。そのうち最も重要なものは、次のとおりです [!DNL Adobe Acrobat Services] API。 これらのAPIは、お客様やお客様のビジネスにとって販売提案や契約をより便利にするために使用されます。 このチュートリアルでは、PDF文書を自動的に作成、結合および表示する方法を示す実践的なテクニックを紹介します。 これらの作業を手動で行うと時間がかかり、手間がかかります。 ～を利用して [!DNL Acrobat Services] APIを使用すると、これらのタスクに費やす時間を短縮できます。

## 関連APIとリソース

* [Microsoft Word](https://www.office.com/)

* [Node.js](https://nodejs.org/en/)

* [npm](https://www.npmjs.com/get-npm)

* [[!DNL Acrobat Services] API](https://www.adobe.io/apis/documentcloud/dcsdk/)

* [Adobe文書生成API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Adobe文書生成タガー](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo)

## 問題を解決する

これでツールがインストールされたので、問題の解決を開始できます。 提案書には、各クライアントに固有の静的コンテンツと動的コンテンツの両方が含まれます。 ボトルネックが発生するのは、提案を行うたびに両方のタイプのデータが必要になるためです。 静止テキストの入力には時間がかかるため、テキストを自動化し、各クライアントからの動的データを手動で処理することにします。

最初に、 [Microsoft Forms](https://www.office.com/launch/forms?auth=1) （または推奨のフォームビルダー）。 販売提案に追加されるクライアントからの動的データ用のフォームです。 このフォームに、会社名、日付、住所、プロジェクトの範囲、価格、その他のコメントなど、必要な詳細をクライアントから取得するための質問を入力します。 独自に構築するには、以下を使用します。 [様式](https://forms.office.com/Pages/ShareFormPage.aspx id=DQSIkWdsW0yxEjajBLZtrQAAAAAAAAAAAAN__rtiGj5UNElTR0pCQ09ZNkJRUlowSjVQWDNYUEg2RC4u&amp;sharetoken=1AJeMavBAzzxuISRKmUy)。 潜在的なクライアントがフォームに入力し、応答をJSONファイルとして書き出して、ワークフローの次の部分に渡すことを目的としています。

一部のフォームビルダーでは、データをCSVファイルとして書き出すことしかできません。 そのため、次の操作が役立つ場合があります。 [変換](http://csvjson.com/csv2json) 生成されたCSVファイルをJSONファイルに変換します。

静的データは、すべての販売提案書で再利用されます。 したがって、Microsoft Wordのセールスプロポーザルテンプレートを使用して、静的なテキストを提供できます。 これを使用できます [template](https://1drv.ms/w/s!AiqaN2pp7giKkmhVu2_2pId9MiPa?e=oeqoQ2)ただし、独自に作成したり、 [Adobeテンプレート](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html).

JSON形式のクライアントからの動的データと、Microsoft Wordテンプレートの静的テキストの両方を取り込んで、クライアントに固有の営業提案を作成する機能が必要になりました。 この [!DNL Acrobat Services] APIは、この2つを結合し、署名可能なPDFを生成するために使用されます。

この機能を使用するには、タグを使用します。 タグは、数字、単語、配列、または複雑なオブジェクトを表すことができる使いやすい文字列です。 タグは、動的データ（この場合は、フォームに入力されるクライアントデータ）のプレースホルダーとして機能します。 テンプレートにタグを挿入したら、JSONファイルからWordテンプレートにフォームフィールドをマッピングできます。

## タグの使用

販売提案テンプレートを開き、 **挿入** タブをクリックします。 を **アドイン** グループ、選択 **アドインを取得**. 次に、 **Adobe文書生成アドイン** 追加します。 追加すると、に文書生成タグが表示されます。 **ホーム** タブを **Adobe** グループ：

を **ホーム** タブを **Adobe** グループ、選択 **文書の生成** をクリックして、文書へのタグ付けを開始します。 ウィンドウの右側にあるパネルに、役立つデモビデオが表示されます。

![Word内のDocument Taggerアドインのスクリーンショット](assets/sales_1.png)

選択 **開始する**. 次に、サンプルデータを入力するよう求められます。 次に示すように、form response JSONファイルをペーストまたはアップロードします。

![サンプルコードのペーストのスクリーンショット](assets/sales_2.png)

選択 **タグの生成** ペーストまたはアップロードしたJSONファイルからフィールドのリストを取得します。 右側のサイドバーには、以下のようなタグが表示されます。

![使用可能なタグのスクリーンショット](assets/sales_3.png)

タグの生成後、そのタグを文書に挿入できます。 タグは、カーソル位置の文書に追加されます。 上に示すように、 **プロジェクトの範囲** タグを **プロジェクトの範囲** 字幕。 これにより、クライアントがフォームでプロジェクトのスコープを入力すると、その応答は **プロジェクトの範囲** サブタイトル。追加したタグが置き換えられます。 タグの追加が完了すると、ドキュメントの一部が下の画面キャプチャのようになります。

![Word文書にタグを追加したスクリーンショット](assets/sales_4.png)

## APIの使用

に移動 [!DNL Acrobat Services] API [ホームページ](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html). 使用を開始するには [!DNL Acrobat Services] APIを使用するには、アプリケーションの資格情報が必要です。 下にスクロールして選択 **無料体験を開始** 資格情報を作成します。 次のサービスを利用できます [6か月間無料、その後は従量課金](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 1つの文書トランザクションにつき$ 0.05ですので、必要なものだけを支払うことができます。

選択 **PDFサービスAPI** をサービスとして選択し、以下に示すその他の詳細を入力します。

![資格情報を取得するスクリーンショット](assets/sales_5.png)

資格情報を作成すると、いくつかのコードサンプルが表示されます。 使用する言語を選択します（このチュートリアルではNode.jsを使用します）。 API資格情報はzipファイルです。 ファイルをPDFToolsSDK-Node.jsSamplesに展開します。

まず、auto-doc\*\*という名前の空のフォルダを作成します。\*\*フォルダー内で次のコマンドを実行してNode.jsプロジェクトを初期化します。 `npm init`. プロジェクトに「auto-doc」という名前を付けます。*.*

をクリックします。/PDFToolsSDK-Node.jsSamples/adobe-dc-pdf-tools-sdk-node-samplesには、pdftools-api-credentials.jsonというファイルがあります。 private.keyをauto-docフォルダに移動します。 これには、API資格情報が含まれます。 また、自動ドキュメントフォルダーに、「resources」というサブフォルダーを作成します。 セールスプロポーザルを生成するたびに、クライアントから受信するJSON形式のデータが保持されます。 同じフォルダーに、Microsoft Wordからsales proposalテンプレートを保存します。

これで、魔法を使う準備ができました。 このチュートリアルではNode.jsを使用しているため、Node.jsをインストールする必要があります [!DNL Acrobat Services] SDK。 これを行うには、auto-docフォルダーでyarn add @adobe/documentservices-pdftools-node-sdkを実行します。

merge.jsという名前のファイルを作成して、次のコードを貼り付けます。

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

このコードは、次を使用して作成したタグを使用して、MicrosoftフォームからJSONファイルを取得します [!DNL Acrobat Services]. 次に、そのデータをMicrosoft Wordで作成した販売提案書テンプレートと結合して、まったく新しいPDFを作成します。 PDFは新しく作成されたに保存されます。/outputフォルダーです。

また、コードでは、 [ADOBE SIGN API](https://www.adobe.io/apis/documentcloud/sign.html) 生成された販売提案に両社が署名できるようにする。 このAPIの詳細な説明については、このブログ記事を参照してください。

## 次の手順

自動化が必要な、非効率的で手間のかかるプロセスから始めました。 クライアントごとに手動でドキュメントを作成する代わりに、合理化されたワークフローを作成して自動化と合理化を図る必要がありました [販売提案プロセス](https://www.adobe.io/apis/documentcloud/dcsdk/sales-proposals-and-contracts.html).

Microsoft Formsを使用すると、お客様からそれぞれの提案に含まれる重要なデータを取得できます。 Microsoft Wordでセールスプロポーザルテンプレートを作成し、毎回作成したくない固定テキストを提供しています。 その後は使用しました [!DNL Acrobat Services] フォームとテンプレートのデータを結合し、より効率的な方法でクライアントの販売提案PDFを作成するためのAPI。

この実践チュートリアルでは、これらのAPIで可能なことのほんの一例を示します。 その他のソリューションを見つけるには、 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) APIページ これらのツールはすべて6か月間無料で使用できます。 その後、 [従量課金制](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) プランを作成しておけば、チームが見込み客をセールスパイプラインに追加した場合にのみ、お支払いいただくことができます。

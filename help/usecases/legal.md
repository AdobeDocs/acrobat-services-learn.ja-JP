---
title: 法的契約の管理
description: カスタムデータ入力により、法律文書を自動的に生成して保護する方法について説明します
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8097.jpg
jira: KT-8097
exl-id: e0c32082-4f8f-4d8b-ab12-55d95b5974c5
source-git-commit: 2d1151c17dfcfa67aca05411976f4ef17adf421b
workflow-type: tm+mt
source-wordcount: '2006'
ht-degree: 1%

---

# 法的契約の管理

![ユースケースのヒーローバナー](assets/UseCaseLegalHero.jpg)

デジタル化には課題が伴います 今日、多くの組織では、 [法的契約](https://www.adobe.io/apis/documentcloud/dcsdk/legal-contracts.html) 別の関係者によって作成、編集、承認、署名される必要があります。 このような法的契約では、多くの場合、独自のカスタマイズとブランディングが必要です。 組織は、署名した後に保護された形式で保存して、セキュリティを確保する必要がある場合もあります。 これらを実現するには、堅牢な文書生成および管理ソリューションが必要です。

多くのソリューションでは、一部の文書生成が提供されますが、データ入力や、特定のシナリオにのみ適用される句などの条件付きロジックをカスタマイズすることはできません。 企業の法的テンプレートを手動で更新することは、文書が大規模になるにつれて困難になり、エラーが発生しやすくなります。 これらのプロセスを自動化する必要があります。

## 学習内容

この実践チュートリアルでは、 [[!DNL Adobe Acrobat Services] API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html) ドキュメントのカスタム入力フィールドの生成に使用されます。 また、生成されたドキュメントを保護されたポータブルドキュメントフォーマット (PDF) に簡単に変換して、データ操作を防ぐ方法についても説明します。

このチュートリアルでは、契約書をPDFに変換する際のプログラミングについて説明します。 効果的に追跡するには [Microsoft Word](https://www.microsoft.com/en-us/download/office.aspx) および [Node.js](https://nodejs.org/) を PC にインストールする必要があります。 Node.js および [ES6 構文](https://www.w3schools.com/js/js_es6.asp) も推奨されます。

## 関連する API とリソース

* [Adobeドキュメント生成 API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [PDF埋め込み API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [プロジェクトコード](https://github.com/agavitalis/adobe_legal_contracts.git)

## テンプレートドキュメントの作成

法律文書は、Microsoft Word アプリケーションを使用するか、Adobeの [サンプル Word テンプレート](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html#sample-blade)を選択します。 それでも、入力をカスタマイズし、これらの文書に電子署名するには、次のような補助的なツールを使用する必要があります [Adobeドキュメント生成タグアドイン](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin) Microsoft Word の場合

Document Generation Tagger は、タグを使用して文書をシームレスにカスタマイズできるようにしたMicrosoft Word アドインです。 これにより、JSON データを使用して動的に入力される文書テンプレートに、動的フィールドを作成できます。

![Adobe Document Generation Tagger を Word に追加する方法のスクリーンショット](assets/legal_1.png)

Document Generation Tagger の使い方を説明するために、このアドインをインストールして JSON データモデルを作成します。これは、シンプルな法的契約書のタグ付けに使用されます。

Word で文書生成タグをインストールするには、 **挿入** タブの「アドイン」グループで、「 **マイアドイン**&#x200B;を選択します。 Office アドインメニューで、「Adobeドキュメントの生成」を検索し、 **追加** を選択し、手順に従います。 これらの手順は、上記の画面キャプチャで確認できます。

Document Generation Tagger for Word アドインをインストールしたら、法務文書にタグ付けするためのシンプルな JSON データモデルを作成します。

続行するには、任意のエディターを開いて Agreement.json というファイルを作成し、作成した JSON ファイルに以下のコードスニペットを貼り付けます。

```
{
"Agreement": {
"Date": "1/24/2021",
"Prime Contractor Name": "Ogbonna Vitalis Corp",
"Prime State": "Lagos",
"Address": "Maryland Ave, Lagos State, Ng",
"Sub Contractor Name": "Vivvaa Soln",
"Sub Contractor State": "California",
"Sub Contractor Address": "Molusi Avenue, Dallas Texas, CA",
"Agreement Date": "1/24/2021",
"Length": 5
}
}
```

この JSON ドキュメントを保存したら、Document Generation Tagger アドインに読み込みます。 ドキュメントを読み込むには、 **文書生成** Word 画面の右上にあるAdobeグループに入力します（以下のスクリーンキャプチャを参照）。

![Word のAdobe文書生成タグアドインのスクリーンショット](assets/legal_2.png)

ガイドとなるビデオが表示されます。 タグ管理フィールドを表示または直接移動するには、 **はじめに**&#x200B;を選択します。 クリック後 **はじめに**&#x200B;をクリックすると、アップロードフォームが表示されます。 クリック **JSON ファイルのアップロード** 作成した JSON ファイルを選択します。 読み込みが完了したら、「 **タグを生成** を使用してタグを生成します。

タグを取り込んで生成した後、これらのタグを文書に追加できます。 追加するには、タグを表示する場所にカーソルを置きます。 次に、文書生成 API からタグを選択し、「 **テキストを挿入**&#x200B;を選択します。 次のスクリーンキャプチャは、この手順の概要を示しています。

![文書へのタグ追加のスクリーンショット](assets/legal_3.png)

読み込まれた JSON データモデルを使用して作成された基本的なタグの他に、イメージ、条件付きロジック、計算、繰り返しエレメント、条件付きフレーズなどの詳細なオプションに関する拡張機能も使用できます。 これらの機能にアクセスするには、 **詳細** をクリックします。 以下の画面キャプチャで確認できます。

![タグドキュメント生成タグの「詳細」タブのAdobeのスクリーンショット](assets/legal_4.png)

これらの高度な機能は、基本タグと変わりません。 条件ロジックを含めるには、文書で入力する部分を選択します。 次に、タグの挿入を決定するルールを設定します。

さらに説明するために、契約書に条件付きのみで含めるセクションがあるとします。 「コンテンツタイプを選択」フィールドで、「 **セクション** 「レコードの選択」フィールドで、条件付きセクションを表示するかどうかを決定するオプションを選択します。 目的の条件演算子を選択し、「値」フィールドにテスト対象の値を設定します。 次に、「 **条件を挿入** 次のスクリーンキャプチャは、このプロセスを示しています。

![条件付きコンテンツを挿入したスクリーンショット](assets/legal_5.png)

計算の場合は、「算術」または「集計」を選択し、使用可能なテンプレート・タグに基づいて、関連する最初のレコード、演算子および 2 番目のレコードを含めます。 次に、「 **計算の挿入**&#x200B;を選択します。

また、法的契約では、関係者の署名が必要になることがよくあります。 「数値計算」セクションのすぐ下にあるAdobe Signテキストタグを使用して、電子サインを挿入できます。 電子サインを含めるには、「 **署名者**」をクリックし、ドロップダウンリストのフィールドの種類を選択します。 完了したら、「 **挿入/Adobe Sign/テキストタグ** 」をクリックしてプロセスを完了します。

データの整合性を確保するには、保護された形式で法律文書を保存します。 を [!DNL Acrobat Services] API を使用すると、ドキュメントをすばやくPDF形式に シンプルな Express Node.js アプリケーションを構築し、Document Generation API を組み込み、このシンプルなアプリケーションを使用してタグ付き文書を Word からPDF形式に変換できます。

## プロジェクト設定

まず、Node.js アプリケーションのフォルダー構造を設定します。 この例では、この単純なアプリケーションを AdobeLegalContractAPI と呼び出します。 ソースコードを取得できます [ここ](https://github.com/agavitalis/adobe_legal_contracts.git)を選択します。

### ディレクトリ構造

AdobeLegalContractAPI というフォルダーを作成し、任意のエディターで開きます。 Node.js アプリケーションを作成し、 ```npm init``` コマンドを実行します。

```
###Directory Structure
AdobeLegalContractAPI
-----config
----------default.json
-----controllers
----------createPDFController.js
----------previewController.js
-----models
----------document.js
-----routes
----------web.js
-----services
-----------upload.js
-----uploads
-----views
-----index.js
```

上記は、アプリケーションの簡単な Node.js アプリケーション構造です。 次に、必要な npm パッケージのインストールを進めます。

### パッケージのインストール

次のコードスニペットに示すように、npm install コマンドを使用して、必要なパッケージをインストールします。

```
npm install express body-parser morgan multer hbs path config mongoose
```

パッケージをインストールした後、package.json ファイルの内容が次のコードスニペットのようになっていることを確認します。

```
###package.json
{
"name": "adobelegalcontractapi",
"version": "1.0.0",
"description": "",
"main": "index.js",
"directories": {
"test": "test"
},
"dependencies": {
"body-parser": "^1.19.0",
"config": "^3.3.6",
"express": "^4.17.1",
"hbs": "^4.1.1",
"mongoose": "^5.12.1",
"morgan": "^1.10.0",
"multer": "^1.4.2",
"path": "^0.12.7"
},
"devDependencies": {},
"scripts": {
"start": "node index.js"
},
"repository": {
"type": "git",
"url": "https://github.com/agavitalis/adobe_legal_contracts.git"
},
"author": "Ogbonna Vitalis",
"license": "ISC",
"bugs": {
"url": "https://github.com/agavitalis/adobe_legal_contracts/issues"
},
"homepage": "https://github.com/agavitalis/adobe_legal_contracts#readme"
}
```

これらのコードスニペットでは、ビューのハンドルバーテンプレートエンジンなど、アプリケーションの依存関係をインストールしました。

このチュートリアルでは、主に [[!DNL Acrobat Services] API](https://www.adobe.io/apis/documentcloud/dcsdk/) 文書をPDFに したがって、この Node.js アプリケーションを構築する手順はありません。 ただし、 [GitHub](https://github.com/agavitalis/adobe_legal_contracts.git)を選択します。

## 統合 [!DNL Adobe Acrobat Services] Node.js アプリケーションへの API

[!DNL Adobe Acrobat Services] API は、ドキュメントをシームレスに操作するために設計された、クラウドベースの信頼性の高いサービスです。 3 つの API が用意されています。

* Adobe PDF Services API

* Adobe PDF Embed API

* Adobeドキュメント生成 API

使用するには資格情報が必要です [!DNL Acrobat Services] API( 埋め込み API 資格情報PDFとは異なります )。 有効な資格情報がない場合は、 [登録](https://www.adobe.com/go/dcsdks_credentials?ref=getStartedWithServicesSDK) 次のスクリーンキャプチャに示すように、ワークフローを完了します。 楽しむ [6 ヶ月間無料で試用した後、従量課金制](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)文書トランザクション 1 件あたり 0.05 USD です。

![新しい資格情報の作成のスクリーンショット](assets/legal_6.png)

登録プロセスが完了すると、コードサンプルが自動的にお使いの PC にダウンロードされ、作業を開始できるようになります。 このコードサンプルを抽出して使用できます。 抽出したコードサンプルの pdftools-api-credentials.json ファイルと private.key ファイルを Node.js プロジェクトのルートディレクトリにコピーすることを忘れないでください。 アクセスするには、資格情報が必要です [!DNL Acrobat Services] API エンドポイント。 また、サンプルコード内のキーを更新する必要がないように、パーソナライズされた資格情報を使用して SDK サンプルをダウンロードすることもできます。

次に、 ```npm install \--save @adobe/documentservices-pdftools-node-sdk``` コマンドを実行するには、アプリケーションのルートディレクトリにあるターミナルを使用します。 正常にインストールされたら、 [!DNL Acrobat Services] アプリケーションでドキュメントを操作するための API。

## ドキュメントPDFの作成

[!DNL Acrobat Services] API を使用すると、Microsoft Office 文書 (Word、Excel、PowerPoint) などのPDFを作成できます [サポートされているファイル形式](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf) .txt、.rtf、.bmp、.jpeg、.gif、.tiff、.png など。 Acrobat Service の API を使用すれば、法的契約書をその他のファイル形式からPDFに簡単に変換できます。

Adobeドキュメント生成 API を使用すると、Word ファイルまたはPDFに変換できます。 例えば、Word テンプレートを使用して、編集したテキストを墨消ししてマークするなど、契約書を生成できます。 次に、文書をPDFに変換し、PDFサービス API を使用して、文書をパスワードで保護したり、署名用に送信したりします。

使用可能なサポート対象ファイル形式からPDFドキュメントの作成を実装するには、次を使用して変換用のドキュメントをアップロードするフォームがあります [!DNL Acrobat Services]を選択します。

デザインされたアップロードフォームが下の画面に表示されます。HTMLと CSS ファイルには [GitHub](https://github.com/agavitalis/adobe_legal_contracts.git)を選択します。

![フォームアップロードのスクリーンショット](assets/legal_7.png)

次のコードスニペットを controllers /createPDFController.jsファイルに追加します。 このコードは、アップロードされたドキュメントを取得し、PDFに変換します。 [!DNL Acrobat Services] アップロードされた元のファイルと変換されたファイルを別のフォルダーに保存します。

```
###controllers/createPDFController.js
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk');
const Document = require('../models/document');
/*
* GET / route to show the createPDF form.
*/
function createPDF(req, res) {
//catch any response on the url
let response = req.query.response
res.render('index', { response })
}
/*
* POST /createPDF to create a new PDF File.
*/
function createPDFPost(req, res) {
let filePath = req.file.path;
let fileName = req.file.filename;
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Create an ExecutionContext using credentials and create a new operation instance.
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials),
createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();
// Set operation input from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile(filePath);
createPdfOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
createPdfOperation.execute(executionContext)
.then(async(result) => {
let newFileName = `createPDFFromDOCX-${Math.random() * 171}.pdf`
let newFilePath = require('path').resolve('./') + `\\output\\${newFileName}`
await result.saveAsFile(`views/output/${newFileName}`)
//Creates a new document
let newDocument = new Document({
documentName: newFileName,
url: newFilePath
});
//Save it into the DB.
newDocument.save((err, docs) => {
if (err) {
res.send(err);
}
else {
res.redirect('/?response=PDF Successfully created')
}
});
})
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
}
//export all the functions
module.exports = { createPDF, createPDFPost };
```

上記のコードスニペットでは、ドキュメントモデルと [!DNL Acrobat Services] 以前にインストールしたノード SDK。 次の 2 つの関数があります。

* createPDF に文書のアップロードフォームが表示されます。

* createPDFPost は、アップロードされた文書をPDFに変換します。

この関数は、変換されたPDFドキュメントを views/output ディレクトリに保存し、PC にダウンロードできます。

変換されたPDFファイルは、無料のPDFEmbed API を使用してプレビューすることもできます。 PDF埋め込み API を使用して、認証情報をAdobeできます [ここ](https://www.adobe.com/go/dcsdks_credentials) ( [!DNL Acrobat Services] 認証情報 ) を取得し、API にアクセスできるドメインを登録します。 プロセスに従って、アプリケーションのPDF埋め込み API 資格情報を生成します。 デモもご覧ください [ここ](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf)から簡単にコードを生成して、すぐに作業を開始できます。

アプリケーションに戻り、アプリケーションの view フォルダーに list.hbs ファイルと preview.hbs ファイルを作成し、以下のコードスニペットをそれぞれ list.hbs ファイルと preview.hbs ファイルに貼り付けます。

```
###views/list.hbs
<!DOCTYPE html>
<html lang="en">
<head>
<title>Adobe Legal Contract</title>
<!-- Meta tags -->
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,
initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="ie=edge">
<!-- //Meta tags -->
<link
href=".min.css" rel="stylesheet" integrity="sha384-eOJMYsd53ii+scO/
bJGFsiCZc+5NDVN2yr8+0RDqr0Ql0h+rP48ckxlpbzKgwra6" crossorigin="anonymous">
<link rel="stylesheet" href="css/style.css" type="text/css"
media="all" /><!-- Style-CSS -->
<link href="css/font-awesome.css" rel="stylesheet" /><!--
font-awesome-icons -->
</head>
<body>
<section>
<div class="form-36-mian section-gap">
<div class="wrapper">
<div class="container">
<div class="row">
{{#each documents}}
<div class="col-md-4 mb-2">
<div class="card" style="width:
18rem;">
<img class="card-img-top"
src="./images/pdf.png"
alt="Card image cap">
<div class="card-body">
<h5
class="card-title">{{documentName}}</h5>
<a
href="/downloadPDF/{{_id}}" class="btn btn-primary"><i class="fa
fa-download" aria-hidden="true"></i> Download</a>
<a
href="/previewPDF/{{_id}}" class="btn btn-info"><i class="fa fa-eye"
aria-hidden="true"></i> Preview</a>
</div>
</div>
</div>
{{/each}}
</div>
</div>
<!-- copyright -->
<div class="copy-right">
<p>(c) 2021 Vitalis</p>
</div>
<!-- //copyright -->
</div>
</div>
</section>
</body>
</html>
###views/preview.hbs
<!DOCTYPE html>
<html lang="en">
<head>
<title>[!DNL Adobe Acrobat Services] PDF Embed API</title>
<meta charset="utf-8" />
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
<meta id="viewport" name="viewport" content="width=device-width,
initial-scale=1" />
</head>
<body style="margin: 0px">
<input type="hidden" id="pdfDocumentName"
value={{document.documentName}} />
<input type="hidden" id="pdfDocumentUrl" value={{document.url}} />
<div id="adobe-dc-view"></div>
<script
src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
let pdfDocumentName =
document.getElementById("pdfDocumentName").value;
let pdfDocumentUrl =
document.getElementById("pdfDocumentUrl").value;
document.addEventListener("adobe_dc_view_sdk.ready", function
() {
var adobeDCView = new AdobeDC.View({ clientId:
"XXXXXXXXXXXXXXXX", divId: "adobe-dc-view" });
adobeDCView.previewFile({
content: { location: { url:
`http://localhost:5000/output/${pdfDocumentName}` } },
metaData: { fileName: pdfDocumentName }
}, {});
});
</script>
</body>
</html>
```

また、controller/previewController.jsファイルを作成し、その中に以下のコードスニペットを貼り付けます。

```
const Document = require('../models/document');
/*
* GET /listFiles route to show PDF file lists.
*/
async function listFiles(req, res) {
let documents = await Document.find({});
res.render('lists', { documents })
}
/*
* GET /previewPDF route to show PDF file in AdobeEmbedAPI.
*/
async function previewPDF(req, res) {
//catch any response on the url
let documentId = req.params.documentId
let document = await Document.findOne({_id:documentId});
res.render('preview', { document })
}
/*
* GET /downloadPDF To Download PDF Documents.
*/
async function downloadPDF(req, res) {
let documentId = req.params.documentId
let document = await Document.findOne({_id:documentId});
res.download(document.url);
}
//export all the functions
module.exports = {listFiles, previewPDF, downloadPDF };
```

上記のコントローラーファイルには、listFiles、previewPDF、downloadPDF の 3 つの関数があります。 listFiles 関数は、 [!DNL Acrobat Services] API previewPDF 関数を使用すると、PDF埋め込み API を使用してPDFファイルをプレビューできます。一方、downloadPDF 関数を使用すると、生成されたPDFファイルを PC にダウンロードできます。 以下の画面は、PDF埋め込み API を使用したPDFプレビューの例です。

![スクリーンショット、PDFプレビュー](assets/legal_8.png)

## 要約

この実践チュートリアルでは、Document Generation Tagger Microsoft Word アドインを使用して文書にタグを付けました。 統合 [!DNL Acrobat Services] API を Node.js アプリケーションに変換し、タグ付き文書をダウンロード可能なPDF形式に変換しました。ただし、法的契約書を直接PDFに作成することもできます。 最後に、Adobe PDF Embed API を使用して、生成された検証および署名用のPDFをプレビューしました。

完成したアプリケーションを使用すると、タグ付けがさらに簡単になります [法的契約テンプレート](https://www.adobe.io/apis/documentcloud/dcsdk/legal-contracts.html) ダイナミックフィールドを使用すると、PDFに変換し、プレビューし、次を使用して署名できます [!DNL Acrobat Services] API 独自の契約書を作成する時間がなくなり、チームは自動的に各クライアントに適切な契約書を送信して、ビジネスの成長により多くの時間を費やすことができます。

組織での使用 [!DNL Adobe Acrobat Services] API の完成度と使いやすさ 何よりも、あなたは [6 ヶ月間の無料体験の後に従量制](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)を選択します。 使用した分だけ支払います。 さらに、PDF埋め込み API は常に無料です。

今すぐ文書フローを改善して生産性を向上させましょう [今すぐ始める](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 今日です。

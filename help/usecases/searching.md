---
title: 検索とインデックス作成
description: スキャンした文書から検索可能なPDFファイルを作成する方法
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8095.jpg
kt: 8095
exl-id: a22230b5-1ff2-4870-84da-f06a904c99e1
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1364'
ht-degree: 1%

---

# 検索とインデックス作成

![ユースケースのヒーローバナー](assets/UseCaseSearchingHero.jpg)

組織は、多くの場合、ハードコピーの文書やスキャンしたファイルをデジタル化する必要があります。 以下について考える [scenario](https://docs.google.com/document/d/11jZdVQAw-3fyE3Y-sIqFFTlZ4m02LsCC/edit)を選択します。 ある法律事務所は、デジタルファイルを作成するためにスキャンした数千もの法的契約書を所有しています。 これらの法的契約のいずれかに特定の条項があるかどうかを判断したり、修正する必要がある補足条項があるかどうかを判断したりします。 コンプライアンスの目的には正確さが必要です。 その解決策は、デジタル文書のインベントリを作成し、テキストを検索可能にし、この情報を検索するためのインデックスを作成することです。

編集やダウンストリーム・オペレーションのために情報を取得するためにデジタル・アーカイブを作成するという課題は、ほとんどの組織にとって悪夢です。

## 学習内容

この実践チュートリアルでは、 [!DNL Adobe Acrobat Services] API の機能を備えており、文書のアーカイブとデジタル化に簡単に使用できます。 これらの機能は、Express NodeJS アプリケーションを構築し、次に [!DNL Acrobat Services] アーカイブ、デジタル化、ドキュメント変換のための API

この手順に従うには、 [Node.js](https://nodejs.org/) Node.js がインストールされており、Node.js および [ES6 構文](https://www.w3schools.com/js/js_es6.asp)を選択します。

## 関連する API とリソース

* [PDFサービス API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [プロジェクトコード](https://github.com/agavitalis/AdobeDocumentServicesAPIs.git)

## プロジェクト設定

まず、アプリケーションのフォルダー構造を設定します。 ソースコードを取得できます [ここ](https://github.com/agavitalis/AdobeDocumentAPI.git)を選択します。

## ディレクトリ構造

AdobeDocumentServicesAPIs というフォルダーを作成し、任意のエディターで開きます。 次を使用して基本的な NodeJS アプリケーションを作成 `npm init` コマンドを実行します。

```
AdobeDocumentServicesAPIs
config
default.json
controllers
createPDFController.js
makeOCRController.js
searchController.js
models
document.js
output
.gitkeep
routes
web.js
services
upload.js
views
index.hbs
ocr.hbs
search.hbs
index.js
```

このアプリケーションのデータベースとして MongoDB を使用しています。 そのため、設定するには、config/フォルダーにデフォルトのデータベース設定を配置します。以下のコードスニペットをこのフォルダーの default.json ファイルに貼り付けて、データベースの URL を追加します。

```
### config/default.json and config/dev.json
{ "DBHost": "YOUR_DB_URI" }
```

## パッケージのインストール

次のコードスニペットに示すように、npm install コマンドを使用して、いくつかのパッケージをインストールします。

```
{
    "name": "adobedocumentservicesapis",
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
    "start": "set NODE_ENV=dev && node index.js"
    },
    "repository": {
    "type": "git",
    "url": "git+https://github.com/agavitalis/AdobeDocumentServicesAPIs.git"
    },
    "author": "Ogbonna Vitalis",
    "license": "ISC",
    "bugs": {
    "url": "https://github.com/agavitalis/AdobeDocumentServicesAPIs/issues"
    },
    "homepage": "https://github.com/agavitalis/AdobeDocumentServicesAPIs#readme"
}
```

```
###bash
npm install express mongoose config body-parser morgan multer hbs path pdf-parse
Ensure that the content of your package.json file is similar to this code snippet:
###package.json
{
```

これらのコードスニペットは、ビューのハンドルバーテンプレートエンジンなど、アプリケーションの依存関係をインストールします。 scripts タグでは、アプリケーションのランタイムパラメーターを設定します。

## 統合 [!DNL Acrobat Services] API

[!DNL Acrobat Services] には、次の 3 つの API があります。

* Adobe PDF Services API

* Adobe PDF Embed API

* Adobeドキュメント生成 API

これらの API は、クラウドベースの一連の Web サービスを通じて、PDFコンテンツの生成、操作、変換を自動化します。

必要な資格情報を取得するには [登録](https://www.adobe.com/go/dcsdks_credentials?ref=getStartedWithServicesSDK) 」をクリックし、ワークフローを完了します。 PDF埋め込み API は無料で使用できます。 PDFサービス API とドキュメント生成 API は 6 ヶ月間無料です。 体験期間が終了したら、次の操作を行います [従量制の](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 文書トランザクション 1 件につき 0.05 USD です。 企業の成長と契約プロセスの増加に応じて料金が決定します。

![資格情報の作成のスクリーンショット](assets/searching_1.png)

サインアップが完了すると、API 資格情報を含むコードサンプルが PC にダウンロードされます。 このコードサンプルを抽出し、private.key ファイルと pdftools-api-credentials.json ファイルをアプリケーションのルートディレクトリに配置します。

次に、 [PDFサービス Node.js SDK](https://www.npmjs.com/package/@adobe/documentservices-pdftools-node-sdk) を実行して、 ` npm install --save @adobe/documentservices-pdftools-node-sdk ` コマンドを実行します。

## PDF

[!DNL Acrobat Services] では、Microsoft Office 文書 (Word、Excel、PowerPoint) およびその他の文書からのPDFの作成をサポートしています [サポートされているファイル形式](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf) .txt、.rtf、.bmp、.jpg、.gif、.tiff、.png など。

サポートされているファイル形式からPDFドキュメントを作成するには、このフォームを使用してドキュメントをアップロードします。 フォームのHTMLと CSS ファイルには [GitHub](https://github.com/agavitalis/AdobeDocumentServicesAPIs.git)を選択します。

![Web フォームのスクリーンショット](assets/searching_2.png)

次に、次のコードスニペットをcontrollers/createPDFController.jsファイルに追加します。 このコードは、ドキュメントを取得してPDFに変換します。

元のファイルと変換されたファイルは、アプリケーション内のフォルダーに保存されます。

```
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk');
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
// Create an ExecutionContext using credentials and create a new operation
instance.
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials),
createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();
// Set operation input from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile(filePath);
createPdfOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
createPdfOperation.execute(executionContext)
.then((result) => {
result.saveAsFile('output/createPDFFromDOCX.pdf')
//download the file
res.redirect('/?response=PDF Successfully created')
})
.catch(err => {
if (err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log('Exception encountered while executing operation',
err);
} else {
console.log('Exception encountered while executing operation',
err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
//export all the functions
module.exports = { createPDF, createPDFPost };
```

このコードスニペットには [PDFサービス Node.js SDK](https://www.npmjs.com/package/@adobe/documentservices-pdftools-node-sdk)を選択します。 次の関数を使用します。

* createPDF：文書のアップロードフォームを表示します。

* createPDFPost：アップロードされたドキュメントをPDF

変換されたPDFドキュメントは出力ディレクトリに保存され、元のファイルはアップロードディレクトリに保存されます。

## テキスト認識の使用

光学文字認識 (OCR) は、画像およびスキャンした文書を検索可能なファイルに変換します。 ファイルは、 [!DNL Acrobat Services] API、画像、スキャンした文書を検索可能なPDFに OCR 操作を実行すると、ファイルは編集可能になり、検索可能になります。 ファイルの内容をデータストアに保存して、インデックス作成やその他の用途に使用できます。

スキャンしたドキュメントの検索とインデックス作成は、ファイル管理と情報処理が不可欠な多くの組織にとって非常に重要です。 OCR 機能によってこれらの課題を解決できます。

この機能を実装するには、上記と同様のアップロードフォームをデザインする必要があります。 今回は、OCR 機能はPDF文書でのみ使用できるため、フォームをPDFファイルに制限します。

この例のアップロードフォームは次のとおりです。

![ファイルをアップロードするフォームのスクリーンショット](assets/searching_3.png)

ここで、アップロードされたPDFを操作し、OCR 操作を実行するには、controllers/makeOCRController.jsファイルに以下のコードスニペットを追加します。 このコードは、アップロードされたファイルに OCR プロセスを実装し、ファイルをアプリケーションのファイルシステムに保存します。

```
const fs = require('fs')
const pdf = require('pdf-parse');
const mongoose = require('mongoose');
const Document = require('../models/document');
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk');
/*
* GET /makeOCR route to show the makeOCR form.
*/
function makeOCR(req, res) {
//catch any response on the url
let response = req.query.response
res.render('ocr', { response })
}
/*
* POST /makeOCRPost to create a new PDF File.
*/
function makeOCRPost(req, res) {
let filePath = req.file.path;
let fileName = req.file.filename;
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Create an ExecutionContext using credentials and create a new operation
instance.
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials),
ocrOperation = PDFToolsSdk.OCR.Operation.createNew();
// Set operation input from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile(filePath);
ocrOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
ocrOperation.execute(executionContext)
.then(async (result) => {
let newFileName = `createPDFFromDOCX-${Math.random() * 171}.pdf`;
await result.saveAsFile(`output/${newFileName}`);
let documentContent = fs.readFileSync(
require("path").resolve("./") + `\\output\\${newFileName}`
);
pdf(documentContent)
.then(function (data) {
//Creates a new document
var newDocument = new Document({
documentName: fileName,
documentDescription: description,
documentContent: data.text,
url: require("path").resolve("./") + `\\output\\${newFileName}`
});
//Save it into the DB.
newDocument.save((err, docs) => {
if (err) {
res.send(err);
} else {
//If no errors, send it back to the client
res.redirect(
"/makeOCR?response=OCR Operation Successfully performed on
the PDF File"
);
}
});
})
.catch(function (error) {
// handle exceptions
console.log(error);
});
})
.catch(err => {
if (err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log('Exception encountered while executing operation',
err);
} else {
console.log('Exception encountered while executing operation',
err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
//export all the functions
module.exports = { makeOCR, makeOCRPost };
```

必要なのは、 [!DNL Acrobat Services] Node SDK と mongoose、pdf-parse、fs の各モジュールと文書モデルスキーマ。 これらのモジュールは、変換されたファイルのコンテンツを MongoDB データベースに保存するために必要です。

次の 2 つの関数を作成します。makeOCR を使用して、アップロードされたフォームを表示し、アップロードされた文書を処理するために makeOCRPost を実行します。 元のフォームをデータベースに保存し、変換したフォームをアプリケーションの出力フォルダーに保存します。

Adobeから提供された pdftools-api-credentials.json ファイルの資格情報は、ファイルを変換する前に各場合に読み込まれます。

>[!NOTE]
>
>OCR 機能は、ドキュメントのPDFのみ。

また、アプリケーションのModes/Document.jsファイルに以下のコードスニペットを追加します。

コードスニペットで、マングースモデルを定義し、データベースに保存するドキュメントのプロパティを記述します。 また、documentContent フィールドにインデックスを設定すると、テキストの検索が簡単かつ効率的になります。

```
const mongoose = require("mongoose");
const Schema = mongoose.Schema;
//Document schema definition
var DocumentSchema = new Schema(
{
documentName: { type: String, required: false },
documentDescription: { type: String, required: false },
documentContent: { type: String, required: false },
url: { type: String, required: false },
status: {
type: String,
enum : ["active","inactive"],
default: "active"
}
},
{ timestamps: true }
);
//for text search
DocumentSchema.index({
documentContent: "text",
});
//Exports the DocumentSchema for use elsewhere.
module.exports = mongoose.model("document", DocumentSchema);
```

## テキストの検索

ユーザーが簡単なテキスト検索を実行できるように、簡単な検索機能を実装しました。 また、ダウンロード機能を追加して、アプリケーションファイルのダウンロードをPDFできます。

この機能を使用するには、検索結果を表示するためのシンプルなフォームとカードが必要です。 フォームとカードのデザインは、 [GitHub](https://github.com/agavitalis/AdobeDocumentServicesAPIs.git)を選択します。

以下のスクリーンショットは、検索機能と検索結果を示しています。 任意の検索結果をダウンロードできます。

![検索機能のスクリーンショット](assets/searching_4.png)

検索機能を実装するには、アプリケーションの controller フォルダー内に searchController.js ファイルを作成し、以下のコードスニペットを貼り付けます。

```
const fs = require('fs')
const mongoose = require('mongoose');
const Document = require('../models/document');
/*
* GET / route to show the search form.
*/
function search(req, res) {
//catch any response on the url
let response = req.query.response
res.render('search', { response })
}
/*
* POST /searchPost to search the contents of your saved file.
*/
function searchPost(req, res) {
let searchString = req.body.searchString;
Document.aggregate([
{ $match: { $text: { $search: searchString } } },
{ $sort: { score: { $meta: "textScore" } } },
])
.then(function (documents) {
res.render('search', { documents })
})
.catch(function (error) {
let response = error
res.render('search', { response })
});
}
//export all the functions
module.exports = { search, searchPost, downloadPDF };
```

ダウンロード機能を実装して、ユーザーの検索から返されたドキュメントをダウンロードできるようになりました。

## ドキュメントのダウンロード

ダウンロード機能の実装は、これまでの手順と同様です。 controllers/earchController.jsファイルの searchPost 関数の後に次のコードスニペットを追加します。

```
/*
* POST /downloadPDF To Download PDF Documents.
*/
async function downloadPDF(req, res) {
console.log("here")
let documentId = req.params.documentId
let document = await Document.findOne({_id:documentId});
res.download(download.link);
}
```

## 次の手順

この実践チュートリアルでは、 [!DNL Acrobat Services] API を Node.js アプリケーションに組み込み、API を使用してファイルを変換するドキュメントPDFを実装 写真やスキャンしたファイルを検索可能にする OCR 機能を追加しました。 次に、ファイルをフォルダーに保存してダウンロードできるようにします。

次に、OCR でテキストに変換された文書を検索する検索機能を追加しました。 最後に、これらのファイルを簡単にダウンロードできるように、ダウンロード機能を実装しました。 完成したアプリケーションを使用すれば、法務部門は特定のテキストをより簡単に見つけて処理することができます。

使用方法 [!DNL Acrobat Services] 文書の変換は、堅牢性と他のサービスに比べて使いやすさがあるため、強くお勧めします。 アカウントをすばやく作成して、 [!DNL Acrobat Services] 文書の変換と管理のための API

これで、の使用方法について理解が深まりました [!DNL Acrobat Services] API を使用すると、練習でスキルを向上させることができます。 このチュートリアルで使用するリポジトリを複製し、学習したスキルを試すことができます。 さらに優れた方法として、このアプリケーションのリビルドを試みる一方で、 [!DNL Acrobat Services] API

さっそく自分のアプリで文書の共有とレビューを有効にしましょう サインアップ [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)
開発者アカウント 6 ヶ月間の無料体験をお試しください [従量制の](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) ビジネスの成長に合わせて、文書トランザクションあたり 0.05 USD で利用可能

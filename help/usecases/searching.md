---
title: 検索とインデックス作成
description: スキャンした文書から検索可能なPDFファイルを作成する方法について説明します
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8095
thumbnail: KT-8095.jpg
exl-id: a22230b5-1ff2-4870-84da-f06a904c99e1
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '1298'
ht-degree: 0%

---

# 検索とインデックス作成

![使用事例の英雄バナー](assets/UseCaseSearchingHero.jpg)

多くの場合、組織はハードコピー文書やスキャンしたファイルをデジタル化する必要があります。 この[シナリオ](https://docs.google.com/document/d/11jZdVQAw-3fyE3Y-sIqFFTlZ4m02LsCC/edit)を検討してください。 ある法律事務所では、デジタル・ファイルを作成するためにスキャンした数千もの法的契約を処理しています。 彼らは、これらの法的契約のいずれかに特定の条項があるかどうか、または彼らが改訂する必要があります補足を決定したいと考えています。 コンプライアンスには正確性が必要です。 このソリューションでは、デジタルドキュメントのインベントリを作成し、テキストを検索可能にして、この情報を見つけるためのインデックスを作成します。

編集やダウンストリーム操作のために情報を取得するデジタルアーカイブを作成することは、ほとんどの組織にとって困難です。

## 学習内容

この実践チュートリアルでは、[!DNL Adobe Acrobat Services]個のAPIの機能を紹介し、文書のアーカイブとデジタル化に簡単に使用する方法を説明します。 これらの機能を確認するには、Express NodeJSアプリケーションを構築し、アーカイブ、デジタル化、およびドキュメント変換用の[!DNL Acrobat Services]個のAPIを統合します。

実行するには、[Node.js](https://nodejs.org/)をインストールし、Node.jsおよび[ES6構文](https://www.w3schools.com/js/js_es6.asp)の基本的な理解が必要です。

## 関連APIとリソース

* [PDFサービスAPI](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [プロジェクトコード](https://github.com/agavitalis/AdobeDocumentServicesAPIs.git)

## プロジェクト設定

まず、アプリケーションのフォルダー構造を設定します。 ソースコード[ここ](https://github.com/agavitalis/AdobeDocumentAPI.git)を取得できます。

## ディレクトリ構造

AdobeDocumentServicesAPIsという名前のフォルダーを作成し、任意のエディターで開きます。 次のフォルダー構造を使用して、`npm init`コマンドで基本のNodeJSアプリケーションを作成します：

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

このアプリケーションのデータベースとしてMongoDBを使用しています。 したがって、設定するには、下のコードスニペットをこのフォルダーのdefault.jsonファイルに貼り付けて、デフォルトのデータベース設定をconfig/フォルダーに配置し、データベースのURLを追加します。

```
### config/default.json and config/dev.json
{ "DBHost": "YOUR_DB_URI" }
```

## パッケージのインストール

次に、次のコードスニペットに示すように、npm installコマンドを使用していくつかのパッケージをインストールします。

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

これらのコードスニペットは、ビューのHandlebarsテンプレートエンジンなどのアプリケーション依存関係をインストールします。 scriptsタグで、アプリケーションの実行時パラメーターを設定します。

## [!DNL Acrobat Services] APIを統合しています

[!DNL Acrobat Services]には次の3つのAPIが含まれています：

* Adobe PDF Services API

* Adobe PDF Embed API

* Adobe文書生成API

これらのAPIにより、一連のクラウドベースのWebサービスを介してPDFコンテンツの生成、操作、変換が自動化されます。

資格情報を取得するには、[登録](https://www.adobe.com/go/dcsdks_credentials?ref=getStartedWithServicesSDK)してワークフローを完了する必要があります。 PDF埋め込みAPIは無料で使用できます。 PDFサービスAPIとDocument Generation APIは6か月間無料で利用できます。 体験版が終了すると、1つのドキュメントトランザクションにつき[従量課金制](https://developer.adobe.com/document-services/pricing/main)で課金されます。 お客様の会社が成長し、より多くの契約を処理するにつれて、お支払いいただきます。

![資格情報の作成のスクリーンショット](assets/searching_1.png)

サインアップが完了すると、API資格情報を含むコードサンプルがPCにダウンロードされます。 このコード例を展開し、 private.keyとpdftools-api-credentials.jsonファイルをアプリケーションのルートディレクトリに配置します。

次に、アプリケーションのルートディレクトリでターミナルを使用して` npm install --save @adobe/documentservices-pdftools-node-sdk `コマンドを実行し、[PDFサービスNode.js SDK](https://www.npmjs.com/package/@adobe/documentservices-pdftools-node-sdk)をインストールします。

## PDFの作成

[!DNL Acrobat Services]では、Microsoft Office文書(Word、Excel、PowerPoint)からのPDFの作成と、その他[サポートされているファイル形式](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf) （.txt、.rtf、.bmp、.jpg、.gif、.tiff、.pngなど）からのフォントの作成がサポートされています。

サポートされているファイル形式からPDF文書を作成するには、このフォームを使用して文書をアップロードします。 [GitHub](https://github.com/agavitalis/AdobeDocumentServicesAPIs.git)で、フォームのHTMLファイルとCSSファイルにアクセスできます。

![Webフォームのスクリーンショット](assets/searching_2.png)

次のコードスニペットをcontrollers/createPDFController.jsファイルに追加します。 このコードは、文書を取得し、PDFに変換します。

元のファイルと変換されたファイルが、アプリケーション内のフォルダーに保存されます。

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

このコードスニペットには、[PDFサービスNode.js SDK](https://www.npmjs.com/package/@adobe/documentservices-pdftools-node-sdk)が必要です。 次の関数を使用します。

* createPDF:「文書をアップロード」フォームを表示

* createPDFPost：アップロードした文書をPDFに変換します。

変換されたPDF文書は出力ディレクトリに保存され、元のファイルはアップロードディレクトリに保存されます。

## テキスト認識の使用

光学式文字認識(OCR)は、画像やスキャンされた文書を検索可能なファイルに変換します。 [!DNL Acrobat Services]個のAPI、画像、スキャンされた文書を検索可能なPDFに変換できます。 OCR操作を実行した後、ファイルは編集可能で検索可能になります。 インデックス作成やその他の用途のために、データストアにファイルの内容を保存することができます。

ファイル管理と情報処理が不可欠な組織では、スキャンしたドキュメントの検索とインデックス作成が重要です。 OCR機能を使用すると、このような課題を解決できます。

この機能を実装するには、上記と同様のアップロードフォームを設計する必要があります。 OCR機能はPDF文書でのみ使用できるため、今回はフォームをPDFファイルに制限します。

この例のアップロードフォームは次のとおりです。

![ファイルをアップロードするフォームのスクリーンショット](assets/searching_3.png)

アップロードしたPDFを処理してOCR処理をおこなうには、以下のコードスニペットをcontrollers/makeOCRController.jsファイルに追加します。 このコードは、アップロードされたファイルのOCRプロセスを実装し、そのファイルをアプリケーションのファイルシステムに保存します。

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

[!DNL Acrobat Services]ノードSDK、mongoose、pdf-parse、およびfsモジュール、および文書モデルスキーマが必要です。 これらのモジュールは、変換されたファイルの内容をMongoDBデータベースに保存するために必要です。

ここで、2つの関数を作成します。makeOCRはアップロードされたフォームを表示し、makeORPostはアップロードされた文書を処理します。 元のフォームをデータベースに保存し、変換したフォームをアプリケーションの出力フォルダーに保存します。

pdftools-api-credentials.jsonファイルからAdobeが提供した資格情報は、ファイルを変換する前に各ケースで読み込まれます。

>[!NOTE]
>
>OCR機能では、PDF文書のみがサポートされます。

また、以下のコードスニペットをアプリケーションのModes/Document.jsファイルに追加します。

コードスニペットで、モノグルモデルを定義してから、データベースに保存する文書のプロパティを記述します。 また、documentContentフィールドにインデックスを作成すると、テキストの検索が簡単かつ効率的になります。

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

これで、シンプルな検索機能を実装して、ユーザーが簡単なテキスト検索を実行できるようになりました。 また、ダウンロード機能を追加して、PDFファイルのダウンロードを可能にします。

この機能には、検索結果を表示するための簡単なフォームとカードが必要です。 フォームとカードのデザインは、[GitHub](https://github.com/agavitalis/AdobeDocumentServicesAPIs.git)で確認できます。

下のスクリーンショットは、検索機能と検索結果を示しています。 任意の検索結果をダウンロードできます。

![検索機能のスクリーンショット](assets/searching_4.png)

検索機能を実装するには、アプリケーションのコントローラフォルダー内にsearchController.jsファイルを作成し、以下にコードスニペットを貼り付けます。

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

ダウンロード機能の実装は、既におこなった操作と似ています。 controllers/earchController.jsファイルのsearchPost関数の後に、次のコードスニペットを追加します。

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

この実践チュートリアルでは、[!DNL Acrobat Services]個のAPIをNode.jsアプリケーションに統合し、APIを使用してファイルをPDFに変換する文書変換を実装しました。 画像やスキャンしたファイルを検索できるようにするOCR機能を追加しました。 次に、ダウンロードできるようにファイルをフォルダーに保存しました。

次に、テキストに変換された文書をOCRで検索する検索機能を追加しました。 最後に、これらのファイルを簡単にダウンロードできるダウンロード機能を実装しました。 完成したアプリケーションを使用すると、法律会社が特定のテキストを簡単に検索および処理できます。

他のサービスと比べて堅牢性と使いやすいため、ドキュメント変換に[!DNL Acrobat Services]を使用することを強くお勧めします。 アカウントをすばやく作成して、ドキュメントの変換と管理のための[!DNL Acrobat Services]個のAPIの機能の利用を開始できます。

[!DNL Acrobat Services]個のAPIの使い方をよく理解したので、練習を積んでスキルを向上させることができます。 このチュートリアルで使用したリポジトリーをコピーして、先ほど学習したスキルを試すことができます。 さらに優れた点として、[!DNL Acrobat Services]個のAPIの可能性を無制限に調査しながら、このアプリケーションを再構築できます。

独自のアプリでドキュメントの共有とレビューを有効にしますか？ [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)にサインアップ
開発者アカウント。 6か月間の無料体験版を利用した後、[従量課金制](https://developer.adobe.com/document-services/pricing/main)で、ビジネスの成長に合わせて文書トランザクションあたり\$0.05を支払うことができます。

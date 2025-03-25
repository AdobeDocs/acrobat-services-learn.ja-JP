---
title: NDAの作成
description: 共同作業のために動的なNDA PDFを作成する方法について説明します
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8098
thumbnail: KT-8098.jpg
exl-id: f4ec0182-a46e-43aa-aea3-bf1d19f1a4ec
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '1072'
ht-degree: 0%

---

# NDAの作成

![使用事例の英雄バナー](assets/UseCaseNDAHero.jpg)

組織は、外部のコントリビューターと協力してサービスや製品を構築します。 機密保持契約(NDA)は、これらの共同作業の重要な要素です。 これにより、すべての当事者がいずれかの事業体に損害を与える可能性のある機密情報を解放することを拘束します。

最も広く使用されているNDA形式はPDF文書です。 組織はNDAを準備し、すべての関係者に送信します。 その後、全員が署名すると、契約が開始されます。 高速チームでは、手動でPDFを作成すると進行が遅くなります。

## 学習内容

この実践チュートリアルでは、会社に特化したMicrosoft Word NDAテンプレートの作成方法を説明します。 Microsoft Word向けのAdobeの無料アドインである[Adobe文書生成タグ](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo)には、動的値を入力するための「タグ」が挿入されます。 JSONデータをテンプレートに渡し、動的PDFを作成する方法について説明します。 作成されたPDFは、業務要件や目標に応じて、電子メールで送信したり、ブラウザーで共同作業者に表示したりできます。 Node.js、JavaScript、Express.js、HTML、CSSの使い方を学びましょう。

## 関連APIとリソース

[!DNL Adobe Acrobat Services]を使用すると、動的データを使用してPDF文書をその場で作成できます。 [!DNL Acrobat Services]は、[NDA作成](https://developer.adobe.com/document-services/use-cases/agreements-and-contracts/nda-creation)を自動化するためのAdobe Document Generation APIを含むPDFツールスイートを提供します。

* [Adobe文書生成API](https://developer.adobe.com/document-services/apis/doc-generation)

* [Adobe Sign API](https://developer.adobe.com/adobesign-api/)

* [Adobe文書生成タガー](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo)

* [プロジェクトコード](https://github.com/afzaal-ahmad-zeeshan/adobe-docugen-sample)

* [[!DNL Acrobat Services] キー](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#getcred)

## JSONモデルの作成

Microsoft WordテンプレートはJSONモデルに依存しているため、最初にJSONモデルを作成します。 チュートリアルには、連絡先情報など、会社の詳細を含む基本的なJSON構造を使用します。

```
{
"vendor": {
"companyName": "GlobalCorp",
"street": "123 Any Street",
"street2": "",
"city":"Anywhere",
"state":"CA",
"primaryContact": {
"firstName":"John",
"lastName":"Doe",
"email":"john-doe@example.com",
"phone":"123-456-7890"
}
},
"authorizedSigner": {
"firstName": "Sarah",
"lastName": "Rose",
"email": "sarah@example.com",
"phone":"555-555-1234"
}
}
```

Microsoft Word内でこの構造を使用して、テンプレートを作成します。 このデータは、JSON形式である限り、任意のデータソースから取得できます。 簡単にするために、Node.jsアプリケーション内に複数のファイルを作成しますが、使用例によっては、ベンダー情報を取得するためにデータベース接続が必要な場合があります。

## Microsoft Wordテンプレートを作成する

Microsoft Word文書にNDAテンプレートを作成します。 Adobe PDF Services APIでは、サービスがJSONドキュメントから値を注入できるタグがMicrosoft Wordドキュメントに含まれている必要があります。 すべてのAdobeリクエストのテンプレートは同じですが、JSONの動的データが変わります。 このタグは、1つのMicrosoft Wordテンプレートを使用して、NDA文書の生成を自動化し、プロセスを高速化することで、この場合にすべてのベンダーのPDF文書を作成するのに役立ちます。

Microsoft Wordに[無料のDocument Generation Taggerアドイン](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo)をインストールできます。 組織に所属している場合は、Microsoft Office管理者に依頼して、すべてのユーザー向けの無償アドインのインストールを依頼できます。

アドインをインストールすると、[ホーム]タブの[Adobe]カテゴリにアドインが表示されます。 タブを開くには、**ドキュメントの生成**&#x200B;を選択します。

![WordのDocument Generationアドインのスクリーンショット](assets/nda_1.png)

タブ内で、サンプルのJSONドキュメントをアップロードできます。 この文書は、Microsoft Wordテンプレートの作成にのみ使用するので、サンプルにすることができます。

![Document Generationアドインのサンプルデータのスクリーンショット](assets/nda_2.png)

「**タグを生成**」を選択して、テンプレート内で使用できるアイテムを表示します。 JSON構造から抽出されたプロパティは次のとおりです。これにより、テンプレートで使用できます。

![Document Generationアドインのテキストタグのスクリーンショット](assets/nda_3.png)

`authorizedSigner`フィールドの機能です。 その他のフィールドは折り返され、Microsoft Wordでビューを展開できます。 アドインには、テーブル、リスト、計算値などの高度なデータオプションも用意されています。

## タグの作成

テンプレートを作成するか、[既存のテンプレート](https://developer.adobe.com/document-services/apis/doc-generation#sample-blade)をMicrosoft Wordに読み込んでください。 文書を設定したら、アドインで対応するトークンをクリックして、各フィールドにタグを追加します。

Microsoft Wordファイル内の次のテンプレート：

![サンプルテンプレートのスクリーンショット](assets/nda_4.png)

このファイルにはいくつかのタグが含まれています。 プログラムを実行すると、これらのフィールドにはベンダー情報が入力されます。

Document Generation TaggerはAdobe Sign APIと統合されます。 この統合により、Signテキストタグを自動的に作成し、生成された文書をAdobe Signに送信して署名することができます。

## ベンダーのNDAを生成しています

サンプルアプリケーション内で、入力および出力用のフォルダーを準備しました。 前述のように、JSONファイルを使用することで、システムで使用可能なベンダーを表示するためのファイルが2つできます。 ファイルは、ブラウザーに印刷されるフォーム内に表示されます。

```
<h1><b>NDA</b>: Generate for vendor.</h1>
<hr />
<p>Following ({{files.length}}) vendors are ready, select to generate NDA and deliver for signature:</p>
<form method="POST">
<ul>
{{#each files }}
<li><input type="checkbox" name="vendor" value="{{this}}" id="file-{{@index}}" /> <label for="file-{{@index}}">{{this}}</label></li>
{{/each}}
</ul>
<input type="submit" value="Create NDA" />
</form>
```

このコードは、ブラウザーで次のユーザーインターフェイス(UI)を生成します。

![[NDAの作成]ユーザーインターフェイスのスクリーンショット](assets/nda_5.png)

管理者がユーザーを選択すると、アプリはAdobe PDFサービスを使用して、外出先でNDAを生成します。

```
async function compileDocFile(json, inputFile, outputPdf) {
try {
// configurations
const credentials = adobe.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("./src/pdftools-api-credentials.json")
.build();
// Capture the credential from app and show create the context
const executionContext = adobe.ExecutionContext.create(credentials);
// create the operation
const documentMerge = adobe.DocumentMerge,
documentMergeOptions = documentMerge.options,
options = new documentMergeOptions.DocumentMergeOptions(json, documentMergeOptions.OutputFormat.PDF);
const operation = documentMerge.Operation.createNew(options);
// Pass the content as input (stream)
const input = adobe.FileRef.createFromLocalFile(inputFile);
operation.setInput(input);
// Async create the PDF
let result = await operation.execute(executionContext);
await result.saveAsFile(outputPdf);
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
```

Expressルータ内で次のコードを使用します。

```
// Create one report and send it back
try {
console.log(`[INFO] generating the report...`);
const fileContent = fs.readFileSync(`./public/documents/raw/${vendor}`, 'utf-8');
const parsedObject = JSON.parse(fileContent);
await pdf.compileDocFile(parsedObject, `./public/documents/template/Adobe-NDA-Sample.docx`, `./public/documents/processed/output.pdf`);
console.log(`[INFO] sending the report...`);
res.status(200).render("preview", { page: 'nda', filename: 'output.pdf' });
} catch(error) {
console.log(`[ERROR] ${JSON.stringify(error)}`);
res.status(500).render("crash", { error: error });
}
```

GitHubで[完全なサンプルコード](https://github.com/afzaal-ahmad-zeeshan/adobe-docugen-sample)を表示できます。

このコードでは、[!DNL Adobe Acrobat Services] SDKへのAPI呼び出しでJSONドキュメントとMicrosoft Wordテンプレートを使用します。 応答で、出力を受け取り、アプリケーションのファイルシステムに保存します。 無料の[Adobe PDF Embed API](https://developer.adobe.com/document-services/apis/pdf-embed)を使用すると、生成されたドキュメントをメールでクライアントに転送したり、ブラウザー内でプレビューを表示したりできます。

この呼び出しにより、次のNDAドキュメントが作成されます。

![NDAドキュメントプレビューのスクリーンショット](assets/nda_6.png)

[!DNL Adobe Acrobat Services] APIは、コンテンツを挿入してPDF文書を作成します。 これらのツールを使用しないと、Officeドキュメントを処理し、未加工のPDFファイル形式で作業するためのコードを記述しなければならない場合があります。 Adobe PDFサービスを使用すると、これらの手順をすべて1回のAPI呼び出しで実行できます。

[Adobe Sign API](https://developer.adobe.com/adobesign-api/)を使用して、NDAに対する署名を依頼し、最終的な署名済み文書をすべての関係者に配信します。 Adobe Signから[Webhookを使用して](https://opensource.adobe.com/acrobat-sign/developer_guide/index.html#!adobedocs/adobe-sign/master/webhooks.md)通知されます。 このwebhookをリッスンして、NDAのステータスを取得できます。

Adobe Signプロセスの詳細については、[ドキュメントを参照](https://opensource.adobe.com/acrobat-sign/developer_guide/index.html)するか、この詳細なブログ投稿を参照してください。

## 次の手順

この実践チュートリアルでは、Microsoft WordテンプレートとJSONデータファイルを使用してAdobe文書を動的に生成するために、PDF文書生成Taggerを使用します。 このアドインにより、[各関係者に合わせてカスタマイズされたNDAを自動的に作成](https://developer.adobe.com/document-services/use-cases/agreements-and-contracts/nda-creation)し、Sign APIを使用して署名を収集できました。

これらの方法を使用して独自のNDAやその他のドキュメントを動的に作成することで、チームが生産性の高い作業に集中する時間を節約できます。 [[!DNL Adobe Acrobat Services]](https://developer.adobe.com/document-services/apis/pdf-services)を検索して、選択した言語およびランタイムのAPIとSDKを見つけてください。アプリケーションに直接PDF関数を追加して、PDF文書をすばやく作成できます。 6か月間の無料体験後は、[使用を開始](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)
[従量課金制](https://developer.adobe.com/document-services/pricing/main) （ドキュメントトランザクションあたり$0.05のみ）。

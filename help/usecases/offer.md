---
title: 従業員オファー・レターの管理
description: 新しい従業員に署名用に配信できる内定通知を生成する方法について説明します
role: Developer
level: Intermediate
type: Tutorial
feature: Use Cases
thumbnail: KT-8096.jpg
jira: KT-8096
exl-id: 92f955f0-add5-4570-aa3a-ea63055dadb2
source-git-commit: b65ffa3efa3978587564eb0be0c0e7381c8c83ab
workflow-type: tm+mt
source-wordcount: '1794'
ht-degree: 2%

---

# 従業員の内定通知の管理

![ユースケースの英雄バナー](assets/UseCaseOfferHero.jpg)

従業員の内定通知は、従業員が組織で最初に体験する通知の1つです。 その結果、オファーレターがブランドであることを確認したいが、毎回ワープロで最初から文字を作成する必要はない。 [!DNL Adobe Acrobat Services] APIは、 [新入社員へのオファーレターの作成と配信](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html).

## 学習内容

この実践チュートリアルでは、従業員の詳細を入力するユーザーのwebフォームを表示するNode Expressプロジェクトの設定について段階的に説明します。 これらの詳細は、 [!DNL Acrobat Services] Adobe Sign APIを使用して署名のためにお客様に配信できるPDFとして、Web上でオファーレターを作成します。

## 関連APIとリソース

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Adobe文書生成API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Document Generation Tagger Wordアドイン](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin)

* [プロジェクトサンプル](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html)

## はじめに

[Node.js](https://nodejs.org/) は、プログラミングプラットフォームです。 これには、Express webサーバーなどの膨大なライブラリのセットが付属しています。 [Node.jsのダウンロード](https://nodejs.org/en/download/) 手順に従って、この優れたオープンソース開発環境をインストールしてください。

Node.jsでAdobe Document Generation APIを使用するには、 [Document Generation API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html) アカウントにアクセスしたり、新しいアカウントに新規登録したりするためのサイトです。 アカウント: [6か月無料、その後従量課金制](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) ドキュメントのトランザクションあたりわずか0.05ドルで、リスクフリーを試して、会社の成長に応じてのみ支払うことができます。

にログインした後 [Adobe Developer Console](https://console.adobe.io/)、をクリック **[!UICONTROL 新規プロジェクトを作成]**. デフォルトでは、プロジェクト名は「Project 1」です。 「 **[!UICONTROL プロジェクトを編集]** をクリックして、名前を「Offer Letter Generator」に変更します。 画面の中央には、 **[!UICONTROL 新しいプロジェクトの作成]** セクションに追加します。 プロジェクトのセキュリティを有効にするには、次の手順を実行します。

クリック **APIを追加**. 選択可能なAPIが多数あります。 を **[!UICONTROL 製品でフィルタリング]** セクション、選択 **[!UICONTROL Document Cloud]**&#x200B;を選択し、 **[!UICONTROL Next]**.

ここで、APIにアクセスするための資格情報を生成します。 資格情報は、 JSON Webトークン([JWT](https://jwt.io/))：安全な通信のためのオープンスタンダードです。 JWTに精通していて、すでにキーを生成している場合は、ここで公開キーをアップロードできます。 または、以下を選択して続行します。 **オプション1** Adobeにキーを生成してもらいます。

![資格情報を生成するスクリーンショット](assets/offer_1.png)

「 **[!UICONTROL キーペアを生成]** をクリックします。 config.zipファイルをダウンロードできます。 アーカイブファイルを解凍します。 このファイルには、certificate_pub.crtとprivate.keyの2つのファイルが含まれています。 後者はプライベートな認証情報を含み、制御不能な場合に偽の文書を生成するために使用される可能性があるため、安全に保たれるようにしてください。

「**[!UICONTROL 次へ]**」をクリックします。いいえ、PDF生成APIへのアクセスを有効にします。 を **[!UICONTROL 製品プロファイルの選択]** 画面、確認 **[!UICONTROL 企業PDFサービス開発者]**&#x200B;を選択し、 **[!UICONTROL 設定したAPIを保存]** をクリックします。 これで、APIの使用を開始する準備ができました。

## プロジェクトの設定

コードを実行するためのノードプロジェクトを設定します。 この例では、 [Visual Studioコード](https://code.visualstudio.com/) （VSコード）をエディターとして使用します。 「letter-generator」というフォルダーを作成し、VSコードで開きます。 から **[!UICONTROL ファイル]** メニュー、選択 **[!UICONTROL ターミナル]** \> **[!UICONTROL 新しいターミナル]** このフォルダ内のシェルを開きます。 次のコマンドを入力して、ノードがパスにインストールされていることを確認します。

```
node -v
```

インストールしたNodeのバージョンが表示されます。

開発環境がインストールされたので、プロジェクトを作成できます。

まず、ノードパッケージマネージャー(npm)を使用してプロジェクトを初期化します。 次のように入力します。

```
npm init
```

Nodeプロジェクトに関する質問が表示されます。 これらの質問はほとんどスキップできますが、プロジェクト名が「letter-generator」で、エントリポイントが **index.js**. 選択 **はい** プロジェクトの初期化を完了します。

package.jsonファイルが作成されました。 ノードはこのファイルを使用してプロジェクトを整理します。 index.jsを作成する前に、次のコマンドを使用してAdobeライブラリを追加する必要があります。

```
npm install --save @adobe/documentservices-pdftools-node-sdk
```

プロジェクトにnode_modulesという名前の新しいフォルダが追加されます。 このフォルダーは、すべてのライブラリ（ノードでは依存ファイルと呼ばれます）のダウンロード先です。 package.jsonファイルも、Adobe PDFサービスへの参照で更新されます。

Expressを軽量Webフレームワークとしてインストールします。 次のコマンドを入力します

```
npm install express –save
```

以前と同様に、package.jsonのdependenciesセクションもそれに応じて更新されます。

## 内定通知テンプレートの作成

次に、プロジェクトルートに「app.js」という名前のファイルを作成します。 次のスターターコードを追加します。

```
const express = require('express');
const bodyParser = require('body-parser');
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk')
const path = require('path');
const app = express();
const port = 8000;
app.use(bodyParser.urlencoded({ extended: true }));
app.get('/', (req, res) => {
res.sendFile(path.join(__dirname + '/index.html'));
});
app.post('/', (req, res) => {
console.log('Got body:', req.body);
res.sendStatus(200);
});
app.listen(port, () => {
console.log(`Candidate offer letter app listening on port ${port}!`)
});
```

getルートが **index.html** ファイル。 この名前と次の簡単な形式でHTMLファイルを作成してみましょう。 CSSスタイルやその他のデザイン要素は、必要に応じて後から追加することもできます。 以下のフォームは、候補者がウェルカムレターを作成する際の基本的な詳細情報を入力するものです。

```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Offer Letter Generator</title>
</head>
<body>
<h1>Enter Candidate Details</h1>
<form action="" method="post">
<div>
<label for="firstname">First name: </label>
<input type="text" name="firstname" id="firstname" required>
</div>
<div>
<label for="lastname">Last name: </label>
<input type="text" name="lastname" id="lastname" required>
</div>
<div>
<label for="salary">Salary ($): </label>
<input type="number" name="salary" id="salary" required>
</div>
<div>
<label for="startdate">Start Date: </label>
<input type="date" name="startdate" id="startdate" required>
</div>
<div>
<input type="submit" value="Generate Letter">
</div>
</form>
</body>
</html>
```

次のコマンドを使用してwebサーバーを実行します。

```
node app.js
```

「Candidate offer letter app listening on port 8000」というメッセージが表示されます。 ブラウザーを開いて <http://localhost:8000/>フォームは次のようになります。

![Webフォームのスクリーンショット](assets/offer_2.png)

フォームがそれ自体に投稿することに注意してください。 データを入力して **文字を生成，** コンソールに次の情報が表示されます。

```
Got body: { firstname: 'John',
lastname: 'Doe',
salary: '887888',
startdate: '2021-04-01' }
```

このコンソールログをWebサービス呼び出しに置き換えます [!DNL Acrobat Services]. まず、情報のJSONベースのモデルを作成する必要があります。 このモデルの書式は次のようになります。

```
{
    "offer_letter": {
    "firstname": "John",
    "lastname": "Doe",
    "salary": "887888",
    "startdate": "2021-04-01"
    }
}
```

このモデルは、必要に応じてさらに複雑にすることができますが、このチュートリアルでは、この簡単な例を使用してください。 この記事の範囲外であるため、このフォームに対する検証はありません。 フォーム本文を上記のデータモデルに変換するには、 app.postハンドラーメソッドを次のコードに変更します。

```
app.post('/', (req, res) => {
const docModel = {'offer_letter': req.body};
generateLetter(docModel);
res.sendStatus(200);
});
```

最初の行では、JSONデータを目的の形式に配置します。 次に、このデータをgenerateLetter関数に渡します。 サーバーを停止し、app.jsの最後に次のコードを貼り付けます。 このコードは、テンプレートとしてWordドキュメントを取り、JSONドキュメントからの情報でプレースホルダーを埋めます。

```
// Letter generation function
function generateLetter(jsonDataForMerge) {
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Create an ExecutionContext using credentials
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials);
// Create a new DocumentMerge options instance
const documentMerge = PDFToolsSdk.DocumentMerge,
documentMergeOptions = documentMerge.options,
options = new documentMergeOptions.DocumentMergeOptions(jsonDataForMerge,
documentMergeOptions.OutputFormat.PDF);
// Create a new operation instance using the options instance
const documentMergeOperation = documentMerge.Operation.createNew(options)
// Set operation input document template from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile(
'resources/OfferLetter-Template.docx');
documentMergeOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
documentMergeOperation.execute(executionContext)
.then(result => result.saveAsFile('output/OfferLetter.pdf'))
.catch(err => {
if(err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log(
'Exception encountered while executing operation', err);
} else {
console.log(
'Exception encountered while executing operation', err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
```

そこには多くのコードが展開されています。 まず本編から始めましょう。 `documentMergeOperation`. このセクションでは、JSONデータを取り出して、Wordドキュメントテンプレートに結合します。 次の用途で [Adobeサイトの例](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html#sample-blade) 参考になりますが、ここでは独自の簡単な例を作成しましょう。 Wordを開いて、空の新規文書を作成します。 好きなだけカスタマイズできますが、少なくとも次のようなものがあります。

X様、

年間$Xのポジションを提供させていただきます。 開始日はXです。

ようこそ

プロジェクトのルートにある「resources」というフォルダーに、ドキュメントを「OfferLetter-Template.docx」という名前で保存します。 ドキュメントに3つのXが記載されています。 これらのXsは、JSON情報用の一時的なプレースホルダーです。 これらのプレースホルダーの代わりに特別な構文を使用することもできますが、Adobeでは、この作業を簡単にするWordアドインを提供しています。 アドインをインストールするには、Adobeに移動します [Document Generation Tagger Wordアドイン](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin) サイト。

OfferLetter-Templateで、新しい **文書の生成** をクリックします。 サイドパネルが開きます。 「**今すぐ始める**」をクリックします。サンプルのJSONデータに貼り付けるテキスト領域が表示されます。 JSONの「offer-data」スニペットを上記からテキスト領域にコピーします。 次のように表示されます。

![文字とコードのスクリーンショット](assets/offer_3.png)

「 **タグの生成** をクリックします。 文書内の適切な位置に挿入するタグのドロップダウンメニューが表示されます。 ドキュメントの最初のXをハイライト表示し、 **[!UICONTROL firstname]**. クリック **[!UICONTROL テキストを挿入]** 「X君」が「X君」に変わる ```{{`offer_letter`.firstname}}```,&quot;. これは、 `documentMergeOperation`. 続けて、該当するXsで残りの3つのタグを追加します。 OfferLetter-template.docxを必ず保存してください。 次のようになります。

```{{`offer_letter`.firstname}} {{`offer_letter`.lastname}}``` 様

$.のポジションを提供させていただきます ```{{`offer_letter`.salary}}``` 1年です。 開始日： ```{{`offer_letter`.startdate}}```.

ようこそ

Wordテンプレートに、JSON形式と一致するマークアップが含まれるようになりました。 例えば、 ```{{`offer_letter`.`firstname`}}``` word文書の冒頭で、JSONデータの「firstname」セクションの値に置き換えられます。

アプリに戻る `generateLetter` 関数に渡します。 REST呼び出しをセキュリティで保護するには、プロジェクトルートにpdftools-api-credentials.jsonという名前の新しいファイルを作成します。 次のJSONデータをペーストして、のサービスアカウント(JWT)セクションから詳細を追加します。 [Developer Console](https://console.adobe.io/).

```
{
"client_credentials": {
"client_id": "<YOUR_CLIENT_ID>",
"client_secret": "<YOUR_CLIENT_SECRET>"
},
"service_account_credentials": {
"organization_id": "<YOUR_ORGANIZATION_ID>",
"account_id": "<YOUR_TECHNICAL_ACCOUNT_ID>",
"private_key_file": "<PRIVATE_KEY_FILE_PATH>"
}
}
```

* クライアントID、クライアントシークレット、組織IDは、 **[!UICONTROL 資格情報の詳細]** コンソールのセクション。

* アカウントIDは **テクニカルアカウントID**.

* 先ほど生成したprivate.keyファイルをプロジェクトにコピーし、pdftools-api-credentials.jsonファイルのprivate_key_fileセクションにその名前を入力します。 必要に応じて、ここに秘密鍵ファイルへのパスを追加できます。 一度は制御できないので、それを安全に保つことを忘れないでください。

JSONデータが入力されたPDFを生成するには、に戻ります **[!UICONTROL 候補者の詳細を入力]** webフォームを作成してデータを投稿できます。 文書をAdobeからダウンロードする必要があるため少し時間がかかりますが、 OfferLetter.pdfという名前のファイルが「output」という名前の新しいフォルダーに作成されています。

## 次の手順

これだ！ これは始まりに過ぎない。 Wordアドインの「ドキュメントの生成」タブの「詳細」セクションを調べると、すべてのプレースホルダーマーカーが関連付けられたJSONデータから取得されているわけではないことに気づきます。 署名タグを追加することもできます。 これらのタグを使用すると、結果の文書を取得して、アップロードし、 [Adobe Sign](https://acrobat.adobe.com/ca/en/sign.html) 配送して新しい従業員に署名するために。 Adobe Sign APIの使用方法については、「今日から始める」を参照してください。 JWTトークンで保護されたREST呼び出しを使用しているため、このプロセスは類似しています。

上記の1つの文書例は、組織が次の条件を満たす必要がある場合に、アプリケーションの基礎として使用できます [季節雇用を増やす](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html) 複数の場所の従業員の数。 実証されたように、メインフローは、オンラインアプリケーションを介して候補からデータを取ることです。 このデータは、オファーレターのフィールドに入力し、電子サイン用に送信するために使用されます。

[!DNL Adobe Acrobat Services] 6か月間無料で使用できます [従量課金制](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 文書のトランザクションあたりわずか0.05 USDで、ビジネスの成長に合わせてオファーレターのワークフローを調整できます。 宛先 [開始する](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)
独自のテンプレートを作成し、 [デベロッパーアカウントの新規登録](https://www.adobe.io/).

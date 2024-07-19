---
title: 従業員オファー・レターの管理
description: 新しい従業員に署名用に配信できる内定通知を生成する方法について説明します
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8096
thumbnail: KT-8096.jpg
exl-id: 92f955f0-add5-4570-aa3a-ea63055dadb2
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1714'
ht-degree: 0%

---

# 従業員の内定通知の管理

![使用事例の英雄バナー](assets/UseCaseOfferHero.jpg)

従業員の内定通知は、従業員が組織で最初に体験する通知の1つです。 その結果、オファーレターがブランドであることを確認したいが、毎回ワープロで最初から文字を作成する必要はない。 [!DNL Adobe Acrobat Services] APIは、[新しい従業員へのオファーレターの作成および配信](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html)の主要な部分を処理するための高速で簡単かつ効果的な方法を提供します。

## 学習内容

この実践チュートリアルでは、従業員の詳細を入力するユーザーのwebフォームを表示するNode Expressプロジェクトの設定について段階的に説明します。 これらの詳細は、Web上で[!DNL Acrobat Services]を使用して、Adobe Sign APIを使用して署名のためにお客様に配信できるPDFとしてオファーレターを作成します。

## 関連APIとリソース

* [PDFサービスAPI](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Adobe文書生成API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Document Generation Tagger Wordアドイン](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin)

* [プロジェクトのサンプル](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html)

## はじめに

[Node.js](https://nodejs.org/)はプログラミングプラットフォームです。 これには、Express webサーバーなどの膨大なライブラリのセットが付属しています。 [Node.js](https://nodejs.org/en/download/)をダウンロードし、手順に従って、この優れたオープンソース開発環境をインストールします。

Node.jsでAdobe Document Generation APIを使用するには、[Document Generation API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)サイトに移動してアカウントにアクセスするか、新しいアカウントにサインアップしてください。 お客様のアカウントは、6か月間[無料、その後は従量課金制](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)で、ドキュメントのトランザクション1件につきわずか0.05ドルです。無料体験して、会社の成長に応じてのみ支払うことができます。

[Adobe Developer Console](https://console.adobe.io/)にサインインしたら、**[!UICONTROL [新しいプロジェクトの作成]]**&#x200B;をクリックします。 デフォルトでは、プロジェクト名は「Project 1」です。 **[!UICONTROL プロジェクトの編集]**&#x200B;ボタンをクリックして、名前を「Offer Letter Generator」に変更します。 画面の中央には、**[!UICONTROL 新しいプロジェクトの開始]**&#x200B;セクションがあります。 プロジェクトのセキュリティを有効にするには、次の手順を実行します。

[**APIの追加**]をクリックします。 選択可能なAPIが多数あります。 **[!UICONTROL Document Cloudでフィルター]**&#x200B;セクションで、**[!UICONTROL 製品]**&#x200B;を選択し、[**[!UICONTROL 次へ]**]をクリックします。

ここで、APIにアクセスするための資格情報を生成します。 資格情報は、JSON Webトークン([JWT](https://jwt.io/))：安全な通信のためのオープンな標準の形式です。 JWTに精通していて、すでにキーを生成している場合は、ここで公開キーをアップロードできます。 または、**オプション1**&#x200B;を選択して、Adobeにキーを生成してもらうこともできます。

![資格情報を生成するスクリーンショット](assets/offer_1.png)

**[!UICONTROL [キーペアの生成]]**&#x200B;ボタンをクリックします。 config.zipファイルをダウンロードできます。 アーカイブファイルを解凍します。 このファイルには、certificate_pub.crtとprivate.keyの2つのファイルが含まれています。 後者はプライベートな認証情報を含み、制御不能な場合に偽の文書を生成するために使用される可能性があるため、安全に保たれるようにしてください。

「**[!UICONTROL 次へ]**」をクリックします。いいえ、PDF生成APIへのアクセスを有効にします。 **[!UICONTROL 製品プロファイルを選択]**&#x200B;画面で、**[!UICONTROL エンタープライズPDFサービスデベロッパー]**&#x200B;にチェックを入れ、**[!UICONTROL 構成されたAPIを保存]**&#x200B;ボタンをクリックします。 これで、APIの使用を開始する準備ができました。

## プロジェクトの設定

コードを実行するためのノードプロジェクトを設定します。 この例では、エディターとして[Visual Studio Code](https://code.visualstudio.com/) (VS Code)を使用しています。 「letter-generator」というフォルダーを作成し、VSコードで開きます。 **[!UICONTROL ファイル]**&#x200B;メニューから、**[!UICONTROL ターミナル]** \> **[!UICONTROL 新しいターミナル]**&#x200B;を選択して、このフォルダーのシェルを開きます。 次のコマンドを入力して、ノードがパスにインストールされていることを確認します。

```
node -v
```

インストールしたNodeのバージョンが表示されます。

開発環境がインストールされたので、プロジェクトを作成できます。

まず、ノードパッケージマネージャー(npm)を使用してプロジェクトを初期化します。 次のように入力します。

```
npm init
```

Nodeプロジェクトに関する質問が表示されます。 これらの質問の多くはスキップできますが、プロジェクト名が「letter-generator」で、エントリポイントが&#x200B;**index.js**&#x200B;であることを確認してください。 **はい**&#x200B;を選択して、プロジェクトの初期化を完了します。

package.jsonファイルが作成されました。 ノードはこのファイルを使用してプロジェクトを整理します。 index.jsを作成する前に、次の内容のAdobeライブラリを追加する必要があります
コマンド：

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

getルートは&#x200B;**index.html**&#x200B;ファイルを返します。 この名前と次の簡単な形式でHTMLファイルを作成してみましょう。 CSSスタイルやその他のデザイン要素は、必要に応じて後から追加することもできます。 以下のフォームは、候補者がウェルカムレターを作成する際の基本的な詳細情報を入力するものです。

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

「Candidate offer letter app listening on port 8000」というメッセージが表示されます。 ブラウザを<http://localhost:8000/>に開くと、フォームは次のようになります。

![Webフォームのスクリーンショット](assets/offer_2.png)

フォームがそれ自体に投稿することに注意してください。 データを入力して&#x200B;**Generate Letter,**&#x200B;をクリックすると、コンソールに次の情報が表示されます。

```
Got body: { firstname: 'John',
lastname: 'Doe',
salary: '887888',
startdate: '2021-04-01' }
```

このコンソールログを[!DNL Acrobat Services]へのWebサービス呼び出しに置き換えます。 まず、情報のJSONベースのモデルを作成する必要があります。 このモデルの書式は次のようになります。

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

そこには多くのコードが展開されています。 まず主要な部分である`documentMergeOperation`を取り上げましょう。 このセクションでは、JSONデータを取り出して、Wordドキュメントテンプレートに結合します。 Adobeサイト](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html#sample-blade)の[例を参考にできますが、簡単な例を独自に作成しましょう。 Wordを開いて、空の新規文書を作成します。 好きなだけカスタマイズできますが、少なくとも次のようなものがあります。

X様、

年間$Xのポジションを提供させていただきます。 開始日はXです。

ようこそ

プロジェクトのルートにある「resources」というフォルダーに、ドキュメントを「OfferLetter-Template.docx」という名前で保存します。 ドキュメントに3つのXが記載されています。 これらのXsは、JSON情報用の一時的なプレースホルダーです。 これらのプレースホルダーの代わりに特別な構文を使用することもできますが、Adobeでは、この作業を簡単にするWordアドインを提供しています。 アドインをインストールするには、Adobe[Document Generation Tagger Wordアドイン](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin)サイトに移動します。

OfferLetter-Templateで、新しい&#x200B;**Document Generation**&#x200B;ボタンをクリックします。 サイドパネルが開きます。 **開始**&#x200B;をクリックします。 サンプルのJSONデータに貼り付けるテキスト領域が表示されます。 JSONの「offer-data」スニペットを上記からテキスト領域にコピーします。 次のように表示されます。

![文字とコードのスクリーンショット](assets/offer_3.png)

「**タグを生成**」ボタンをクリックします。 文書内の適切な位置に挿入するタグのドロップダウンメニューが表示されます。 文書の最初のXをハイライト表示し、**[!UICONTROL firstname]**&#x200B;を選択します。 **[!UICONTROL テキストを挿入]**&#x200B;をクリックすると、「X様」が「X様```{{`offer_letter`.firstname}}```様」に変更されます。 このタグは`documentMergeOperation`の正しい形式です。 続けて、該当するXsで残りの3つのタグを追加します。 OfferLetter-template.docxを必ず保存してください。 次のようになります。

```{{`offer_letter`.firstname}} {{`offer_letter`.lastname}}``` 様

年間$ ```{{`offer_letter`.salary}}```のポジションを提供いたします。 開始日は```{{`offer_letter`.startdate}}```です。

ようこそ

Wordテンプレートに、JSON形式と一致するマークアップが含まれるようになりました。 例えば、Word文書の冒頭の```{{`offer_letter`.`firstname`}}```は、JSONデータの「firstname」セクションの値に置き換えられます。

`generateLetter`関数に戻ります。 REST呼び出しをセキュリティで保護するには、プロジェクトルートにpdftools-api-credentials.jsonという名前の新しいファイルを作成します。 次のJSONデータを貼り付け、[開発者コンソール](https://console.adobe.io/)のサービスアカウント(JWT)セクションから詳細を追加して調整してください。

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

* クライアントID、クライアントシークレット、および組織IDは、コンソールの&#x200B;**[!UICONTROL 資格情報の詳細]**&#x200B;セクションから直接コピーできます。

* アカウントIDは&#x200B;**テクニカルアカウントID**&#x200B;です。

* 前に生成したprivate.keyファイルをプロジェクトにコピーし、その名前を
pdftools-api-credentials.jsonファイル。 必要に応じて、ここに秘密鍵ファイルへのパスを追加できます。 一度は制御できないので、それを安全に保つことを忘れないでください。

JSONデータが入力されたPDFを作成するには、**[!UICONTROL 候補の詳細を入力]** Webフォームに戻り、データを投稿してください。 文書をAdobeからダウンロードする必要があるため少し時間がかかりますが、 OfferLetter.pdfという名前のファイルが「output」という名前の新しいフォルダーに作成されています。

## 次の手順

これだ！ これは始まりに過ぎない。 Wordアドインの「ドキュメントの生成」タブの「詳細」セクションを調べると、すべてのプレースホルダーマーカーが関連付けられたJSONデータから取得されているわけではないことに気づきます。 署名タグを追加することもできます。 これらのタグを使用すると、結果の文書を[Adobe Sign](https://acrobat.adobe.com/ca/en/sign.html)にアップロードして配信し、新しい社員に署名できます。 Adobe Sign APIの使用方法については、「今日から始める」を参照してください。 JWTトークンで保護されたREST呼び出しを使用しているため、このプロセスは類似しています。

上記の単一のドキュメントの例は、組織が複数の事業所の従業員を[季節的な雇用を](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html)増やす必要がある場合に、適用の基礎として使用できます。 実証されたように、メインフローは、オンラインアプリケーションを介して候補からデータを取ることです。 このデータは、オファーレターのフィールドに入力し、電子サイン用に送信するために使用されます。

[!DNL Adobe Acrobat Services]は6か月間無料で使用でき、その後[従量課金制](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)で文書トランザクション1件につきわずか0.05 USDで利用できます。ビジネスの成長に合わせてオファーレターのワークフローを拡張できます。 [使用を開始する](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)
独自のテンプレートを作成するには、[デベロッパーアカウントに新規登録](https://www.adobe.io/)してください。

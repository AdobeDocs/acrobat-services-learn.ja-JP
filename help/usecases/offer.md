---
title: 従業員の内定通知の管理
description: 署名用に新しい従業員に配信できるオファーレターの生成方法を説明します
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8096.jpg
kt: 8096
exl-id: 92f955f0-add5-4570-aa3a-ea63055dadb2
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1794'
ht-degree: 2%

---

# 従業員の内定通知の管理

![ユースケースのヒーローバナー](assets/UseCaseOfferHero.jpg)

内定通知は、従業員が組織で初めて体験するエクスペリエンスの 1 つです。 その結果、オファーレターがブランドどおりであることを確認する必要がありますが、常にワードプロセッサーでレターを一から作成する必要はありません。 [!DNL Adobe Acrobat Services] API は、 [新規従業員への内定通知の生成および配布](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html)を選択します。

## 学習内容

この実践チュートリアルでは、ユーザーが従業員の詳細を入力するための Web フォームを表示する Node Express プロジェクトの設定について説明します。 これらの詳細は、 [!DNL Acrobat Services] web 上でAdobe Sign API を使用して署名を依頼するために顧客に配信できるPDFとしてオファーレターを生成します。

## 関連する API とリソース

* [PDFサービス API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Adobeドキュメント生成 API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [Document Generation Tagger Word Add-in](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin)

* [プロジェクトサンプル](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html)

## はじめに

[Node.js](https://nodejs.org/) は、プログラミングプラットフォームです。 Express Web サーバーなど、膨大なライブラリのセットが付属しています。 [Node.js をダウンロード](https://nodejs.org/en/download/) 手順に従って、この素晴らしいオープンソース開発環境をインストールしてください。

Node.js でAdobeドキュメント生成 API を使用するには、 [ドキュメント生成 API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html) 」サイトを開きます。 アカウントは [6 ヶ月間無料で、その後は従量制](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 1 文書のトランザクションあたり 0.05 ドルで、リスクフリーで試し、会社の成長に応じて支払うことができます。

ログイン後 [Adobe Developer Console](https://console.adobe.io/)」で、「 **[!UICONTROL 新規プロジェクトを作成]**&#x200B;を選択します。 デフォルトでは、プロジェクト名は「プロジェクト 1」です。 ツールバーの「 **[!UICONTROL プロジェクトの編集]** 」ボタンをクリックし、名前を「オファーレタージェネレーター」に変更します。 画面の中央には、 **[!UICONTROL 新規プロジェクトの開始]** セクション プロジェクトのセキュリティを有効にするには、次の手順を実行します。

クリック **API を追加**&#x200B;を選択します。 多数の API が表示されます。 」を **[!UICONTROL 製品でフィルター]** 」セクションで、「 **[!UICONTROL Document Cloud]**&#x200B;を選択し、「 **[!UICONTROL 次へ]**&#x200B;を選択します。

次に、API にアクセスするための資格情報を生成します。 資格情報は、JSON Web トークン ([JWT](https://jwt.io/)):安全な通信のためのオープンスタンダード JWT に精通していて、既にキーを生成している場合は、ここで公開キーをアップロードできます。 または、 **オプション 1** Adobeにキーを生成させる。

![資格情報の生成のスクリーンショット](assets/offer_1.png)

ツールバーの「 **[!UICONTROL キーペアを生成]** 」ボタンをクリックします。 ダウンロードする config.zip ファイルを取得します。 アーカイブファイルを解凍します。 これには、次の 2 つのファイルが含まれます。certificate_pub.crt および private.key です。 後者にはプライベートな認証情報が含まれ、制御不能な場合に偽の文書を生成するために使用される可能性があるため、後者は安全に保たれるようにしてください。

「**[!UICONTROL 次へ]**」をクリックします。いいえ、PDF生成 API にアクセスできます。 」を **[!UICONTROL 製品プロファイルの選択]** スクリーン、チェック **[!UICONTROL 企業PDFサービス開発者]**&#x200B;を選択し、 **[!UICONTROL 設定済み API の保存]** 」ボタンをクリックします。 これで、API を使用する準備が整いました。

## プロジェクトの設定

コードを実行する Node プロジェクトを設定します。 この例では、 [Visual Studio Code](https://code.visualstudio.com/) （VS コード）をエディターとして使用します。 「letter-generator」というフォルダーを作成し、VS Code で開きます。 」を **[!UICONTROL ファイル]** メニューから **[!UICONTROL ターミナル]** \> **[!UICONTROL 新しいターミナル]** 」をクリックして、このフォルダーのシェルを開きます。 次のように入力して、Node がインストールされ、パス上に存在することを確認します。

```
node -v
```

インストールした Node のバージョンが表示されます。

開発環境がインストールされたので、プロジェクトを作成します。

まず、ノードパッケージマネージャー (npm) を使用してプロジェクトを初期化します。 次のように入力します。

```
npm init
```

Node プロジェクトに関するいくつかの質問が表示されます。 これらの質問のほとんどは省略できますが、プロジェクト名が「letter-generator」で、エントリポイントが **index.js**&#x200B;を選択します。 選択 **はい** 」をクリックします。

package.json ファイルができました。 Node はこのファイルを使用してプロジェクトを整理します。 index.js を作成する前に、次のコマンドを使用してAdobeライブラリを追加します。

```
npm install --save @adobe/documentservices-pdftools-node-sdk
```

プロジェクトに node_modules という新しいフォルダが追加されます。 このフォルダーには、すべてのライブラリ（ノードの依存関係と呼ばれる）がダウンロードされます。 package.json ファイルも、Adobe PDF Services への参照で更新されます。

Express を軽量な Web フレームワークとしてインストールします。 次のコマンドを入力します

```
npm install express –save
```

以前と同様に、package.json の dependencies セクションもそれに応じて更新されます。

## オファーレターテンプレートの作成

次に、プロジェクトルートに「app.js」というファイルを作成します。 次のスターターコードを挿入します。

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

get ルートは **index.html** します。 その名前と次の簡単なHTMLフォームを含むテンプレートファイルを作成します。 CSS スタイルやその他のデザイン要素は、後で適切に追加できます。 このフォームには、候補者のウェルカムレター生成に関する基本的な詳細が記載されています。

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

次のコマンドを使用して Web サーバーを実行します。

```
node app.js
```

「Candidate offer letter app listening on port 8000」というメッセージが表示されます。 ブラウザーを開いて <http://localhost:8000/>の場合、フォームは次のようになります。

![Web フォームのスクリーンショット](assets/offer_2.png)

フォームが自分自身に投稿されることに注意してください。 データを入力して **レターの作成** コンソールに次の情報が表示されます。

```
Got body: { firstname: 'John',
lastname: 'Doe',
salary: '887888',
startdate: '2021-04-01' }
```

このコンソールログは、 [!DNL Acrobat Services]を選択します。 まず、情報の JSON ベースのモデルを作成する必要があります。 このモデルの形式は次のようになります。

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

このモデルは必要に応じてさらに複雑にすることができますが、このチュートリアルでは、この単純な例を使用してください。 このフォームはこの記事の範囲外であるため、検証はありません。 フォーム本体を前述のデータモデルに変換するには、app.post ハンドラメソッドを次のコードに変更します。

```
app.post('/', (req, res) => {
const docModel = {'offer_letter': req.body};
generateLetter(docModel);
res.sendStatus(200);
});
```

最初の行では、JSON データを目的の形式で入力します。 このデータを generateLetter 関数に渡します。 サーバーを停止し、app.js の最後に次のコードを貼り付けます。 このコードは、Word ドキュメントをテンプレートとして受け取り、JSON ドキュメントからの情報をプレースホルダーに入力します。

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

そこにはたくさんの解凍コードがある。 まず、本編から見ていきましょう。この `documentMergeOperation`を選択します。 このセクションでは、JSON データを取得し、Word 文書テンプレートに結合します。 この [Adobeサイトの例](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html#sample-blade) 参考になりますが、ここでは簡単な例を作成してみましょう。 Word を開き、新しい空白の文書を作成します。 好きなだけカスタマイズできますが、少なくとも次のような機能があります。

X 様

私たちはあなたに年間$ X のポジションを提供することを喜んでいます。 あなたの出発は X です。

ようこそ

プロジェクトのルートにある「resources」というフォルダーに、「OfferLetter-Template.docx」という名前でドキュメントを保存します。 ドキュメント内の 3 つの X に注目してください。 これらの X は、JSON 情報の一時的なプレースホルダーです。 これらのプレースホルダーを置き換える特別なシンタックスを使用することもできますが、Adobeには、この作業を簡素化する Word アドインが用意されています。 アドインをインストールするには、Adobe [Document Generation Tagger Word Add-in](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=docgen-addin) サイト。

オファーレターテンプレートで、「 **文書生成** 」ボタンをクリックします。 サイドパネルが開きます。 「**今すぐ始める**」をクリックします。サンプルの JSON データに貼り付けるテキスト領域が表示されます。 JSON の「offer-data」スニペットを上からテキスト領域にコピーします。 次のようになります。

![文字とコードのスクリーンショット](assets/offer_3.png)

ツールバーの「 **タグを生成** 」ボタンをクリックします。 文書内の適切な位置に挿入するタグのドロップダウンメニューが表示されます。 ドキュメントの最初の X をハイライト表示し、 **[!UICONTROL 名]**&#x200B;を選択します。 クリック **[!UICONTROL テキストを挿入]** &quot;Dear X&quot;は&quot;Dear ```{{`offer_letter`.firstname}}```,&quot;. このタグは、 `documentMergeOperation`を選択します。 残りの 3 つのタグを適切な X に追加します。 OfferLetter-template.docx も忘れずに保存してください。 次のようになります。

```{{`offer_letter`.firstname}} {{`offer_letter`.lastname}}``` 様

$のポジションをご提供できることを嬉しく思います ```{{`offer_letter`.salary}}``` 年 開始日は ```{{`offer_letter`.startdate}}```を選択します。

ようこそ

これで、Word テンプレートに JSON 形式と一致するマークアップが追加されました。 例えば、 ```{{`offer_letter`.`firstname`}}``` Word ドキュメントの先頭が、JSON データの「firstname」セクションの値に置き換えられます。

前に戻る `generateLetter` 関数。 REST 呼び出しを保護するには、プロジェクトルートに pdftools-api-credentials.json という名前の新しいファイルを作成します。 次の JSON データを貼り付け、 [開発者コンソール](https://console.adobe.io/)を選択します。

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

* クライアント ID、クライアントシークレット、組織 ID は、 **[!UICONTROL 資格情報の詳細]** 」セクションを参照してください。

* アカウント ID は **テクニカルアカウント ID**&#x200B;を選択します。

* 先に生成した private.key ファイルをプロジェクトにコピーし、その名前を pdftools-api-credentials.json ファイルの private_key_file セクションに入力します。 必要に応じて、秘密鍵ファイルへのパスをここに置くことができます。 安全に保つことを忘れないでください。一度使用すると誤って使用される可能性があります。

JSON データを入力したPDFを生成するには、 **[!UICONTROL 候補者の詳細の入力]** web フォームを開き、データを投稿します。 ドキュメントをAdobeからダウンロードする必要があるため、少し時間がかかりますが、「output」という名前の新しいフォルダーに OfferLetter.pdf という名前のファイルが用意されています。

## 次の手順

これで終了です。 これは始まりに過ぎません。 Word アドインの [ ドキュメントの生成 ] タブの [ 詳細設定 ] セクションを調べると、関連する JSON データに含まれていないプレースホルダマーカーがあることに気づきます。 署名タグを追加することもできます。 これらのタグを使用すると、結果の文書を取り込んで、 [Adobe Sign](https://acrobat.adobe.com/ca/en/sign.html) 新しい従業員への配達と署名。 操作方法については、「Adobe Sign API の概要」を参照してください。 JWT トークンで保護された REST 呼び出しを使用しているため、このプロセスは同様です。

上記の 1 つの例は、組織が [季節雇用を増やす](https://www.adobe.io/apis/documentcloud/dcsdk/employee-offer-letters.html) 複数の拠点にいる従業員の数 このように、主な流れは、オンラインアプリケーションを通じて候補者からデータを取得することです。 このデータを使用してオファーレターのフィールドに入力し、電子サイン用に送信します。

[!DNL Adobe Acrobat Services] では 6 ヶ月間無料で使えます [従量制の](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 文書のトランザクション 1 件につき 0.05 ドルで完了します。ぜひお試しください。ビジネスの成長に合わせてオファーレターのワークフローを拡大できます。 宛先 [今すぐ始める](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)
独自のテンプレートの作成 [開発者アカウントの新規登録](https://www.adobe.io/)を選択します。

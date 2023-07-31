---
title: Node.jsの契約書ワークフロー
description: ”[!DNL Adobe Acrobat Services] APIにより、PDF機能をWebアプリケーションに簡単に組み込むことができます」
type: Tutorial
role: Developer
level: Beginner
feature: Use Cases
thumbnail: KT-7473.jpg
jira: KT-7473
keywords: おすすめ
exl-id: 44a03420-e963-472b-aeb8-290422c8d767
source-git-commit: b65ffa3efa3978587564eb0be0c0e7381c8c83ab
workflow-type: tm+mt
source-wordcount: '2182'
ht-degree: 1%

---

# Node.jsの契約書ワークフロー

![ユースケースの英雄バナー](assets/UseCaseAgreementHero.jpg)

多くのビジネスアプリケーションやプロセスでは、提案や契約書などの文書が必要です。 PDF文書を使用すると、ファイルの安全性と修正可能性を高めることができます。 また、クライアントがドキュメントをすばやく簡単に完了できるように、電子署名のサポートも提供します。 [!DNL Adobe Acrobat Services] APIは、PDF機能をwebアプリケーションに簡単に組み込むことができます。

## 学習内容

この実践チュートリアルでは、PDFサービスをNode.jsアプリケーションに追加して、契約書プロセスをデジタル化する方法について説明します。

## 関連APIとリソース

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF埋め込みAPI](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [プロジェクトコード](https://github.com/adobe/pdftools-node-sdk-samples)

## 設定する [!DNL Adobe Acrobat Services]

使用を開始するには、使用する資格情報を設定してください [!DNL Adobe Acrobat Services]. アカウントを登録し、 [Node.jsクイックスタート](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#node-js) を使用して、より大きなアプリケーションに機能を統合する前に、認証情報を検証することができます。

まず、Adobeデベロッパーアカウントを取得します。 その後、 [開始する](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html?ref=getStartedWithServicesSDK) ページで、 *開始する* 「新しい資格情報を作成」の下にあるオプション。 6か月間で使用できる1,000件の文書トランザクションを提供する無料体験版にサインアップできます。

![新しい資格情報の作成の画像](assets/AWNjs_1.png)

次の「新しい資格情報を作成」ページで、PDF埋め込みAPIとPDFサービスAPIのどちらを選択するかを求められます。

選択 *PDFサービスAPI*.

アプリケーションの名前を入力し、表示されたボックスをオンにします。 *パーソナライズされたコードサンプルの作成*. このボックスをオンにすると、コードサンプルに資格情報が自動的に埋め込まれます。 このチェックボックスをオフのままにした場合は、アプリケーションに資格情報を手動で追加する必要があります。

選択 *Node.js* をクリックします。 *資格情報の作成*.

しばらくすると、認証情報を含むサンプルプロジェクトの.zipファイルのダウンロードが開始されます。 のNode.jsパッケージ [!DNL Acrobat Services] は、サンプルプロジェクトコードの一部としてすでに含まれています。

![「PDFサービスAPI資格証明の選択」の図](assets/AWNjs_2.png)

## サンプルプロジェクトの手動設定

「新しい資格情報を作成」ページからサンプルプロジェクトをダウンロードしないことを選択した場合は、プロジェクトを手動で設定することもできます。

次からコードをダウンロードします（資格情報は埋め込みません）: [GitHub](https://github.com/adobe/pdftools-node-sdk-samples). このバージョンのコードを使用する場合は、使用前にpdftools-api-credentials.jsonファイルに資格情報を追加する必要があります。

```
{
  "client_credentials": {
    "client_id": "<client_id>",
    "client_secret": "<client_secret>"
  },
  "service_account_credentials": {
    "organization_id": "<organization_id>",
    "account_id": "<technical_account_id>",
    "private_key_file": "<private_key_file_path>"
  }
}
```

自分のアプリケーションの場合は、秘密キーファイルと資格情報ファイルをアプリケーションソースにコピーする必要があります。

以下の場合は、Node.jsパッケージをインストールする必要があります [!DNL Acrobat Services]. パッケージをインストールするには、次のコマンドを使用します。

```
npm install --save @adobe/documentservices-pdftools-node-sdk
```

## ログの設定

このサンプルでは、アプリケーション・フレームワークとしてExpressを使用しています。 また、アプリケーションのログ記録にもlog4jsを使用します。 log4jsを使用すると、簡単にコンソールに直接ログを記録したり、ファイルにログアウトしたりできます。

```
const log4js = require('log4js');
const logger = log4js.getLogger();
log4js.configure( {
    appenders: { fileAppender: { type:'file', filename: './logs/applicationlog.txt'}},
    categories: { default: {appenders: ['fileAppender'], level:'info'}}
});
 
logger.level = 'info';
logger.info('Application started')
```

上記のコードは、ログされたデータをのファイルに書き込みます。/logs/applicationlog.txt。 代わりにコンソールに書き込むようにする場合は、log4js.configureの呼び出しをコメントアウトできます。

## WordファイルをPDFに変換中

契約書や提案書は、Microsoft Wordなどの生産性向上アプリケーションを使用して作成されることがよくあります。 この書式の文書を受け入れ、文書をアプリケーションに変換するには、PDFの機能を追加します。 Expressアプリケーションでドキュメントをアップロードして保存し、ファイルシステムに保存する方法を見てみましょう。

アプリケーションのHTMLで、アップロードを開始するためのファイルエレメントとボタンを追加します。

```
<input type="file" name="source" id="source" />
<button onclick="upload()" >Upload</button>
```

ページのJavaScriptで、フェッチ関数を使用してファイルを非同期にアップロードします。

```
function upload()
{
  let formData = new FormData();
  var selectedFile = document.getElementById('source').files[0];
  formData.append("source", selectedFile);
  fetch('documentUpload', {method:"POST", body:formData});
}
```

アップロードしたファイルを受け入れるフォルダーを選択します。 アプリケーションには、このフォルダーへのパスが必要です。 \_\_dirnameで連結された相対パスを使用して絶対パスを検索します。

```
const uploadFolder = path.join(__dirname, "../uploads");
```

ファイルはpost経由で送信されるため、サーバー側でpostメッセージに応答する必要があります。

```
router.post('/', (req, res, next) => {
  console.log('uploading')
  if(!req.files || Object.keys(req.files).length === 0) {
  return res.status(400).send('No files were uploaded');
  }
    
  const uploadPath = path.join(uploadFolder, req.files.source.name);
  var buffer = req.files.source.data;
  var result = {"success":true};
  fs.writeFile(uploadPath, buffer, 'binary', (err)=> {
    if(err) {
      result.success = false;
    }
    res.json(result);
  });       
});
```

この関数を実行すると、ファイルはアプリケーションのアップロードフォルダーに保存され、その後の処理に使用できます。

次に、ファイルを元の形式からPDFに変換します。 先ほどダウンロードしたサンプルコードには、 `create-pdf-from-docx.js` 文書をPDFに変換します。 次の関数 `convertDocumentToPDF`、アップロードされた文書を取り出し、別のフォルダーのPDFーに変換します。

```
function convertDocumentToPDF(sourcePath, destinationPath)
{    
  try {   
    const credentials = PDFToolsSDK.Credentials
    .serviceAccountCredentialsBuilder()
    .fromFile("pdftools-api-credentials.json")
    .build();
 
    const executionContext = 
      PDFToolsSDK.ExecutionContext.create(credentials),
    createPdfOperation = PDFToolsSDK.CreatePDF.Operation.createNew();
 
    const docxReadableStream = fs.createReadStream(sourcePath);
    const input = PDFToolsSDK.FileRef.createFromStream(
      docxReadableStream, 
      PDFToolsSDK.CreatePDF.SupportedSourceFormat.docx);
    createPdfOperation.setInput(input);
 
    createPdfOperation.execute(executionContext)
    .then(result => result.saveAsFile(destinationPath))
    .catch(err => {        
      logger.erorr('Exception encountered while executing operation');        
    })
  }
  catch(err) {        
    logger.error(err);
  }
}
```

コードに一般的なパターンが表示される場合があります。

このコードは、資格情報オブジェクトと実行コンテキストを構築し、いくつかの操作を初期化してから、実行コンテキストを使用して操作を実行します。 このパターンは、サンプルコード全体で確認できます。

この関数を呼び出すようにアップロード関数を少し追加することで、ユーザーがアップロードするWord文書が自動的にPDFに変換されるようになりました。

次のコードは、変換されたPDFの変換先パスを構築し、変換を開始します。

```
const documentFolder = path.join(__dirname, "../docs");
var extPosition = req.files.source.name.lastIndexOf('.') - 1;
if(extPosition < 0 ) {
  extPosition = req.files.source.name.length
}
const destinationName = path.join(documentFolder,  
  req.files.source.name.substring(0, extPosition) + '.pdf');
console.log(destinationName);
 
logger.info('converting to ${destinationName}')
  convertDocumentToPDF(uploadPath, destinationName);
```

## 他のファイル形式をPDFに変換しています

Document Toolkitは、静的HTMLなどの他の書式をPDF（別の一般的な文書型）に変換します。 このツールキットでは、ドキュメントが参照するすべてのHTML（CSSファイル、イメージ、その他のファイル）が同じ.zipファイルに含まれる、.zipファイルとしてパッケージ化されたリソース文書を受け付けます。 HTML文書自体は、index.htmlという名前で、.zipファイルのルートに配置する必要があります。

HTMLを含む.zipファイルを変換するには、次のコードを使用します。

```
//Create an HTML to PDF operation and provide the source file to it
htmlToPDFOperation = PDFToolsSdk.CreatePDF.Operation.createNew();     
const input = PDFToolsSdk.FileRef.createFromLocalFile(sourceZipFile);
htmlToPDFOperation.setInput(input);
 
// custom function for setting options
setCustomOptions(htmlToPDFOperation);
 
// Execute the operation and Save the result to the specified location.
htmlToPDFOperation.execute(executionContext)
  .then(result => result.saveAsFile(destinationPdfFile))
  .catch(err => {
    logger.error('Exception encountered while executing operation');
});
```

関数 `setCustomOptions` 用紙サイズなどのその他のPDF設定を指定します。 この関数では、ページサイズが11.5 x 11インチに設定されています。

```
const setCustomOptions = (htmlToPDFOperation) => {    
  const pageLayout = new PDFToolsSdk.CreatePDF.options.PageLayout();
  pageLayout.setPageSize(11.5, 8);

  const htmlToPdfOptions = 
    new PDFToolsSdk.CreatePDF.options.html.CreatePDFFromHtmlOptions.Builder()
    .includesHeaderFooter(true)
    .withPageLayout(pageLayout)
    .build();
  htmlToPDFOperation.setOptions(htmlToPdfOptions);
};
```

いくつかの用語を含むHTML文書を開くと、ブラウザーで次の情報が表示されます。

![コンピューター用語のイメージ](assets/AWNjs_3.png)

この文書のソースは、CSSファイルとHTMLファイルで構成されています。

![CSSとHTMLファイルのイメージ](assets/AWNjs_4.png)

HTMLファイルを処理すると、同じテキストがPDF書式で表示されます。

![コンピューター用語のPDFファイル](assets/AWNjs_5.png)

## ページを追加しています

PDFファイルを使用するもう1つの一般的な操作は、用語のリストなどの標準テキストを含むページを最後に追加することです。 Document Toolkitは、複数のPDF文書を1つの文書にまとめることができます。 文書パスのリストがある場合(ここでは `sourceFileList`)を使用する場合、各ファイルのファイル参照をオブジェクトに追加して、結合操作を行うことができます。

結合操作を実行すると、ソースコンテンツを含む1つのファイルが提供されます。 次を使用できます `saveAsFile` をクリックして、ファイルをストレージに保持します。

```
const executionContext = PDFToolsSDK.ExecutionContext.create(credentials);
var combineOperation = PDFToolsSDK.CombineFiles.Operation.createNew();
 
sourceFileList.forEach(f => {
  var combinedSource = PDFToolsSDK.FileRef.createFromLocalFile(f);
  console.log(f);
  combineOperation.addInput(combinedSource);
});
    
 
combineOperation.execute(executionContext)
  .then(result=>result.saveAsFile(destinationFile))
  .catch(err => {
    logger.error(err.message);
});    
```

## PDF文書の表示

PDFファイルに対して行った操作はいくつかありますが、最終的にはユーザーが文書を表示する必要があります。 AdobeのPDF埋め込みAPIを使用して、文書をwebページに埋め込むことができます。

PDFを表示するページで、 `<div />` 文書を保持し、IDを付与するエレメント。 このIDをすぐに使用します。 Webページで、次を含めます。 `<script />` Adobe JavaScriptライブラリへの参照：

```
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
```

必要なコードの最後のビットは、Adobe PDF Embed API JavaScriptが読み込まれた後にドキュメントを表示する関数です。 adobe_dc_view\_sdk.readyイベントを介してスクリプトが読み込まれたことを示す通知を受け取ったら、新しいAdobeDC.Viewオブジェクトを作成します。 このオブジェクトには、以前に作成したクライアントIDと要素のIDが必要です。 クライアントIDは、 [Adobe Developer Console](https://console.adobe.io/). 以前に資格情報を生成したときに作成したアプリケーションの設定を表示すると、クライアントIDがそこに表示されます。

![APIクライアントキーのイメージ](assets/AWNjs_6.png)

## その他のPDFオプション

この [Adobe PDF埋め込みAPIデモ](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf) PDF文書を埋め込むための他の様々なオプションをプレビューできます。

![埋め込みPDFオプションの画像 ](assets/AWNjs_7.png)

さまざまなオプションのオンとオフを切り替えて、レンダリング方法をすぐに確認できます。 気に入った組み合わせが見つかったら、 *\&lt;/\>コードの生成* ボタンをクリックすると、これらのオプションを使用して実際のHTMLコードが生成されます。

![コードプレビューの画像](assets/AWNjs_8.png)

## デジタル署名とセキュリティの追加

文書の準備が整ったら、Adobe Signを使用してデジタル署名を追加して承認を受けることができます。 この機能は、これまでに使用した機能とは少し異なります。 デジタル署名の場合、ユーザー認証にOAuthを使用するようにアプリケーションを設定する必要があります。

アプリケーションを設定するための最初の手順は、次のとおりです。 [アプリケーションの登録](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/gstarted/create_app.md) Adobe SignでOAuthを使用します。 ログインしたら、 *アカウント*&#x200B;を開き、 *ADOBE SIGN API* セクションを選択し、 *APIアプリケーション* をクリックして、登録されたアプリケーションのリストを開きます。

![アプリケーション登録時の最初の手順の画像](assets/AWNjs_9.png)

新しいアプリケーションエントリを作成するには、右上隅にあるプラスアイコンをクリックします。

![画面の右上隅にあるプラスアイコンの画像](assets/AWNjs_10.png)

開いたウィンドウで、アプリケーション名と表示名を入力します。 選択 *お客様* ドメインの場合、 *保存*.

![アプリケーション名と表示名の入力先のイメージ](assets/AWNjs_11.png)

アプリケーションが作成されたら、リストからアプリケーションを選択し、 *アプリケーションのOAuthを設定*. オプションを選択します。 「リダイレクトURL」に、アプリケーションのURLを入力します。 複数のURLを入力できます。 テストするアプリケーションの値は次のとおりです。

```
http://localhost:3000/signed-in 
```

OAuthを使用してトークンを取得するプロセスは標準です。 アプリケーションによって、ログイン用のURLがユーザーに指示されます。 ユーザーが正常にログインすると、ページのクエリパラメーターに追加情報が記載されたアプリケーションにリダイレクトされます。

ログインURLの場合、アプリケーションはクライアントID、リダイレクトURL、および必要なスコープのリストを渡す必要があります。

URLのパターンは次のようになります。

```
https://secure.adobesign.com/public/oauth?
  redirect_uri=&
  response_type=code&
  client_id=&
  scope=
```

Adobe SignのIDにログインするように求められます。 ログイン後、アプリケーションに権限を付与するかどうかが尋ねられます。

![アクセス確認画面の画像](assets/AWNjs_12.png)

クリックした場合 *アクセスを許可* リダイレクトURLでは、 codeというクエリパラメーターが認証コードを渡します。

https://YourServer.com/?code=**\&lt;authorization_code>**\&amp;api_access_point=https://api.adobesign.com&amp;web_access_point=https://secure.adobesign.com

このコードをクライアントIDとクライアントシークレットとともにAdobe Signサーバーにポストすると、サービスにアクセスするためのアクセストークンが提供されます。 パラメーターの値を保存します `api_access_point` および `web_access_point`. これらの値は、以降の要求で使用されます。

```
var requestURL = ' ${api_access_point}oauth/token?code=${code}'
  +'&client_id=${client_id}'
  +'&client_secret=${client_secret}&'
  +'redirect_uri=${redirect_url}&'
  +'grant_type=authorization_code';
request.post(requestURL, {form: { }
}, (err,response,body)=>{                
    var token_response = JSON.parse(body)
    var access_token = token_response.access_token;
    console.log(access_token);
});
```

文書に署名が必要な場合は、まず文書をアップロードする必要があります。 アプリケーションは、文書を `api_access_point` oauthトークンの要求中に受信した値。 エンドポイントは `/api/rest/v6/transientDocuments`. リクエストデータは次のようになります。

```
POST /api/rest/v6/transientDocuments HTTP/1.1
Host: api.na1.adobesign.com
Authorization: Bearer MvyABjNotARealTokenHkYyi
Content-Type: multipart/form-data
Content-Disposition: form-data; name=";File"; filename="MyPDF.pdf"
<PDF CONTENT>
```

アプリケーション内で、次のコードを使用してリクエストを作成します。

```
var uploadRequest = {
  'method': 'POST',
  'url': '${oauthParameters.signin_domain}/api/rest/v6/transientDocuments',
  'headers': {
    'Authorization': 'Bearer  ${auth_token}'
  },
  formData: {
    'File': {
      'value': fs.createReadStream(documentPath),
      'options': {
        'filename': fileName,
        'contentType': null
      }
    }
  }
};
 
request(uploadRequest, (error, response) => {
  if (error) throw new Error(error);
  var jsonResponse = JSON.parse(response.body);
  var transientDocumentId = jsonResponse.transientDocumentId;
  logger.info('transientDocumentId:', transientDocumentId)
});
```

リクエストは、 `transientID` 値を返します。 文書はアップロードされましたが、まだ送信されていません。 文書を送信するには、 `transientID` をクリックして、文書の送信を要求します。

まず、署名される文書の情報を含むJSONオブジェクトを構築します。 次の変数では、 `transientDocumentId` には上記のコードのIDが含まれ、 `agreementDescription` 署名が必要な契約書を説明するテキストが含まれています。 文書に署名するユーザーが、 `participantSetsInfo` 電子メールアドレスと役割で指定します。

```
var requestBody = {
  "fileInfos":[
    {"transientDocumentId":transientDocumentId}],
    "name":agreementDescription,
    "participantSetsInfo":[
      {"memberInfos":[{"email":"user@domain.com"}],
       "order":1,"role":"SIGNER"}
    ],
    "signatureType":"ESIGN","state":"IN_PROCESS"
};
```

このWebリクエストを送信すると、署名リクエストが構築され、契約書リクエストのIDを含むJSONオブジェクトが返されます。

```
request(requestBody, function (error, response) {
  if (error) throw new Error(error);
  var JSONResponse = JSON.parse(response.body);
  var requestId = JSONResponse.id;
});
```

署名者が署名を忘れて別の通知メールが必要になった場合は、以前に受け取ったIDを使用して通知を再度送信します。 唯一の違いは、関係者の参加者IDも追加する必要があることです。 参加者IDを取得するには、GETリクエストを送信します。 `/agreements/{agreementID}/members`.

リマインダーの送信を要求するには、まずリクエストを記述するJSONオブジェクトを構築します。 最小オブジェクトには、参加者IDのリストとリマインダーのステータス（「ACTIVE」、「COMPLETE」、または「CANCELLED」）が必要です。

リクエストには、ユーザーに表示される「note」の値などの追加情報を必要に応じて含めることができます。 または、リマインダーを送信するまでの遅延時間（時間単位） `firstReminderDelay`)またはリマインダーの頻度（「頻度」フィールド内）。DAILY_UNTIL_SIGNED、EVERY_THIRD_DAY_UNTIL_SIGNED、WEEKLY_UNTIL_SIGNEDなどの値を指定できます。

```
var requestBody = {
  //participantList is an array of participant ID strings
  "recipientParticipantIds":participantList
  ,"status":"ACTIVE",
  "note":"This is a reminder to sign out important agreement."
}
 
var reminderRequest = {
  'method': 'POST',
  'url': '${oauthParameters.signin_domain}/api/rest/v6/agreements/${agreementID}/reminders',
  'headers': {
    'Authorization': `Bearer ${access_token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(requestBody)
 
};

request(reminderRequest, function (error, response) {
});
```

リマインダーリクエストを送信するには、以上の手順を実行します。

![Webフォームの画像](assets/AWNjs_13.png)

## Webフォームの作成

Adobe Sign APIを使用してWebフォームを作成することもできます。 Webフォームを使用すると、webページ内にフォームを埋め込んだり、フォームに直接リンクしたりできます。 Webフォームは作成されると、Adobe SignコンソールのWebフォーム間にも表示されます。 Webフォームフィールドを編集するためのDRAFTステータス、Webフォームフィールドを直ちにホストするためのAUTHORINGステータス、およびACTIVEステータスのWebフォームを作成して、段階的に作成することができます。

![Adobe Signの管理画面のWebフォームの画像](assets/AWNjs_14.png)

Webフォームを作成するには、フォームを使用します `transientDocumentId`. フォームのタイトルと初期化するステータスを決定します。

```
var requestBody = {
  "fileInfos": [
    {
      "transientDocumentId": transientDocumentId
    }
  ],
  "name": webFormTitle,
  "state": status,
  "widgetParticipantSetInfo": {
    "memberInfos": [ { "email": "" } ],
    "role": "SIGNER"
  }
}
```

```
var createWebFormRequest = {
  'method': 'POST',
  'url': `${oauthParameters.signin_domain}/api/rest/v6/widgets`,
  'headers': {
    'Authorization': `Bearer ${access_token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(requestBody)
}
```

```
request(createWebFormRequest, function (error, response) {
  var jsonResp = JSON.parse(response.body);
  var webFormID = jsonResp.id;
});
```

ドキュメントを埋め込んだり、リンクしたりできるようになりました。

## 次の手順

クイックスタートと提供されたコードからわかるように、Nodeを使用してPDFおよびデジタルドキュメントの承認プロセスを簡単に導入できます。 [!DNL Adobe Acrobat Services] API。 AdobeのAPIは、既存のクライアントアプリケーションとシームレスに連携します。

呼び出しに必要なスコープを調べたり、呼び出しの構築方法を確認したりするには、からサンプルの呼び出しを構築できます。 [Rest APIドキュメント](https://secure.na4.adobesign.com/public/docs/restapi/v6). この [クイックスタート](https://github.com/adobe/pdftools-node-sdk-samples) その他の機能とファイル形式についても説明します。 [!DNL Adobe Acrobat Services] APIプロセス。

アプリケーションに様々なPDF機能を追加して、文書の閲覧や署名などをすばやく簡単に行うことができます。 まず、以下をご覧ください [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/) 今日。

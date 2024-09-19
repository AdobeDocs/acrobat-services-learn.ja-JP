---
title: Node.jsの契約書ワークフロー
description: 「[!DNL Adobe Acrobat Services]個のAPIで、PDF機能をwebアプリケーションに簡単に組み込むことができます」
feature: Use Cases
role: Developer
level: Beginner
type: Tutorial
jira: KT-7473
thumbnail: KT-7473.jpg
keywords: おすすめ
exl-id: 44a03420-e963-472b-aeb8-290422c8d767
source-git-commit: f8a31b8f98d99bf1f3787e0f0f19cc9f26e24d8d
workflow-type: tm+mt
source-wordcount: '2094'
ht-degree: 0%

---

# Node.jsの契約書ワークフロー

![使用事例の英雄バナー](assets/UseCaseAgreementHero.jpg)

多くのビジネスアプリケーションやプロセスでは、提案や契約書などの文書が必要です。 PDF文書を使用すると、ファイルの安全性と修正可能性を高めることができます。 また、クライアントがドキュメントをすばやく簡単に完了できるように、電子署名のサポートも提供します。 [!DNL Adobe Acrobat Services]個のAPIは、PDF機能をWebアプリケーションに簡単に組み込むことができます。

## 学習内容

この実践チュートリアルでは、PDFサービスをNode.jsアプリケーションに追加して、契約書プロセスをデジタル化する方法について説明します。

## 関連APIとリソース

* [PDFサービスAPI](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF埋め込みAPI](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [プロジェクトコード](https://github.com/adobe/pdftools-node-sdk-samples)

## [!DNL Adobe Acrobat Services]をセットアップしています

開始するには、[!DNL Adobe Acrobat Services]を使用する資格情報を設定してください。 アカウントを登録し、[Node.js Quickstart](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#node-js)を使用して資格情報の機能を確認してから、機能を大規模なアプリケーションに統合してください。

まず、Adobeデベロッパーアカウントを取得します。 次に、[開始](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html?ref=getStartedWithServicesSDK)ページで、[新しい資格情報の作成]の[*開始*]オプションを選択します。 6か月間で使用できる1,000件の文書トランザクションを提供する無料体験版にサインアップできます。

![新しい資格情報の作成画像](assets/AWNjs_1.png)

次の「新しい資格情報を作成」ページで、PDF埋め込みAPIとPDFサービスAPIのどちらを選択するかを求められます。

*PDFサービスAPI*&#x200B;を選択します。

アプリケーションの名前を入力し、[*パーソナライズされたコードサンプルの作成*]ボックスをオンにします。 このボックスをオンにすると、コードサンプルに資格情報が自動的に埋め込まれます。 このチェックボックスをオフのままにした場合は、アプリケーションに資格情報を手動で追加する必要があります。

アプリケーションの種類として&#x200B;*Node.js*&#x200B;を選択し、*資格情報の作成*&#x200B;をクリックします。

しばらくすると、認証情報を含むサンプルプロジェクトの.zipファイルのダウンロードが開始されます。 [!DNL Acrobat Services]のNode.jsパッケージは、既にサンプルプロジェクトコードの一部として含まれています。

![PDFサービスAPI資格情報を選択する画像](assets/AWNjs_2.png)

## サンプルプロジェクトの手動設定

「新しい資格情報を作成」ページからサンプルプロジェクトをダウンロードしないことを選択した場合は、プロジェクトを手動で設定することもできます。

[GitHub](https://github.com/adobe/pdftools-node-sdk-samples)からコードをダウンロードします（資格情報は埋め込まれません）。 このバージョンのコードを使用する場合は、使用前にpdftools-api-credentials.jsonファイルに資格情報を追加する必要があります。

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

[!DNL Acrobat Services]のNode.jsパッケージをインストールしてください。 パッケージをインストールするには、次のコマンドを使用します。

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

次に、ファイルを元の形式からPDFに変換します。 以前にダウンロードしたサンプルコードには、文書をPDFに変換するための`create-pdf-from-docx.js`という名前のスクリプトが含まれています。 次の関数`convertDocumentToPDF`は、アップロードされた文書を受け取り、別のフォルダー内のPDFーに変換します：

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

関数`setCustomOptions`は、用紙サイズなど、その他のPDF設定を指定します。 この関数では、ページサイズが11.5 x 11インチに設定されています。

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

![コンピューターの使用条件のイメージ](assets/AWNjs_3.png)

この文書のソースは、CSSファイルとHTMLファイルで構成されています。

![CSSとHTMLファイルの画像](assets/AWNjs_4.png)

HTMLファイルを処理すると、PDF書式の同じテキストが表示されます。

![コンピューター利用条件のPDFファイル](assets/AWNjs_5.png)

## ページを追加しています

PDFファイルを使用するもう1つの一般的な操作は、用語のリストなどの標準テキストを含むページを最後に追加することです。 Document Toolkitは、複数のPDF文書を1つの文書にまとめることができます。 ドキュメントパス（`sourceFileList`内）の一覧がある場合は、結合操作で各ファイルのファイル参照をオブジェクトに追加できます。

結合操作を実行すると、ソースコンテンツを含む1つのファイルが提供されます。 オブジェクトで`saveAsFile`を使用して、ファイルをストレージに保持できます。

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

エレメントを表示するページで、`<div />` PDFを追加して文書を保持し、IDを付与します。 このIDをすぐに使用します。 Webページで、Adobe JavaScriptライブラリへの`<script />`参照を含めます。

```
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
```

必要なコードの最後のビットは、Adobe PDF Embed API JavaScriptが読み込まれた後にドキュメントを表示する関数です。 adobe_dc_view\_sdk.readyイベントを介してスクリプトが読み込まれたことを示す通知を受け取ったら、新しいAdobeDC.Viewオブジェクトを作成します。 このオブジェクトには、以前に作成したクライアントIDと要素のIDが必要です。 [Adobe Developer Console](https://console.adobe.io/)でクライアントIDを確認します。 以前に資格情報を生成したときに作成したアプリケーションの設定を表示すると、クライアントIDがそこに表示されます。

![APIクライアントキーのイメージ](assets/AWNjs_6.png)

## その他のPDFオプション

[Adobe PDF Embed APIのデモ](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf)では、PDF文書を埋め込むための他の様々なオプションをプレビューできます。

![埋め込みPDFオプションの画像](assets/AWNjs_7.png)

さまざまなオプションのオンとオフを切り替えて、レンダリング方法をすぐに確認できます。 気に入った組み合わせが見つかったら、「*\&lt;/\>コードを生成*」ボタンをクリックして、これらのオプションを使用して実際のHTMLコードを生成します。

![コードプレビューの画像](assets/AWNjs_8.png)

## デジタル署名とセキュリティの追加

文書の準備が整ったら、Adobe Signを使用してデジタル署名を追加して承認を受けることができます。 この機能は、これまでに使用した機能とは少し異なります。 デジタル署名の場合、ユーザー認証にOAuthを使用するようにアプリケーションを設定する必要があります。

アプリケーションを設定するための最初の手順は、[アプリケーションを登録](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/gstarted/create_app.md)して、Adobe Sign用のOAuthを使用することです。 ログイン後、*アカウント*&#x200B;をクリックしてアプリの作成画面に移動し、*Adobe Sign API*&#x200B;セクションを開き、*APIアプリ*&#x200B;をクリックして、登録されたアプリの一覧を開きます。

![アプリケーション登録時の最初のステップの画像](assets/AWNjs_9.png)

新しいアプリケーションエントリを作成するには、右上隅にあるプラスアイコンをクリックします。

![画面の右上隅にあるプラスアイコンの画像](assets/AWNjs_10.png)

開いたウィンドウで、アプリケーション名と表示名を入力します。 ドメインの&#x200B;*顧客*&#x200B;を選択し、*[保存]*&#x200B;をクリックします。

![アプリケーション名と表示名の入力先の画像](assets/AWNjs_11.png)

アプリケーションが作成されたら、リストからアプリケーションを選択し、*[アプリケーションのOAuthの構成]*&#x200B;をクリックします。 オプションを選択します。 「リダイレクトURL」に、アプリケーションのURLを入力します。 複数のURLを入力できます。 テストするアプリケーションの値は次のとおりです。

```
http://localhost:3000/signed-in 
```

OAuthを使用してトークンを取得するプロセスは標準です。 アプリケーションによって、ログイン用のURLがユーザーに指示されます。 ユーザーが正常にログインした後、
ページのクエリパラメーターに追加情報が含まれていると、アプリケーションにリダイレクトされます。

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

ユーザーがリダイレクトURLで&#x200B;*アクセスを許可*&#x200B;をクリックすると、codeという名前のクエリパラメーターにより認証コードが渡されます。

https://YourServer.com/?code=**\&lt;authorization_code\>**\&amp;api_access_point=https://api.adobesign.com&amp;web_access_point=https://secure.adobesign.com

このコードをクライアントIDとクライアントシークレットとともにAdobe Signサーバーにポストすると、サービスにアクセスするためのアクセストークンが提供されます。 パラメーター`api_access_point`と`web_access_point`の値を保存します。 これらの値は、以降の要求で使用されます。

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

文書に署名が必要な場合は、まず文書をアップロードする必要があります。 アプリケーションは、OAUTHトークンの要求中に受信した`api_access_point`値に文書をアップロードできます。 エンドポイントは`/api/rest/v6/transientDocuments`です。 リクエストデータは次のようになります。

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

要求は`transientID`値を返します。 文書はアップロードされましたが、まだ送信されていません。 ドキュメントを送信するには、`transientID`を使用してドキュメントの送信を要求します。

まず、署名される文書の情報を含むJSONオブジェクトを構築します。 次の場合、変数`transientDocumentId`には上記のコードのIDが含まれ、`agreementDescription`には署名が必要な契約書を説明するテキストが含まれています。 `participantSetsInfo`には、文書に署名するユーザーが電子メールアドレスと役割で一覧表示されます。

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

署名者が署名を忘れて別の通知メールが必要になった場合は、以前に受け取ったIDを使用して通知を再度送信します。 唯一の違いは、関係者の参加者IDも追加する必要があることです。 `/agreements/{agreementID}/members`にGET要求を送信すると、参加者IDを取得できます。

リマインダーの送信を要求するには、まずリクエストを記述するJSONオブジェクトを構築します。 最小オブジェクトには、参加者IDのリストとリマインダーのステータス（「ACTIVE」、「COMPLETE」、または「CANCELLED」）が必要です。

リクエストには、ユーザーに表示される「note」の値などの追加情報を必要に応じて含めることができます。 または、リマインダーを送信するまでの遅延（`firstReminderDelay`単位）と、DAILY_UNTIL_SIGNED、EVERY_THIRD_DAY_UNTIL_SIGNED、WEEKLY_UNTIL_SIGNEDなどの値を受け入れるリマインダーの頻度（「頻度」フィールドの値）。

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

Webフォームを作成するには、フォーム`transientDocumentId`を使用します。 フォームのタイトルと初期化するステータスを決定します。

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

クイックスタートと提供されたコードからわかるように、[!DNL Adobe Acrobat Services] APIを持つNodeを使用して、PDFおよびデジタルドキュメントの承認プロセスを簡単に実装できます。 AdobeのAPIは、既存のクライアントアプリケーションとシームレスに連携します。

呼び出しに必要なスコープを検出したり、呼び出しの構築方法を確認したりするには、[Rest APIドキュメント](https://secure.na4.adobesign.com/public/docs/restapi/v6)からサンプル呼び出しを構築します。 [Quickstarts](https://github.com/adobe/pdftools-node-sdk-samples)は、[!DNL Adobe Acrobat Services]個のAPIプロセスの他の機能とファイル形式も示します。

アプリケーションに様々なPDF機能を追加して、文書の閲覧や署名などをすばやく簡単に行うことができます。 開始するには、今すぐ[[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/)をご覧ください。

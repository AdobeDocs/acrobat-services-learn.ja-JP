---
title: Node.js の契約書ワークフロー
description: '"[!DNL Adobe Acrobat Services] API を使用すると、PDF機能を Web アプリケーションに簡単に組み込むことができます」'
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-7473.jpg
jira: KT-7473
keywords: おすすめ
exl-id: 44a03420-e963-472b-aeb8-290422c8d767
source-git-commit: 2d1151c17dfcfa67aca05411976f4ef17adf421b
workflow-type: tm+mt
source-wordcount: '2182'
ht-degree: 1%

---

# Node.js の契約書ワークフロー

![ユースケースのヒーローバナー](assets/UseCaseAgreementHero.jpg)

多くのビジネスアプリケーションやプロセスでは、提案書や契約書などの文書が必要です。 PDF文書は、ファイルの安全性を高め、変更の可能性を低くします。 電子署名のサポートも提供されるので、クライアントはすばやく簡単に文書を完成できます。 [!DNL Adobe Acrobat Services] API を使用すると、PDF機能を Web アプリケーションに簡単に組み込めます。

## 学習内容

この実践チュートリアルでは、Node.js アプリケーションにPDFサービスを追加して、契約プロセスをデジタル化する方法を説明します。

## 関連する API とリソース

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF埋め込み API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [プロジェクトコード](https://github.com/adobe/pdftools-node-sdk-samples)

## 設定 [!DNL Adobe Acrobat Services]

まず、使用する資格情報を設定します [!DNL Adobe Acrobat Services]を選択します。 アカウントを登録して、 [Node.js クイックスタート](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#node-js) を使用して、この機能を大規模なアプリケーションに統合する前に資格情報が機能することを確認します。

まず、デベロッパーアカウントをAdobeで取得します。 次に、 [はじめに](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html?ref=getStartedWithServicesSDK) 」ページで、「 *はじめに* 」オプションを選択します。 無料体験版に新規登録すると、6 か月間で 1,000 件のドキュメントトランザクションを利用できます。

![新しい資格情報を作成する画像](assets/AWNjs_1.png)

次の「新しい資格情報を作成」ページで、PDFの埋め込み API とPDFサービス API のいずれかを選択するように求められます。

選択 *PDFサービス API*&#x200B;を選択します。

アプリケーションの名前を入力し、「 *パーソナライズされたコードサンプルの作成*&#x200B;を選択します。 このチェックボックスをオンにすると、資格情報がコードサンプルに自動的に埋め込まれます。 このチェックボックスをオフのままにした場合、資格情報をアプリケーションに手動で追加する必要があります。

選択 *Node.js* 」を入力し、「 *資格情報の作成*&#x200B;を選択します。

しばらくすると、資格情報を含むサンプルプロジェクトを含む.zip ファイルのダウンロードが開始されます。 Node.js パッケージ ( [!DNL Acrobat Services] は、サンプルプロジェクトコードの一部として既に含まれています。

![「PDFサービス API 資格情報」を選択する画像](assets/AWNjs_2.png)

## サンプルプロジェクトの手動設定

「新しい資格情報の作成」ページからサンプルプロジェクトをダウンロードしない場合は、プロジェクトを手動で設定することもできます。

資格情報を埋め込まないコードを [GitHub](https://github.com/adobe/pdftools-node-sdk-samples)を選択します。 このバージョンのコードを使用する場合は、使用前に pdftools-api-credentials.json ファイルに資格情報を追加する必要があります。

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

独自のアプリケーションの場合は、秘密キーファイルと資格情報ファイルをアプリケーションソースにコピーする必要があります。

Node.js パッケージを [!DNL Acrobat Services]を選択します。 パッケージをインストールするには、次のコマンドを使用します。

```
npm install --save @adobe/documentservices-pdftools-node-sdk
```

## ログの設定

このサンプルでは、アプリケーションフレームワークに Express を使用しています。 また、アプリケーションのロギングには log4js を使用します。 log4js を使用すると、ログをコンソールまたはファイルに簡単に送信できます。

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

上記のコードは、ログに記録されたデータをのファイルに書き込みます。/logs/applicationlog.txtをご覧ください。 代わりにコンソールに書き込む場合は、log4js.configure の呼び出しをコメントアウトできます。

## Word ファイルからPDF

契約書や提案書は、多くの場合、Microsoft Word などの生産性アプリケーションで作成されます。 この形式の文書を受け入れ、文書をPDFに変換するには、アプリケーションの機能を追加します。 Express アプリケーションでドキュメントをアップロードして保存し、ファイルシステムに保存する方法を見てみましょう。

アプリケーションのHTMLで、アップロードを開始するための file エレメントとボタンを追加します。

```
<input type="file" name="source" id="source" />
<button onclick="upload()" >Upload</button>
```

ページの JavaScript で、fetch 関数を使用してファイルを非同期にアップロードします。

```
function upload()
{
  let formData = new FormData();
  var selectedFile = document.getElementById('source').files[0];
  formData.append("source", selectedFile);
  fetch('documentUpload', {method:"POST", body:formData});
}
```

アップロードしたファイルを受け入れるフォルダーを選択します。 アプリケーションには、このフォルダーへのパスが必要です。 絶対パスを探すには、\_\_dirname で連結された相対パスを使用します。

```
const uploadFolder = path.join(__dirname, "../uploads");
```

ファイルは post を介して送信されるので、サーバー側で post メッセージに応答する必要があります。

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

この関数を実行すると、ファイルは applications のアップロードフォルダーに保存され、以降の処理に使用できるようになります。

次に、ファイルをネイティブ形式からPDFに変換します。 先ほどダウンロードしたサンプルコードには、 `create-pdf-from-docx.js` を使用して、文書をPDFに 次の関数 `convertDocumentToPDF`でアップロードした文書を取得し、別のフォルダーのPDFーに変換します。

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

コードに一般的なパターンがあります。

このコードは、資格情報オブジェクトと実行コンテキストを構築し、一部の操作を初期化し、その操作を実行コンテキストで実行します。 このパターンは、サンプルコード全体で確認できます。

この関数を呼び出すようにアップロード関数にいくつかの追加を行うことで、ユーザーがアップロードする Word 文書が自動的にPDFに変換されます。

次のコードは、変換されたパスの宛先パスを構築し、PDFを開始します。

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

## 他のファイル形式のPDF

ドキュメントツールキットは、他の形式もPDFに変換します。例えば、静的HTMLは一般的なドキュメントタイプです。 このツールキットは、HTMLドキュメントを.zip ファイルとしてパッケージ化し、そのドキュメントで参照されるすべてのリソース（CSS ファイル、イメージ、その他のファイル）を同じ.zip ファイルに格納できます。 HTMLドキュメント自体は index.html という名前で、.zip ファイルのルートに配置する必要があります。

HTMLを含む.zip ファイルを変換するには、次のコードを使用します。

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

関数 `setCustomOptions` その他のPDF設定（ページサイズなど）を指定します。 この例では、関数がページサイズを 11.5 x 11 インチに設定しています。

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

一部の用語を含むHTMLドキュメントを開くと、ブラウザー内に次の内容が表示されます。

![コンピューター用語の画像](assets/AWNjs_3.png)

このドキュメントのソースは、CSS ファイルとHTMLファイル

![CSS の画像とHTMLファイル](assets/AWNjs_4.png)

HTMLファイルを処理すると、同じテキストがPDF形式で

![PDFの用語ファイル](assets/AWNjs_5.png)

## ページの追加

また、PDFファイルでは、用語リストなどの標準テキストを含むページを末尾に追加する操作も一般的です。 ドキュメントツールキットでは、複数のPDFドキュメントを組み合わせて 1 つのドキュメントにできます。 文書パスのリストがある場合 ( `sourceFileList`) を使用すると、各ファイルのファイル参照をオブジェクトに追加して、結合操作を行うことができます。

結合操作を実行すると、ソースコンテンツの単一のファイルが提供されます。 以下を使用できます `saveAsFile` を選択して、ファイルをストレージに保持します。

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

## PDF文書

これまでにPDFファイルに対して複数の操作を実行しましたが、最終的にはユーザーがドキュメントを表示する必要があります。 ドキュメントを Web ページに埋め込むには、AdobeのPDF埋め込み API を使用します。

PDFを表示するページで、 `<div />` エレメントを使用して文書を保持し、ID を付与します。 この ID は近日中に使用してください。 Web ページに、 `<script />` javascript ライブラリAdobeへの参照：

```
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
```

必要な最後のコードは、Adobe PDF Embed API JavaScript が読み込まれるとドキュメントを表示する関数です。 スクリプトが adobe_dc_view\_sdk.ready イベントを介して読み込まれたという通知を受信したら、新しい AdobeDC.View オブジェクトを作成します。 このオブジェクトには、クライアント ID と以前に作成したエレメントの ID が必要です。 クライアント ID は [Adobe Developer Console](https://console.adobe.io/)を選択します。 以前に資格情報を生成したときに作成したアプリケーションの設定を表示すると、クライアント ID が表示されます。

![API クライアントキーのイメージ](assets/AWNjs_6.png)

## その他のPDFオプション

この [Adobe PDF Embed API デモ](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf) では、ドキュメントを埋め込むための他の様々なオプションをプレビューPDFできます。

![埋め込みオプションのPDF画像 ](assets/AWNjs_7.png)

様々なオプションのオンとオフを切り替えて、レンダリングの様子をすぐに確認できます。 目的の組み合わせが見つかったら、 *\&lt;/\> コードの生成* ボタンを使用して、これらのオプションを使用して実際のHTMLコードを生成することができます。

![コードプレビューの画像](assets/AWNjs_8.png)

## 電子署名とセキュリティの追加

文書の準備ができたら、Adobe Signを使用して承認用の電子署名を追加できます。 この機能は、これまでに使用した機能とは少し異なります。 電子署名の場合、ユーザー認証に OAuth を使用するようにアプリケーションを設定する必要があります。

アプリケーションを設定する最初の手順は、次のとおりです [アプリケーションの登録](https://www.adobe.io/apis/documentcloud/sign/docs.html#!adobedocs/adobe-sign/master/gstarted/create_app.md) OAuth for Adobe Signを使用します。 ログインしたら、「 *アカウント*&#x200B;を選択し、 *Adobe Sign API* 」セクションで *API アプリケーション* をクリックして、登録されているアプリケーションのリストを開きます。

![アプリケーション登録の最初のステップのイメージ](assets/AWNjs_9.png)

新しいアプリケーションエントリを作成するには、右上隅のプラスアイコンをクリックします。

![画面の右上隅にあるプラスアイコンの画像](assets/AWNjs_10.png)

開いたウィンドウで、アプリケーション名と表示名を入力します。 選択 *お客様* 」を選択し、「 *保存*&#x200B;を選択します。

![アプリケーション名と表示名を入力する場所のイメージ](assets/AWNjs_11.png)

アプリケーションが作成されたら、リストから選択して「 *アプリケーションの OAuth の設定*&#x200B;を選択します。 オプションを選択します。 「リダイレクト URL」に、アプリケーションの URL を入力します。 ここには複数の URL を入力できます。 テストするアプリケーションの値は次のとおりです。

```
http://localhost:3000/signed-in 
```

OAuth を使用してトークンを取得するプロセスは標準的です。 アプリケーションは、ログイン用の URL にユーザーを誘導します。 ユーザーが正常にログインすると、ページのクエリパラメーターに追加情報が含まれるアプリケーションにリダイレクトされます。

ログイン URL の場合、アプリケーションはクライアント ID、リダイレクト URL、および必要なスコープのリストを渡す必要があります。

URL のパターンは次のようになります。

```
https://secure.adobesign.com/public/oauth?
  redirect_uri=&
  response_type=code&
  client_id=&
  scope=
```

ユーザーは、Adobe Signの ID にログインするように求められます。 ログインすると、アプリケーションに権限を付与するかどうかを確認するメッセージが表示されます。

![アクセス確認画面の画像](assets/AWNjs_12.png)

ユーザーが *アクセスを許可* リダイレクト URL で、code という名前のクエリパラメーターが認証コードを渡します。

https://YourServer.com/?code=**\&lt;authorization_code>**\&amp;api_access_point=https://api.adobesign.com&amp;web_access_point=https://secure.adobesign.com

このコードをクライアント ID とクライアントシークレットとともにAdobe Signサーバーに送信すると、サービスにアクセスするためのアクセストークンが提供されます。 パラメーターの値を保存します `api_access_point` および `web_access_point`を選択します。 これらの値は、以降のリクエストに使用されます。

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

文書に署名が必要な場合は、まず文書をアップロードする必要があります。 アプリケーションは、文書を `api_access_point` oauth トークンの要求中に受信した値です。 エンドポイントは `/api/rest/v6/transientDocuments`を選択します。 要求データは次のようになります。

```
POST /api/rest/v6/transientDocuments HTTP/1.1
Host: api.na1.adobesign.com
Authorization: Bearer MvyABjNotARealTokenHkYyi
Content-Type: multipart/form-data
Content-Disposition: form-data; name=";File"; filename="MyPDF.pdf"
<PDF CONTENT>
```

アプリケーション内で、次のコードを使用してリクエストをビルドします。

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

リクエストが `transientID` 値。 ドキュメントはアップロードされましたが、まだ送信されていません。 文書を送信するには、 `transientID` 」をクリックして文書の送信をリクエストします。

まず、署名する文書の情報を含む JSON オブジェクトを作成します。 次の例では、変数 `transientDocumentId` 上記のコードの ID と `agreementDescription` 署名が必要な契約書を説明するテキストが含まれます。 文書に署名するユーザーのリストは、 `participantSetsInfo` 電子メールアドレスと役割に基づいて送信されます。

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

この Web 要求を送信すると、署名要求が作成され、契約書の要求の ID を含む JSON オブジェクトが返されます。

```
request(requestBody, function (error, response) {
  if (error) throw new Error(error);
  var JSONResponse = JSON.parse(response.body);
  var requestId = JSONResponse.id;
});
```

署名者が署名を忘れ、別の通知メールが必要な場合は、以前に受信した ID を使用して通知を再度送信します。 唯一の違いは、関係者の参加者 ID も追加する必要があることです。 参加者 ID を取得するには、GET要求を `/agreements/{agreementID}/members`を選択します。

リマインダーの送信をリクエストするには、まずリクエストを記述する JSON オブジェクトを作成します。 最小限のオブジェクトには、参加者 ID のリストとリマインダーのステータス（「ACTIVE」、「COMPLETE」または「CANCELLED」）が必要です。

リクエストには、オプションで、ユーザーに表示される「note」の値などの追加情報を含めることができます。 または、リマインダーを送信するまでの待ち時間（時間単位） `firstReminderDelay`)、およびリマインダー頻度（「frequency」フィールド内）を指定します。リマインダーには、DAILY_UNTIL_SIGNED、EVERY_THIRD_DAY_UNTIL_SIGNED、WEEKLY_UNTIL_SIGNED などの値を指定できます。

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

リマインダーリクエストの送信は、これで完了です。

![Web フォームの画像](assets/AWNjs_13.png)

## Web フォームの作成

Adobe Sign API を使用して Web フォームを作成することもできます。 Web フォームを使用すると、Web ページ内にフォームを埋め込んだり、直接フォームにリンクしたりできます。 Web フォームが作成されると、Adobe Signコンソールの Web フォームにも表示されます。 増分作成用のドラフトステータス、Web フォームフィールドを編集用のオーサリングステータス、およびフォームをすぐにホストするためのアクティブステータスを備えた Web フォームを作成できます。

![Adobe Signの管理画面の Web フォームの画像](assets/AWNjs_14.png)

Web フォームを作成するには、次のフォームを使用します `transientDocumentId`を選択します。 フォームのタイトルと初期化のステータスを決定します。

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

ドキュメントへの埋め込みまたはリンクが可能になりました。

## 次の手順

クイックスタートと提供されたコードからわかるように、 [!DNL Adobe Acrobat Services] API Adobeの API は、既存のクライアントアプリケーションとシームレスに統合できます。

呼び出しに必要なスコープを調べたり、呼び出しの構築方法を確認したりするには、 [Rest API ドキュメント](https://secure.na4.adobesign.com/public/docs/restapi/v6)を選択します。 この [Quickstarts](https://github.com/adobe/pdftools-node-sdk-samples) その他の機能とファイル形式についても説明します。 [!DNL Adobe Acrobat Services] API プロセス、

アプリケーションに多数のPDF機能を追加して、ユーザーが文書をすばやく簡単に表示して署名できるようにすることが可能です。 まず、 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/) 今日です。

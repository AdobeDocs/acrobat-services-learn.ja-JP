---
title: レビューと承認
description: クロスチーム共同作業のためのドキュメントのレビューと承認ワークフローを構築する方法について説明します
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8094
thumbnail: KT-8094.jpg
exl-id: d704620f-d06a-4714-9d09-3624ac0fcd3a
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '1540'
ht-degree: 0%

---

# レビューと承認

![使用事例の英雄バナー](assets/UseCaseReviewsHero.jpg)

COVID-19の大流行の間に、多くの企業でチーム間のリモート共同作業が必要となりました。[デジタルドキュメントの共有とレビュー](https://developer.adobe.com/document-services/use-cases/collaboration/review-and-approval)には、チームや部門間のリソースにとって一連の課題が伴います。

例えば、異なるファイル形式でのドキュメントの共有、コンテンツの効果的なレビューとコメント、最新の編集内容との同期などの課題があります。 [!DNL Adobe Acrobat Services]個のAPIは、アプリケーション開発者がユーザーに対してこれらの課題を解決できるように設計されています。

## 学習内容

この実践チュートリアルでは、Node.jsとExpress Webアプリケーションでドキュメントのレビューと承認のワークフローを構築する方法を説明します。 このチュートリアルを実行するには、Node.jsの使用経験が必要です。

このアプリケーションには次の機能があります。

* 異なるファイル形式をPDFに変換

* ファイルアップロードを有効にする

* ユーザーがコメントや注釈を追加できるようにする

* PDFとそのコメントを表示する

* ユーザープロファイルを有効にしてコメント作成者を識別

* ファイルを結合して最終的なPDFを作成し、ダウンロードする

## 関連APIとリソース

* [PDFサービスAPI](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF埋め込みAPI](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [プロジェクトコード](https://github.com/contentlab-io/adobe_reviews_and_approvals)

## AdobeAPI資格情報を作成しています

コードを開始する前に、Adobe PDF Embed APIおよびAdobe PDF Services APIの[資格情報を作成](https://www.adobe.com/go/dcsdks_credentials)する必要があります。 PDF埋め込みAPIは無料で使用できます。 PDFサービスAPIは6か月間無料で使用できます。その後、文書のトランザクション1件につきわずか\$0.05で[従量課金制](https://developer.adobe.com/document-services/pricing/main)に切り替えることができます。

PDFサービスAPIの資格情報を作成する際に、**個人用コードサンプルの作成**&#x200B;オプションを選択し、その言語のNode.jsを選択します。 ZIPファイルを保存し、 pdftools-api-credentials.jsonとprivate.keyをNode.js Expressプロジェクトのルートディレクトリに抽出します。

## プロジェクトと依存関係の設定

「public」という名前のフォルダから静的ファイルを提供するように、Node.jsとExpressプロジェクトを設定します。 環境設定に応じて、プロジェクトの方法を設定できます。 すばやく起動して実行するには、[Expressアプリジェネレーター](https://expressjs.com/en/starter/generator.html)を使用します。 シンプルな構成にする場合は、[ゼロから開始](https://expressjs.com/en/starter/hello-world.html)して、コードを1つのJavaScriptファイルに保存します。 上記のリンクのサンプルプロジェクトでは、1ファイルによるアプローチを使用し、コード全体をindex.jsに保存しています。

`pdftools-api-credentials.json`ファイルと`private.key`ファイルを、パーソナライズされたコードサンプルからプロジェクトのルートディレクトリにコピーします。 また、資格情報ファイルが誤ってリポジトリに送信されないように、.gitignoreファイルがある場合は、追加します。

次に`npm install @adobe/documentservices-pdftools-node-sdk`を実行して、PDFサービス用のNode.js SDKをインストールします。 このモジュールを読み込み、残りの依存関係の読み込み後、コード内（サンプルプロジェクトのindex.js）にAPI資格情報オブジェクトを作成します。以下に例を示します。

```
  const PDFToolsSdk = require( "@adobe/documentservices-pdftools-node-sdk" );

  // Create Credentials
  const credentials =  PDFToolsSdk.Credentials
      .serviceAccountCredentialsBuilder()
      .fromFile( "pdftools-api-credentials.json" )
      .build();
```

開始コードは次のようになります。

```
  
  const express = require( "express" );
  const PDFToolsSdk = require( "@adobe/documentservices-pdftools-node-sdk" );

  // Create Credentials
  const credentials =  PDFToolsSdk.Credentials
      .serviceAccountCredentialsBuilder()
      .fromFile( "pdftools-api-credentials.json" )
      .build();

  const app = express();

  app.use( express.static( "public" ) );

  app.listen( 8889, function() {
      console.log( "Server started on port", 8889 );
  } );
```

これで、[!DNL Acrobat Services]個のAPIを使用する準備ができました。

## ファイルのPDFへの変換

文書ワークフローの前半では、エンドユーザーが共有する文書をアップロードする必要があります。 これを可能にするには、アップロード機能を追加し、異なる文書ファイルフォーマットをPDFに統合して、レビュープロセスに備えます。

まず、[PDFサービスAPIのサンプルスニペット](https://developer.adobe.com/document-services/apis/pdf-services)に基づいて文書をPDFに変換する関数を作成します。 この例では、光学式文字認識(OCR)、パスワード保護と削除、圧縮など、その他の重要な機能のスニペットも示しています。

```
function fileToPDF( filename, outputFilename, callback ) {
      // Create an ExecutionContext using credentials and create a new operation
  instance.
      const executionContext = PDFToolsSdk.ExecutionContext.create( credentials ),
          createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();

      // Set operation input from a source file.
      const input = PDFToolsSdk.FileRef.createFromLocalFile( filename );
      createPdfOperation.setInput( input );

      // Execute the operation and Save the result to the specified location.
      createPdfOperation.execute( executionContext )
          .then( result => {
              result.saveAsFile( outputFilename );
              callback( outputFilename );
          } );
  }
```

アップロードした文書から、この機能を使用してPDFを作成できるようになりました。

## ファイルのアップロード処理

次に、サーバーはドキュメントを受信して処理するために、webサーバー上にファイルアップロードエンドポイントを必要とします。

まず、アップロードフォルダー内にフォルダーを作成し、「drafts」という名前を付けます。 アップロードしたファイルと変換したPDFファイルはここに保存します。 次に`npm install express-fileupload`を実行してExpress-FileUploadモジュールをインストールし、コード内でミドルウェアをExpressに追加します：

```
const fileUpload = require( "express-fileupload" );
app.use( fileUpload() );
```

次に、`/upload `エンドポイントを追加し、アップロードしたファイルを、同じファイル名を使用して下書きフォルダー内に保存します。 次に、先ほど記述した関数を呼び出して、同じ文書のPDFファイルを作成します(まだPDF書式になっていない場合)。 アップロードされた元の文書の名前に基づいて、新しいPDFファイルのファイル名を作成できます。

```
// Create a PDF file from an uploaded file
app.post( "/upload", ( req, res ) => {
    if( !req.files || Object.keys( req.files ).length === 0 ) {
        return res.status( 400 ).send( "No files were uploaded." );
    }
    
    // Create PDF from the uploaded file
    let file = req.files.myFile;
    file.mv( __dirname + "/uploads/drafts/" + file.name, ( err ) => {
        if( err ) {
            return res.status( 500 ).send( err );
        }
        if( file.name.endsWith( ".pdf" ) ) {
            res.redirect( "/" );
        }
        else {
            // Convert to PDF
            fileToPDF( __dirname + "/uploads/drafts/" + file.name, __dirname + "/uploads/drafts/" + file.name.replace( /\./g, "-" ) + ".pdf", ( file ) => {
                res.redirect( "/" );
            } );
        }
    });
} );
```

## アップロードページの作成

Webアプリケーションからファイルをアップロードするには、アップロードフォルダー内に`index.html` Webページを作成します。 ページで、 /uploadエンドポイントにファイルを送信するファイルアップロードフォームを追加します。

```
<form ref="uploadForm" 
      action="/upload"
      method="post" 
      encType="multipart/form-data">
      <input type="file" name="myFile" accept=".doc,.docx,.ppt,.pptx,.xls,.xlsx,.txt,.rtf,.bmp,.jpg,.gif,.tiff,.png">
      <input type="submit" value="Upload File" />
  </form>
```

![Webページのファイルのアップロード機能のスクリーンショット](assets/reviews_1.png)

ドキュメントをNode.jsサーバーにアップロードできるようになりました。 アップロード/下書きフォルダー内にファイルが保存され、そのファイルと一緒にPDFフォーマットのバージョンが作成されます。

これでアップロードした文書を埋め込む準備が整いました。この場合は、Adobe Embed APIを使用して、PDFが文書に簡単にコメントや注釈を追加できるようにしてください。

## PDFファイルを列挙しています

一般的なドキュメントワークフローには複数のドキュメントが含まれる場合があるため、ドキュメントのリストを表示して、それぞれをアプリの新しいドキュメントレビューページにリンクする必要があります。

まず、サーバーコード内で、 uploads/draftsフォルダーに保存されているすべてのPDFファイルのリストを取得して返す/filesエンドポイントを追加します。

```
const fs = require( "fs" );

app.get( "/files", ( req, res ) =\> {

fs.readdir( \_\_dirname + "/uploads/drafts/", ( err, files ) =\> {

if( err ) {

return res.status( 500 ).send( err );using

}

return res.json( files.filter( f =\> f.endsWith( ".pdf" ) ) );

} );

} );
```

Webページに埋め込むアップロード済みPDFファイルへのアクセスを提供する`/download/:file`ルートを追加します。

>[!NOTE]
>
>実稼働アプリケーションでは、要求が有効なユーザーから送信されていること、およびユーザーに文書へのアクセスが許可されていることを確認するために、認証と承認を追加する必要があります。

```
app.get( "/download/:file", function( req, res ){
    // Note: In production code, this should check authentication and user access permissions
    res.download( __dirname + "/uploads/drafts/" + req.params[ "file" ] );
});
```

読み込み時に入力されるファイルリスト要素でindex.htmlページを更新します。 各アイテムはdraft.html webページにリンクでき、クエリ文字列パラメーターを使用してファイル名をページに渡します。

>[!NOTE]
>
>各アイテムの追加にはjQueryを使用するため、webページにjQueryライブラリを読み込むか、別のメソッドを使用してエレメントを追加する必要があります。

```
  <ul id="filelist">
      <li>Loading documents...</li>
  </ul>

  ...

  <script>
      // Load current files
      fetch( "/files" )
      .then( r => r.json() )
      .then( files => {
          if( files && files.length > 0 ) {
              $( "#filelist" ).empty();
              files.forEach( file => {
                  $( "#filelist" ).append( `<li><a
  href="/draft.html?file=${file}">${file}</a></li>` );
              })
          } else {
                  $("#filelist").append("<div>No documents found.</div>");
                }
      });
  </script>
```

![レビュー用のファイルを選択したスクリーンショット](assets/reviews_2.png)

## PDFの埋め込み

これで、webアプリケーション内にPDFファイルを埋め込んで表示する準備ができました。

「draft.html」という名前のwebページを作成し、そのページに埋め込みPDF用のdivエレメントを追加します。

```
  <div id="adobe-dc-view"></div>
```

[!DNL Acrobat Services]ライブラリを含める：

```
  <script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
```

カスタムスクリプトタグ内で、クエリ文字列パラメーターからファイル名を解析して、ページに埋め込むファイルを特定します。

```
  <script type="text/javascript">
          let params = new URLSearchParams( window.location.search );
          let filename = params.get( "file" );
  </script>
```

指定したPDFファイルをdivエレメント内の埋め込みビューに読み込むadobe_dc_view_sdk.readyイベントのドキュメントイベントリスナーを追加します。 PDF Embed APIの資格情報からクライアントIDを使用します。 コメントと注釈を有効にして、ビューをFULL_WINDOWモードに埋め込み、showAnnotationsToolsオプションをtrueに設定します。

```
  document.addEventListener( "adobe_dc_view_sdk.ready", () => { 
      var adobeDCView = new AdobeDC.View( { 
          clientId: "YOUR CLIENT ID HERE",
          divId: "adobe-dc-view",
          locale: "en-US",
      } );
      adobeDCView.previewFile( {
          content: { location: { url: "download/" + filename } },
          metaData: { fileName: "Draft Version.pdf" }
      }, {
          embedMode: "FULL_WINDOW",
          showAnnotationTools: true,
          showPageControls: true
      } );
  });
```

## ユーザープロファイルの作成

デフォルトでは、コメントと注釈は、このビューで「ゲスト」として表示されます。 PDFビューにページコードでユーザープロファイルのコールバックを登録することで、コメントとアノテーションの現在のレビュー担当者の名前を設定できます。 プロファイルの例を次に示します。 ユーザー認証を含む本格的なアプリケーションでは、ログインしたユーザーセッションのプロファイル情報を、この方法で設定して、レビューワークフロー内の文書の各コメントを識別できます。

```
  adobeDCView.registerCallback(
      AdobeDC.View.Enum.CallbackType.GET_USER_PROFILE_API,
      () => {
          return new Promise( ( resolve, reject ) => {
              resolve({
                  code: AdobeDC.View.Enum.ApiResponseCode.SUCCESS,
                  data: {
                      userProfile: {
                          name: "YOUR NAME",
                          firstName: "FIRST",
                          lastName: "LAST",
                          email: "document.editor@adobe.com"
                      }
                  }
              });
          });
      }
  );
```

このwebページを使用して、アップロードされたドキュメントを表示および注釈を追加する場合、プロファイルによって特定のユーザーとして識別されます。

## 文書のフィードバックを保存中

ユーザーがドキュメントにコメントしたら、[**保存]をクリックします。**&#x200B;既定では、[**保存**]をクリックすると、更新されたPDFファイルがダウンロードされます。 サーバ上の現在のPDFファイルを更新するには、このアクションを変更します。

アップロード/下書きフォルダー内のサーバーファイルを上書きするPDFコードに`/save`エンドポイントを追加します：

```
  // Overwrite the PDF file with latest PDF changes and annotations
  app.post( "/save", ( req, res ) => {
      if( !req.files || Object.keys( req.files ).length === 0 ) {
          return res.status( 400 ).send( "No files were uploaded." );
      }

      let file = req.files.pdf;
      file.mv( __dirname + "/uploads/drafts/" + file.name, ( err ) => {
          if( err ) {
              return res.status( 500 ).send( err );
          }
          res.send( "File uploaded" );
      });
  } );
```

コンテンツを/saveエンドポイントにアップロードするSAVE_APIのPDFビューコールバックを登録します。 必要に応じて、autoSaveFrequencyの値を変更して、アプリケーションが自動的にタイマーに変更を保存し、完了時に現在埋め込まれているファイルに追加のメタデータを含めるようにすることができます。

```
  adobeDCView.registerCallback(
      AdobeDC.View.Enum.CallbackType.SAVE_API,
      ( metaData, content, options ) => {
          return new Promise( ( resolve, reject ) => {
              let formData = new FormData();
              formData.append( "pdf", new Blob( [ content ] ), "drafts/" + filename
  );
              fetch( "/save", {
                  method: "POST",
                  body: formData
              }).then( resp => {
                  resolve({
                      code: AdobeDC.View.Enum.ApiResponseCode.SUCCESS,
                      data: {
                          /* Updated file metadata after successful save operation */
                          metaData: Object.assign( metaData, {} )
                      }
                  });
              });
          });
      },
      {
          autoSaveFrequency: 0,
          enableFocusPolling: false,
          showSaveButton: true
      }
  );
```

下書き文書へのコメントと注釈がサーバーに保存されるようになりました。 [コールバックの詳細](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/howtos_ui.html#callbacks-workflows)を読んで、ワークフローに合わせることができます。 例えば、複数のユーザーが同じドキュメントを同時にレビューおよびコメントする場合、[ステータスコールバック](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/howtos_ui.html#status-callback)を使用してファイルの競合を処理できます。

最後のステップでは、PDFサービスAPIを使用して、編集したすべての文書を1つのPDFファイルに結合します。

## PDFファイルを結合中

PDF結合コードはPDF作成コードに似ていますが、CombineFilesオペレーションを使用し、各ファイルを入力として追加します。

```
  function combineFilesToPDF( files, outputFilename, callback ) {
      // Create an ExecutionContext using credentials and create a new operation
  instance.
      const executionContext = PDFToolsSdk.ExecutionContext.create( credentials ),
          combineFilesOperation = PDFToolsSdk.CombineFiles.Operation.createNew();

      // Set operation inputs from source files.
      files.forEach( file => {
          const input = PDFToolsSdk.FileRef.createFromLocalFile( file );
          combineFilesOperation.addInput( input );
      } );

      // Execute the operation and Save the result to the specified location.
      combineFilesOperation.execute( executionContext )
          .then( result => {
              result.saveAsFile( outputFilename );
              callback( outputFilename );
          } );
 }
```

## 最終PDFのダウンロード

この関数を呼び出す/finalizeというエンドポイントを追加して、`uploads/drafts`フォルダー内のすべてのPDFファイルを`Final.pdf`ファイルに結合してからダウンロードします。

```
  app.get( "/finalize", ( req, res ) => {
      fs.readdir( __dirname + "/uploads/drafts/", ( err, files ) => {
          if( err ) {
              return res.status( 500 ).send( err );
          }
          combineFilesToPDF(
              files.filter( f => f.endsWith( ".pdf" ) ).map( f => __dirname + 
  "/uploads/drafts/" + f ),
              __dirname + "/uploads/Final.pdf", ( file ) => {
              res.download( file );
          } );
      } );
  } );
```

最後に、メインのindex.html webページで、この/finalizeエンドポイントへのリンクを追加します。 このリンクを使用すると、ユーザーは文書ワークフローの結果をダウンロードできます。

```
<a href="/finalize">Download final PDF</a>
```

![最終ドキュメントをダウンロードしたスクリーンショット](assets/reviews_3.png)

## 次の手順

この実践チュートリアルでは、[!DNL Acrobat Services]個のAPIで[ドキュメント共有とレビューワークフロー](https://developer.adobe.com/document-services/use-cases/collaboration/review-and-approval)をWebアプリケーションに統合する方法を示しました。 このアプリケーションを使用すると、リモートワーカーはファイルを共有し、チームメイトと共同作業を行うことができます。これは、特に在宅勤務の従業員や請負業者に役立ちます。

これらのテクニックを使用して、アプリでの共同作業を有効にすることも、GitHubで[PDFサービスノードSDKサンプル](https://github.com/adobe/pdftools-node-sdk-samples)と[PDF埋め込みAPIサンプル](https://github.com/adobe/pdf-embed-api-samples)を確認して、AdobeのAPIを他の方法で使用するためのヒントを得ることもできます。

独自のアプリでドキュメントの共有とレビューを有効にしますか？ [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)開発者アカウントに新規登録してください。 Adobe PDF Embedには無料でアクセスでき、他のAPIの6か月間無料体験版をご利用いただけます。 体験版終了後は、ビジネスの成長に合わせて、文書トランザクションごとに\$0.05で[従量課金制](https://developer.adobe.com/document-services/pricing/main)で利用できます。

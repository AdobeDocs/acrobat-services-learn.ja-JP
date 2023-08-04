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
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1623'
ht-degree: 1%

---

# レビューと承認

![ユースケースの英雄バナー](assets/UseCaseReviewsHero.jpg)

COVID-19の大流行の間、多くの企業でチーム間のリモート・コラボレーションが必要となりました。 [デジタルドキュメントの共有とレビュー](https://www.adobe.io/apis/documentcloud/dcsdk/review-and-approval.html) チームと部門間のリソースに関する一連の課題を提示します。

例えば、異なるファイル形式でのドキュメントの共有、コンテンツの効果的なレビューとコメント、最新の編集内容との同期などの課題があります。 [!DNL Adobe Acrobat Services] APIは、アプリケーション開発者がユーザーに対してこれらの課題を解決できるように設計されています。

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

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF埋め込みAPI](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [プロジェクトコード](https://github.com/contentlab-io/adobe_reviews_and_approvals)

## AdobeAPI資格情報を作成しています

コードを開始する前に、次のことを行う必要があります [資格情報の作成](https://www.adobe.com/go/dcsdks_credentials) Adobe PDF Embed APIおよびAdobe PDF Services API用。 PDF埋め込みAPIは無料で使用できます。 PDFサービスAPIは6か月間無料で使用できます。その後、次のプランに切り替えることができます。 [従量課金制のプラン](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) 文書トランザクションあたり\$0.05です。

PDFサービスAPIの資格情報を作成するときに、 **パーソナライズされたコードサンプルの作成** を選択し、言語としてNode.jsを選択します。 ZIPファイルを保存し、 pdftools-api-credentials.jsonとprivate.keyをNode.js Expressプロジェクトのルートディレクトリに抽出します。

## プロジェクトと依存関係の設定

「public」という名前のフォルダから静的ファイルを提供するように、Node.jsとExpressプロジェクトを設定します。 環境設定に応じて、プロジェクトの方法を設定できます。 すばやく起動して実行するには、 [Express app generator](https://expressjs.com/en/starter/generator.html). または、シンプルな構成にする場合は、 [ゼロから作成](https://expressjs.com/en/starter/hello-world.html) コードを1つのJavaScriptファイルに保存します。 上記のリンクのサンプルプロジェクトでは、1ファイルによるアプローチを使用し、コード全体をindex.jsに保存しています。

をコピー `pdftools-api-credentials.json` および `private.key` パーソナライズされたコードサンプルからプロジェクトのルートディレクトリまでのファイル。 また、資格情報ファイルが誤ってリポジトリに送信されないように、.gitignoreファイルがある場合は、追加します。

次に実行 `npm install @adobe/documentservices-pdftools-node-sdk` PDFサービス用のNode.js SDKをインストールします。 このモジュールを読み込み、残りの依存関係の読み込み後、コード内（サンプルプロジェクトのindex.js）にAPI資格情報オブジェクトを作成します。以下に例を示します。

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

これで、を使用する準備ができました [!DNL Acrobat Services] API。

## ファイルのPDFへの変換

文書ワークフローの前半では、エンドユーザーが共有する文書をアップロードする必要があります。 これを可能にするには、アップロード機能を追加し、異なる文書ファイルフォーマットをPDFに統合して、レビュープロセスに備えます。

PDFまず、 [PDFサービスAPIのスニペット例](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-tools.html). この例では、光学式文字認識(OCR)、パスワード保護と削除、圧縮など、その他の重要な機能のスニペットも示しています。

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

まず、アップロードフォルダー内にフォルダーを作成し、「drafts」という名前を付けます。 アップロードしたファイルと変換したPDFファイルはここに保存します。 次に実行 `npm install express-fileupload` express-FileUploadモジュールをインストールし、コード内でミドルウェアをExpressに追加するには：

```
const fileUpload = require( "express-fileupload" );
app.use( fileUpload() );
```

次に `/upload `アップロードしたファイルを保存し、同じファイル名で「下書き」フォルダーに保存します。 次に、先ほど記述した関数を呼び出して、同じ文書のPDFファイルを作成します(まだPDF書式になっていない場合)。 アップロードされた元の文書の名前に基づいて、新しいPDFファイルのファイル名を作成できます。

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

Webアプリケーションからファイルをアップロードするには、 `index.html` アップロードフォルダー内のwebページ。 ページで、 /uploadエンドポイントにファイルを送信するファイルアップロードフォームを追加します。

```
<form ref="uploadForm" 
      action="/upload"
      method="post" 
      encType="multipart/form-data">
      <input type="file" name="myFile" accept=".doc,.docx,.ppt,.pptx,.xls,.xlsx,.txt,.rtf,.bmp,.jpg,.gif,.tiff,.png">
      <input type="submit" value="Upload File" />
  </form>
```

![Webページの「ファイルをアップロード」機能のスクリーンショット](assets/reviews_1.png)

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

追加 `/download/:file` アップロードされたPDFファイルにアクセスしてwebページに埋め込むルート。

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

![レビューするファイルを選択したスクリーンショット](assets/reviews_2.png)

## PDFの埋め込み

これで、webアプリケーション内にPDFファイルを埋め込んで表示する準備ができました。

「draft.html」という名前のwebページを作成し、そのページに埋め込みPDF用のdivエレメントを追加します。

```
  <div id="adobe-dc-view"></div>
```

を含む [!DNL Acrobat Services] ライブラリ：

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

ユーザーが文書にコメントしたら、 **をクリックします。** デフォルトでは、 **保存** 更新されたPDFファイルをダウンロードします。 サーバ上の現在のPDFファイルを更新するには、このアクションを変更します。

追加 `/save` アップロード/下書きフォルダー内のPDFファイルを上書きするサーバーコードのエンドポイント：

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

下書き文書へのコメントと注釈がサーバーに保存されるようになりました。 次のことができます [コールバックの方法の詳細](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/howtos_ui.html#callbacks-workflows) ワークフローに合わせる 例えば [ステータスコールバック](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/howtos_ui.html#status-callback) 複数のユーザーが同じドキュメントを同時にレビューおよびコメントする場合のファイルの競合の処理に役立ちます。

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

PDF /finalizeという名前のエンドポイントを追加します。このエンドポイントは、 `uploads/drafts` フォルダーを `Final.pdf` ファイルをダウンロードします。

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

この実践チュートリアルで示したのは、次の方法です [!DNL Acrobat Services] APIは [ドキュメント共有とレビューワークフロー](https://www.adobe.io/apis/documentcloud/dcsdk/review-and-approval.html) をWebアプリケーションに追加します。 このアプリケーションを使用すると、リモートワーカーはファイルを共有し、チームメイトと共同作業を行うことができます。これは、特に在宅勤務の従業員や請負業者に役立ちます。

これらの方法を使用して、アプリでの共同作業や検索を可能にすることができます [PDFサービスノードSDKサンプル](https://github.com/adobe/pdftools-node-sdk-samples) および [PDF埋め込みAPIサンプル](https://github.com/adobe/pdf-embed-api-samples) AdobeのAPIの他の使用方法に関するインスピレーションを得るには、GitHubをご覧ください。

独自のアプリでドキュメントの共有とレビューを有効にしますか？ 新規登録 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 開発者アカウント。 Adobe PDF Embedには無料でアクセスでき、他のAPIの6か月間無料体験版をご利用いただけます。 体験版の終了後は、 [従量課金制](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) ビジネスの成長に応じて、文書トランザクションあたり\$0.05です。

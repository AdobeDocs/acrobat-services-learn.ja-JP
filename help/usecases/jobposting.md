---
title: 求人情報
description: 応募者と雇用者にとってスムーズで一貫性のある web エクスペリエンスを構築する方法を学びます
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8092.jpg
kt: 8092
exl-id: 0e24c8fd-7fda-452c-96f9-1e7ab1e06922
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1527'
ht-degree: 0%

---

# 求人情報

![ユースケースのヒーローバナー](assets/UseCaseJobHero.jpg)

複数のユーザーで web サイトを運用する場合は、すべてのユーザーにスムーズなエクスペリエンスを提供できるようにデザインすることが重要です。

次のシナリオを想像してください。雇用者が～できるウェブサイトを持っている [求人情報のアップロード](https://www.adobe.io/apis/documentcloud/dcsdk/job-posting.html)を選択します。 求職者にとっては、一貫した形式で投稿に関連するすべての文書を簡単に表示できるのが便利です。 しかし、雇用者にとっては、どのようなファイル形式であっても情報を添付するのが便利です。 両方のタイプのユーザーに利便性を提供するために、アップロードされたすべての文書を自動的にPDFに変換し、投稿にインラインで埋め込むことができます。

## 学習内容

この実践チュートリアルでは、 [!DNL Adobe Acrobat Services] およびその [Node.js SDK](https://www.npmjs.com/package/@adobe/documentservices-pdftools-node-sdk) 」をクリックして、これらの機能を求人掲載サイトに追加します。 これにより、より使いやすく、雇用者と求職者の両方にとって魅力的なウェブサイトが作成されます。 ここに [complete](https://github.com/contentlab-io/adobe_job_posting) [プロジェクトコード](https://github.com/contentlab-io/adobe_job_posting)を使用します。

まず、簡単な Express ベースの Node.js Web アプリケーションを設定します。 [Express](https://expressjs.com/) は、ルーティングやテンプレート化などの機能を提供する、シンプルな web アプリケーションフレームワークです。 アプリケーションのコードは、 [GitHub](https://github.com/contentlab-io/adobe_job_posting)を選択します。 また、 [PostgreSQL データベース](https://www.postgresql.org/) を選択し、設定を行ってPDFを

## 関連 [!DNL Acrobat Services] API

* [PDF埋め込み API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [PDFサービス API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

## AdobeAPI 資格情報の作成

まず、次の操作を実行する必要があります [資格情報の作成](https://www.adobe.com/go/dcsdks_credentials) Adobe PDF Embed API（無料で使用）およびAdobe PDF Services API（6 ヶ月間無料）の場合 [従量制の](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) （ドキュメントトランザクションあたり\$0.05 のみ）。 PDFサービス API の資格情報を作成するときは、「パーソナライズされたコードサンプルを作成」オプションを選択します。 ZIP ファイルを保存し、pdftools-api-credentials.json と private.key を Node.js Express プロジェクトのルートディレクトリに抽出します。

また、自由に利用できる Embed API の API キーも必要です。 差出人 [プロジェクト](https://console.adobe.io/projects)で、作成したプロジェクトに移動します。 次に、「 **プロジェクトに追加** を選択し、 **API**&#x200B;を選択します。 最後に、「 **PDF埋め込み API**&#x200B;を選択します。

ドメイン埋め込み API のPDFを指定します。 API キーは公開されている必要があります（ブラウザーによって実行されるコードで検索します）。 ドメインを指定すると、別のドメインの他のユーザーが API キーを使用できなくなります。

「localhost」をドメインとして使用することはできません。 「testing.local」などのドメインを指定し、コンピュータ上の hosts ファイルを編集して、そのドメインをコンピュータである 127.0.0.1 にリダイレクトします。 次に、アプリケーションを localhost:3000 でテストする代わりに、testing.local:3000 でテストできます。 完了したら、プロジェクトページでPDF埋め込み API の API キーを見つけます。

## アップロードフォームとハンドラーの追加

Express アプリケーションと API 資格情報が機能している場合は、ユーザーが自分のドキュメントを Web サイトにアップロードするためのフォームも必要です。 この目的のために index.jade テンプレートを編集します。

アップロードされた求人情報の名前と、詳細情報を含むドキュメントの入力フィールドを作成します。

テンプレートのコンテンツブロック内に、次のフォームを追加します。

```
extends layout

block content
  h1= title

  form(action="/upload", enctype="multipart/form-data", method="POST")
    label Job posting name:&nbsp;
    input(type="text", name="name", required="required")
    br
    br
    label Describing document:&nbsp;
    input(type="file", name="attachment", required="required")
    br
    br
    input(type="submit", value="Submit job posting")
```

次に、/upload アクションにPOST要求のハンドラーを追加します。 次に、 routes/index.jsファイルに/upload のルートを追加します。 このルート用に新しいファイルを作成できますが、新しいファイルを反映するには app.js ファイルを更新する必要があります。 このルートハンドラの内部では、与えられた名前とアップロードされたファイルにアクセスできます。

```
router.post('/upload', async function (req, res, next) {
    const name = req.body.name;
    const fileContents = req.files.attachment.data;

    // code to work with the uploaded document
  });
```

この関数は非同期なので、関数で await キーワードを使用できます。これは、API 呼び出しを実行するメソッドを呼び出す場合に便利です。

![求人情報サイトのスクリーンショット](assets/jobs_1.png)

## PDFサービス API の使用

PDFサービス API を使用する前に、ルートファイルの先頭に次のインポートを追加する必要があります。

```
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk');
  const { Readable } = require('stream');
```

読み込みの直下で、API 資格情報を読み込み、 [実行内容](https://www.javascripttutorial.net/javascript-execution-context/)を選択します。 実行コンテキストは別の操作に再利用できるため、1 回だけ実行することをお勧めします。

```
  const credentials = PDFToolsSdk.Credentials
  .serviceAccountCredentialsBuilder()
  .fromFile("pdftools-api-credentials.json")
  .build();

  const executionContext = PDFToolsSdk.ExecutionContext.create(credentials);
```

次に、 `router.post` ブロックします。 まず、ドキュメントをPDFに

```
  const createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();

  const input = PDFToolsSdk.FileRef.createFromStream(Readable.from(fileContents),
  req.files.attachment.mimetype);

  createPdfOperation.setInput(input);

  let result = await createPdfOperation.execute(executionContext);

  result.saveAsFile('output-pdf' + new Date().getTime() + '.pdf');
  return res.send('success!');
```

ほとんどの操作は、同じ 4 つの手順を実行します。 まず、適切なクラスの createNew メソッドを使用して、操作の型を初期化します。 次に、操作の入力である FileRef を作成します。 操作の結果も FileRef になるため、以降の操作ではこの手順を省略できます。 この初期操作では、アップロードされたファイルのバイトから FileRef を作成します。 3 番目に、入力を操作に割り当てる必要があります。 最後に、実行コンテキストをパラメータとして実行メソッド内で実行します。 このメソッドは Promise を返すので、結果を待つことができます。

このコードは、返されたPDFをファイルに保存し、単純な「success」応答をブラウザーに送信します。 ファイル名の「日付」部分は、一意のファイル名であることを保証します。 出力先ファイルが存在する場合、saveAsFile はエラーを返します。

## 画像のテキストへの変換とPDF

次に、光学文字認識 (OCR) を使用して画像をテキストに変換し、結果を圧縮します。 この操作には、CreatePDF 操作と同様に、OCR および CompressPDF 操作を使用します。 ルートファイルに `router.post`:

```
  const name = req.body.name;
  const fileContents = req.files.attachment.data;

  const createPdfOperation = PDFToolsSdk.CreatePDF.Operation.createNew();
  const input = PDFToolsSdk.FileRef.createFromStream(Readable.from(fileContents),
  req.files.attachment.mimetype);
  createPdfOperation.setInput(input);

  let result = await createPdfOperation.execute(executionContext);

  const ocrOperation = PDFToolsSdk.OCR.Operation.createNew();
  ocrOperation.setInput(result);
  result = await ocrOperation.execute(executionContext);

  const compressPdfOperation = PDFToolsSdk.CompressPDF.Operation.createNew();
  compressPdfOperation.setInput(result);
  result = await compressPdfOperation.execute(executionContext);

  result.saveAsFile('output-pdf' + new Date().getTime() + '.pdf');
  return res.send('success!');
```

この操作は 1 回だけ実行する必要があります。これは、コードが setInput に渡すことができる FileRef が結果になるためです。

ファイルをハードディスクに保存して、簡略化しすぎた HTTP レスポンスを返すよりも良い方法があります。 代わりに、データベースにPDFを保存し、Adobeの無料のPDF埋め込み API を使用してPDFを埋め込む Web ページを表示します。 こうすることで、求職者が会社のロゴやその他のデザイン要素を含む求人情報を見つけて閲覧できる web サイトに、雇用主の求人情報やパンフレットが表示されます。

## データベースへのPDFの保存

PostgreSQL データベースにPDFを格納します。 Node.js で Postgres に接続するための node-postgres パッケージを取得します。 ある時点では、PDFの内容をバッファーに格納する必要があり、FileRef はストリームでのみ機能するため、stream-buffers パッケージをインストールします。 したがって、stream-buffers パッケージを使用して内容をバッファに書き込みます。

```
npm install pg stream-buffers
```

次に、ジョブをポストするためのデータベーステーブルを作成します。 一意の識別子の列、名前の列、および関連付けられたPDFの列が必要です。 データベーステーブルは、Postgres コマンドラインインターフェイス (CLI) から作成できます。

```
CREATE TABLE job_postings (id TEXT PRIMARY KEY, name TEXT NOT NULL, attachment
BYTEA NOT NULL);
```

Node.js ファイルに戻ります。 ファイルの先頭に読み込みを追加します。

```
  const { Client } = require('pg');
  const streamBuffers = require('stream-buffers');
```

データベーステーブルにPDFを格納するには、アップロード関数を変更します。 最後の 2 行（saveAsFile と send）を次のコードスニペットで置き換えます。

```
  const pgClient = new Client();
  pgClient.connect();

  const id = Math.random().toString(36).substr(2, 6); // not securely random at all,
  but serves the purpose for this demo

  const writableStream = new streamBuffers.WritableStreamBuffer();
  writableStream.on("finish", async () => {    
    await pgClient.query("INSERT INTO job_postings VALUES ($1, $2, $3)", [
      id,
      name,
      writableStream.getContents()
    ]);
    res.redirect(`/job/${id}`);
  })
  result.writeToStream(writableStream);
```

コンテンツを書き込むには、WritableStreamBuffer を作成します。 finish イベントで、SQL クエリを実行します。 node-postgres パッケージは自動的に Buffer パラメータを BYTEA フォーマットに変換します。 クエリは、後で作成されるエンドポイントである/job/{id} にユーザーをリダイレクトします。

PDF埋め込み API の場合は、エンドポイントのコンテンツのみを返すPDFも必要です。

```
  router.get('/pdf/:id', async function (req, res, next) {
    const id = req.params.id;
 
    const pgClient = new Client();
    pgClient.connect();

  const pgResult = await pgClient.query("SELECT attachment FROM job_postings WHERE id
  = $1", [id]);
  const buffer = pgResult.rows[0].attachment;
  res.type('pdf');
    return res.send(buffer);
  });
```

## PDF

次に、/job/{id} エンドポイントを作成します。このエンドポイントは、要求された求人情報の名前と埋め込みテンプレートを含むPDFを生成します。

```
router.get('/job/:id', async function(req, res, next) {
    const id = req.params.id;

    const pgClient = new Client();
    pgClient.connect();

    const pgResult = await pgClient.query("SELECT name FROM job_postings WHERE id =
  $1", [id]);
    const name = pgResult.rows[0].name;

    res.render('job', { pdf_url: `/pdf/${id}`, name });
  });
```

views/ディレクトリで、次の内容の job.jade ファイルを作成します。

```
  extends layout

  block content
    h1= name
    div(id='adobe-dc-view')
    script(src='https://documentcloud.adobe.com/view-sdk/main.js')
    script.
      window.embedUrl = "!{pdf_url}";
    script(src='/javascripts/embed-pdf.js')
```

最初のスクリプトは、Adobeの View SDK です。これにより、PDFを簡単に埋め込むことができます。 2 番目のスクリプトは、window.embedUrl の値を、Express ルートハンドラによって提供されるPDFの URL に設定するインラインの 1 行記述です。 次のように、3 番目のスクリプトを自分で作成します。

```
  document.addEventListener("adobe_dc_view_sdk.ready", function () {
    var adobeDCView = new AdobeDC.View({ clientId: "YOUR API KEY HERE", divId:
   "adobe-dc-view" });
    adobeDCView.previewFile({
      content: { location: { url: '//' + window.location.host + window.embedUrl }
         },
      metaData: { fileName: "Job posting" }
    });
  });
```

これで、ドキュメントのアップロード、/job/id ページへのリダイレクト、埋め込みPDFの表示のプロセス全体をテストできます。 ユーザーは、同じ手順で求人情報やその他のドキュメントを Web サイトに追加します。

![アップロードされたテストドキュメントのPDF画面](assets/jobs_2.png)

インライン埋め込みの動作を確認するには、こちらを参照してください [ライブデモ](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/IN_LINE/Bodea%20Brochure.pdf)を選択します。

## 次の手順

この実践チュートリアルでは、Node.js を [!DNL Acrobat Services] アップロードした [求人情報](https://www.adobe.io/apis/documentcloud/dcsdk/job-posting.html) 様々な形式でPDFに 作成されたPDFは、Web ページに埋め込まれました。 web サイトに同じ機能を追加すれば、求職者が見つけられる求人情報やパンフレットなどを簡単にアップロードできます。 これらの機能は、誰もが自分の夢の仕事を見つけるために必要な情報を取得するのに役立ちます。

[!DNL Acrobat Services] web サイトまたはアプリケーションに主要なドキュメント処理関数を追加する際に役立ちます。 これらの API でできることについて詳しく知りたい場合は、次のクイックスタートドキュメントを参照してください。

* [PDF埋め込み API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [PDFサービス API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

Web サイトに使いやすいドキュメント処理機能を追加するには、 [無料体験版に新規登録](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)を選択します。 Adobe PDF Embed API は常に無料で使用でき、Adobe PDF Services API は 6 ヶ月間無料です。ドキュメントトランザクションあたり 0.05 USD で、次の処理を [従量制の](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) ビジネスの成長に合わせて

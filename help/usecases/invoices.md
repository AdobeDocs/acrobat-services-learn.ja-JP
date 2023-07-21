---
title: 請求書の処理
description: 顧客の請求書を自動的に生成し、パスワードで保護し、配送する方法を説明します
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8145.jpg
jira: KT-8145
exl-id: 5871ef8d-be9c-459f-9660-e2c9230a6ceb
source-git-commit: 2d1151c17dfcfa67aca05411976f4ef17adf421b
workflow-type: tm+mt
source-wordcount: '1427'
ht-degree: 1%

---

# 請求書の処理

![ユースケースのヒーローバナー](assets/UseCaseInvoicesHero.jpg)

ビジネスが活況を呈している場合は優れていますが、請求書をすべて準備する場合は生産性が低下します。 請求書の手動生成には時間がかかり、エラーが発生して損失が発生したり、間違った金額で顧客を怒らせたりする可能性があります。

例えば、Danielle は [経理部](https://www.adobe.io/apis/documentcloud/dcsdk/invoices.html) [医療供給会社の](https://www.adobe.io/apis/documentcloud/dcsdk/invoices.html)を選択します。 月末の取引なので、複数の異なるシステムから情報を取得し、その正確性を再確認して、請求書の書式を設定しています。 作業がすべて終わったら、文書をPDFに変換し（特定のソフトウェアを購入しなくても誰でも表示できるように）、個々の顧客に個別の請求書を送付する準備が整いました。

毎月の請求が完了しても、ダニエルはそれらの請求書を逃れることはできません。 一部のお客様は毎月ではない請求サイクルを使用しているため、お客様は常に誰かの請求書を作成しています。 顧客が請求書を編集して過小支払いを行う場合があります。 Danielle は、この請求書の不一致のトラブルシューティングに時間を費やします。 この調子だと仕事に追いつくためにアシスタントを雇う必要があるんです！

Danielle が必要としているのは、月の終わりにバッチ処理で請求書を迅速かつ正確に生成する方法と、その他の場合は場当たり的な方法の両方です。 これらの請求書を編集から保護できれば、金額の不一致に関するトラブルシューティングを行う必要がなくなります。

## 学習内容

この実践チュートリアルでは、Password Document Generation API を使用して請求書を自動生成し、PDFをAdobeで保護し、各顧客に請求書を提供する方法を学習します。 必要なのは、Node.js、JavaScript、Express.js、HTML、CSS に関する知識だけです。

このプロジェクトの完全なコードは [github で利用可能](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-invoice-generation)を選択します。 テンプレートと raw データフォルダーを含むパブリックディレクトリを設定する必要があります。 本番環境では、External API からデータをフェッチする必要があります。 また、テンプレートリソースが含まれているアーカイブ版のアプリケーションを確認することもできます。

## 関連する API とリソース

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Adobeドキュメント生成 API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [プロジェクトコード](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-invoice-generation)

## データの準備

このチュートリアルでは、データウェアハウスからデータがどのように読み込まれるかについては説明しません。 顧客の注文は、データベース、External API、またはカスタムソフトウェアに存在する場合があります。 Adobeドキュメント生成 API では、顧客関係管理 (CRM) プラットフォームや e コマースプラットフォームからの情報など、請求データを含む JSON ドキュメントが必要です。 このチュートリアルでは、データが既に JSON 形式であることを前提としています。

簡単にするために、請求には次の JSON 構造を使用します。

```
{ 
    "customerName": "John Doe", 
    "customerEmail": "john-doe@example.com", 
    "order": [ 
        { 
            "productId": 26, 
            "productTitle": "Bandages", 
            "price": 15.82 
        }, 
        { 
            "productId": 54, 
            "productTitle": "Masks", 
            "price": 25 
        }, 
        { 
            "productId": 76, 
            "productTitle": "Gloves", 
            "price": 7.59 
        } 
    ] 
} 
```

JSON 文書には、顧客の詳細と注文情報が含まれています。 この構造化文書を使用して請求書を作成し、エレメントをPDF形式で表示

## 請求書テンプレートの作成

Adobeドキュメント生成 API では、動的テンプレートまたは Word ドキュメントを作成するために、Microsoft Word ベースのPDFと JSON ドキュメントが必要です。 請求アプリケーション用のMicrosoft Word テンプレートを作成し、 [無料ドキュメント生成タグアドイン](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo) を使用して、テンプレートタグを生成します。 アドインをインストールし、Microsoft Word でタブを開きます。

![ドキュメント生成タグアドインのスクリーンショット](assets/invoices_1.png)

上記のように JSON コンテンツをアドインにペーストしたら、「タグを生成」をクリックします。 このプラグインは、オブジェクトの形式を表示します。 基本的なテンプレートでは、顧客の名前と電子メールを使用できますが、注文情報は表示されません。 注文情報については、このチュートリアルの後半で説明します。

![文書生成タグ作成者テンプレートのスクリーンショット](assets/invoices_2.png)

Microsoft Word 文書内で、請求書テンプレートを作成します。 動的データを挿入する位置にカーソルを置き、タグアドインウィンドウからAdobeを選択します。 クリック **テキストを挿入** そのため、Adobe文書生成タグアドインでは、タグを生成および挿入できます。 パーソナライゼーションのために、お客様の名前とメールアドレスを挿入しましょう。

次に、新しい請求書が発行されるたびに変更されるデータに移動します。 ツールバーの「 **詳細** タブに表示されます。 顧客が注文した製品に基づいて動的テーブルを生成するために使用できるオプションを表示するには、「 **表とリスト** を選択します。

選択 **注文** を選択します。 2 番目のドロップダウンで、このテーブルの列を選択します。 このチュートリアルでは、表をレンダリングするオブジェクトの 3 つの列をすべて選択します。

![「ドキュメント生成タグの詳細」タブのスクリーンショット](assets/invoices_3.png)

ドキュメント生成 API は、配列内の要素の集約などの複雑な操作を実行することもできます。 」を **詳細** 」タブで、「 **数値計算**&#x200B;および **集約** 」タブで、計算を適用するフィールドを選択します。

![文書生成タグの数値計算のスクリーンショット](assets/invoices_4.png)

ツールバーの「 **計算の挿入** ボタンを使用して、このタグを文書内の必要な場所に挿入します。 次のテキストがMicrosoft Word ファイルに表示されます。

![Microsoft Word 文書のタグのスクリーンショット](assets/invoices_5.png)

この請求書サンプルには、顧客情報、注文した製品、および合計請求金額が含まれています。

## Adobe文書生成 API を使用した請求書の生成

Adobe PDF Services Node.js ソフトウェア開発キット (SDK) を使用して、Microsoft Word 文書と JSON 文書を結合します。 Node.js アプリケーションを構築し、文書生成 API を使用して請求書を作成します。

PDFサービス API にはドキュメント生成サービスが含まれているため、両方に同じ資格情報を使用できます。 楽しむ [6 ヶ月間無料](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)の場合、ドキュメントトランザクションあたり 0.05 USD のみをお支払いいただきます。

次のコードを使用してPDFを

```
async function compileDocFile(json, inputFile, outputPdf) { 
    try { 
        // configurations 
        const credentials =  adobe.Credentials 
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

このコードは、入力 JSON ドキュメントと入力テンプレートファイルから情報を取得します。 次に、文書の結合操作を作成して、ファイルを 1 つの結合レポートにPDFします。 最後に、API 資格情報を使用して操作を実行します。 まだお持ちでない場合は、 [資格情報の作成](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#getting-credentials) ( ドキュメント生成とPDFサービス API では同じ資格情報が使用されます )。

ドキュメント要求を処理するには、Express ルータ内で次のコードを使用します。

```
// Create one report and send it back
try {
    console.log(\`[INFO] generating the report...\`);
    const fileContent = fs.readFileSync(\`./public/documents/raw/\${vendor}\`,
    'utf-8');
    const parsedObject = JSON.parse(fileContent);

    await pdf.compileDocFile(parsedObject,
    \`./public/documents/template/Adobe-Invoice-Sample.docx\`,
    \`./public/documents/processed/output.pdf\`);

    await pdf.applyPassword("p@55w0rd", './public/documents/processed/output.pdf',
    './public/documents/processed/output-secured.pdf');

    console.log(\`[INFO] sending the report...\`);
    res.status(200).render("preview", { page: 'invoice', filename: 'output.pdf' });
} catch(error) {
    console.log(\`[ERROR] \${JSON.stringify(error)}\`);
    res.status(500).render("crash", { error: error });
}
```

このコードを実行すると、提供されたデータに基づいて動的に生成された請求書を含むPDFドキュメントが提供されます。 サンプルの JSON データ（上記で提供）の場合、このコードの出力は次のとおりです。

![動的に生成されたPDF請求書](assets/invoices_6.png)

この請求書には、JSON ドキュメントからの動的データが含まれています。

## 請求書のパスワード保護

会計士のダニエルは顧客が請求書を変更することを心配しているので、パスワードを適用して編集を制限します。 [PDFサービス API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html) では、文書にパスワードを自動的に適用できます。 ここでは、Adobe PDF Services SDK を使用して、文書をパスワードで保護します。 コードは次のとおりです。

```
async function applyPassword(password, inputFile, outputFile) {
    try {
        // Initial setup, create credentials instance.
        const credentials = adobe.Credentials
        .serviceAccountCredentialsBuilder()
        .fromFile("./src/pdftools-api-credentials.json")
        .build();

        // Create an ExecutionContext using credentials
        const executionContext = adobe.ExecutionContext.create(credentials);
        // Create new permissions instance and add the required permissions
        const protectPDF = adobe.ProtectPDF,
        protectPDFOptions = protectPDF.options;
        // Build ProtectPDF options by setting an Owner/Permissions Password, Permissions,
        // Encryption Algorithm (used for encrypting the PDF file) and specifying the type of content to encrypt.
        const options = new protectPDFOptions.PasswordProtectOptions.Builder()
        .setOwnerPassword(password)
        .setEncryptionAlgorithm(protectPDFOptions.EncryptionAlgorithm.AES_256)
        .build();

        // Create a new operation instance.
        const protectPDFOperation = protectPDF.Operation.createNew(options);

        // Set operation input from a source file.
        const input = adobe.FileRef.createFromLocalFile(inputFile);
        protectPDFOperation.setInput(input);

        // Execute the operation and Save the result to the specified location.
        let result = await protectPDFOperation.execute(executionContext);

        result.saveAsFile(outputFile);
    } catch (err) {
        console.log('Exception encountered while executing operation', err);
    }
}
```

このコードを使用すると、文書がパスワードで保護され、新しい請求書がシステムにアップロードされます。 このコードの使用方法の詳細、または実際に試すには、 [コードサンプル](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-invoice-generation)を選択します。

請求書の作成が完了したら、自動的にクライアントに電子メールで送信できます。 自動的にあなたの顧客に電子メールを送ることを達成するいくつかの方法がある。 最も簡単な方法は、サードパーティの電子メール API とヘルプライブラリを使用することです。 [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs)を選択します。 また、既に SMTP サーバーにアクセスできる場合は、 [nodemailer](https://www.npmjs.com/package/nodemailer) SMTP 経由でメールを送信します。

## 次の手順

この実践チュートリアルでは、Danielle が [請求](https://www.adobe.io/apis/documentcloud/dcsdk/invoices.html)を選択します。 PDFサービス API と Document Generation SDK を使用して、JSON ドキュメントからの顧客注文情報を含むMicrosoft Word テンプレートを入力し、PDF請求書を作成しました。 次に、 [PDFサービス API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)を選択します。

Danielle は請求書を自動的に生成でき、顧客が請求書を編集する心配がなくなるため、すべての手作業を支援するアシスタントを雇う必要はありません。 余分な時間を費やして、買掛金ファイルでコスト削減を見つけることができます。

このシンプルなアプリを他のAdobeツールで拡張し、web サイトに請求書を埋め込むことができます。 例えば、顧客はいつでも請求書や残高を確認できます。 [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html) は自由に使用できます。 人事部や営業部に異動して、契約書の自動化や電子サインの収集をおこなうこともできます。

すべての可能性を探索し、独自の便利なアプリケーションの構築を開始するには、無料の [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) アカウントをご利用ください。 初月無料 [従量制の](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)
ビジネスの拡大に合わせて、文書のトランザクション 1 件につき 0.05 ドルで対応できます。

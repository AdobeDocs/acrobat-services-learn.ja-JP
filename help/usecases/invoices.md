---
title: 請求書の処理
description: 顧客請求書の自動生成、パスワード保護、配信を行う方法を説明します
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8145
thumbnail: KT-8145.jpg
exl-id: 5871ef8d-be9c-459f-9660-e2c9230a6ceb
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1427'
ht-degree: 1%

---

# 請求書の処理

![ユースケースの英雄バナー](assets/UseCaseInvoicesHero.jpg)

ビジネスが活況を呈している場合には素晴らしいことですが、これらすべての請求書を準備する時期になると、生産性が低下します。 請求書の手動生成には時間がかかるだけでなく、エラーが発生して費用が失われたり、誤った金額で顧客を怒らせたりするリスクもあります。

例えば、ダニエルは [経理部](https://www.adobe.io/apis/documentcloud/dcsdk/invoices.html) [医療供給会社の](https://www.adobe.io/apis/documentcloud/dcsdk/invoices.html). 月末になったので、彼女はいくつかの異なるシステムから情報を引き出し、その精度を再確認し、請求書をフォーマットしています。 この作業が完了すると、彼女はついに文書をPDFに変換し（専用のソフトウェアを購入しなくても誰でも文書を閲覧できます）、それぞれのお客様にパーソナライズされた請求書を送信できるようになります。

毎月の請求書が完成しても、Danielleはその請求書を逃すことはできません。 一部のお客様は月額以外の請求サイクルを使用しているため、常に誰かの請求書を作成しています。 場合によっては、お客様が請求書を編集して未払いすることがあります。 その後、Danielleは、この請求書の不一致のトラブルシューティングに時間を費やします。 この調子で彼女は仕事を全部ついていくために助手を雇う必要がある。

Danielleが必要としているのは、月末にバッチで請求書を迅速かつ正確に作成し、それ以外の場合にはアドホックで請求書を作成する方法です。 理想的には、これらの請求書を編集から保護できれば、金額が一致しない場合のトラブルシューティングについて心配する必要はありません。

## 学習内容

この実践チュートリアルでは、Password Document Generation APIを使用して請求書の自動生成、PDFのAdobe保護、各顧客への請求書の配信を行う方法について説明します。 必要なのは、Node.js、JavaScript、Express.js、HTML、CSSについてのわずかな知識だけです。

このプロジェクトの完全なコード： [githubで利用可能](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-invoice-generation). テンプレートとRAWデータフォルダーを使用して、パブリックディレクトリを設定する必要があります。 本番環境では、外部APIからデータを取得する必要があります。 テンプレートリソースを含むアプリケーションのこのアーカイブされたバージョンを検索することもできます。

## 関連APIとリソース

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Adobe文書生成API](https://www.adobe.io/apis/documentcloud/dcsdk/doc-generation.html)

* [Adobe Sign API](https://www.adobe.io/apis/documentcloud/sign.html)

* [プロジェクトコード](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-invoice-generation)

## データの準備

このチュートリアルでは、データがデータ・ウェアハウスからインポートされる方法については説明しません。 顧客の注文は、データベース、外部API、またはカスタムソフトウェアに存在する場合があります。 Customer Document Generation APIでは、Adobe関係管理(CRM)やeコマースプラットフォームからの情報など、請求データが含まれるJSON文書が必要です。 このチュートリアルでは、データがすでにJSON形式であることを前提としています。

簡単にするために、次のJSON構造を請求に使用します。

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

JSON文書には、お客様の詳細と注文情報が含まれています。 この構造化文書を使用して請求書を作成し、エレメントをPDF形式で表示します。

## 請求書テンプレートの作成

Adobe文書生成APIでは、Microsoft Wordベースのテンプレートと、JSONドキュメントを使用して、動的なPDFまたはWordドキュメントを作成する必要があります。 請求アプリケーション用のMicrosoft Wordテンプレートを作成し、 [無料のDocument Generation Taggerアドイン](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo) をクリックして、テンプレートタグを生成します。 アドインをインストールして、Microsoft Wordでタブを開きます。

![Document Generation Taggerアドインのスクリーンショット](assets/invoices_1.png)

上記のようにJSONコンテンツをアドインにペーストしたら、「タグを生成」をクリックします。 これで、このプラグインはオブジェクトの形式を表示します。 基本テンプレートでは、お客様の名前とメールアドレスを使用できますが、注文情報は表示されません。 注文情報については、このチュートリアルの後半で説明します。

![Document Generation Tagger Authorテンプレートのスクリーンショット](assets/invoices_2.png)

Microsoft Word文書の中で、請求書テンプレートの記述を開始します。 動的データを挿入する位置にカーソルを置き、Adobeアドインウィンドウからタグを選択します。 クリック **テキストを挿入** Adobe Document Generation Taggerアドインがタグを生成し、挿入できるようにします。 カスタマイズするには、お客様の名前とメールアドレスを入力します。

次に、新しい請求書ごとに変更されるデータに移動します。 を選択します **詳細** アドインのタブです。 顧客が注文した製品に基づいて動的テーブルを生成するために使用可能なオプションを表示するには、 **テーブルとリスト** .

選択 **順序** 最初のドロップダウンから。 2番目のドロップダウンで、このテーブルの列を選択します。 このチュートリアルでは、表をレンダリングするオブジェクトの3つの列をすべて選択します。

![「Document Generation Tagger Advanced」タブのスクリーンショット](assets/invoices_3.png)

Document Generation APIは、配列内の要素の集約などの複雑な操作も実行できます。 を **詳細** タブ、選択 **数値計算**、および **集約** タブで、計算を適用するフィールドを選択します。

![文書生成のタガーの数値計算のスクリーンショット](assets/invoices_4.png)

「 **計算の挿入** をクリックして、文書内の必要な場所にこのタグを挿入します。 次のテキストがMicrosoft Wordファイルに表示されます。

![Microsoft Word文書のタグのスクリーンショット](assets/invoices_5.png)

この請求書サンプルには、顧客情報、注文済製品、および合計未払金額が含まれています。

## Adobe文書生成APIを使用した請求書の生成

Adobe PDF Services Node.js software development kit(SDK)を使用して、MicrosoftのWord文書とJSON文書を組み合わせます。 Document Generation APIを使用して、Node.jsアプリケーションを構築して請求書を作成します。

PDFサービスAPIにはDocument Generation Serviceが含まれているため、両方に同じ資格情報を使用できます。 楽しむ [6か月間の無料体験](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)を選択した場合は、文書取引あたり0.05ドルをお支払いいただきます。

PDFを結合するコードは次のとおりです。

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

このコードは、入力JSONドキュメントと入力テンプレートファイルから情報を取得します。 次に、文書の結合処理を行い、複数のファイルを1つのPDFレポートにまとめます。 最後に、API資格情報を使用して操作が実行されます。 まだお持ちでない場合は、 [資格情報の作成](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#getting-credentials) (Document GenerationとPDFサービスAPIは同じ資格情報を使用します)。

ドキュメント要求を処理するには、Expressルータ内で次のコードを使用します。

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

このコードが実行されると、提供されたデータに基づいて、動的に生成された請求書を含むPDF文書が提供されます。 サンプルのJSONデータ（上記で提供）を使用すると、このコードの出力は次のようになります。

![動的に生成されるPDF請求書のスクリーンショット](assets/invoices_6.png)

この請求書には、JSON文書の動的データが含まれています。

## 請求書のパスワード保護

Danielleの経理担当者は、顧客が請求書を変更することを心配しているため、パスワードを適用して編集を制限します。 [PDFサービスAPI](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html) では、文書にパスワードを自動的に適用できます。 ここでは、Adobe PDF Services SDKを使用して文書をパスワードで保護します。 コード：

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

このコードを使用すると、文書がパスワードで保護され、新しい請求書がシステムにアップロードされます。 このコードの使用方法または試し方について詳しくは、 [コード例](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-invoice-generation).

請求書の作成が完了したら、自動的にクライアントにメール送信することができます。 自動的にクライアントに電子メールを送信するには、いくつかの方法があります。 最も簡単な方法は、サードパーティの電子メールAPIを [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs). または、既にSMTPサーバーにアクセスできる場合は、 [nodemailer](https://www.npmjs.com/package/nodemailer) smtp経由で電子メールを送信します。

## 次の手順

この実践チュートリアルでは、Danielleが以下を使用して会計を行うのに役立つ簡単なアプリを作成しました。 [請求](https://www.adobe.io/apis/documentcloud/dcsdk/invoices.html). PDFサービスAPIとDocument Generation SDKを使用して、JSONドキュメントから顧客注文情報をMicrosoft Wordテンプレートに入力し、PDFの請求書を作成しました。 次に、次の方法でパスワード保護サービスを使用して、各文書をパスワードで保護します [PDFサービスAPI](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html).

Danielleは請求書を自動的に生成できるので、顧客が請求書を編集する心配はありません。すべての手作業をサポートするアシスタントを雇う必要もありません。 買掛金ファイルのコスト削減に余分な時間をかけることができます。

簡単であることが分かりました。他のAdobeツールを使用して、このシンプルなアプリを拡張し、webサイトに請求書を埋め込むことができます。 例えば、顧客はいつでも請求書や残高を表示できます。 [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html) 自由に使用できます。 さらに、人事部や営業部に移動して、契約書を自動化し、電子サインを収集することもできます。

あらゆる可能性を探求し、独自の便利なアプリケーションを構築し始めるには、無料の [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 今すぐ開始するアカウント その後、6か月間の無料体験版をご利用ください [従量課金制](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)
ビジネスの拡大・縮小に合わせて、文書トランザクションあたり0.05ドルで利用できます。

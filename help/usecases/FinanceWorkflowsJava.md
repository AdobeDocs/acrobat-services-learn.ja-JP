---
title: Javaでの財務文書ワークフローの管理
description: 「[!DNL Adobe Acrobat Services]は、PDFの財務文書のデータを処理および抽出するために必要なすべてのツール、サービス、および機能を提供します」
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-7482
thumbnail: KT-7482.jpg
exl-id: 3bdc2610-d497-4a54-afc0-8b8baa234960
source-git-commit: 558bd677d3b357a98488ada9dda1054bb21b81af
workflow-type: tm+mt
source-wordcount: '1204'
ht-degree: 0%

---

# Javaでの財務文書ワークフローの管理

![使用事例の英雄バナー](assets/UseCaseFinancialHero.jpg)

金融業界では、文書のフォーマット、デザイン、構造を維持するために、PDFファイルを広範囲にわたって使用してデータを交換しています。 この堅牢な形式により、財務アナリストやアドバイザーは、顧客が十分な情報に基づいて意思決定を行えるように支援できます。

ただし、PDFフォーマットは、特に複数のデータソースを組み合わせる場合（金融業界で一般的なユースケース）には、処理と自動化が難しい場合があります。 PDF文書を処理するカスタムソリューションを構築することも選択肢の1つですが、ソフトウェアやインフラストラクチャに多くの時間とコストを費やす必要はありません。 [!DNL Adobe Acrobat Services]は、PDF文書のデータを処理および抽出するために必要なすべてのツール、サービス、および機能を提供します。

## 学習内容

この実践チュートリアルでは、[!DNL Java Spring Boot]個のアプリケーションに[!DNL Adobe Acrobat Services]個のAPIを使用する方法について学習します。 PDF文書からコンテンツを抽出してExcelなどの他のデータ形式に変換し、複数のリソースを結合してPDFを保護するモデルビューコントローラ(MVC)アプリを構築します。 このチュートリアルでは、Adobe [PDF埋め込みAPI](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html)を使用してPDF文書を処理し、Webサイトで表示する方法について説明します。

## 関連APIとリソース

* [PDFサービスAPI](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF埋め込みAPI](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [プロジェクトのサンプル](https://github.com/adobe/pdftools-java-sdk-samples)

## 設定

[!DNL Adobe Acrobat Services]は認証システムを使用してリソースアクセスを制御します。 サービスにアクセスするには、組織またはアプリケーションのAPIキーをAdobeにリクエストする必要があります。 APIキーがある場合は、次のセクションに進みます。 新しいAPIキーを作成するには、[!DNL Acrobat Services]サイトで[Getting Started](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)にアクセスしてください。 キーは無料体験版を使用して作成できます。この無料体験版では、最大6か月間で使用できる1,000件の文書トランザクションを提供します。

このチュートリアルを実行するには、次の2つのAPIキーセットが必要です。

* Adobe PDF Services —PDF文書の処理に使用

* Adobe PDF Embed API

資格情報を作成した後、Resourcesセクション内の[!DNL Spring Boot]アプリケーションにPDFサービスAPI資格情報と秘密キーをコピーします。 [!DNL Adobe Acrobat Services] Webサイトの[MavenとGradleのライブラリと依存関係](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=services)の詳細をご覧ください。 作業を進める前に、必要なすべてのパッケージとライブラリを設定してください。

![PDFサービスAPI資格情報のディレクトリの場所のスクリーンショット](assets/FAWJ_1.png)

ログサービスを構成するには、[Adobeドキュメント](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=services)にアクセスし、[ログ]セクションまでスクロールします。

>[!NOTE]
>
> 運用環境では、秘密鍵をバージョン管理に保存しないでください。 資格情報の不正使用を防ぐために、常に秘密の資格情報コンテナーまたはキーインジェクションサービスを使用してください。

[!DNL Spring Boot] PDFが構成されたので、アプリケーションの処理に進み、お客様向けのレポートを作成できます。

## レポートデータの送信

Adobe PDFサービスAPIを使用するには、まず、指定した資格情報を使用する`ExecutionContext`を設定します。 アプリケーション内に資格情報があるので、ファイルから読み取り、次のようにコンテキストを作成できます。

```
Credentials credentials = Credentials.serviceAccountCredentialsBuilder()
    .fromFile(AUTH_FILE_PATH)
    .build();

ExecutionContext executionContext = ExecutionContext.create(credentials);
```

次に、PDF文書を処理するためのコンテキストを取得します。 実行できるアクションは次のとおりです。

* PDF文書をExcel、Wordまたはグラフィック形式に変換

* PDF文書(HTML、Excel、Wordなど)を作成

* 複数のPDF文書を結合する

* PDF文書をProtectして保護解除する（パスワードが必要）

* PDF文書をネットワーク配信に最適化する

これらのサンプルはすべて、[GitHubサンプル](https://github.com/adobe/pdfservices-java-sdk-samples/tree/master/src/main/java/com/adobe/pdfservices/operation/samples)リポジトリで利用できます。

次に、[!DNL Spring Boot]で、文字列パスまたはファイルがアップロードされているストリームを使用してファイルを取得できます。 実行するすべての操作を初期化し、入力ファイルパスを設定する必要があります。 このチュートリアルでは、[Blackrock](https://www.blackrock.com/us/individual/products/investment-funds)で一般に公開されているPDFレポートを使用します。 独自のレポートなど、他のソースを使用できます。

まず、ファイルからFileRefオブジェクトをキャプチャします。 わかりやすくするために、文字列パスによるファイルに焦点を当てます。 以下では、パスに含まれるファイルをPDFからExcelに変換する手順を説明します。

```
ExecutionContext executionContext = ExecutionContext.create(credentials);
ExportPDFOperation exportOperation = ExportPDFOperation.createNew(ExportPDFTargetFormat.XLSX);

// Create the input source
FileRef inputPdf = FileRef.createFromLocalFile(INPUT_PDF);
exportOperation.setInput(inputPdf);
```

この手順を実行すると、プログラムはPDF上で最初のオペレーションを実行する準備が整います。 次に、操作を実行して、Excelシートに結果を取得します。

```
try {
    FileRef output = exportOperation.execute(executionContext);
    output.saveAs(OUTPUT_EXCEL);
} catch (ServiceApiException e) {
    e.printStackTrace();
}
```

この場合、PDFファイルは1つしか処理されません。 複数のPDFファイルから始めて、それらを1つのファイルに結合することもできます。 財務データのレポートでは、複数のファイルを使用することが一般的です。これは、包括的なレポートを提供するために、複数のソースから資金を処理する必要があるためです。

## レポートの生成

[!DNL Adobe Acrobat Services]は、すぐに使えるExcelドキュメントの処理をサポートしていませんが、コミュニティフレームワークとライブラリを使用してコンテンツを処理することはできます。

たとえば、[Apache POI](https://poi.apache.org/)を使用して、[!DNL Java Spring Boot]アプリでExcel (またはその他のMicrosoftドキュメント)を処理したり、Excelファイルに対して他の手動または自動の操作を行うことができます。

この例では、PDF文書から始めて、3つの資金の純資産額を抽出し、表に示します。 要件と使用可能なデータに基づいて、チャートや表などの他の情報を取得することもできます。 他のソースからデータを取り込むこともできます。

この例では、Excel形式でレポートが生成された後に、Adobe PDF Servicesのオペレーションを使用してレポートをPDFの文書に変換し、保護することができます。

レポートをExcel書式からPDF文書に変換するには、次の操作を実行します。

```
ExecutionContext executionContext = ExecutionContext.create(credentials);
CreatePDFOperation exportOperation = CreatePDFOperation.createNew();

// Create the input source
FileRef inputPdf = FileRef.createFromLocalFile(INPUT_EXCEL);
exportOperation.setInput(inputPdf);

try {
    FileRef output = exportOperation.execute(executionContext);
    output.saveAs(OUTPUT_PDF);
} catch (ServiceApiException e) {
    e.printStackTrace();
}
```

>[!TIP]
>
> 要求が発生するたびにオブジェクトを再作成する必要がないようにするには、Springの依存性注入を使用して`ExecutionContext`オブジェクトを注入します。

このコードは、レポートからExcelフォーマットのPDF文書を生成します。

このPDFをお客様に提供する前に、パスワードで保護することができます。 この保護を処理する別の操作ProtectPDFOperationを作成し、ProtectPDFOptionsを使用して文書にパスワードを追加します。

```
ProtectPDFOptions options = ProtectPDFOptions.passwordProtectOptionsBuilder()
                    .setUserPassword("p@55w0rd")
                    .setEncryptionAlgorithm(EncryptionAlgorithm.AES_256)
                    .build();
ProtectPDFOperation operation = ProtectPDFOperation.createNew(options);
```

次に、入力を指定して操作を実行します。 結果のファイルには、不正アクセスを防止するためのパスワードが必要です。

## レポートの表示

PDFレポートが生成されたので、Adobe PDF Embed APIを使用して、webサイトでレポートを表示できます。 このJavaScript APIを使用すると、Web開発者はWebブラウザー内でPDFドキュメントをネイティブに読み込んでレンダリングできます。

>[!NOTE]
>
> この時点で、2番目の資格情報トークンであるクライアントIDが必要になります。

[!DNL Spring Boot]アプリケーションで、PDFレポートを表示する場所に次のHTMLスニペットを追加します。

```
<div id="pdf-viewer"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
    document.addEventListener("adobe_dc_view_sdk.ready", function()
    {
        var adobeDCView = new AdobeDC.View({ clientId: "<your-client-id-here>", divId: "pdf-viewer" });
        adobeDCView.previewFile(
        {
            content: {
                location: {
                    url: "<your-document.pdf>"
                }
            },
            metaData: {
                fileName: "<document-name.pdf>"
            }
        });
    });
</script>
```

このスクリプトは、PDF文書を読み込み、閲覧者が文書に注釈を付けたり、注釈を付けたりできるようにします。 Firefoxに表示されるこのEmbed APIのビューは次のとおりです。

![FirefoxのPDFドキュメントのスクリーンショット](assets/FAWJ_2.png)

PDF埋め込みAPIには、PDFのプレビューとレポートの注釈付けに必要なすべてのツールが用意されています。

## 次の手順

この実践チュートリアルでは、[[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/)のAPIについて説明し、これらのサービスを使用してPDFデータを処理し、財務上の意思決定に関するレポートを作成する方法について説明しました。 サンプルフレームワークとして[!DNL Java Spring Boot]を使用してAPIをシステムに組み込む方法を示し、PDF文書の迅速な処理がどれほど簡単かを示しました。

[[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/)を検索して、Adobe PDFサービスがお客様のビジネスに何ができるかを確認してください。 SDKで利用できるその他の機能については、[GitHubリポジトリー](https://github.com/adobe/pdftools-java-sdk-samples)でサンプルを確認し、[Application Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html)によってPDF内のPDFをすばやく表示する方法を確認してください。

金融関係の顧客に役立つPDFレポートを作成したり、文書を簡単に組み合わせて操作するには、まず今すぐ無料の[Adobe開発者アカウント](https://www.adobe.io/apis/documentcloud/dcsdk/)にサインアップしてください。

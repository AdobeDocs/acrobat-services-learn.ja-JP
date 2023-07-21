---
title: Java での財務文書ワークフローの管理
description: '"[!DNL Adobe Acrobat Services] PDF財務文書のデータを処理および抽出するために必要なすべてのツール、サービス、および機能を提供します」'
type: Tutorial
role: Developer
level: Intermediate
thumbnail: KT-7482.jpg
jria: KT-7482
exl-id: 3bdc2610-d497-4a54-afc0-8b8baa234960
source-git-commit: 2d1151c17dfcfa67aca05411976f4ef17adf421b
workflow-type: tm+mt
source-wordcount: '1315'
ht-degree: 0%

---

# Java での財務文書ワークフローの管理

![ユースケースのヒーローバナー](assets/UseCaseFinancialHero.jpg)

金融業界では、PDFファイルを広範囲に使用してデータを交換しています。文書の形式、デザイン、構造を維持するのに役立つためです。 この堅牢なフォーマットにより、金融アナリストとアドバイザーは、クライアントが十分な情報に基づいた意思決定を行うのを支援できます。

ただし、PDF形式は、特に複数のデータソースを組み合わせる場合など、処理や自動化が困難になる可能性があります。これは、金融業界の一般的なユースケースです。 PDF文書を処理するカスタムソリューションの構築は選択肢の 1 つですが、ソフトウェアやインフラストラクチャに多大な時間とコストを費やす必要はありません。 [!DNL Adobe Acrobat Services] には、PDFドキュメントのデータを処理および抽出するために必要なすべてのツール、サービス、および機能が用意されています。

## 学習内容

この実践チュートリアルでは、 [!DNL Adobe Acrobat Services] API [!DNL Java Spring Boot] します。 PDF文書からコンテンツを抽出し、Excel などの他のデータ形式に変換し、複数のPDFを結合し、リソースをパスワードで保護するモデルビューコントローラー (MVC) アプリケーションを構築します。 このチュートリアルでは、Adobe [PDF埋め込み API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html)を選択します。

## 関連する API とリソース

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF埋め込み API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [プロジェクトサンプル](https://github.com/adobe/pdftools-java-sdk-samples)

## 設定

[!DNL Adobe Acrobat Services] は、認証システムを使用してリソースアクセスを制御します。 サービスにアクセスするには、組織またはアプリケーションの API キーをAdobeにリクエストする必要があります。 API キーがある場合は、次のセクションに進みます。 新しい API キーを作成するには、 [はじめに](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 」を [!DNL Acrobat Services] サイト。 1,000 件のドキュメントトランザクションを最大 6 か月間使用できる無料体験版を使用してキーを作成できます。

このチュートリアルに沿って作業を進めるには、2 組の API キーが必要です。

* Adobe PDF Services:PDF文書の処理に使用

* Adobe PDF Embed API

資格情報を作成したら、PDFサービス API の資格情報と秘密キーを [!DNL Spring Boot] アプリケーションを呼び出します。 詳しくは、 [Maven および Gradle ライブラリと依存関係](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=services) 」を [!DNL Adobe Acrobat Services] web サイトを開きます。 先に進む前に、必要なパッケージとライブラリをすべて設定してください。

![ディレクトリサービス API 資格情報のPDFの場所のスクリーンショット](assets/FAWJ_1.png)

ロギングサービスを設定するには、 [Adobe文書](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=services) を選択し、「ログ」セクションまでスクロールします。

>[!NOTE]
>
> 実稼働環境では、バージョン管理で秘密鍵を保存しないでください。 資格情報の不正使用を防ぐには、必ずシークレット資格情報コンテナーまたはキー挿入サービスを使用してください。

これで [!DNL Spring Boot] アプリケーションが設定されたら、顧客のPDFの処理とレポートの生成を続行できます。

## レポートデータの送信

Adobe PDF Services API を使用するには、まず `ExecutionContext` 指定した資格情報を使用します。 アプリケーション内に資格情報があるので、ファイルから資格情報を読み取って、次のようにコンテキストを作成できます。

```
Credentials credentials = Credentials.serviceAccountCredentialsBuilder()
    .fromFile(AUTH_FILE_PATH)
    .build();

ExecutionContext executionContext = ExecutionContext.create(credentials);
```

次に、コンテキストを取得してPDF文書を処理 次の操作を実行できます。

* PDFドキュメントを Excel、Word、またはグラフィックに変換する

* PDF文書の作成 (HTML、Excel、Word など )

* 複数のPDF文書の結合

* ProtectしてPDF文書の保護を解除（パスワードが必要）

* ネットワークでのPDFドキュメントの配信を最適化する

これらのサンプルはすべて、 [GitHub サンプル](https://github.com/adobe/pdfservices-java-sdk-samples/tree/master/src/main/java/com/adobe/pdfservices/operation/samples) します。

次の [!DNL Spring Boot]を使用すると、String パスまたはファイルがアップロードされているストリームを使用してファイルを取得できます。 実行するすべての操作を初期化し、入力ファイルパスを設定する必要があります。 このチュートリアルでは、 [ブラックロック](https://www.blackrock.com/us/individual/products/investment-funds)を選択します。 独自のレポートを含め、他のソースを使用できます。

まず、 [FileRef](https://opensource.adobe.com/pdfservices-java-sdk-samples/apidocs/latest/com/adobe/pdfservices/operation/io/FileRef.html) オブジェクトを選択します。 簡単にするために、文字列パスでファイルにフォーカスします。 次に、パス内のファイルを Excel から Excel に変換する操作をPDFします。

```
ExecutionContext executionContext = ExecutionContext.create(credentials);
ExportPDFOperation exportOperation = ExportPDFOperation.createNew(ExportPDFTargetFormat.XLSX);

// Create the input source
FileRef inputPdf = FileRef.createFromLocalFile(INPUT_PDF);
exportOperation.setInput(inputPdf);
```

この手順の後、プログラムはPDFで最初の操作を実行する準備ができました。 次に、操作を実行し、結果を Excel シートに表示します。

```
try {
    FileRef output = exportOperation.execute(executionContext);
    output.saveAs(OUTPUT_EXCEL);
} catch (ServiceApiException e) {
    e.printStackTrace();
}
```

このシナリオでは、1 つのPDFファイルのみ また、複数のメディアファイルをPDFして、それらを 1 つのファイルに結合することもできます。 財務データ・レポートでは、複数のソースからの資金を処理して包括的なレポートを作成する必要があるため、複数のファイルを使用するのが一般的です。

## レポートの生成

[!DNL Adobe Acrobat Services] では、そのままの状態で Excel ドキュメントを処理することはできませんが、コミュニティフレームワークとライブラリを使用してコンテンツを処理することはできます。

例えば、 [Apache POI](https://poi.apache.org/) を使用して、 [!DNL Java Spring Boot] または、Excel ファイルに対して他の手動タスクや自動化タスクを実行することもできます。

この例では、PDF文書から、3 つの資金の正味資産額を抽出し、表に示します。 また、要件や利用可能なデータに基づいて、チャートや表などの他の情報も取得できます。 他のソースからデータを取り込むこともできます。

レポートが生成されたら（この例では Excel 形式）、Adobe PDF Services の操作を使用して、レポートをPDFドキュメントに変換し直して保護できます。

レポートを Excel 形式からPDF文書に変換するには、次の操作を行います。

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
> 要求が来るたびにオブジェクトを再作成する必要がなくなるように、Spring の依存性注入を使用して `ExecutionContext` します。

このコードは、レポートからPDFドキュメントを Excel 形式で生成します。

このPDFを顧客に提供する前に、パスワードで保護できます。 この保護を処理する別の操作を作成します。 [ProtectPDFOperation](https://opensource.adobe.com/pdfservices-java-sdk-samples/apidocs/latest/com/adobe/pdfservices/operation/pdfops/ProtectPDFOperation.html)を選択し、 [ProtectPDFOptions](https://opensource.adobe.com/pdfservices-java-sdk-samples/apidocs/latest/com/adobe/pdfservices/operation/pdfops/options/protectpdf/package-summary.html) 」をクリックして、文書にパスワードを追加します。

```
ProtectPDFOptions options = ProtectPDFOptions.passwordProtectOptionsBuilder()
                    .setUserPassword("p@55w0rd")
                    .setEncryptionAlgorithm(EncryptionAlgorithm.AES_256)
                    .build();
ProtectPDFOperation operation = ProtectPDFOperation.createNew(options);
```

次に、入力を指定して操作を実行します。 不正アクセスを防ぐために、作成されるファイルにはパスワードが設定されている必要があります。

## レポートの表示

PDFレポートが生成されたら、Adobe PDF Embed API を使用して Web サイトにレポートを表示できます。 この JavaScript API を使用すると、Web 開発者は Web ブラウザー内でネイティブにPDFドキュメントを読み込んでレンダリングできます。

>[!NOTE]
>
> この時点で、2 番目の資格情報トークン（クライアント ID）が必要です。

を [!DNL Spring Boot] アプリケーションで、HTMLレポートを表示する次のPDFスニペットを

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

このスクリプトは、PDFドキュメントを読み込み、閲覧者がドキュメントに注釈やコメントを追加できるようにします。 Firefox に示すように、この Embed API は次のように表示されます。

![Firefox のPDF文書のスクリーンショット](assets/FAWJ_2.png)

PDF埋め込み API は、レポートに注釈を付けるだけでなく、PDFのプレビューに必要なすべてのツールを提供します。

## 次の手順

この実践チュートリアルでは、 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/) API を使用して、これらのサービスを使用して財務データを処理し、PDF上の意思決定のためのレポートを生成する方法について説明しました。 API をシステムに統合する方法を示し、 [!DNL Java Spring Boot] フレームワークの例として、PDF・ドキュメントの迅速な処理がいかに容易であるかを示します。

概要 [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/) Adobe PDF Services がお客様のビジネスにどのように役立つのかをご案内します。 SDK で使用できる機能について詳しくは、 [GitHub リポジトリ](https://github.com/adobe/pdftools-java-sdk-samples) サンプルを入手し、方法を調べる [PDF埋め込み API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html) では、アプリケーション内のPDFをすばやく表示できます。

文書を簡単に結合して操作し、金融機関の顧客に役立つPDFレポートを作成するには、まず無料の電子サインアップを行います [Adobe開発者アカウント](https://www.adobe.io/apis/documentcloud/dcsdk/) 今日です。

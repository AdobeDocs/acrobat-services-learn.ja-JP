---
title: 学生・教職員連携
description: ここでは、教職員や生徒がPDFで簡単にリソースを共有できるオンライン学習プラットフォームを構築する方法を説明します
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8091
thumbnail: KT-8091.jpg
exl-id: 570a635c-e539-4afc-a475-ecf576415217
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1485'
ht-degree: 1%

---

# 学生・教職員間の連携

![ユースケースの英雄バナー](assets/UseCaseStudentHero.jpg)

教育機関は、PDF文書を使用して学習教材を学生と共有します。 PDFは、教員が文書を交換できる形式です。

統合 [Adobe PDF Services API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-tools.html) および [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html) intoは、教師や学生に教えたり学んだりするための単一のプラットフォームを提供します。 例えば、アプリを使用して、学生は課題やレポートカードに関する質問を行ったり、グループの課題で共同作業を行うことができます。

Node.jsアプリケーションがPDFサービスAPIにアクセスするための公式SDKがあります。 これにより、Microsoft WordやMicrosoft Excelなどの文書をPDFに変換できます。 また、複数のレポートの結合、ページの並べ替え、PDFの保護など、より高度な操作を実行できます。 詳しくは、「 [製品ドキュメント](https://www.adobe.io/apis/documentcloud/dcsdk/).

## 学習内容

この実践チュートリアルでは、以下の特徴を備えたオンライン学習プラットフォームの作成方法を学習します。 [教員と学生がリソースを簡単に共有できます。](https://www.adobe.io/apis/documentcloud/dcsdk/student-teacher-collaboration.html) PDF。 このチュートリアルでは、 [学習ポータル](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-tools-for-teachers) node.js JavaScriptランタイム(Node.js)とPDFサービスを使用して作成されました。

学習ポータルには次の機能があります。

* 教員がリソースをアップロードできるようにします。

* 複数の文書を選択してPDFに変換できます

* 文書をPDFに変換できます。

* 学生のPDFプレビューをwebブラウザーで表示し、特別なソフトウェアを使用せずに文書に注釈を付けることができます

* 学生がコメントを残してコンピューターにダウンロードできるようにします

操作方法 [!DNL Adobe Acrobat Services] PDFを使用して、生徒に豊かな体験を提供します。 [!DNL Acrobat Services] APIは既存のアプリケーションとシームレスに統合されるため、学生はファイルをアップロード、変換、表示し、コメントを作成、保存することができます。これらはすべて、現在の設定内で行うことができます。

## 関連APIとリソース

* [PDF埋め込みAPI](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [プロジェクトコード](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-tools-for-teachers)

## 学習ポータルにリソースをアップロード中

学習ポータルの教員セクションで、教員は課題やテストなどのドキュメントをアップロードできます。 文書は、Microsoft Word、Microsoft Excel、HTML、様々な画像形式など、任意の形式にすることができます。

![学習ポータルの教員セクションのスクリーンショット](assets/edu_1.png)

アップロードされた文書は保存され、学生がWebページを開いたときに表示されます。

アプリケーションによるファイルのアップロード方法については、 [プロジェクトコード](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-tools-for-teachers).

## 文書のPDFへの変換

学生は、あらゆる種類の1つまたは複数の文書(Microsoft Word、Excel、PowerPointなど)や、その他の一般的なテキストおよび画像ファイルの種類をPDFに変換できます。 ラーニングポータルは、PDFサービスを使用して、ファイルをPDFに変換します。

独自の学習ポータルを作成するには、まず独自の資格情報を作成する必要があります。 [新規登録](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 6か月間と最大1,000件の文書取引でPDFサービスAPIを無料で使用する場合。 その後 [従量課金制](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) クラスが割り当てを強化する際に、文書トランザクションあたり\$0.05になります。

学生がダッシュボードからドキュメントを選択すると、次のように表示されます。

![学習ポータルの「学生」セクションのスクリーンショット](assets/edu_2.png)

変換する文書を選択し、クリックするだけです **レポートを取得**.

学習ポータルで文書がPDFに変換され、レポートページとPDFファイルのプレビューが表示されます。

この手順のサンプルコードは次のとおりです。

```
async function createPdf(rawFile, outputPdf) {
    try {
            // configurations
            const credentials =  adobe.Credentials
            .serviceAccountCredentialsBuilder()
            .fromFile("./src/pdftools-api-credentials.json")
            .build();
 
            // Capture the credential from app and show create the context
            const executionContext = adobe.ExecutionContext.create(credentials),
            operation = adobe.CreatePDF.Operation.createNew();
 
            // Pass the content as input (stream)
            const input = adobe.FileRef.createFromLocalFile(rawFile);
            operation.setInput(input);
 
            // Async create the PDF
            let result = await operation.execute(executionContext);
            await result.saveAsFile(outputPdf);
    } catch (err) {
            console.log('Exception encountered while executing operation', err);
    }
}
```

このサンプルコードは、 `createPdf` PDFを生成するためのExpressルートハンドラー内のメソッドです。

このメソッドの呼び出し方法については、「 [プロジェクトコード](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-tools-for-teachers/blob/master/src/helpers/pdf.js).

## 学習リソースのプレビュー

ユーザーインターフェイスは、PDF埋め込みAPIを使用して、webブラウザーでPDFをレンダリングします。 このAPIは無料で使用できます。

PDF埋め込みAPIはPDFサービスAPIとは異なる資格情報を使用するので、次のことを行う必要があります [資格情報の作成](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)
使用する前に これで、PDF埋め込みを完全に無料で使用できます。

トークンに正しいwebサイトURLを入力してください。 そうしないと、トークンを使用してPDFをレンダリングできない可能性があります。

ユーザーインターフェイスでは、 [ハンドル](https://handlebarsjs.com/) テンプレートの言語。 WebブラウザーにPDFが表示されます。

この手順のコードは次のとおりです。

```
<div id="adobe-dc-view" style="height: 750px; width: 700px;"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
    document.addEventListener("adobe_dc_view_sdk.ready", function () {
        var adobeDCView = new AdobeDC.View({ clientId: "<your-credentials-here>", divId: "adobe-dc-view" });
        adobeDCView.previewFile(
            {
                content: {
                    location: { url: "<file-url>" }
                },
                    metaData: { fileName: "<file-name>" }
            },
           );
    });
</script>
 
<p>Material has been generated, <a href="/students/download/{{filename}}" target="_blank">click here</a> to download it.
</p>
```

このコードは、次の画面キャプチャに示すように、PDF出力と、PDFレポートをダウンロードするためのリンクを表示します。

![学生のPDFプレビューのスクリーンショット](assets/edu_3.png)

学生は、レポートをダウンロードしたり、ここで資料に取り組んだりできます。

## PDF文書に注釈を付ける

ラーニングプラットフォームは、PDFでの基本的なコメント、コメント、ディスカッションをサポートする必要があります。 PDF埋め込みAPIには、これらすべての機能が用意されています。 次を使用して、注釈サポートをアクティブ化します `showAnnotationTools`PDFの一環として、教職員や学生が文書にコメントを付けたり、コメントをアーカイブしたりできるようにします。

PDF文書でアノテーションを有効にするには、引数を渡すだけです `showAnnotationTools` : `previewFile` メソッドです。 これにより、PDFプレビューアに注釈ツールが表示されます。 このツールには、プレビューの右上隅にある3点メニューからアクセスします。

![PDFのコメントツールのスクリーンショット](assets/edu_4.png)

教員がアップロードした文書では、学生はテキストをハイライト表示したり、コメントを追加したりできます。

![PDFにコメントを追加したスクリーンショット](assets/edu_5.png)

上記の画面キャプチャでは、ユーザーは「Guest」というラベルが付いていますが、学生や教員などのユーザーのプロファイルを設定できます。

学生が注釈を適用すると、PDF埋め込みAPIにより **保存** トップバナーに沿ってボタンをクリックします。 保存すると、注釈がファイルに追加されます。 クリックしてみてください **保存** レポートに埋め込まれた注釈を使用して、ファイルがどのように保存されるかを確認します。

学生は、注釈を使用して、学習教材に関する質問やコメントを共有できます。

## 文書の使用状況の追跡

教師や学校は、学生がオンラインプラットフォームをどのように使用しているかを確認することが重要です。 これにより、教師は課題のパフォーマンスを向上させるのに役立つリソースを使用して学生をサポートできます。 PDF埋め込みAPIは、分析と統合され、ユーザーが文書を開く、読む、閉じるなど、発生するすべてのイベントを測定するために使用できます。 PDFサービスAPIを使用すると、教員は印刷、ダウンロード、ファイル編集を無効にして、アカデミックな学習の一貫性を維持することもできます。

以下がある場合： [Adobe Analytics](https://www.adobe.io/apis/experiencecloud/analytics.html) ライセンスを取得すると、その [すぐに使える統合](https://experienceleague.adobe.com/docs/document-services/tutorials/pdfembed/controlpdfexperience.html?lang=en#adobe-analytics). それ以外の場合は、コールバックを使用して、PDFサービスを次のような他の分析プロバイダーと統合します [Google](https://experienceleague.adobe.com/docs/document-services/tutorials/pdfembed/controlpdfexperience.html?lang=en#google-analytics).

文書イベントの測定を有効にするには、 `registerCallback` Adobe DCビューインスタンスを使用するメソッド。 文書を開いたり、ページを読んだりといった基本的なメトリクスをコンソールに表示できます。 メトリックはログに保存したり、他の分析ストアに公開することもできます。

イベントハンドラをアタッチするためのサンプルコードを次に示します。

```
adobeDCView.registerCallback(
    AdobeDC.View.Enum.CallbackType.EVENT_LISTENER,
    function(event) {
           console.log(event);
    },
    {
           enablePDFAnalytics: true
    }
);
```

教員は、課題を見た学生の数、メモの全ページを通した学生の数、その他の貴重な詳細情報を確認できます。

Webブラウザーコンソールの画面キャプチャを次に示します。

![Webブラウザーコンソールのスクリーンショット](assets/edu_6.png)

このスクリーンショットは、学生が課題ファイルを開き、最初のページを読んだことを示しています。追加ページまでスクロールしなかったか、文書に1ページしかなかった場合に、ファイルをダウンロードしたことを示しています。 これらの指標を収集して分析を実行し、学生の行動を研究できます。

また、 [Adobe Analytics](https://business.adobe.com/products/analytics/adobe-analytics.html) はPDF埋め込みAPIと統合されているため、Adobe Analyticsスイートのサブスクリプションをお持ちの場合は、サブスクリプションでメトリクスを公開できます。 Adobe Analyticsでメトリックを公開するには、スイートIDをPDF埋め込みAPIコンストラクタに渡すだけです。 (PDFサービスAPI資格情報ではなく、PDF埋め込みAPI資格情報を使用する必要があります)。

次に、スイートIDをPDFのEmbed APIコンストラクタに渡す方法を示すサンプルコードを示します。

```
var adobeDCView = new AdobeDC.View({
    clientId: "<your-adobe-dc-credential>",
    divId: "<#element>"
    reportSuiteId: <your-id-here>,
}); 
```

## 次の手順

この実践チュートリアルでは、PDFサービスAPIとPDF埋め込みAPIを使用して、効果的な学習ポータルを構築する方法を確認しました [学生と教員の連携](https://www.adobe.io/apis/documentcloud/dcsdk/student-teacher-collaboration.html). このポータルを使用すると、教員は学習教材を任意の形式でアップロードし、PDFサービスAPIを使用してPDFに変換できます。 その後、学生はPDF埋め込みAPIを使用して、これらのPDFをプレビューできます。

これで、PDFレポートに注釈を付ける方法、注釈をアーカイブする方法、およびPDFレポートの使用状況を追跡する方法が分かったので、これらのソリューションを独自のプロジェクトに導入できるようになりました。

次を使用できます [!DNL Adobe Acrobat Services] Webサイト上で、使いやすくインタラクティブなPDF体験を作成するためのAPI。 6か月間Adobe PDFサービスAPIを無料でご利用いただけます [従量課金制](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) (AWSまたは直接契約書を通じて)文書トランザクションあたり\$0.05のみです。 時間制限なしでAdobe PDF Embedを無料で使用できます。 無料アカウントを作成して [開始する](https://www.adobe.com/go/dcsdks_credentials) 今日。

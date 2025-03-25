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
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '1385'
ht-degree: 0%

---

# 学生・教職員間の連携

![使用事例の英雄バナー](assets/UseCaseStudentHero.jpg)

教育機関は、PDF文書を使用して学習教材を学生と共有します。 PDFは、教員が文書を交換できる形式です。

[Adobe PDFサービスAPI](https://developer.adobe.com/document-services/apis/pdf-services)と[Adobe PDF Embed API](https://developer.adobe.com/document-services/apis/pdf-embed)をアプリに統合することで、教職員と生徒が共に学習できる単一のプラットフォームを提供できます。 例えば、アプリを使用して、学生は課題やレポートカードに関する質問を行ったり、グループの課題で共同作業を行うことができます。

Node.jsアプリケーションがPDFサービスAPIにアクセスするための公式SDKがあります。 これにより、Microsoft WordやMicrosoft Excelなどの文書を
PDF。 また、複数のレポートの結合、ページの並べ替え、PDFの保護など、より高度な操作を実行できます。 詳しくは、[製品ドキュメント](https://developer.adobe.com/document-services/homepage/)を参照してください。

## 学習内容

この実践チュートリアルでは、[教職員と生徒がPDFでリソースを簡単に共有できる](https://developer.adobe.com/document-services/use-cases/collaboration/student-teacher-collaboration)オンライン学習プラットフォームの作成方法について説明します。 このチュートリアルでは、Node.js JavaScriptランタイム(Node.js)とPDFサービスを使用して作成された[学習ポータル](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-tools-for-teachers)を使用します。

学習ポータルには次の機能があります。

* 教員がリソースをアップロードできるようにします。

* 複数の文書を選択してPDFに変換できます

* 文書をPDFに変換できます。

* 学生のPDFプレビューをwebブラウザーで表示し、特別なソフトウェアを使用せずに文書に注釈を付けることができます

* 学生がコメントを残してコンピューターにダウンロードできるようにします

[!DNL Adobe Acrobat Services]を使用して、PDFを持つ生徒に豊かな体験を提供する方法を説明します。 [!DNL Acrobat Services]個のAPIは既存のアプリケーションとシームレスに統合されるため、学生はファイルをアップロード、変換、表示し、コメントを作成、保存することができます。これらはすべて、現在の設定内で行うことができます。

## 関連APIとリソース

* [PDF埋め込みAPI](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [PDFサービスAPI](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [プロジェクトコード](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-tools-for-teachers)

## 学習ポータルにリソースをアップロード中

学習ポータルの教員セクションで、教員は課題やテストなどのドキュメントをアップロードできます。 文書は、Microsoft Word、Microsoft Excel、HTML、様々な画像形式など、任意の形式にすることができます。

![学習ポータルの教師セクションのスクリーンショット](assets/edu_1.png)

アップロードされた文書は保存され、学生がWebページを開いたときに表示されます。

アプリケーションによるファイルのアップロード方法については、[プロジェクトコード](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-tools-for-teachers)を参照してください。

## 文書のPDFへの変換

学生は、あらゆる種類の1つまたは複数の文書(Microsoft Word、Excel、PowerPointなど)や、その他の一般的なテキストおよび画像ファイルの種類をPDFに変換できます。 ラーニングポータルは、PDFサービスを使用して、ファイルをPDFに変換します。

独自の学習ポータルを作成するには、まず独自の資格情報を作成する必要があります。 [新規登録](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)して
6か月間と最大1,000件の文書処理にPDFサービスAPIを無料で使用できます。 その後、クラスが割り当てを増やしているため、[従量課金制](https://developer.adobe.com/document-services/pricing/main)でドキュメントトランザクションあたり\$0.05になります。

学生がダッシュボードからドキュメントを選択すると、次のように表示されます。

![学習ポータルの学生セクションのスクリーンショット](assets/edu_2.png)

学生は変換する文書を選択し、**「レポートを取得」**&#x200B;をクリックするだけです。

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

サンプルコードは、Expressルートハンドラー内の`createPdf`メソッドを呼び出してPDFを生成します。

このメソッドの呼び出し方法については、[プロジェクトコード](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-tools-for-teachers/blob/master/src/helpers/pdf.js)を参照してください。

## 学習リソースのプレビュー

ユーザーインターフェイスは、PDF埋め込みAPIを使用して、webブラウザーでPDFをレンダリングします。 このAPIは無料で使用できます。

PDF埋め込みAPIはPDFサービスAPIとは異なる資格情報を使用するため、[資格情報を作成](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)する必要があります
使用する前に これで、PDF埋め込みを完全に無料で使用できます。

トークンに正しいwebサイトURLを入力してください。 そうしないと、トークンを使用してPDFをレンダリングできない可能性があります。

ユーザーインターフェイスでは、[ハンドル](https://handlebarsjs.com/)のテンプレート言語が使用されます。 WebブラウザーにPDFが表示されます。

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

![生徒のPDFプレビューのスクリーンショット](assets/edu_3.png)

学生は、レポートをダウンロードしたり、ここで資料に取り組んだりできます。

## PDF文書に注釈を付ける

ラーニングプラットフォームは、PDFでの基本的なコメント、コメント、ディスカッションをサポートする必要があります。 PDF埋め込みAPIには、これらすべての機能が用意されています。 `showAnnotationTools`を使用して注釈のサポートをアクティブ化し、教員や学生が文書にコメントを追加したり、PDFの一部としてコメントをアーカイブしたりできます。

PDF文書で注釈を有効にするには、引数`showAnnotationTools` : trueを`previewFile`メソッドに渡すだけです。 これにより、PDFプレビューアに注釈ツールが表示されます。 このツールには、プレビューの右上隅にある3点メニューからアクセスします。

![PDFのコメントツールのスクリーンショット](assets/edu_4.png)

教員がアップロードした文書では、学生はテキストをハイライト表示したり、コメントを追加したりできます。

![PDFにコメントを追加したスクリーンショット](assets/edu_5.png)

上記の画面キャプチャでは、ユーザーは「Guest」というラベルが付いていますが、学生や教員などのユーザーのプロファイルを設定できます。

学生が注釈を適用すると、PDF埋め込みAPIの上部バナーに「**保存**」ボタンが表示されます。 保存すると、注釈がファイルに追加されます。 **保存**&#x200B;をクリックして、レポートに埋め込まれた注釈を使用してファイルがどのように保存されるかを確認してください。

学生は、注釈を使用して、学習教材に関する質問やコメントを共有できます。

## 文書の使用状況の追跡

教師や学校は、学生がオンラインプラットフォームをどのように使用しているかを確認することが重要です。 これにより、教師は課題のパフォーマンスを向上させるのに役立つリソースを使用して学生をサポートできます。 PDF埋め込みAPIは、分析と統合され、ユーザーが文書を開く、読む、閉じるなど、発生するすべてのイベントを測定するために使用できます。 PDFサービスAPIを使用すると、教員は印刷、ダウンロード、ファイル編集を無効にして、アカデミックな学習の一貫性を維持することもできます。

[Adobe Analytics](https://developer.adobe.com/analytics-apis/docs/2.0/)ライセンスをお持ちの場合は、[すぐに使える統合](https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfembed/controlpdfexperience#adobe-analytics)を使用できます。 それ以外の場合は、コールバックを使用して、PDFサービスを[Google](https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfembed/controlpdfexperience#google-analytics)などの他の分析プロバイダーと連携します。

ドキュメントイベントの測定を有効にするには、Adobe DCビューインスタンスで`registerCallback`メソッドを使用してイベントハンドラーをアタッチします。 文書を開いたり、ページを読んだりといった基本的なメトリクスをコンソールに表示できます。 メトリックはログに保存したり、他の分析ストアに公開することもできます。

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

また、[Adobe Analytics](https://business.adobe.com/products/adobe-analytics.html)はPDF埋め込みAPIと統合されているため、Adobe Analyticsスイートのサブスクリプションをお持ちの場合は、サブスクリプションでメトリックスを公開できます。 Adobe Analyticsでメトリックを公開するには、スイートIDをPDF埋め込みAPIコンストラクタに渡すだけです。 (PDFサービスAPI資格情報ではなく、PDF埋め込みAPI資格情報を使用する必要があります)。

次に、スイートIDをPDFのEmbed APIコンストラクタに渡す方法を示すサンプルコードを示します。

```
var adobeDCView = new AdobeDC.View({
    clientId: "<your-adobe-dc-credential>",
    divId: "<#element>"
    reportSuiteId: <your-id-here>,
}); 
```

## 次の手順

この実践チュートリアルでは、PDFサービスAPIとPDF埋め込みAPIを使用して学習ポータルを構築する方法を確認し、[生徒と教職員の効果的な共同作業](https://developer.adobe.com/document-services/use-cases/collaboration/student-teacher-collaboration)を促進しました。 このポータルを使用すると、教員は学習教材を任意の形式でアップロードし、PDFサービスAPIを使用してPDFに変換できます。 その後、学生はPDF埋め込みAPIを使用して、これらのPDFをプレビューできます。

これで、PDFレポートに注釈を付ける方法、注釈をアーカイブする方法、およびPDFレポートの使用状況を追跡する方法が分かったので、これらのソリューションを独自のプロジェクトに導入できるようになりました。

[!DNL Adobe Acrobat Services]個のAPIを使用して、Webサイト上のユーザーフレンドリーでインタラクティブなPDF体験を作成できます。 6か月間Adobe PDFサービスAPIを無料で利用した後は、(AWSまたは直接契約を通じて)[従量課金制](https://developer.adobe.com/document-services/pricing/main)で、1件の文書取引でわずか$ 0.05の利用が可能です。 時間制限なしでAdobe PDF Embedを無料で使用できます。 無料アカウントを作成して、今すぐ[使用を開始](https://www.adobe.com/go/dcsdks_credentials)しましょう。

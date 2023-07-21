---
title: 学生・教職員の共同作業
description: 教職員や学生がPDFで簡単にリソースを共有できるオンライン学習プラットフォームを作成する方法について説明します
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8091.jpg
jira: KT-8091
exl-id: 570a635c-e539-4afc-a475-ecf576415217
source-git-commit: 2d1151c17dfcfa67aca05411976f4ef17adf421b
workflow-type: tm+mt
source-wordcount: '1485'
ht-degree: 1%

---

# 学生・教職員の共同作業

![ユースケースのヒーローバナー](assets/UseCaseStudentHero.jpg)

教育機関では、PDFドキュメントを使用して学習教材を学生と共有します。 PDFは、教職員向けに交換可能な文書形式を提供します。

統合 [Adobe PDF Services API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-tools.html) および [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html) を単一のアプリに統合することで、教職員と学生が授業と学習をおこなうための単一のプラットフォームを提供できます。 例えば、アプリケーションを使用すると、学生は課題やレポートカードについて質問したり、グループ課題で共同作業したりできます。

Node.js アプリケーションがPDFサービス API にアクセスするための公式 SDK があります。 これにより、Microsoft Word やMicrosoft Excel などの文書をPDFに変換できます。 また、複数のレポートの結合、ページの並べ替え、アクションの保護など、より高度なPDFも実行できます。 詳しくは、 [製品ドキュメント](https://www.adobe.io/apis/documentcloud/dcsdk/)を選択します。

## 学習内容

この実践チュートリアルでは、以下の内容を実現するオンライン学習プラットフォームの作成方法を学びます。 [教職員や学生が簡単にリソースを共有できます](https://www.adobe.io/apis/documentcloud/dcsdk/student-teacher-collaboration.html) PDF。 このチュートリアルでは、 [学習ポータル](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-tools-for-teachers) node.js JavaScript ランタイム (Node.js) およびPDFサービス

学習ポータルには次の機能があります。

* 教職員がリソースをアップロードできるようにします

* 学生が複数の文書を選択してPDF

* 文書の変換を有効にします。PDF

* Web ブラウザーでPDFプレビューを表示し、追加のソフトウェアを使用せずに学生がドキュメントに注釈を付けられるようにします。

* 学生がコメントを残して、自分のコンピューターにダウンロードできるようにします

使用方法を見る [!DNL Adobe Acrobat Services] 学生に豊かなエクスペリエンスを提供するPDF。 [!DNL Acrobat Services] API は既存のアプリケーションとシームレスに統合できます。学生はファイルのアップロード、変換、表示、注釈の追加と保存を、すべて現在の設定内でおこなうことができます。

## 関連する API とリソース

* [PDF埋め込み API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [プロジェクトコード](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-tools-for-teachers)

## 学習ポータルへのリソースのアップロード

学習ポータルの教職員セクションで、教職員は課題やテストなどのドキュメントをアップロードできます。 文書は、Microsoft Word、Microsoft Excel、HTML、様々な画像形式など、任意の形式で保存できます。

![学習ポータルの「教職員」セクションのスクリーンショット](assets/edu_1.png)

アップロードされた文書は保存され、学生が Web ページを開いたときに提示されます。

アプリケーションがファイルをアップロードする方法については、 [プロジェクトコード](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-tools-for-teachers)を選択します。

## ドキュメントのPDF

学生は、1 つまたは複数の文書を、Microsoft Word、Excel、PowerPoint などの任意の形式でPDFに変換できます。また、他の一般的なテキストファイルや画像ファイル形式にも変換できます。 学習ポータルでは、PDFサービスを使用してファイルをPDFに変換します。

独自の学習ポータルを作成するには、まず独自の資格情報を作成する必要があります。 [新規登録](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) PDFサービス API を 6 ヶ月間無料で使用でき、最大 1,000 件の文書トランザクションを処理できます。 その後 [従量制の](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) クラスが割り当てを増やすにつれて、ドキュメントトランザクションあたり\$0.05 になります。

学生がダッシュボードからドキュメントを選択すると、以下が表示されます。

![学習ポータルの「学生」セクションのスクリーンショット](assets/edu_2.png)

学生は変換する文書を選択してクリックするだけです **レポートを読む**&#x200B;を選択します。

学習ポータルは、文書をPDFに変換し、レポートページと変換ファイルのプレビューをPDFします。

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

サンプルコードでは、 `createPdf` メソッドを生成するための Express ルートハンドラー内のPDF。

このメソッドの呼び出し方法については、 [プロジェクトコード](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-tools-for-teachers/blob/master/src/helpers/pdf.js)を選択します。

## 学習リソースのプレビュー

ユーザーインターフェイスでは、PDF埋め込み API を使用して、Web ブラウザーでPDFをレンダリングします。 この API は無料で利用できます。

PDF埋め込み API はPDFサービス API とは異なる資格情報を使用するため、 [資格情報の作成](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)
を選択します。 その後、完全に無料で埋め込みPDFを使用することができます。

トークンに正しい Web サイト URL を入力してください。 そうしないと、トークンを使用してPDFを表示できません。

ユーザーインターフェイスでは、 [ハンドル](https://handlebarsjs.com/) テンプレートの言語 Web ブラウザーにPDFが表示されます。

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

このコードは、次の画面に示すように、PDF出力と、PDFレポートをダウンロードするためのリンクを表示します。

![学生プレビューのPDF画像](assets/edu_3.png)

学生はレポートをダウンロードするか、こちらから資料に取り組むことができます。

## 注釈、PDF文書

学習プラットフォームは、基本的な注釈、コメント、ディスカッションをサポートする必要があるPDFです。 PDF埋め込み API は、これらのすべての機能を提供します。 次の方法で注釈サポートを有効にします。 `showAnnotationTools`を使用すると、教職員と学生が文書にコメントしたり、コメントをアーカイブしたりして、PDFの一部として利用できます。

PDF文書で注釈を有効にするには、引数 `showAnnotationTools` :～に忠実である `previewFile` メソッド。 注釈プレビューアに注釈ツールがPDFされます。 このツールには、プレビューの右上隅にある 3 点ドットメニューからアクセスします。

![PDFの注釈ツールのスクリーンショット](assets/edu_4.png)

教員がアップロードしたドキュメントでは、テキストのハイライト表示、コメントの追加などを行うことができます。

![コメント追加のスクリーンショット、PDF](assets/edu_5.png)

上のスクリーンキャプチャでは、ユーザーには「ゲスト」というラベルが付いていますが、学生や教職員などのユーザーのプロファイルを設定できます。

学生が注釈を適用すると、PDF埋め込み API に **保存** 」ボタンをクリックします。 保存すると、ファイルに注釈が追加されます。 クリックしてみる **保存** 」をクリックして、レポートに埋め込まれた注釈を使用してファイルがどのように保存されるかを確認します。

学生は注釈を使用して、学習教材について質問したり、コメントを共有したりできます。

## 文書の使用状況の追跡

教職員や学校にとって、生徒がオンラインプラットフォームをどのように使用しているかを確認することは重要です。 これにより、教職員は、課題を効果的に遂行するためのリソースを学生に提供できます。 PDF埋め込み API は、ユーザーが文書を開く、読む、閉じるなどのイベントをすべて測定できる分析と統合されています。 PDFサービス API を使用すると、教員は印刷、ダウンロード、ファイルの変更を無効にして、学業の整合性を維持することもできます。

DITA マップの [Adobe Analytics](https://www.adobe.io/apis/experiencecloud/analytics.html) ライセンスがあれば、 [すぐに利用できる統合](https://experienceleague.adobe.com/docs/document-services/tutorials/pdfembed/controlpdfexperience.html?lang=en#adobe-analytics)を選択します。 それ以外の場合は、コールバックを使用して、PDFサービスを他の分析プロバイダー ( [Google](https://experienceleague.adobe.com/docs/document-services/tutorials/pdfembed/controlpdfexperience.html?lang=en#google-analytics)を選択します。

ドキュメントイベントの測定を有効にするには、 `registerCallback` メソッドとAdobeDC ビューインスタンス 文書を開く、ページを読むなどの基本的な指標をコンソールに表示できます。 メトリックをログに保存したり、他の分析ストアに公開したりすることもできます。

イベントハンドラーを関連付けるサンプルコードを次に示します。

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

教師は、課題を見た学生の数、ノートのすべてのページを閲覧した学生の数、その他の貴重な詳細を確認できます。

次に、Web ブラウザコンソールのスクリーンキャプチャを示します。

![Web ブラウザーコンソールのスクリーンショット](assets/edu_6.png)

このスクリーンキャプチャは、学生が課題ファイルを開き、最初のページを読み上げた後、ファイルをダウンロードしたことを示しています。追加のページにスクロールしないか、文書が 1 ページしかないかのいずれかです。 これらの指標を収集して分析を実行し、学生の行動を調べることができます。

また、 [Adobe Analytics](https://business.adobe.com/products/analytics/adobe-analytics.html) はPDF埋め込み API と統合されているので、Adobe Analyticsスイートのサブスクリプションがある場合は、サブスクリプションでメトリックを公開できます。 Adobe Analyticsでメトリックを公開するには、スイート ID をPDFEmbed API コンストラクターに渡すだけです。 (PDFの埋め込み API 資格情報を使用する必要があります。PDFサービス API 資格情報ではありません )。

次のサンプルコードは、スイート ID を Embed API コンストラクターのPDFに渡す方法を示します。

```
var adobeDCView = new AdobeDC.View({
    clientId: "<your-adobe-dc-credential>",
    divId: "<#element>"
    reportSuiteId: <your-id-here>,
}); 
```

## 次の手順

この実践チュートリアルでは、PDFサービス API とPDF埋め込み API を使用して学習ポータルを作成し、効果的な [学生と教員の共同作業](https://www.adobe.io/apis/documentcloud/dcsdk/student-teacher-collaboration.html)を選択します。 このポータルを使用すると、教職員は学習教材を任意の形式でアップロードし、PDFサービス API を使用してPDFに変換できます。 その後、学生はPDF埋め込み API を使用してこれらのPDFをプレビューできます。

これで、PDFレポートに注釈を付け、注釈をアーカイブし、PDFレポートの使用を追跡する方法を理解できました。次に、これらのソリューションを自分のプロジェクトに導入します。

以下を使用できます [!DNL Adobe Acrobat Services] ユーザーフレンドリーでインタラクティブなPDF体験を作成するための API Adobe PDF Services API を 6 ヶ月間無料でご利用いただけます [従量制の](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) (AWSまたは直接契約を通じて ) 文書トランザクション 1 件あたり 0.05 USD のみ。 Adobe PDF Embed を無料で使用できます。 ～するための無料アカウントを作成する [今すぐ始める](https://www.adobe.com/go/dcsdks_credentials) 今日です。

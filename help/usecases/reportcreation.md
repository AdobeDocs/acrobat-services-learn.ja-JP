---
title: レポートの作成と編集
description: お客様向けに Web サイトでPDFレポートを生成する方法を説明します
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8093.jpg
jira: KT-8093
exl-id: 2f2bf1c2-1b33-4eee-9fd2-5d0b77e6b0a9
source-git-commit: 2d1151c17dfcfa67aca05411976f4ef17adf421b
workflow-type: tm+mt
source-wordcount: '1346'
ht-degree: 1%

---

# レポートの作成と編集

![ユースケースのヒーローバナー](assets/UseCaseReportHero.jpg)

金融、教育、マーケティングなどの業界では、PDFを使用して顧客や関係者とデータを共有しています。 PDFを使用すると、表、グラフィック、インタラクティブコンテンツなど、リッチなドキュメントを誰もが表示できる形式で簡単に共有できます。 [!DNL Adobe Acrobat Services] API を利用することで、これらの企業は共有可能なPDFレポートをMicrosoft Word、Microsoft Excel、グラフィックなどの様々な文書形式から生成できます。

言え [ソーシャルメディア追跡会社を経営する](https://www.adobe.io/apis/documentcloud/dcsdk/on-demand-report-creation.html)を選択します。 顧客はパスワードで保護されたサイトの一部にログインして、キャンペーン分析を表示します。 多くの場合、これらの統計を経営幹部、株主、寄付者、その他の関係者と共有したいと考えています。 PDF文書をダウンロードして、顧客と数字やグラフなどを共有するのに便利です。

組み込むことによって [PDFサービス API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-tools.html) web サイトにアクセスして、顧客ごとにPDFレポートをその場で生成できます。 作成したPDFを組み合わせて 1 つのレポートにすれば、顧客がダウンロードして関係者に伝えることができます。

## 学習内容

この実践チュートリアルでは、Node.js および Express.js 環境 ( 一部の JavaScript、HTMLおよび CSS のみ ) でPDFサービス SDK を使用して、既存の Web サイトにPDF指向の機能をすばやく簡単に追加する方法を学習します。 この Web サイトには、管理者がレポートをアップロードするページがあります。このページでは、利用可能なレポートの一覧を確認し、PDFに変換する文書を選択できます。また、システムによって生成されたPDFをダウンロードするための役立つエンドポイントも表示されます。

## 関連する API とリソース

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF埋め込み API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

## 顧客のキャンペーンレポートダッシュボード

>[!NOTE]
>
>このチュートリアルでは、Node.js のベストプラクティスや、Web アプリケーションのセキュリティ保護方法については説明しません。 Web サイトの一部の領域は一般公開されており、ドキュメントの命名は制作環境に適さない場合があります。 このようなシステムを設計するための最善のアプローチについて話し合うには、建築家やエンジニアに相談してください。

ここでは、顧客レポート領域と管理者セクションを持つ基本的な Express.js Web アプリケーションがあります。 このアプリケーションは、ソーシャルメディアキャンペーンのレポートを表示できます。 例えば、広告がクリックされた回数を示すことができます。

![パーソナライズされたレポートを取得する方法のスクリーンショット](assets/report_1.png)

このプロジェクトは、 [GitHub リポジトリ](https://github.com/afzaal-ahmad-zeeshan/express-adobe-pdf-tools)を選択します。

それでは、レポートの公開方法について説明します。

## レポートのアップロード

簡単にするために、ファイルシステムベースのアップロードと処理のみを使用してください。 Express.js では、fs モジュールを使用して、ディレクトリ下で利用可能なすべてのファイルを一覧表示できます。

同じページで、管理者がレポートファイルをサーバーにアップロードし、顧客が参照できるようにします。 これらのファイルには、Microsoft Word、Microsoft Excel、HTML、 [その他のデータ形式]https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf) に保存されます。 管理ページは次のようになります。

![管理者機能のスクリーンショット](assets/report_2.png)

>[!NOTE]
>
>URL をパスワードで保護するか、npm のパスポートパッケージを使用して、アプリケーションを認証および承認レイヤーの背後に保護します。

管理者がファイルを選択してアップロードすると、パブリックリポジトリに移動され、他のユーザーがそのファイルにアクセスできるようになります。 同じリポジトリーを使用して、管理者ページからドキュメントを公開し、顧客が利用できるマーケティングレポートを一覧表示します。 このコードは次のとおりです。

```
router.get('/', (req, res) => {
try {
let files = fs.readdirSync('./public/documents/raw') // read the files
res.status(200).render("reports", { page: 'reports', files: files });
} catch (error) {
res.status(500).render("crash", { error: error });
}
});
```

このコードはすべてのファイルをリストし、ファイルリストのビューをレンダリングします。

## レポートの選択

ユーザー側では、ソーシャルメディアのキャンペーンレポートに含める文書を顧客が選択するためのフォームが用意されています。 わかりやすくするために、サンプルページには、文書名と、文書を選択するためのチェックボックスのみが表示されます。 単一のレポートまたは複数のレポートを選択して、単一のドキュメントに結合PDFできます。

より高度なユーザーインターフェイスの場合は、ここでレポートのプレビューを表示することもできます。

![顧客能力のスクリーンショット](assets/report_3.png)

## 生成，PDF報告

データ入力からPDFレポートを作成するには、PDFサービス SDK を使用します。 上記のスクリーンショットに示されているように、データは、Microsoft Word、Microsoft Excel、HTML、グラフィックなど、様々なデータ形式から取得できます。 まず、インストールサービス SDK の npm パッケージをPDFします。

```
$ npm install --save @adobe/documentservices-pdftools-node-sdk
```

開始する前に、API 資格情報が必要です。 [Adobe](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#getcred)を選択します。 使用する [!DNL Acrobat Services] アカウント [6 ヶ月間無料で、その後は従量制](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) の値は、ドキュメントトランザクションあたり\$0.05 です。

アーカイブファイルをダウンロードして、資格情報と秘密キーの JSON ファイルを抽出します。 サンプルプロジェクトでは、ファイルを src ディレクトリに配置します。

![src ディレクトリのスクリーンショット](assets/report_4.png)

資格情報の設定が完了したら、PDF変換タスクを作成できます。 このデモでは、アプリケーションで実行する必要のある操作が 2 つあります。

* RAW ドキュメントのPDF変換

* 複数のPDFファイルを 1 つのレポートに結合

全体的な手順は、どの操作を実行する場合でも同様です。 唯一の違いは、使用するサービスです。 次のコードでは、RAW ドキュメントを変換ファイルにPDFします。

```
async function createPdf(rawFile, outputPdf) {
try {
// configurations
const credentials = adobe.Credentials
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

上記のコードでは、資格情報を読み取り、実行コンテキストを作成します。 PDFサービス SDK では、要求を認証するために実行コンテキストが必要です。

次に、Raw ドキュメントをPDF形式に変換するPDF作成 最後に、 `outputPdf` パラメータを使用してPDFレポートをコピー このコードは、コードサンプルのsrc/helpers/pdf.jsファイルにあります。 このチュートリアルの後半で、モジュールモジュールを読み込み、PDFモジュールを呼び出します。

前のセクションで示したように、お客様は次のページに移動して、PDFに変換するレポートを選択できます。

![顧客能力のスクリーンショット](assets/report_3.png)

顧客がこれらのレポートを 1 つ以上選択すると、作成ファイルがPDFされます。

まず、1 つのPDFファイルを ユーザーが単一のレポートを選択した場合は、レポートをPDFに変換してダウンロードリンクを提供します。

```
try {
console.log(`[INFO] generating the report...`);
await pdf.createPdf(`./public/documents/raw/${reports}`, `./public/documents/processed/output.pdf`);
console.log(`[INFO] sending the report...`);
res.status(200).render("download", { page: 'reports', filename: 'output.pdf' });
} catch(error) {
console.log(`[ERROR] ${JSON.stringify(error)}`);
res.status(500).render("crash", { error: error });
}
```

このコードは、レポートを作成し、ダウンロード URL をお客様と共有します。 出力 Web ページは次のとおりです。

![お客様のダウンロード画面のスクリーンショット](assets/report_5.png)

出力PDF:

![一般的なレポートのスクリーンショット](assets/report_6.png)

お客様は、結合レポートを生成するために複数のファイルを選択できます。 顧客が複数の文書を選択した場合は、次の 2 つの操作を実行します。最初のレポートでは、各ドキュメントの一部のPDFが作成され、2 番目のレポートでは、それらの部分が 1 つのPDFレポートに結合されます。

```
async function combinePdf(pdfs, outputPdf) {
try {
// configurations
const credentials = adobe.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("./src/pdftools-api-credentials.json")
.build();
// Capture the credential from app and show create the context
const executionContext = adobe.ExecutionContext.create(credentials),
operation = adobe.CombineFiles.Operation.createNew();
// Pass the PDF content as input (stream)
for (let pdf of pdfs) {
const source = adobe.FileRef.createFromLocalFile(pdf);
operation.addInput(source);
}
// Async create the PDF
let result = await operation.execute(executionContext);
await result.saveAsFile(outputPdf);
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
```

このメソッドはsrc/helpers/pdf.jsファイルに含まれており、モジュールの書き出しの一部として表示されます。

```
try {
console.log(`[INFO] creating a batch report...`);
// Create a batch report and send it back
let partials = [];
for (let index in reports) {
const name = `partial-${index}-${reports[index]}`;
await pdf.createPdf(`./public/documents/raw/${reports[index]}`, `./public/documents/processed/${name}`);
partials.push(`./public/documents/processed/${name.replace('docx', 'pdf').replace('xlsx', 'pdf')}`);
}
await pdf.combinePdf(partials, `./public/documents/processed/output.pdf`);
console.log(`[INFO] sending the combined report...`);
res.status(200).render("download", { page: 'reports', filename: 'output.pdf' });
} catch(error) {
console.log(`[ERROR] ${JSON.stringify(error)}`);
res.status(500).render("crash", { error: error });
}
```

このコードは、複数の入力ドキュメントに対してコンパイルされたレポートを生成します。 追加された関数は、 `combinePdf` メソッドがPDFファイルのパス名のリストを取得し、単一の出力PDFを返す

これで、ソーシャルメディアダッシュボードの顧客は、アカウントから関連レポートを選択して、1 つの便利なPDFとしてダウンロードできます。 このダッシュボードを使用すると、経営陣やその他の関係者に対して、データ、表、グラフを使用してキャンペーンの成果を広く開きやすい形式で示すことができます。

## 次の手順

この実践チュートリアルでは、PDFサービス API を使用して、顧客が関連レポートを共有しやすいPDFとしてダウンロードする方法を説明しました。 Node.js アプリケーションを作成し、サービスのレポート作成と読み取りのためのPDFサービス APIPDFの機能を紹介しました。 このアプリケーションでは、単一のレポート文書をダウンロードする方法、または複数の文書を結合して単一のレポートを作成する方法をPDFしました。

このAdobeを搭載したアプリケーションは、 [ソーシャルメディアダッシュボードの顧客](https://www.adobe.io/apis/documentcloud/dcsdk/on-demand-report-creation.html) 受信者の全員がMicrosoft Office やその他のソフトウェアをデバイスにインストールしているかどうかを心配することなく、必要なレポートを取得して共有できます。 ユーザーがドキュメントを表示、結合、ダウンロードするときに役立つ同じテクニックを独自のアプリケーションで使用できます。 または、Adobeの他の多くの API をチェックして、署名の追加や追跡などをおこないます。

無料で始める [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) アカウントを作成し、従業員と顧客にとって魅力的なレポートを作成できます。 6 か月間無料でアカウントをお使いいただけます [従量制の](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) マーケティング活動が拡大しても、文書のトランザクション 1 件あたり 0.05 USD で完了します。

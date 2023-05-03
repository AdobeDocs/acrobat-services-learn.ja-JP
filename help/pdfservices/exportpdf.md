---
title: PDFサービス API を使用した Word、PowerPoint などへのPDFの書き出し
description: Node.js、Java および.Net 言語のサンプル・ファイルを使用して、PDF・サービス API エクスポート操作を実行する方法を説明します
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-6674.jpg
kt: 6674
exl-id: 55f5b04e-0249-47d9-9131-2f9ec01db7e8
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '500'
ht-degree: 5%

---

# PDFサービス API を使用した Word、PowerPoint などへのPDFの書き出し

![PDFHero 画像の作成](assets/ExportPDF_hero.jpg)

Adobe PDFサービス API は、API を使用してPDFファイルを MS Office、テキスト、および画像に変換します。 既存のPDFを活用してコンテンツの編集や分析を行う場合は、多くの一般的なユースケースが存在します。PDFサービス API を使用すると、開発者はこの機能を既存のシステムやアプリケーションに簡単に組み込むことができます。 PDFファイルを MS Word に変換して、コンテンツの編集、承認、後で署名用に送信して、カスタムの契約ワークフローを作成できます。 また、PDFコンテンツを MS Excel 形式に書き出して、請求書や財務計算、データ分析に使用できます。

Export 操作では、次の変換ファイルPDFがサポートされます。

* Microsoft Word(DOC、DOCX) へのPDF
* Microsoft PowerPoint(PPTX) のPDF
* Microsoft Excel(XLSX) へのPDF
* PDFからテキスト (RTF)
* PDFから画像 (JPEG、PNG)

このチュートリアルでは、Node.js、Java および.Net 言語のサンプル・ファイルを使用して、最初のPDF・サービス API エクスポート操作を実行する方法の基本を学習します。

## 手順 1:資格情報を作成し、環境を設定します。

以下の入門チュートリアルを使用して、API 資格情報の作成、サンプルファイルのダウンロード、環境の設定を行います。

[PDFサービス API と Java 入門](gettingstartedjava.md)
[PDFサービス APIと.Net 入門](gettingstartednet.md)
[PDFサービス API と Node.js 入門](createpdffromhtml.md)

## 手順 2:サンプルファイルを使用した PDF の書き出し操作の実行

**Java**

1. コマンドプロンプトを開きます。

1. サンプルコードディレクトリにディレクトリを変更します。

   例： C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-java-samples

1. 次のコマンドを実行します:

   `mvn -f pom.xml exec:java -Dexec.mainClass=com.adobe.platform.operation.samples.exportpdf.ExportPDFToDOCX`

PDFは src/main/resources ディレクトリに作成されます。

**.Net**

1. コマンドプロンプトを開きます。

1. サンプルコードディレクトリにディレクトリを変更します。

   例： C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-NetSamples

1. ディレクトリを ExportPDFtoDocx ディレクトリに再度変更します。

1. 次のコマンドを実行します:

   `dotnet run ExportPDFToDocx.csproj`

PDFは同じディレクトリに作成されます。

**Node.js**

1. コマンドプロンプトを開きます。

1. サンプルコードディレクトリにディレクトリを変更します。

   例： C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples

1. 次のコマンドを実行します:

   `node src/ocr/ocr-pdf.js`

PDFは、出力で指定された場所（デフォルトでは pdfServicesSdkResult ディレクトリ）に作成されます。

## 結論

これで、既存のアプリケーションにインポートして概念実証を開始できる実例が完成しました。 各サンプルディレクトリには、別のサンプルが表示され、イメージフォーマットにPDFファイルを書き出すことができます。 上記と同じ手順で、そのサンプルも実行できます。 別の形式に変更するには、コードを新しい形式に更新します。

SupportedTargetFormats.PPTX

次のような出力結果が得られます。

output/exportPdfOutput.PPTX

別の形式に変換します。

## リソースと次のステップ

* その他のヘルプやサポートについては、 [[!DNL Adobe Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) コミュニティフォーラム

* PDFサービス API [文書](https://www.adobe.com/go/pdftoolsapi_doc)

* [FAQ](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) PDFサービス API の質問

* [お問い合わせ](https://www.adobe.com/go/pdftoolsapi_requestform) ライセンスと価格に関する質問

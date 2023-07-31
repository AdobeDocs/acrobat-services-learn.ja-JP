---
title: PDFサービスAPIを使用したWordやPowerPointなどへのPDFの書き出し
description: Node.js、Java、.Netの言語用のサンプルファイルを使用して、PDFサービスAPIの書き出し処理を実行する方法について説明します
type: Tutorial
role: Developer
level: Intermediate
feature: PDF Services API
thumbnail: KT-6674.jpg
kt: 6674
exl-id: 55f5b04e-0249-47d9-9131-2f9ec01db7e8
source-git-commit: b65ffa3efa3978587564eb0be0c0e7381c8c83ab
workflow-type: tm+mt
source-wordcount: '500'
ht-degree: 5%

---

# PDFサービスAPIを使用したWordやPowerPointなどへのPDFの書き出し

![PDFのヒーロー画像を作成](assets/ExportPDF_hero.jpg)

Adobe PDF Services APIは、APIを使用して、PDFファイルをMS Office、テキスト、および画像に変換します。 コンテンツの編集や分析に既存のPDFを活用する一般的なユースケースは数多く存在します。PDFサービスを利用することで、API開発者は既存のシステムやアプリケーションにこの機能を簡単に組み込むことができます。 PDFファイルをMS Wordに変換すると、コンテンツの編集や承認を行えるほか、後で署名用に送信して、カスタム契約ワークフローを構築できます。 または、請求書や財務計算、データ分析のために、PDFのコンテンツをMS Excelフォーマットにエクスポートします。

エクスポート操作では、次のPDFファイル変換がサポートされます。

* Microsoft Word(DOC、DOCX)へのPDF
* Microsoft PowerPoint(PPTX)へのPDF
* Microsoft Excel (XLSX)へのPDF
* PDFテキスト変換(RTF)
* PDFから画像(JPEG、PNG)

このチュートリアルでは、Node.js、Java、および.Net言語用のサンプルファイルを使用して、最初のPDFサービスAPI書き出し操作を実行する方法の基本を学習します。

## 手順1：資格情報を作成して環境を設定する：

以下の入門チュートリアルを使用して、API資格情報を作成し、サンプルファイルをダウンロードして、環境を設定します。

[PDFサービスAPIおよびJavaの概要](gettingstartedjava.md)

[PDFサービスAPIおよび.Netの概要](gettingstartednet.md)

[PDFサービスAPIおよびNode.jsの概要](createpdffromhtml.md)

## 手順2：サンプルファイルを使用したPDFを書き出し操作を実行する

**Java**

1. コマンドプロンプトを開きます。

1. ディレクトリをサンプルコードディレクトリに変更します。

   例えば、 C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-java-samplesのように入力します。

1. 次のコマンドを実行します:

   `mvn -f pom.xml exec:java -Dexec.mainClass=com.adobe.platform.operation.samples.exportpdf.ExportPDFToDOCX`

PDFはsrc/main/resourcesディレクトリに作成されます。

**.Net**

1. コマンドプロンプトを開きます。

1. ディレクトリをサンプルコードディレクトリに変更します。

   例えば、 C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-NetSamplesのように入力します。

1. 再度ディレクトリをExportPDFtoDocxディレクトリに変更します。

1. 次のコマンドを実行します:

   `dotnet run ExportPDFToDocx.csproj`

PDFは同じディレクトリに作成されます。

**Node.js**

1. コマンドプロンプトを開きます。

1. ディレクトリをサンプルコードディレクトリに変更します。

   例えば、 C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samplesのように入力します。

1. 次のコマンドを実行します:

   `node src/ocr/ocr-pdf.js`

PDFは、出力で指定されている場所（デフォルトではpdfServicesSdkResultディレクトリ）に作成されます。

## 最後のアイデア

これで、既存のアプリケーションに読み込んで概念実証を開始できる作業例ができました。 各サンプルディレクトリには、PDFファイルをimage formatに書き出す別のサンプルが表示されます。 上記の同じ手順を使用すると、そのサンプルも実行できます。 別の形式に変更するには、コードを希望の新しい形式に更新します。

SupportedTargetFormats.PPTX

保存先の結果：

output/exportPdfOutput.PPTX

別の形式に変換します。

## リソースと次のステップ

* その他のヘルプとサポートについては、 [[!DNL Adobe Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) フォーラム

* PDFサービスAPI [文書](https://www.adobe.com/go/pdftoolsapi_doc)

* [FAQ](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) PDFサービスAPIに関する質問

* [お問い合わせ](https://www.adobe.com/go/pdftoolsapi_requestform) ライセンスと価格に関する質問

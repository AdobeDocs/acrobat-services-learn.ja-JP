---
title: Adobe PDFサービスAPIを使用したOCRPDFファイルの書き出し
description: OCR(Optical Character Recognition)を使用すると、スキャンした文字をロック解除して、テキストを抽出し、検索可能なファイルをPDFできます
type: Tutorial
role: Developer
level: Beginner
feature: PDF Services API
thumbnail: KT-6677.jpg
jira: KT-6677
keywords: Hero
exl-id: 61a9a2d1-94c3-41c2-8f90-a56a938ef245
source-git-commit: b65ffa3efa3978587564eb0be0c0e7381c8c83ab
workflow-type: tm+mt
source-wordcount: '600'
ht-degree: 4%

---

# Adobe PDFサービスAPIを使用したOCRPDFファイルの書き出し

![PDFのヒーロー画像を作成](assets/OCR_hero.jpg)

OCR(Optical Character Recognition)を使用すると、スキャンしたPDFをロック解除して、テキストを抽出し、検索可能なファイルを作成できます。 強力なクラウドベースのAPIを使用して任意の文書ワークフローにOCRを統合し、テキストのアーカイブ、コピー、検索可能な文書索引の作成に最適なソリューションを提供します。 スキャンされたPDF・リポジトリから検索可能なアーカイブを作成して、重要な情報のロックを解除し、迅速な検索性で時間を節約できます。 または、アップロードされたスキャンからPDFにOCRを適用して、オンボーディングワークフローで使用できるように編集します。

OCR用に提供されているサンプルファイルをすぐに実行できるため、開発者はわずか数分で作業を開始できます。

このチュートリアルでは、Node.js、Java、および.Net言語用のサンプルファイルを使用して、最初のPDFサービスAPI OCR操作を実行する方法の基本について説明します。

## 手順1：資格情報を作成して環境を設定する

以下の入門チュートリアルを使用して、API資格情報を作成し、サンプルファイルをダウンロードして、環境を設定します。

[PDFサービスAPIおよびJavaの概要](gettingstartedjava.md)

[PDFサービスAPIおよび.Netの概要](gettingstartednet.md)

[PDFサービスAPIおよびNode.jsの概要](createpdffromhtml.md)

## サンプルファイルにあるOCRの例を実行します。

このOCR操作では、デフォルトで英語のロケールが使用できますが、ドイツ語、フランス語、デンマーク語、 [その他の言語](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#ocr-with-explicit-language). デフォルトはen-usロケールです。

特定のロケールを含むOCR操作のオプションを渡す場合、メソッドは、次の2つのオプションを持つ&#39;type&#39;パラメーターも受け入れます。

* SEARCHABLE_IMAGE：クリーンアップ処理中（例えば、元の画像を表示しないテキストレイヤーを配置する前）に、元の画像を変更します。 このタイプでは、不要なアーティファクトが削除され、場合によっては文書が読みやすくなります。

* SEARCHABLE_IMAGE_EXACT:テキストが検索および選択可能であることを確認します。 このオプションを選択すると、元の画像が保持され、その上に非表示のテキストレイヤーが配置されます。 元の画像に対する忠実度を最大限に高める必要がある場合に推奨されます。

**Java**

1. コマンドプロンプトを開きます。

1. ディレクトリをサンプルコードディレクトリに変更します。

   例：C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-java-samples>。

1. 次のコマンドを実行します:

   `mvn -f pom.xml exec:java -Dexec.mainClass=com.adobe.platform.operation.samples.ocrpdf.OcrPDF`

PDFはsrc/main/resourcesディレクトリに作成されます。

**.Net**

1. コマンドプロンプトを開きます。

1. ディレクトリをサンプルコードディレクトリに変更します。

   例： C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-NetSamples

1. ディレクトリをOcrPDFディレクトリにもう一度変更します。

1. 次のコマンドを実行します:

   `dotnet run OcrPDF.csproj`

PDFは同じディレクトリに作成されます。

**Node.js**

1. コマンドプロンプトを開きます。

1. ディレクトリをサンプルコードディレクトリに変更します。

   例： C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples

1. 次のコマンドを実行します:

   `node src/ocr/ocr-pdf.js`

PDFは、出力で指定された場所（デフォルトでは出力ディレクトリ）に作成されます。

## 最後のアイデア

サンプルファイルを使用するこれらの簡単な手順では、上に構築できる作業例が必要です。 このチュートリアルで使用したOCRの例に加えて、前述のサポートされているタイプとロケールのオプションを使用してOCRを行う別の例もあります。

ここから、サンプルにある入力ファイルと出力ファイルを置き換えるだけで、独自のPDFを使用して、独自のユースケースの概念実証を完成させることができます。

![Proof of Concept](assets/OCR_poc.png)

## リソースと次のステップ

* その他のヘルプとサポートについては、Adobeを参照してください [[!DNL Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) フォーラム

* PDFサービスAPI [文書](https://www.adobe.com/go/pdftoolsapi_doc)

* [FAQ](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) PDFサービスAPIに関する質問

* [お問い合わせ](https://www.adobe.com/go/pdftoolsapi_requestform) ライセンスと価格に関する質問

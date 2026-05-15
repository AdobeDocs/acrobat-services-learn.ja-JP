---
title: Adobe PDFサービスAPIを使用したOCRPDFファイルの書き出し
description: OCR(Optical Character Recognition)を使用すると、スキャンした文字をロック解除して、テキストを抽出し、検索可能なファイルをPDFできます
feature: PDF Services API
role: Developer
level: Beginner
type: Tutorial
jira: KT-6677
thumbnail: KT-6677.jpg
exl-id: 61a9a2d1-94c3-41c2-8f90-a56a938ef245
TQID: https://experienceleague.adobe.com/5FjC4a9OBqfo7jiA1lHFkN5r1w2PtrG13T1qhpBJjis
product_v2:
  - id: acdc2bde-2937-4877-90d9-031dd66278c9
feature_v2:
  - id: b1809bd0-a86b-4991-8083-2e3b517fc3b8
subfeature_v2:
  - id: c6f72a9c-54c4-4933-93c9-d7c656ff1f14
role_v2:
  - id: ff6a42d2-313e-452e-93a6-792e4fad9ff8
level_v2:
  - id: e8ccd51f-da0d-4e3b-939b-e30d5ebb1ea5
source-git-commit: 0110d2606056220c4236fe2f0e3afbfc112746e7
workflow-type: tm+mt
source-wordcount: 626
ht-degree: 1%

---

# Adobe PDFサービスAPIを使用したOCRPDFファイルの書き出し

![PDFのヒーロー画像の作成](assets/OCR_hero.jpg)

OCR(Optical Character Recognition)を使用すると、スキャンしたPDFをロック解除して、テキストを抽出し、検索可能なファイルを作成できます。 強力なクラウドベースのAPIを使用して任意の文書ワークフローにOCRを統合し、テキストのアーカイブ、コピー、検索可能な文書索引の作成に最適なソリューションを提供します。 スキャンされたPDF・リポジトリから検索可能なアーカイブを作成して、重要な情報のロックを解除し、迅速な検索性で時間を節約できます。 または、アップロードされたスキャンからPDFにOCRを適用して、オンボーディングワークフローで使用できるように編集します。

OCR用に提供されているサンプルファイルをすぐに実行できるため、開発者はわずか数分で作業を開始できます。

このチュートリアルでは、Node.js、Java、および.Net言語用のサンプルファイルを使用して、最初のPDFサービスAPI OCR操作を実行する方法の基本について説明します。

## 手順1：資格情報を作成して環境を設定する

以下の入門チュートリアルを使用して、API資格情報を作成し、サンプルファイルをダウンロードして、環境を設定します。

[PDFサービスAPIおよびJavaの概要](gettingstartedjava.md)

[PDFサービスAPIおよび.Netの概要](gettingstartednet.md)

[PDFサービスAPIおよびNode.jsの概要](createpdffromhtml.md)

## サンプルファイルにあるOCRの例を実行します。

このOCR操作では、デフォルトで英語ロケールが使用できますが、ドイツ語、フランス語、デンマーク語および[その他の言語](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#ocr-with-explicit-language)もサポートされます。 デフォルトはen-usロケールです。

特定のロケールを含むOCR操作のオプションを渡す場合、メソッドは、次の2つのオプションを持つ&#39;type&#39;パラメーターも受け入れます。

* SEARCHABLE_IMAGE：クリーンアップ処理中（例えば、元の画像を表示しないテキストレイヤーを配置する前）に、元の画像を変更します。 このタイプでは、不要なアーティファクトが削除され、場合によっては文書が読みやすくなります。

* SEARCHABLE_IMAGE_EXACT:テキストが検索および選択可能であることを確認します。 このオプションを選択すると、元の画像が保持され、その上に非表示のテキストレイヤーが配置されます。 元の画像に対する忠実度を最大限に高める必要がある場合に推奨されます。

**Java**

1. コマンドプロンプトを開きます。

1. ディレクトリをサンプルコードディレクトリに変更します。

   例：C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-java-samples>。

1. 次のコマンドを実行します。

   `mvn -f pom.xml exec:java -Dexec.mainClass=com.adobe.platform.operation.samples.ocrpdf.OcrPDF`

PDFはsrc/main/resourcesディレクトリに作成されます。

**.Net**

1. コマンドプロンプトを開きます。

1. ディレクトリをサンプルコードディレクトリに変更します。

   例： C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-NetSamples

1. ディレクトリをOcrPDFディレクトリにもう一度変更します。

1. 次のコマンドを実行します。

   `dotnet run OcrPDF.csproj`

PDFは同じディレクトリに作成されます。

**Node.js**

1. コマンドプロンプトを開きます。

1. ディレクトリをサンプルコードディレクトリに変更します。

   例： C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples

1. 次のコマンドを実行します。

   `node src/ocr/ocr-pdf.js`

PDFは、出力で指定された場所（デフォルトでは出力ディレクトリ）に作成されます。

## 最後のアイデア

サンプルファイルを使用するこれらの簡単な手順では、上に構築できる作業例が必要です。 このチュートリアルで使用したOCRの例に加えて、前述のサポートされているタイプとロケールのオプションを使用してOCRを行う別の例もあります。

ここから、サンプルにある入力ファイルと出力ファイルを置き換えるだけで、独自のPDFを使用して、独自のユースケースの概念実証を完成させることができます。

![概念実証](assets/OCR_poc.png)

## リソースと次のステップ

* その他のヘルプとサポートについては、[[!DNL Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&sort=latest_replies&filter=all) Adobeフォーラムにアクセスしてください

* PDFサービスAPI [ドキュメント](https://www.adobe.com/go/pdftoolsapi_doc)

* PDFサービスAPIの質問に関する[FAQ](https://community.adobe.com/t5/contentarchivals/contentarchivedpage/message-uid/10726197)

* ライセンスと価格に関する質問については、[お問い合わせ](https://www.adobe.com/go/pdftoolsapi_requestform)ください

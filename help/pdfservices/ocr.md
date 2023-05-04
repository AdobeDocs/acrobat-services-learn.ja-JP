---
title: Adobe PDF Services API を使用した OCRPDFファイル
description: OCR（光学式文字認識）を使用すると、スキャンしたPDFのロックを解除してテキストを抽出し、検索可能なファイルを作成できます
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-6677.jpg
kt: 6677
keywords: 英雄
exl-id: 61a9a2d1-94c3-41c2-8f90-a56a938ef245
source-git-commit: c1937561d607f1eabbc1921d6090858abb13f0d3
workflow-type: tm+mt
source-wordcount: '600'
ht-degree: 4%

---

# Adobe PDF Services API を使用した OCRPDFファイル

![PDFHero 画像の作成](assets/OCR_hero.jpg)

OCR（光学文字認識）を使用すると、スキャンした文字のロックを解除してテキストを抽出し、PDF可能なファイルを作成することができます。 アドビの強力なクラウドベースの API を使用して OCR を文書ワークフローに統合すれば、アーカイブ、テキストのコピー、検索可能な文書インデックスの作成などの完璧なソリューションが実現します。 スキャンしたPDF・リポジトリから検索可能なアーカイブを作成して、重要な情報のロックを解除し、迅速な検索機能で時間を節約します。 または、アップロードしたスキャンからPDFに OCR を適用し、オンボーディングワークフローで使用できるように編集できるようにします。

開発者は、OCR 用に用意されたサンプルファイルを実行できる状態で、ほんの数分で開始できます。

このチュートリアルでは、Node.js、Java および.Net 言語のサンプル・ファイルを使用して、最初のPDF・サービス API OCR 操作を実行する方法の基本について説明します。

## 手順 1:資格情報を作成し、環境を設定します

以下の入門チュートリアルを使用して、API 資格情報の作成、サンプルファイルのダウンロード、環境の設定を行います。

[PDFサービス API と Java 入門](gettingstartedjava.md)

[PDFサービス APIと.Net 入門](gettingstartednet.md)

[PDFサービス API と Node.js 入門](createpdffromhtml.md)

## サンプルファイルに含まれている OCR の例を実行します

この OCR 操作では、デフォルトで英語ロケールが使用できますが、ドイツ語、フランス語、デンマーク語および [その他の言語](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#ocr-with-explicit-language)を選択します。 デフォルトは en-us ロケールです。

特定のロケールを含む OCR 操作でオプションを渡す場合、メソッドは 2 つのオプションを持つ「type」パラメーターも受け取ります。

* SEARCHABLE_IMAGE:元の画像の上に非表示のテキストレイヤーを配置する前に、クリーンアップ処理中に元の画像を変更します（ゆがみの除去など）。 このタイプでは、不要なアーティファクトが削除され、場合によっては読みやすい文書になる可能性があります。

* SEARCHABLE_IMAGE_EXACT:テキストを検索および選択できるようにします。 このオプションでは、元の画像が保持され、その上に非表示のテキストレイヤーが配置されます。 元の画像を忠実に再現する必要がある場合にお勧めします。

**Java**

1. コマンドプロンプトを開きます。

1. サンプルコードディレクトリにディレクトリを変更します。

   例：C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-java-samples>

1. 次のコマンドを実行します:

   `mvn -f pom.xml exec:java -Dexec.mainClass=com.adobe.platform.operation.samples.ocrpdf.OcrPDF`

PDFは src/main/resources ディレクトリに作成されます。

**.Net**

1. コマンドプロンプトを開きます。

1. サンプルコードディレクトリにディレクトリを変更します。

   例：C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-NetSamples

1. ディレクトリを OcrPDF ディレクトリに再度変更します。

1. 次のコマンドを実行します:

   `dotnet run OcrPDF.csproj`

PDFは同じディレクトリに作成されます。

**Node.js**

1. コマンドプロンプトを開きます。

1. サンプルコードディレクトリにディレクトリを変更します。

   例：C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples

1. 次のコマンドを実行します:

   `node src/ocr/ocr-pdf.js`

PDFは、出力で指定された場所（デフォルトでは出力ディレクトリ）に作成されます。

## 結論

サンプルファイルを使用するこれらの簡単な手順で、実際に動作するサンプルを作成できます。 このチュートリアルで使用した OCR の例に加えて、前述のサポートされているタイプとロケールオプションを使用した OCR の例もあります。

ここから、サンプルにある入力ファイルと出力ファイルを置き換えて、独自のPDFを使用し、独自のユースケースの概念証明を完成させることができます。

![概念実証](assets/OCR_poc.png)

## リソースと次のステップ

* 追加のヘルプとサポートについては、Adobe [[!DNL Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) コミュニティフォーラム

* PDFサービス API [文書](https://www.adobe.com/go/pdftoolsapi_doc)

* [FAQ](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) PDFサービス API の質問

* [お問い合わせ](https://www.adobe.com/go/pdftoolsapi_requestform) ライセンスと価格に関する質問

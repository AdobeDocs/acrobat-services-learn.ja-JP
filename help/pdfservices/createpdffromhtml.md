---
title: PDFサービス API と Node.js を使用して、HTMLまたは MS Office から数分でPDFを作成できます
description: PDFサービス API には、PDFの作成と操作、PDFから MS Office やその他の形式への書き出しに使用できるサービスがいくつかあります
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-6673.jpg
jira: KT-6673
exl-id: 1bd01bb8-ca5e-4a4a-8646-3d97113e2c51
source-git-commit: 2d1151c17dfcfa67aca05411976f4ef17adf421b
workflow-type: tm+mt
source-wordcount: '711'
ht-degree: 0%

---

# PDFサービス API と Node.js を使用して、HTMLまたは MS Office から数分でPDFを作成できます

![PDFHero 画像の作成](assets/createpdffromhtml_hero.jpg)

新しいAdobe PDFサービス API により、ドキュメントワークフローのデジタル化がかつてないほど容易になりました。この API では、開発者が複数の強力なPDF操作サービスから選択して、複雑なビジネスワークフローのニーズを満たすことができます。 これらのクラウドベースの Web サービスを利用すれば、複雑なアーキテクチャ、実装戦略、テクノロジーの強化を合理化できます。

PDFサービス API には、PDFの作成と操作、PDFから MS Office やその他の形式への書き出しに使用できるサービスがいくつかあります。

* 静的または動的PDF、MS Word、PowerPoint、Excel などからHTMLファイルを作成できます
* MS Word、PowerPoint、Excel などへのExport PDF
* 文書ファイル内のテキストを認識し、PDF検索を可能にする OCR
* Protectで文書を開くときにPDFにパスワードを設定
* PDFページまたはPDF文書を 1 つのPDFに
* PDFを圧縮して、電子メールまたはオンラインで共有するためのサイズを縮小する
* リニア化してPDFを最適化し、Web ですばやく表示
* 挿入、置換、並べ替え、削除、回転サービスを使用したPDFページの整理

開発者は、利用可能なすべての Web サービスにアクセスするために提供されるサンプルファイルを実行する準備が整っていれば、ほんの数分で開始できます。 手順は以下のとおりです。

## 資格情報の取得とサンプルファイルのダウンロード

最初の手順は、使用をロック解除するための資格情報（API キー）を取得することです。 [無料体験版の新規登録はこちら](https://www.adobe.com/go/dcsdks_credentials) 「開始」をクリックして、新しい資格情報を作成します。

![API キー](assets/apikey.png)

無料体験版に新規登録するには、「個人アカウント」を選択することが重要です。

![個人アカウント](assets/personalaccount.png)

次の手順では、PDFサービス API サービスを選択し、資格情報の名前と説明を追加します。

「パーソナライズされたコードサンプルを作成」チェックボックスがあります。 このオプションを選択すると、新しい資格情報がサンプルファイルに自動的に追加され、手動の手順はスキップされます。

次に、Node.js を言語として選択し、Node.js 固有のサンプルを受け取り、「資格情報を作成」ボタンをクリックします。

![資格情報の作成](assets/createcredentials.png)

ダウンロード用の.zip ファイル (PDFToolsSDK-Node.jsSamples.zip) が提供されます。このファイルはローカルファイルシステムに保存できます。

## コードサンプルへの資格情報の追加

「パーソナライズされたコードサンプルを作成」オプションを選択した場合は、クライアント ID をコードサンプルファイルに手動で追加する必要はありません。次の手順をスキップして、以下の「コードサンプルの実行」セクションに直接移動できます。

「パーソナライズされたコードサンプルを作成」オプションを選択しなかった場合は、AdobeWeb コンソールからクライアント ID（API キー）をコピーする必要があります。

![コードサンプル](assets/codesample.png)

PDFToolsSDK-Node.jsSamples.zip の内容を解凍します。

adobe-dc-pdf-tools-sdk-node-samples フォルダーの下のルートディレクトリに移動します。

テキストエディタまたは IDE で pdftools-api-credentials.json を開きます。

コードのクライアント ID のフィールドに資格情報を貼り付けます。

```javascript
{
 "client_credentials": {
  "client_id": "abcdefghijklmnopqrstuvwxyz",
```

ファイルを保存し、次の手順に進んでコードサンプルを実行します。

## 最初のコードサンプルの実行

コマンドプロンプトを使用して、 adobe-dc-pdf-tools-sdk-node-samples フォルダーの下のルートディレクトリに移動します。

「 npm install:

C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples>npm install

これで、サンプルファイルを実行する準備ができました。

最初のサンプルとして、次のPDFを作成します。

コマンドプロンプトで、次のコマンドを使用して createPDFサンプルを実行します。

C:\Temp\PDFToolsAPI\adobe-dc-pdf-tools-sdk-node-samples>node src/createpdf/create-pdf-from-docx.js

出力例：

![出力例](assets/exampleoutput.png)

PDFは、出力で指定された場所（デフォルトでは pdfServicesSdkResult ディレクトリ）に作成されます。

## リソースと次のステップ

* 追加のヘルプとサポートについては、Adobe [[!DNL Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) コミュニティフォーラム

PDFサービス API [文書](https://www.adobe.com/go/pdftoolsapi_doc)

* [FAQ](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) PDFサービス API の質問

* [お問い合わせ](https://www.adobe.com/go/pdftoolsapi_requestform) ライセンスと価格に関する質問

* 関連記事:
  [新しいPDFサービス API では、ドキュメントワークフローにさらに多くの機能が提供されます](https://community.adobe.com/t5/document-services-apis/new-pdf-tools-api-brings-more-capabilities-for-document-services/m-p/11294170)

  [7 月リリース [!DNL Adobe Acrobat Services]:PDFの埋め込みとPDFサービス](https://medium.com/adobetech/july-release-of-adobe-document-services-pdf-embed-and-pdf-tools-17211bf7776d)

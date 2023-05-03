---
title: Adobe PDF Services API と Java の概要
description: 開発者は、利用可能なすべての Web サービスにアクセスするために提供されるサンプルファイルを実行する準備が整っていれば、ほんの数分で開始できます
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-6676.jpg
kt: 6676
exl-id: 4a8f2119-c464-496b-bdc8-35dd387bef25
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '501'
ht-degree: 0%

---

# Adobe PDF Services API と Java の概要

![PDFHero 画像の作成](assets/GettingStartedJava_hero.jpg)

開発者は、利用可能なすべての Web サービスにアクセスするために提供されるサンプルファイルを実行する準備が整っていれば、ほんの数分で開始できます。 このチュートリアルでは、PDFサービス Java SDK を使用してサンプルの実行を開始するすべての手順を説明します。

## 手順 1:資格情報の取得とサンプルファイルのダウンロード

最初の手順は、使用をロック解除するための資格情報（API キー）を取得することです。 [無料体験版の新規登録はこちら](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 「開始」をクリックして、新しい資格情報を作成します。

![手順 1](assets/GettingStartedJava_step1.png)

無料体験版に新規登録するには、「個人アカウント」を選択することが重要です。

![個人用](assets/GettingStartedJava_personal.png)

次の手順では、PDFサービス API サービスを選択し、資格情報の名前と説明を追加します。

「パーソナライズされたコードサンプルを作成」チェックボックスがあります。 新しい資格情報をサンプルファイルに自動的に追加する場合は、このオプションを選択します。これにより、プロジェクトに資格情報を追加する手作業が省けます。

次に、Java 固有のサンプルを受け取る言語として Java を選択し、「資格情報を作成」ボタンをクリックします。

![資格情報](assets/GettingStartedJava_credentials.png)

ダウンロード用の.zip ファイル (PDFToolsSDK-JavaSamples.zip) が提供されます。このファイルはローカルファイルシステムに保存できます。

## 手順 2:Java 環境の設定

1. Install [Java 8 以上](https://www.oracle.com/java/technologies/javase-downloads.html) まだお持ちでない場合は、
1. 実行 `javac -version` 」をクリックして、インストールを確認します。
1. JDK の bin フォルダーが PATH 変数に含まれていることを確認します（方法は OS によって異なります）。
1. Install [Maven](https://maven.apache.org/install.html) お好みのツールをまだ使用していない場合は、使用します。

パーソナライズされたサンプルは、実行可能なサンプルコード、埋め込まれた資格情報 json ファイル、事前設定された接続から依存関係まで、あらゆるものを提供します。

1. ダウンロード [サンプルプロジェクト](https://github.com/adobe/pdftools-java-sdk-samples)を選択します。
1. Maven でサンプル・プロジェクトを構築します。mvn クリーンインストール。
1. コマンドラインまたは任意の IDE でサンプルコードをテストします。

## 結論

PDFサービス API は、一般的なワークフローを自動化し、処理負荷をクラウドに移行することで、手動プロセスを排除するのに役立ちます。 PDFの取り扱いがブラウザーごとに異なる現代において、Adobe PDF Embed API とPDFサービス API を組み合わせることで、正常に実行され表示される、合理化され、信頼性が高く、予測可能なプロセスを構築できます **毎回** プラットフォームやデバイスを問いません。

## リソースと次のステップ

* 追加のヘルプとサポートについては、Adobe [[!DNL Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) コミュニティフォーラム

* PDFサービス API [文書](https://www.adobe.com/go/pdftoolsapi_doc)

* [FAQ](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) PDFサービス API の質問

* [お問い合わせ](https://www.adobe.com/go/pdftoolsapi_requestform) ライセンスと価格に関する質問

* 関連記事

   [新しいPDFサービス API では、ドキュメントワークフローにさらに多くの機能が提供されます](https://community.adobe.com/t5/document-services-apis/new-pdf-tools-api-brings-more-capabilities-for-document-services/m-p/11294170)

   [7 月リリース [!DNL Adobe Acrobat Services]:PDFの埋め込みとPDFサービス](https://medium.com/adobetech/july-release-of-adobe-document-services-pdf-embed-and-pdf-tools-17211bf7776d)

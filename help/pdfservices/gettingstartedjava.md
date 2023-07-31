---
title: Adobe PDF Services APIおよびJavaの概要
description: 利用可能なすべてのWebサービスにアクセスできるように用意されたサンプルファイルを、開発者はわずか数分で開始できます
type: Tutorial
role: Developer
level: Beginner
feature: PDF Services API
thumbnail: KT-6676.jpg
kt: 6676
exl-id: 4a8f2119-c464-496b-bdc8-35dd387bef25
source-git-commit: b65ffa3efa3978587564eb0be0c0e7381c8c83ab
workflow-type: tm+mt
source-wordcount: '501'
ht-degree: 0%

---

# Adobe PDF Services APIおよびJavaの概要

![PDFのヒーロー画像を作成](assets/GettingStartedJava_hero.jpg)

利用可能なすべてのWebサービスにアクセスできるように用意されたサンプルファイルを、開発者はわずか数分で開始できます。 このチュートリアルでは、PDFサービスJava SDKを使用してサンプルの実行を開始するためのすべての手順について説明します。

## 手順1：資格情報の取得とサンプルファイルのダウンロード

最初の手順は、使用のロックを解除するための資格情報（APIキー）を取得することです。 [こちらから無料体験版に新規登録](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 「Get Started」をクリックして、新しい資格情報を作成します。

![手順 1](assets/GettingStartedJava_step1.png)

「個人アカウント」を選択して無料体験版に登録することは重要です。

![個人](assets/GettingStartedJava_personal.png)

次の手順では、PDFサービスAPIサービスを選択し、資格情報の名前と説明を追加します。

「パーソナライズされたコードサンプルを作成」チェックボックスがあります。 このオプションを選択すると、新しい資格情報がサンプルファイルに自動的に追加され、プロジェクトに手動で追加する手順が保存されます。

次に、Java固有のサンプルを受け取る言語としてJavaを選択し、「資格情報の作成」ボタンをクリックします。

![資格情報](assets/GettingStartedJava_credentials.png)

ダウンロードする.zipファイルはPDFToolsSDK-JavaSamples.zipと呼ばれ、ローカルファイルシステムに保存できます。

## 手順2: Java環境を設定する

1. インストール [Java 8以上](https://www.oracle.com/java/technologies/javase-downloads.html) まだお持ちでない場合に
1. 実行 `javac -version` をクリックして、インストールを確認します。
1. JDK binフォルダーがPATH変数に含まれていることを確認します（方法はOSによって異なります）。
1. インストール [Maven](https://maven.apache.org/install.html) 使用していない場合は、お好みのツールを使用します。

パーソナライズされたサンプルは、すぐに実行できるサンプルコード、埋め込まれた資格情報のjsonファイル、事前設定された接続から依存関係まで、あらゆるものを提供します。

1. ダウンロード [サンプルプロジェクト](https://github.com/adobe/pdftools-java-sdk-samples).
1. Mavenでサンプルプロジェクトをビルドします。 mvn clean install.
1. コマンドラインまたは任意のIDEでサンプルコードをテストします。

## 最後のアイデア

PDFサービスAPIは、一般的なワークフローを自動化し、処理負荷をクラウドに移行することで、手動プロセスを排除するのに役立ちます。 Adobe PDF Embed APIをBrowser Services APIと共に使用すると、PDFの処理がPDFごとに異なる場合に、効率的で信頼性が高く、予測可能なプロセスを構築して、正しく実行および表示することができます **毎回** プラットフォームまたはデバイスに関係なく

## リソースと次のステップ

* その他のヘルプとサポートについては、Adobeを参照してください [[!DNL Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) フォーラム

* PDFサービスAPI [文書](https://www.adobe.com/go/pdftoolsapi_doc)

* [FAQ](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) PDFサービスAPIに関する質問

* [お問い合わせ](https://www.adobe.com/go/pdftoolsapi_requestform) ライセンスと価格に関する質問

* 関連記事

  [新しいPDFサービスAPIは、文書ワークフロー用にさらに多くの機能を提供します](https://community.adobe.com/t5/document-services-apis/new-pdf-tools-api-brings-more-capabilities-for-document-services/m-p/11294170)

  [7月リリース [!DNL Adobe Acrobat Services]:PDFの埋め込みおよびPDFサービス](https://medium.com/adobetech/july-release-of-adobe-document-services-pdf-embed-and-pdf-tools-17211bf7776d)

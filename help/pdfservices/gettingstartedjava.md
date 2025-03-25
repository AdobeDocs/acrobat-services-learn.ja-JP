---
title: Adobe PDF Services APIおよびJavaの概要
description: 利用可能なすべてのWebサービスにアクセスできるように用意されたサンプルファイルを、開発者はわずか数分で開始できます
feature: PDF Services API
role: Developer
level: Beginner
type: Tutorial
jira: KT-6676
thumbnail: KT-6676.jpg
exl-id: 4a8f2119-c464-496b-bdc8-35dd387bef25
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '457'
ht-degree: 0%

---

# Adobe PDF Services APIおよびJavaの概要

![PDFのヒーロー画像の作成](assets/GettingStartedJava_hero.jpg)

利用可能なすべてのWebサービスにアクセスできるように用意されたサンプルファイルを、開発者はわずか数分で開始できます。 このチュートリアルでは、PDFサービスJava SDKを使用してサンプルの実行を開始するためのすべての手順について説明します。

## 手順1：資格情報の取得とサンプルファイルのダウンロード

最初の手順は、使用のロックを解除するための資格情報（APIキー）を取得することです。 [こちらから無料体験版に登録](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)し、[使用を開始]をクリックして新しい資格情報を作成してください。

![手順 1](assets/GettingStartedJava_step1.png)

「個人アカウント」を選択して無料体験版に登録することは重要です。

![個人](assets/GettingStartedJava_personal.png)

次の手順では、PDFサービスAPIサービスを選択し、資格情報の名前と説明を追加します。

「パーソナライズされたコードサンプルを作成」チェックボックスがあります。 このオプションを選択すると、新しい資格情報がサンプルファイルに自動的に追加され、プロジェクトに手動で追加する手順が保存されます。

次に、Java固有のサンプルを受け取る言語としてJavaを選択し、「資格情報の作成」ボタンをクリックします。

![資格情報](assets/GettingStartedJava_credentials.png)

ダウンロードする.zipファイルはPDFToolsSDK-JavaSamples.zipと呼ばれ、ローカルファイルシステムに保存できます。

## 手順2: Java環境を設定する

1. [Java 8以上](https://www.oracle.com/java/technologies/javase-downloads.html)をお持ちでない場合は、インストールしてください。
1. `javac -version`を実行してインストールを確認してください。
1. JDK binフォルダーがPATH変数に含まれていることを確認します（方法はOSによって異なります）。
1. ご希望のツールを使用して[Maven](https://maven.apache.org/install.html)をインストールしてください（まだインストールしていない場合）。

パーソナライズされたサンプルは、すぐに実行できるサンプルコード、埋め込まれた資格情報のjsonファイル、事前設定された接続から依存関係まで、あらゆるものを提供します。

1. [サンプルプロジェクト](https://github.com/adobe/pdftools-java-sdk-samples)をダウンロードします。
1. Mavenでサンプルプロジェクトをビルドします。 mvn clean install.
1. コマンドラインまたは任意のIDEでサンプルコードをテストします。

## 最後のアイデア

PDFサービスAPIは、一般的なワークフローを自動化し、処理負荷をクラウドに移行することで、手動プロセスを排除するのに役立ちます。 Adobe PDF Embed APIをBrowser Services APIと共に利用することで、PDFの扱いがブラウザーによって異なる場合は、PDFやデバイスに関係なく、**毎回**&#x200B;正しく実行され、表示される、合理的で信頼性の高い予測可能なプロセスを構築できます。

## リソースと次のステップ

* その他のヘルプとサポートについては、[[!DNL Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) Adobeフォーラムにアクセスしてください

* PDFサービスAPI [ドキュメント](https://www.adobe.com/go/pdftoolsapi_doc)

* PDFサービスAPIの質問に関する[FAQ](https://community.adobe.com/t5/contentarchivals/contentarchivedpage/message-uid/10726197)

* ライセンスと価格に関する質問については、[お問い合わせ](https://www.adobe.com/go/pdftoolsapi_requestform)ください

* 関連記事

  [新しいPDFサービスAPIは、文書ワークフロー用にさらに多くの機能を提供します](https://community.adobe.com/t5/acrobat-services-api-discussions/new-pdf-tools-api-brings-more-capabilities-for-document-services/m-p/11294170)

  [7月リリース [!DNL Adobe Acrobat Services]: PDFの埋め込みとPDFのサービス](https://medium.com/adobetech/july-release-of-adobe-document-services-pdf-embed-and-pdf-tools-17211bf7776d)

---
title: Adobe PDF Services APIおよび.Netの概要
description: 利用可能なすべてのWebサービスにアクセスできるように用意されたサンプルファイルを、開発者はわずか数分で開始できます
type: Tutorial
role: Developer
level: Beginner
feature: PDF Services API
thumbnail: KT-6675.jpg
jira: KT-6675
keywords: おすすめ
exl-id: 22c59c75-fd99-4467-a6f6-917fb246469a
source-git-commit: b65ffa3efa3978587564eb0be0c0e7381c8c83ab
workflow-type: tm+mt
source-wordcount: '524'
ht-degree: 0%

---

# Adobe PDF Services APIおよび.Netの概要

![PDFのヒーロー画像を作成](assets/GettingStartedJava_hero.jpg)

利用可能なすべてのWebサービスにアクセスできるように用意されたサンプルファイルを、開発者はわずか数分で開始できます。 このチュートリアルでは、PDFサービス.Net SDKを使用してサンプルの実行を開始するためのすべての手順を説明します。

## 手順1：資格情報の取得とサンプルファイルのダウンロード

最初の手順は、使用のロックを解除するための資格情報（APIキー）を取得することです。 [こちらから無料体験版に新規登録](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 「Get Started」をクリックして、新しい資格情報を作成します。

![手順 1](assets/GettingStartedJava_step1.png)

「個人アカウント」を選択して無料体験版に登録することは重要です。

![個人](assets/GettingStartedJava_personal.png)

次の手順では、PDFサービスAPIサービスを選択し、資格情報の名前と説明を追加します。

「パーソナライズされたコードサンプルを作成」チェックボックスがあります。 このオプションを選択すると、新しい資格情報がサンプルファイルに自動的に追加され、プロジェクトに手動で追加する手順が保存されます。

次に、Node.js固有のサンプルを受け取る言語としてNode.jsを選択し、「資格情報の作成」ボタンをクリックします。

![資格情報](assets/GettingStartedJava_credentials.png)

ダウンロードする.zipファイルはPDFToolsSDK-.NetSamples.zipと呼ばれ、ローカルファイルシステムに保存できます。

## 手順2:.NET環境をセットアップし、サンプルコードを実行する

1. ダウンロードしてインストール [.Net SDK](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)
1. ダウンロードした **[!UICONTROL PDFToolsSDK-.NetSamples.zip]** コンテンツを解凍します
1. cdをサンプルのルートディレクトリに移動します。 **[!UICONTROL adobe-DC.PDFTools.SDK.NET.Samples]**
1. サンプルのルートディレクトリから、 `dotnet build`

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples>dotnet build

   これで、サンプルファイルを実行する準備ができました。

   次の最後の手順では、「WordからPDFを作成」操作で最初のサンプルを実行する方法を示します。

1. サンプルのルートディレクトリにあるディレクトリをCreatePDFFromDocxフォルダーに移動し、 cd CreatePDFFromDocx/

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples>cd CreatePDFFromDocx/

1. 実行 `dotnet run CreatePDFFromDocx.csproj`

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples\CreatePDFFromDocx>dotnet run CreatePDFFromDocx.csproj

PDFは、出力で指定された場所に作成されます。デフォルトでは同じフォルダーです。

## 最後のアイデア

PDFサービスAPIは、一般的なワークフローを自動化し、処理負荷をクラウドに移行することで、手動プロセスを排除するのに役立ちます。 Adobe PDF Embed APIをBrowser Services APIと共に使用すると、PDFの処理がPDFごとに異なる場合に、効率的で信頼性が高く、予測可能なプロセスを構築して、正しく実行および表示することができます **毎回** プラットフォームまたはデバイスに関係なく

## リソースと次のステップ

* その他のヘルプとサポートについては、 [[!DNL Adobe Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) フォーラム

* PDFサービスAPI [文書](https://www.adobe.com/go/pdftoolsapi_doc)

* [FAQ](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) PDFサービスAPIに関する質問

* [お問い合わせ](https://www.adobe.com/go/pdftoolsapi_requestform) ライセンスと価格に関する質問

* 関連記事

  [新しいPDFサービスAPIは、文書ワークフロー用にさらに多くの機能を提供します](https://community.adobe.com/t5/document-services-apis/new-pdf-tools-api-brings-more-capabilities-for-document-services/m-p/11294170)

  [7月リリース [!DNL Adobe Acrobat Services]:PDFの埋め込みおよびPDFサービス](https://medium.com/adobetech/july-release-of-adobe-document-services-pdf-embed-and-pdf-tools-17211bf7776d)

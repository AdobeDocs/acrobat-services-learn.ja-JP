---
title: Adobe PDF Services APIと.Net の概要
description: 開発者は、利用可能なすべての Web サービスにアクセスするために提供されるサンプルファイルを実行する準備が整っていれば、ほんの数分で開始できます
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-6675.jpg
kt: 6675
keywords: おすすめ
exl-id: 22c59c75-fd99-4467-a6f6-917fb246469a
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '524'
ht-degree: 0%

---

# Adobe PDF Services APIと.Net の概要

![PDFHero 画像の作成](assets/GettingStartedJava_hero.jpg)

開発者は、利用可能なすべての Web サービスにアクセスするために提供されるサンプルファイルを実行する準備が整っていれば、ほんの数分で開始できます。 このチュートリアルでは、PDFサービス.Net SDK を使用してサンプルを実行するすべての手順を説明します。

## 手順 1:資格情報の取得とサンプルファイルのダウンロード

最初の手順は、使用をロック解除するための資格情報（API キー）を取得することです。 [無料体験版の新規登録はこちら](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 「開始」をクリックして、新しい資格情報を作成します。

![手順 1](assets/GettingStartedJava_step1.png)

無料体験版に新規登録するには、「個人アカウント」を選択することが重要です。

![個人用](assets/GettingStartedJava_personal.png)

次の手順では、PDFサービス API サービスを選択し、資格情報の名前と説明を追加します。

「パーソナライズされたコードサンプルを作成」チェックボックスがあります。 新しい資格情報をサンプルファイルに自動的に追加する場合は、このオプションを選択します。これにより、プロジェクトに資格情報を追加する手作業が省けます。

次に、Node.js を言語として選択し、Node.js 固有のサンプルを受け取り、「資格情報を作成」ボタンをクリックします。

![資格情報](assets/GettingStartedJava_credentials.png)

ダウンロード用の.zip ファイル (PDFToolsSDK-.NetSamples.zip) が提供されます。このファイルはローカルファイルシステムに保存できます。

## 手順 2:.Net 環境を設定し、サンプルコードを実行します

1. ダウンロードして [.Net SDK](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)
1. ダウンロードした **[!UICONTROL PDFToolsSDK-.NetSamples.zip]** を選択し、内容を解凍します
1. cd を samples ルート・ディレクトリに移動 **[!UICONTROL adobe-DC.PDFTools.SDK.NET.Samples]**
1. samples ルートディレクトリから、次を実行します。 `dotnet build`

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples>dotnet build

   これで、サンプルファイルを実行する準備ができました。

   最後に、「Word からPDFを作成」操作を使用して最初のサンプルを実行する方法を説明します。

1. サンプルルートディレクトリからディレクトリを CreatePDFFromDocx フォルダーに変更して、 cd CreatePDFFromDocx/

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples>cd CreatePDFFromDocx/

1. 走る `dotnet run CreatePDFFromDocx.csproj`

   C:\Temp\PDFToolsAPI\ PDFToolsSDK-.NetSamples\adobe-DC.PDFTools.SDK.NET.Samples\CreatePDFFromDocx>dotnet run CreatePDFFromDocx.csproj

PDFは、出力で指定された場所に作成されます。デフォルトでは、同じフォルダーが指定されます。

## 結論

PDFサービス API は、一般的なワークフローを自動化し、処理負荷をクラウドに移行することで、手動プロセスを排除するのに役立ちます。 PDFの取り扱いがブラウザーごとに異なる現代において、Adobe PDF Embed API とPDFサービス API を組み合わせることで、正常に実行され表示される、合理化され、信頼性が高く、予測可能なプロセスを構築できます **毎回** プラットフォームやデバイスを問いません。

## リソースと次のステップ

* その他のヘルプやサポートについては、 [[!DNL Adobe Acrobat Services] API](https://community.adobe.com/t5/document-cloud-sdk/bd-p/Document-Cloud-SDK?page=1&amp;sort=latest_replies&amp;filter=all) コミュニティフォーラム

* PDFサービス API [文書](https://www.adobe.com/go/pdftoolsapi_doc)

* [FAQ](https://community.adobe.com/t5/document-cloud-sdk/faq-for-document-services-pdf-tools-api/m-p/10726197) PDFサービス API の質問

* [お問い合わせ](https://www.adobe.com/go/pdftoolsapi_requestform) ライセンスと価格に関する質問

* 関連記事

   [新しいPDFサービス API では、ドキュメントワークフローにさらに多くの機能が提供されます](https://community.adobe.com/t5/document-services-apis/new-pdf-tools-api-brings-more-capabilities-for-document-services/m-p/11294170)

   [7 月リリース [!DNL Adobe Acrobat Services]:PDFの埋め込みとPDFサービス](https://medium.com/adobetech/july-release-of-adobe-document-services-pdf-embed-and-pdf-tools-17211bf7776d)

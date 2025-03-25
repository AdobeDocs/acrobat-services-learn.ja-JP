---
title: Microsoft Power Automateの資格情報の取得
description: 資格情報を取得して、Adobe PDFサービスの使用またはトライアルを開始する方法について説明します。
feature: PDF Services API
role: Developer
level: Beginner
type: Tutorial
jira: KT-10382
thumbnail: KT-10382.jpg
exl-id: 68ec654f-74aa-41b7-9103-44df13402032
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '872'
ht-degree: 2%

---

# Microsoft Power Automateの資格情報の取得

[Microsoft Power Automate](https://powerautomate.microsoft.com/)を使用すると、民間の開発者や開発者は、コードを記述することなく、業務を改善するための強力な自動プロセスを構築できます。 [Adobe PDFサービス](https://us.flow.microsoft.com/en-us/connectors/shared_adobepdftools/adobe-pdf-services/)コネクタは、[[!DNL Adobe Acrobat Services]](https://developer.adobe.com/document-services)の一部であり、Microsoft Power Automate内のAdobe PDFサービスAPIで使用可能な任意の操作を実行できます。

このチュートリアルでは、資格情報を取得して、Adobe PDFサービスの使用または体験版のダウンロードを開始する方法について説明します。 体験版ユーザーか既存のお客様かに応じて、このチュートリアルでは資格情報を取得するための適切な手順を説明します。

## Microsoft Power AutomateユーザーがAdobe PDFサービスコネクタを使用し始めるにはどうすればよいですか？

既存のMicrosoft Power Automateユーザーは、Adobe PDFサービスの[体験版の資格情報を取得](https://www.adobe.com/go/powerautomate_getstarted)できます。 上記のリンクは、このプロセス、特にMicrosoft Power Automateユーザー向けに役立つ特別な登録リンクです。

![Adobe Developerユーザーサインイン](assets/credentials_1.png)


>[!IMPORTANT]
> 体験版にログインする場合は、Enterprise IDではなくAdobe IDを使用する必要があります。 お客様がAdobe PDF Services APIの現在のサブスクライバーではなく、企業でログインしようとすると、Enterprise IDがAdobe PDF Services APIの使用権限を持っていないため、権限エラーが発生する場合があります。 このため、個人版のAdobe IDを無料で使用することをお勧めします。
>

1. ログイン後、新しい資格情報の名前を選択するように求められます。 *資格情報*&#x200B;を入力してください。
1. チェックボックスをオンにすると、デベロッパー条件に同意します。
1. **[!UICONTROL 資格情報の作成]**&#x200B;を選択します。

   ![資格情報の名前付け](assets/credentials_2.png)

これらの資格情報は、次の5つの異なる値をカバーします。

* クライアント ID（API キー）
* クライアントシークレット
* 組織 ID
* テクニカルアカウント ID
* Base64（エンコードされた秘密キー）

![新しい資格情報](assets/credentials_3.png)

これらすべての値を含むJSONファイルも自動的にシステムにダウンロードされます。 このファイルの名前は`pdfservices-api-pa-credentials.json`で、次のようになります：

```json
{
 "client_id": "client id value",
 "client_secret": "client secret value",
 "organization_id": "organized id value",
 "account_id": "account id value",
 "base64_encoded_private_key": "base64 version of the private key"
}
```

秘密キーのコピーを再度取得することはできないため、このファイルは安全な場所に保存してください。

### Microsoft Power Automateでの連携の追加

これで資格情報が完成したので、Microsoft Power Automateフローで資格情報を使用できるようになりました。

1. サイドバーメニューで、**[!UICONTROL データ]**&#x200B;メニューを開き、**接続**&#x200B;を選択します。

   ![Microsoft Power Automateサイトの「接続」メニュー](assets/credentials_4.png)

1. **+ [!UICONTROL 新しい接続]**&#x200B;を選択します。

1. 次の画面は、使用可能な接続タイプの一覧を示します。 右上隅で、「adobe」と入力して、オプションをフィルタリングします。

   ![Adobe接続の一覧](assets/credentials_5.png)

1. **[!UICONTROL Adobe PDFサービス（プレビュー）]**&#x200B;を選択します。
1. モーダルウィンドウで、先ほど生成した5つの値をすべて入力します。 完了したら、**[!UICONTROL 作成]**&#x200B;を選択します。

   ![資格情報を入力するためのフォームフィールド](assets/credentials_6.png)

これで、Microsoft Power AutomateでAdobe PDFサービスを使用する準備ができました。

### 作成後の資格情報へのアクセス

既に資格情報を作成していて、ダウンロードした資格情報を間違って配置した場合は、[Adobe Developer Console](https://developer.adobe.com/console)で再度取得できます。

1. [Adobe Developer Console](https://developer.adobe.com/console)にログインしたら、まずプロジェクトを見つけて選択します。
1. *資格情報*&#x200B;の下の左側のメニューで、**サービスアカウント(JWT)**&#x200B;を選択します。

   ![既存の資格情報](assets/credentials_7.png)

1. ここに表示されている5つの値に注意してください： *クライアントID*、*クライアントシークレット*、*テクニカルアカウントID*、*テクニカルアカウントの電子メール*、および&#x200B;*組織ID*。

残念ながら、以前の秘密キーはダウンロードできませんが、「公開/秘密キーペアを生成」ボタンを使用して新しいキーを作成することができます。

## 既存のAdobe PDFサービスの資格情報を使用する

[!DNL Adobe Acrobat Services] Webサイトから生成された既存のAdobe PDF Services API資格情報がある場合は、それらをMicrosoft Power Automateで使用できます。 サインアップ中にSDKをダウンロードした場合、既存の資格情報は`pdfservices-api-credentials.json`という名前のJSONファイルの形式で提供されていた可能性があります。 このJSONファイルには、接続資格情報の作成時に必要な5つのキーが含まれています。 それぞれの値をJSONファイルから対応する接続フィールドにコピーします。

秘密キーの値は、`private.key`という名前の2番目のファイルから取得されます。

上記のように、Adobe Developer Consoleから値を取得することもできます。

## [!DNL Adobe Acrobat Services]人のユーザーがMicrosoft Power Automateの使用を開始するにはどうすればよいですか？

Power Automateの使用を開始するには、まず<https://powerautomate.microsoft.com>に移動し、[無料で開始]ボタンを使用します。 Microsoftアカウントをお持ちでない場合は作成する必要があります。 ログインすると、Power Automateダッシュボードが表示されます。

![PAダッシュボード、初期表示](assets/credentials_8.png)

このチュートリアルの冒頭で説明したように、新しいフローを作成し、手順を追加して、Adobe PDFサービスを見つけます。 アクションを選択すると、プレミアムアカウントが必要であるという警告が表示されます。

![プレミアムアカウントに関する警告](assets/credentials_9.png)

上のスクリーンショットが示すように、職場アカウントに切り替えるか、新しい組織アカウントを設定できます。 利用できるようになったら、Adobe PDFサービスのアクションを追加できます。

[!DNL Adobe Acrobat Services]を使用して最初のMicrosoft Power Automateフローを作成する方法の詳細については、[Microsoft Power Automateで最初のワークフローを作成する](https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfservices/create-workflow-power-automate)を参照してください。

## その他の参考資料

さらに役立つように、その他のリソースのリストを次に示します。

* 最初に表示されるのは、Adobe PDFサービスのPower Automateドキュメントです： <https://docs.microsoft.com/en-us/connectors/adobepdftools/>。 これらのリソースは、ここで学習した内容を補完するものです。
* 例が必要ですか？ PDFサービスのデモとして、[Power Automateテンプレート](https://powerautomate.microsoft.com/en-us/connectors/details/shared_adobepdftools/adobe-pdf-services/)が多数用意されています。
* アドビのライブビデオコンテンツである[ペーパークリップ](https://www.youtube.com/playlist?list=PLcVEYUqU7VRe4sT-Bf8flvRz1XXUyGmtF)には、Power Automateの使用方法を説明するビデオも含まれています。
* [Adobe技術ブログ](https://medium.com/adobetech/tagged/microsoft-power-automate)には、Power Automateの使用に関する多くの記事があります。
* 最後に、コア[PDFサービス](https://developer.adobe.com/document-services/docs/overview/)のドキュメントも参照してください。

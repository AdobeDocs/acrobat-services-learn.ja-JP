---
title: Microsoft Power Automate の資格情報の取得
description: 資格情報を取得して、Adobe PDF Services の使用または試用を開始する方法について説明します
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-10382.jpg
kt: 10382
exl-id: 68ec654f-74aa-41b7-9103-44df13402032
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '918'
ht-degree: 3%

---

# Microsoft Power Automate の資格情報の取得

[Microsoft Power Automate](https://powerautomate.microsoft.com/) では、市民の開発者や開発者が強力な自動プロセスを作成して強力な方法を提供し、コードを記述することなくビジネスを向上させることができます。 [Adobe PDF Services](https://japan.flow.microsoft.com/ja-jp/connectors/shared_adobepdftools/adobe-pdf-services/) コネクタ、 [[!DNL Adobe Acrobat Services]](https://developer.adobe.com/document-services)を使用すると、ユーザーはMicrosoft Power Automate 内のAdobe PDF Services API で使用可能なアクションのいずれかを実行できます。

このチュートリアルでは、資格情報を取得してAdobe PDF Services の使用または試行を開始する方法について説明します。 このチュートリアルでは、お客様が体験版ユーザーか既存のお客様かに応じて、資格情報を取得するための適切な手順を順を追って説明します。

## Microsoft Power Automate ユーザーがAdobe PDF Services コネクタを使用し始めるにはどうすればよいですか？

既存のMicrosoft Power Automate ユーザーは、 [体験版の資格情報の取得](https://www.adobe.com/go/powerautomate_getstarted_jp) Adobe PDF Services の場合 上のリンクは、このプロセスを特にMicrosoft Power Automate ユーザー向けに支援する特別な登録リンクです。

![Adobe Developerユーザーのログイン](assets/credentials_1.png)


>[!IMPORTANT]
> 体験版にログインする場合は、Enterprise IDではなくAdobe IDを使用する必要があります。 Adobe PDF Services API の現在のサブスクライバーではない場合にEnterprise IDでログインしようとすると、Adobe PDF Services API を使用する権限がエンタープライズにないために権限エラーが発生する場合があります。 このため、無料の個人用Adobe IDの使用をお勧めします。

1. ログインすると、新しい資格情報の名前を選択するように求められます。 次の *資格情報名*&#x200B;を選択します。
1. 開発者の条件に同意するには、このチェックボックスをオンにします。
1. 選択 **[!UICONTROL 資格情報の作成]**&#x200B;を選択します。

   ![資格情報の命名](assets/credentials_2.png)

これらの資格情報には、次の 5 つの異なる値が含まれます。

* クライアント ID（API キー）
* クライアントシークレット
* 組織 ID
* テクニカルアカウント ID
* Base64 （エンコードされた秘密キー）

![新しい資格情報](assets/credentials_3.png)

これらの値をすべて含む JSON ファイルも自動的にシステムにダウンロードされます。 このファイルの名前は `pdfservices-api-pa-credentials.json` 次のようになります。

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

### Microsoft Power Automate での接続の追加

資格情報が完成したので、Microsoft Power Automate フローでその使用を開始できます。

1. サイドバーメニューで、 **[!UICONTROL データ]** メニューから **接続**:

   ![Microsoft Power Automate サイトの接続メニュー](assets/credentials_4.png)

1. 選択 **+ [!UICONTROL 新しい接続]**&#x200B;を選択します。

1. 次の画面に、使用可能な接続タイプのリストが表示されます。 右上隅で、「adobe」と入力してオプションをフィルタリングします。

   ![Adobe接続](assets/credentials_5.png)

1. 選択 **[!UICONTROL Adobe PDF Services（プレビュー）]**&#x200B;を選択します。
1. モーダルウィンドウで、前に生成した 5 つの値をすべて入力します。 選択 **[!UICONTROL 作成]** 完了したら、

   ![資格情報を入力するためのフォームフィールド](assets/credentials_6.png)

これで、Microsoft Power Automate でAdobe PDFサービスを使用する準備ができました。

### 資格情報の作成後のアクセス

既に資格情報を作成していて、ダウンロードした資格情報を置き換えた場合は、 [Adobe Developer Console](https://developer.adobe.com/console)を選択します。

1. ログイン後 [Adobe Developer Console](https://developer.adobe.com/console)を選択します。
1. 左側のメニューの *資格情報*&#x200B;で、 **サービスアカウント (JWT)**:

   ![既存の資格情報](assets/credentials_7.png)

1. 次に示す 5 つの値に注意してください。 *クライアント ID*, *クライアントシークレット*, *テクニカルアカウント ID*, *テクニカルアカウントメール*&#x200B;および *組織 ID*&#x200B;を選択します。

残念ながら、以前の秘密鍵はダウンロードできませんが、「公開/秘密鍵を生成」ボタンを使用して新しい秘密鍵を作成することができます。

## 既存のAdobe PDF Services 資格情報の使用

既存のAdobe PDF Services API 資格情報が [!DNL Adobe Acrobat Services] Microsoft Power Automate で使用できます。 サインアップ中に SDK をダウンロードした場合、既存の資格情報は JSON ファイルの形式で提供されます。通常、 `pdfservices-api-credentials.json`を選択します。 この JSON ファイルには、接続資格情報の作成時に必要な 5 つのキーが含まれています。 JSON ファイルの各値を対応する接続フィールドにコピーします。

秘密鍵の値は、次の名前の 2 番目のファイルから取得されます `private.key`を選択します。

前述のように、Adobe Developer Console から値を取得することもできます。

## どうすれば [!DNL Adobe Acrobat Services] ユーザーはMicrosoft Power Automate を使用し始めますか？

Power Automate の使用を開始するには、まず <https://powerautomate.microsoft.com> 「無料で始める」ボタンを使用します。 Microsoftアカウントをお持ちでない場合は、作成する必要があります。 ログインすると、Power Automate ダッシュボードが表示されます。

![PA ダッシュボード、初期ビュー](assets/credentials_8.png)

このチュートリアルの最初で説明したように、新しいフローを作成し、ステップを追加して、Adobe PDF Services を見つけます。 アクションを選択すると、プレミアムアカウントが必要であるという警告が表示される場合があります。

![プレミアムアカウントの警告](assets/credentials_9.png)

上のスクリーンショットに示すように、職場アカウントに切り替えるか、新しい組織アカウントを設定できます。 完了すると、Adobe PDF Services アクションを追加できます。

初めてのMicrosoft Power Automate フローの作成方法については、 [!DNL Adobe Acrobat Services]を参照してください。 [Microsoft Power Automate での最初のワークフローの作成](https://experienceleague.adobe.com/docs/document-services/tutorials/pdfservices/create-workflow-power-automate.html)を選択します。

## その他の参考資料

以下では、その他のリソースを紹介します。

* まず、Adobe PDF Services Power Automate のドキュメントを参照してください。 <https://docs.microsoft.com/en-us/connectors/adobepdftools/>を選択します。 これらのリソースは、ここで学習した内容を補完するものです。
* 例が必要ですか？ 数多くの [Power Automate テンプレート](https://powerautomate.microsoft.com/en-us/connectors/details/shared_adobepdftools/adobe-pdf-services/) PDFサービス
* ライブビデオコンテンツ、 [ペーパークリップ](https://www.youtube.com/playlist?list=PLcVEYUqU7VRe4sT-Bf8flvRz1XXUyGmtF)には、Power Automate の使用方法を示すビデオも含まれています。
* この [Adobe技術ブログ](https://medium.com/adobetech/tagged/microsoft-power-automate) には、Power Automate の操作に関する多くの記事があります。
* 最後に、コアに相談してください [PDFサービス](https://developer.adobe.com/document-services/docs/overview/) の説明も参照してください。

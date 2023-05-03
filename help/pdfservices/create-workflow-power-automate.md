---
title: Microsoft Power Automate での最初のワークフローの作成
description: Microsoft Power Automate でのAdobe PDF Services コネクタの使用方法について説明します
type: Tutorial
role: Developer
level: Beginner
thumbnail: KT-10379.jpg
kt: 10379
exl-id: 095b705f-c380-42cc-9329-44ef7de655ee
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1999'
ht-degree: 2%

---


# Microsoft Power Automate での最初のフローの作成

最初のフローを [Microsoft Power Automate](https://flow.microsoft.com) の使用 [Adobe PDF Services](https://japan.flow.microsoft.com/ja-jp/connectors/shared_adobepdftools/adobe-pdf-services/) コネクタ。

この実践チュートリアルでは、次の方法を学習します。

* Word 文書をPDF
* PDF文書を 1 つのPDF
* PDF文書にパスワードをProtect

## 準備

### 必要なもの

* **Adobe PDF Services の体験版または本番環境の資格情報**
Microsoft Power Automate で資格情報を取得および設定する方法について詳しく説明します [ここ](https://experienceleague.adobe.com/docs/document-services/tutorials/pdfservices/getting-credentials-power-automate.html)を選択します。
* **プレミアムコネクタ搭載のMicrosoft Power Automate**
Power Automate のライセンスレベルを確認する方法について説明します [ここ](https://docs.microsoft.com/en-us/power-platform/admin/power-automate-licensing/types)を選択します。
* **OneDrive**
このチュートリアルでは OneDrive ストレージコネクタを使用しますが、どのストレージコネクタでも置き換えることができます。

### サンプルファイル

二つある [サンプルファイル](assets/sample-assets.zip) を解凍して OneDrive にアップロードする必要があります。

* WordDocument01.docx
* WordDocument02.docx

### 資格情報の取得

このチュートリアルを完了するには、Adobe PDFサービス用のMicrosoft Power Automate で既に資格情報が設定されている必要があります。 この手順をまだ完了していない場合は、 [こちらの手順](https://experienceleague.adobe.com/docs/document-services/tutorials/pdfservices/getting-credentials-power-automate.html)を選択します。

## パート 1:新しいフローを作成し、Word をPDFに

### フローの作成

このパートでは、 [Microsoft Power Automate](https://flow.microsoft.com) インスタントフローを使用して、パラメーターを追加し、OneDrive からファイルを取得し、それらをPDFに変換します。

1. 次の場所に移動 [Microsoft Power Automate](https://flow.microsoft.com) 資格情報を使用してログインします。
1. サイドバーで、「 **[!UICONTROL 作成]**&#x200B;を選択します。

   ![「作成」ボタン](assets/createButtonPowerAutomate.png)

1. 選択 **[!UICONTROL インスタントフロー]**&#x200B;を選択します。
1. フローに名前を付けます。
1. Under *このフローをトリガーする方法を選択します*&#x200B;で、 **[!UICONTROL フローの手動トリガー]**&#x200B;を選択します。
1. 「**[!UICONTROL 作成]**」を選択します。

### ファイルの内容の取得

次に、サンプルファイルのファイル内容を取得します。

>[!PREREQUISITES]
>
>まだアップロードしていない場合は、 [サンプルファイル](assets/sample-assets.zip) OneDrive に解凍してアップロードします。


1. 入力 [Power Automate](https://flow.microsoft.com)で、 **[!UICONTROL +新しいステップ]**&#x200B;を選択します。
1. 検索対象 *OneDrive* 」をクリックします。
1. 職場または個人の OneDrive アカウントを選択するには、 **[!UICONTROL OneDrive for Business]** または **[!UICONTROL OneDrive]**&#x200B;を選択します。
1. 検索対象 *ファイルコンテンツの取得* 」をクリックします。
1. 」を **[!UICONTROL ファイル]** 」フィールドで、フォルダーアイコンを選択して「 *WordDocument01.docx* ファイルを開きます。

   ![Microsoft Power Automate でのファイルコンテンツの取得 OneDrive アクション](assets/getFileContentOneDrive.png)

### ファイルをPDFに

ファイルの内容が完成したので、ドキュメントをPDFに

1. 入力 [Power Automate](https://flow.microsoft.com)で、 **[!UICONTROL +新しいステップ]**&#x200B;を選択します。
1. 検索対象 *Adobe PDF Services* 」をクリックします。
1. 選択 **[!UICONTROL Adobe PDF Services]**&#x200B;を選択します。
1. 検索対象 *Word をPDF* 」をクリックします。
1. 入力 **[!UICONTROL ファイル名]**&#x200B;で、必要に応じてファイルに名前を付けます。ただし、 *.docx*&#x200B;を選択します。 この拡張機能は、文書を Word からPDFに変換するために必要です。
1. カーソルを **[!UICONTROL ファイルコンテンツ]** 」フィールドに入力します。
1. エレメントを **[!UICONTROL 動的コンテンツ]** パネルで、 **[!UICONTROL ファイルコンテンツ]**&#x200B;を選択します。

   ![Microsoft Power Automate で Word をPDFに変換するアクション](assets/convertWordToPDFActionPowerAutomate.png)

### ファイルを OneDrive に保存します

ドキュメントが生成されたら、ファイルを OneDrive に保存し直します。

1. 入力 [Microsoft Power Automate](https://flow.microsoft.com)で、 **[!UICONTROL +新しいステップ]**&#x200B;を選択します。
1. 検索対象 *OneDrive* 」をクリックします。
1. 職場または個人の OneDrive アカウントを選択するには、 **[!UICONTROL OneDrive for Business]** または **[!UICONTROL OneDrive]**&#x200B;を選択します。
1. 検索対象 *ファイルコンテンツの取得* 」をクリックします。
1. 検索対象 *ファイルの作成* 」をクリックします。
1. 選択 **[!UICONTROL ファイルの作成]**&#x200B;を選択します。
1. 」を **[!UICONTROL フォルダパス]** 」フィールドで、フォルダーアイコンを選択して、OneDrive 内のファイルの保存場所を指定します。
1. 入力 **[!UICONTROL ファイル名]**&#x200B;で、必要に応じてファイルに名前を付けます。ただし、 *.docx*&#x200B;を選択します。 この拡張機能は、文書を Word からPDFに変換するために必要です。
1. 」を **[!UICONTROL ファイルコンテンツ]** フィールド、使用 **[!UICONTROL 動的コンテンツ]** パネルを使用して、PDFファイル内容変数を挿入します。

### フローを試す

1. 左上で、「 **[!UICONTROL 無題]** を選択して、フローの名前を変更します。
1. 「**[!UICONTROL 保存]**」を選択します。
1. 選択 **[!UICONTROL テスト]**&#x200B;を選択します。
1. 選択 **[!UICONTROL 手動]** その後 **[!UICONTROL 保存とテスト]**&#x200B;を選択します。
1. 「**[!UICONTROL 続行]**」を選択します。
1. 選択 **[!UICONTROL フローを実行]**&#x200B;を選択します。

OneDrive フォルダーに、変換されたフォルダーが表示されます。PDF。

![OneDrive 内の選択した変換されたPDFドキュメント](assets/selectedGeneratedFileInOneDrive.png)

## パート 2:テンプレートからの動的ドキュメントの生成

この次のパートは、パート 1 を基にして、 *Word からドキュメントを生成* 動的にデータを結合するためのテンプレートです。

### 文書テンプレートを確認する

開く *WordDocument02_.docx* OneDrive のサンプルファイルから。 Word 文書には、データが文書に入力される場所を表す複数の異なるテキストタグが含まれます。

### トリガーへのパラメーターの追加

動的データをドキュメントにプッシュするには、トリガーが値の入力を求めるためのパラメーターをいくつか作成する必要があります。

1. フローを編集するときは、 **[!UICONTROL フローの手動トリガー]** 」をクリックしてアクションを展開します。
1. 選択 **[!UICONTROL 入力の追加]**&#x200B;を選択します。
1. 選択 **[!UICONTROL テキスト]**&#x200B;を選択します。
1. フィールドに名前を付ける *名*&#x200B;を選択します。

手順 2 ～ 4 を繰り返して、次のフィールドを追加します。

* 姓
* 給与

![パラメーターフィールドを使用した Power Automate のトリガー](assets/triggerParametersInPowerAutomate.png)

### テンプレートのファイルコンテンツの取得

文書を生成するには、まず Word テンプレートのファイルコンテンツを取得する必要があります。

1. Power Automate で「+」を選択します。 **[!UICONTROL 新しいステップ]**&#x200B;を選択します。
1. 検索対象 *OneDrive* 」をクリックします。
1. 職場または個人の OneDrive アカウントを選択するには、 **[!UICONTROL OneDrive for Business]** または **[!UICONTROL OneDrive]**&#x200B;を選択します。
1. 検索対象 *ファイルコンテンツの取得* 」をクリックします。
1. 」を **[!UICONTROL ファイル]** 」フィールドで、フォルダーアイコンを選択して「 *WordDocument02.docx* ファイルを開きます。

![Microsoft Power Automate の「OneDrive からファイルコンテンツを取得」アクション](assets/getFileContentAction02.png)

### テンプレートからドキュメントを生成

1. Power Automate で、 **[!UICONTROL +新しいステップ]**&#x200B;を選択します。
1. 検索対象 *Adobe PDF Services* 」をクリックします。
1. 選択 **[!UICONTROL Adobe PDF Services]**&#x200B;を選択します。
1. ツールバーの「 **[!UICONTROL Word テンプレートからドキュメントを生成]** アクション
1. 」を **[!UICONTROL テンプレートファイル名]** 」フィールドで、必要に応じてファイルに名前を付けます。ただし、ファイル名の末尾は *.docx*&#x200B;を選択します。

#### データの結合

エレメントを *Word テンプレートからドキュメントを生成* アクションを実行すると、動的コンテンツを使用して、フロー内の以前の任意の変数からドキュメントにデータを結合できます。

以下の JSON データを **データの結合** フィールド：

```
{
    "FirstName": "",
    "LastName": "",
    "Salary": ""
}
```

1. フィールド内で、 *FirstName* 値。
1. エレメントを **[!UICONTROL 動的コンテンツ]** パネルで、 *名* フローアクションを手動でトリガーする値。

   ![JSON でデータタグを含むドキュメントを生成する](assets/generateDocumentJSONAction.png)

1. に対して手順 7 ～ 8 を繰り返します。 **[!UICONTROL LastName]** および **[!UICONTROL 給与]** フィールド、
1. 」を **[!UICONTROL テンプレートファイルの内容]** 」フィールドで、「 **[!UICONTROL 動的コンテンツ]** パネルを使用して、 **[!UICONTROL ファイルコンテンツ]** 値を *ファイルコンテンツの取得* ステップ

![Power Automate の「Word テンプレートから文書を生成」アクション（すべての値が完了している場合）](assets/generateDocumentJSONActionCompleted.png)

>[!TIP]
>
>この *Word テンプレートからドキュメントを生成* アクションはAdobeドキュメント生成 API を使用します。 テンプレートの作成方法について詳しくは、次のリソースを参照してください。
>
>* [ドキュメント生成Adobeの詳細](https://developer.adobe.com/document-services/apis/doc-generation/)
>* [Microsoft Word 用Adobe文書生成タグ](https://appsource.microsoft.com/en-US/product/office/WA200002654)
>* [Adobeドキュメント生成 API ドキュメント](https://developer.adobe.com/document-services/docs/overview/document-generation-api/)


### ファイルを OneDrive に保存します

ドキュメントが生成されたら、ファイルを OneDrive に保存し直すことができます。

1. Power Automate で、 **+ [!UICONTROL 新しいステップ]**&#x200B;を選択します。
1. 検索対象 *OneDrive* 」をクリックします。
1. 職場または個人の OneDrive アカウントを選択するには、 **[!UICONTROL OneDrive for Business]** または **[!UICONTROL OneDrive]**&#x200B;を選択します。
1. 検索対象 *ファイルの作成* 」をクリックします。
1. 選択 **[!UICONTROL ファイルの作成]**&#x200B;を選択します。
1. 」を **[!UICONTROL フォルダパス]** 」フィールドで、フォルダーアイコンを選択して、OneDrive 内のファイルの保存場所を指定します。
1. 」を **[!UICONTROL ファイル名]** 」フィールドで、ファイルの名前を設定します。 出力はPDFであるため、ファイル名の末尾には.pdf 拡張子を付ける必要があります。
1. 次の **[!UICONTROL 動的コンテンツ]** パネルを使用して、PDFファイル内容変数を **[!UICONTROL ファイルコンテンツ]** 」フィールドに入力します。

### フローを試す

![Microsoft Power Automate の入力プロンプトのフロー画面](assets/runFlowParameters.png)

1. 「**[!UICONTROL 保存]**」を選択します。
1. 選択 **[!UICONTROL テスト]**&#x200B;を選択します。
1. 選択 **[!UICONTROL 手動]** その後 **[!UICONTROL 保存とテスト]**&#x200B;を選択します。
1. 「**[!UICONTROL 続行]**」を選択します。
1. 値を入力 *名*, *姓*&#x200B;および *給与*&#x200B;を選択します。
1. 選択 **[!UICONTROL フローを実行]**&#x200B;を選択します。

OneDrive フォルダーに、Word 文書から生成されたPDFが表示されます。 OneDrive でPDFドキュメントを開くと、データがテキストタグの場所に結合されます。


## パート 3:PDFを 1 つに

Word 文書を生成してPDFに変換したので、次のパートでは、複数のPDF文書を結合します。

>[!NOTE]
>
>前のアクションでは、ドキュメントのコピーをファイルとして OneDrive に保存しました。 Merge Tools などのツールを使用するために、PDFを OneDrive に保存する必要はありません。 代わりに、あるアクションの出力を次のアクションに直接渡すことができます。これは、各アクションの後に OneDrive に保存するよりも便利です。 しかし、デモンストレーションの目的で、これらのファイルを OneDrive に保存しています。

### 結合の追加PDF手順

1. フローを編集するときは、 **[!UICONTROL +次のステップ]** 」をクリックして、フローの最後にアクションを追加します。
1. 検索対象 *Adobe PDF Services* 」をクリックします。
1. 選択 **[!UICONTROL Adobe PDF Services]**&#x200B;を選択します。
1. ツールバーの「 **[!UICONTROL 結合PDF]** を選択します。
1. 」を **[!UICONTROL 結合PDFファイル名]** 」フィールドに、目的のファイル名を入力します (*CombinedDocument.pdf*)。
1. 」を **[!UICONTROL ファイルコンテンツ —1]** 」フィールドで、「 **[!UICONTROL 動的コンテンツ]** パネルを使用して、 *PDFファイルの内容* 値を **[!UICONTROL Word をPDF]** ステップ
1. 次の文書を追加するには、 **+ [!UICONTROL 新しいアイテムの追加]**&#x200B;を選択します。
1. 」を **[!UICONTROL ファイルの内容 — 2]** 」フィールドで、「 **[!UICONTROL 動的コンテンツ]** パネルを使用して、 **[!UICONTROL 出力ファイルの内容]** 値を *Word テンプレートからドキュメントを生成* ステップ

![Microsoft Power Automate の「PDFの結合」アクション](assets/mergePDFAction.png)

### 結合されたPDFを OneDrive に保存

文書が結合されたら、文書を OneDrive に保存し直すことができます。

1. Power Automate で、 **+ [!UICONTROL 新しいステップ]**&#x200B;を選択します。
1. 検索対象 *OneDrive* 」をクリックします。
1. 職場または個人の OneDrive アカウントを選択するには、 **[!UICONTROL OneDrive for Business]** または **[!UICONTROL OneDrive]**&#x200B;を選択します。
1. 検索対象 *ファイルの作成* 」をクリックします。
1. 選択 **[!UICONTROL ファイルの作成]**&#x200B;を選択します。
1. 」を **[!UICONTROL フォルダパス]** 」フィールドで、フォルダーアイコンを選択して、OneDrive 内のファイルの保存場所を指定します。
1. 」を **[!UICONTROL ファイル名]** 」フィールドで、ファイルの名前を設定します。 出力はPDFなので、ファイル名の末尾は.pdf にする必要があります。
1. 」を **[!UICONTROL ファイルコンテンツ]** フィールド、使用 **[!UICONTROL 動的コンテンツ]** パネルを使用して、 *PDFファイルの内容* 値を **[!UICONTROL 結合PDF]** ステップ

   ![Microsoft Power Automate のフローの概要](assets/flowOverviewSavedMergedDocument.png)

### フローを試す

1. 「**[!UICONTROL 保存]**」を選択します。
1. 選択 **[!UICONTROL テスト]**&#x200B;を選択します。
1. 選択 **[!UICONTROL 手動]** その後 **[!UICONTROL 保存とテスト]**&#x200B;を選択します。
1. 「**[!UICONTROL 続行]**」を選択します。
1. 値を入力 *名*, *姓*&#x200B;および *給与*&#x200B;を選択します。
1. 選択 **[!UICONTROL フローを実行]**&#x200B;を選択します。

OneDrive フォルダーに、1 つ目と 2 つ目のドキュメントのPDFが結合された状態で表示されます。

## パート 4:ProtectPDF文書

ドキュメントを生成した後、OneDrive に保存する前に追加の手順を追加することで、ドキュメントを編集から保護できます。

### PDF を保護

1. Power Automate でフローを編集する際に、 **+** 間に **[!UICONTROL 結合PDF]** アクションと **[!UICONTROL ファイル 3 を作成]** を選択します。

   ![新しいアクションを追加するための 2 つのアクション間のプラス記号](assets/addActionToProtect.png)

1. 選択 **[!UICONTROL アクションの追加]**&#x200B;を選択します。
1. 検索対象 *Adobe PDF Services* 」をクリックします。
1. 選択 **[!UICONTROL Adobe PDF Services]**&#x200B;を選択します。
1. ツールバーの「 **[!UICONTROL ProtectPDFの表示]** を選択します。
1. 」を **[!UICONTROL ファイル名]** 」フィールドで、拡張子.pdf で終わる名前を設定します。
1. 設定 **[!UICONTROL パスワード]** 」フィールドにパスワードを入力して、文書を開きます。
1. 」を **[!UICONTROL ファイルコンテンツ]** 」フィールドで、「 **[!UICONTROL 動的コンテンツ]** パネルを使用して、 *PDFファイルの内容* 値を **[!UICONTROL 結合PDF]** ステップ

### OneDrive への保存の更新

ドキュメントが保護されたら、ファイルを OneDrive に保存し直すことができます。 この例では、既存の **ファイル 3 を作成** 新しい *ファイルコンテンツ* 値。

1. カーソルを **[!UICONTROL ファイルコンテンツ]** フィールドを **[!UICONTROL ファイル 3 を作成]** を選択します。
1. 次の **[!UICONTROL 動的コンテンツ]** パネルを使用して、 *PDFファイルの内容* 値を **ProtectPDFの表示** ステップ

### フローを試す

1. 「**[!UICONTROL 保存]**」を選択します。
1. 選択 **[!UICONTROL テスト]**&#x200B;を選択します。
1. 選択 **[!UICONTROL 手動]** その後 **[!UICONTROL 保存とテスト]**&#x200B;を選択します。
1. 「**[!UICONTROL 続行]**」を選択します。
1. 値を入力 *名*, *姓*&#x200B;および *給与*&#x200B;を選択します。
1. 選択 **[!UICONTROL フローを実行]**&#x200B;を選択します。

OneDrive フォルダーに結合されたPDFが表示され、文書を表示するためにパスワードの入力を求められます。

## 次の手順

このチュートリアルでは、Word 文書をPDFに変換し、データに基づいて文書を生成し、文書を結合し、パスワードで保護しました。 詳しくは、Microsoft Power Automate のAdobe PDF Services コネクタで使用できるその他のアクションをご覧ください。

* Microsoft Power Automate で使用できる作成済みのテンプレートを表示します。
* 学ぶ [記事](https://medium.com/adobetech/tagged/microsoft-power-automate) Adobe技術ブログ
* レビュー [文書](https://developer.adobe.com/document-services/docs/overview/document-generation-api/) (Adobe文書生成 API)。

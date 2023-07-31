---
title: Microsoft Power Automateで最初のワークフローを作成
description: Microsoft Power AutomateでAdobe PDFサービスコネクタを使用する方法について説明します。
type: Tutorial
role: Developer
level: Beginner
feature: PDF Services API
thumbnail: KT-10379.jpg
kt: 10379
exl-id: 095b705f-c380-42cc-9329-44ef7de655ee
source-git-commit: b65ffa3efa3978587564eb0be0c0e7381c8c83ab
workflow-type: tm+mt
source-wordcount: '1999'
ht-degree: 2%

---


# Microsoft Power Automateで最初のフローを作成

最初のフローを作成する方法 [Microsoft Power Automate](https://flow.microsoft.com) の使用 [Adobe PDF Services](https://japan.flow.microsoft.com/ja-jp/connectors/shared_adobepdftools/adobe-pdf-services/) コネクタ。

この実践チュートリアルでは、次の方法について学習します。

* Word文書をPDFに変換
* PDF文書を1つのPDFに結合
* PDF文書をパスワードでProtectする

## 準備

### 必要なもの

* **Adobe PDFサービスの体験版または製品版の認証情報**
Microsoft Power Automateで資格情報を取得および設定する方法の詳細 [こちら](https://experienceleague.adobe.com/docs/document-services/tutorials/pdfservices/getting-credentials-power-automate.html).
* **プレミアムコネクタ搭載のMicrosoft Power Automate**
Power Automateのライセンスレベルを確認する方法について説明します [こちら](https://docs.microsoft.com/en-us/power-platform/admin/power-automate-licensing/types).
* **OneDrive**
このチュートリアルではOneDriveストレージコネクターを使用しますが、他のストレージコネクターで代用することもできます。

### サンプルファイル

二つある [サンプルファイル](assets/sample-assets.zip) 解凍してOneDriveにアップロードする必要があるファイル：

* WordDocument01.docx
* WordDocument02.docx

### 資格情報の取得

このチュートリアルを完了するには、Adobe PDFサービス向けMicrosoft Power Automateで資格情報が既に設定されている必要があります。 この手順を完了していない場合は、 [ここでの手順](https://experienceleague.adobe.com/docs/document-services/tutorials/pdfservices/getting-credentials-power-automate.html).

## パート1：新しいフローを作成し、WordをPDFに変換する

### フローの作成

このパートでは、 [Microsoft Power Automate](https://flow.microsoft.com) インスタントフローを使用して、パラメーターを追加し、OneDriveからファイルを取得して、PDFに変換します。

1. 移動先 [Microsoft Power Automate](https://flow.microsoft.com) 資格情報を使用してログインします。
1. サイドバーで、 **[!UICONTROL 作成]**.

   ![ボタンを作成](assets/createButtonPowerAutomate.png)

1. 選択 **[!UICONTROL インスタントフロー]**.
1. フローに名前を付けます。
1. 未満 *このフローをトリガーする方法を選択*、選択 **[!UICONTROL 手動によるフローのトリガー]**.
1. 「**[!UICONTROL 作成]**」を選択します。

### ファイルの内容を取得する

次に、サンプルファイルのファイル内容を取得します。

>[!PREREQUISITES]
>
>をアップロードしていない場合 [サンプルファイル](assets/sample-assets.zip) onedriveに解凍してアップロードします。


1. イン [Power Automate](https://flow.microsoft.com)、選択 **[!UICONTROL +新しい手順]**.
1. 検索 *OneDrive* をクリックします。
1. 職場または個人のOneDriveアカウントを選択するには、 **[!UICONTROL OneDrive for Business]** または **[!UICONTROL OneDrive]**.
1. 検索 *ファイルコンテンツを取得* をクリックします。
1. を **[!UICONTROL ファイル]** フィールドで、フォルダーアイコンを選択して *WordDocument01.docx* onedrive内のファイルです。

   ![Microsoft Power AutomateでのOneDriveアクションの取得](assets/getFileContentOneDrive.png)

### ファイルをPDFに変換

これでファイルコンテンツが完成したので、文書をPDFに変換できます。

1. イン [Power Automate](https://flow.microsoft.com)、選択 **[!UICONTROL +新しい手順]**.
1. 検索 *Adobe PDF Services* をクリックします。
1. 選択 **[!UICONTROL Adobe PDF Services]**.
1. 検索 *WordをPDFに変換* をクリックします。
1. イン **[!UICONTROL ファイル名]**、必要に応じてファイル名を付けますが、末尾はで始まる必要があります *.docx*. この拡張子は、文書をWordからPDFに変換するために必要です。
1. カーソルを **[!UICONTROL ファイルコンテンツ]** フィールドに入力します。
1. を使用する **[!UICONTROL 動的コンテンツ]** パネル、選択 **[!UICONTROL ファイルコンテンツ]**.

   ![Microsoft Power Automateの「WordをPDFに変換」アクション](assets/convertWordToPDFActionPowerAutomate.png)

### ファイルをOneDriveに保存

ドキュメントが生成されたら、ファイルをOneDriveに保存し直します。

1. イン [Microsoft Power Automate](https://flow.microsoft.com)、選択 **[!UICONTROL +新しい手順]**.
1. 検索 *OneDrive* をクリックします。
1. 職場または個人のOneDriveアカウントを選択するには、 **[!UICONTROL OneDrive for Business]** または **[!UICONTROL OneDrive]**.
1. 検索 *ファイルコンテンツを取得* をクリックします。
1. 検索 *ファイルを作成* をクリックします。
1. 選択 **[!UICONTROL ファイルを作成]**.
1. を **[!UICONTROL フォルダーパス]** フィールドで、フォルダーアイコンを選択して、OneDrive内のファイルの保存先を指定します。
1. イン **[!UICONTROL ファイル名]**、必要に応じてファイル名を付けますが、末尾はで始まる必要があります *.docx*. この拡張子は、文書をWordからPDFに変換するために必要です。
1. を **[!UICONTROL ファイルコンテンツ]** フィールド、使用 **[!UICONTROL 動的コンテンツ]** パネルに移動し、PDFファイルコンテンツ変数を挿入します。

### 体験版のフロー

1. 左上で、 **[!UICONTROL 無題]** フローの名前を変更します。
1. 「**[!UICONTROL 保存]**」を選択します。
1. 選択 **[!UICONTROL テスト]**.
1. 選択 **[!UICONTROL 手動]** その後 **[!UICONTROL 保存とテスト]**.
1. 「**[!UICONTROL 続行]**」を選択します。
1. 選択 **[!UICONTROL フローを実行]**.

OneDriveフォルダーに、変換されたPDFが表示されます。

![OneDriveで選択した変換済みのPDF文書](assets/selectedGeneratedFileInOneDrive.png)

## パート2:テンプレートから動的ドキュメントを生成する

次のパートでは、パート1をベースにして、 *Wordから文書を生成* データを文書に動的に結合するテンプレート。

### 文書テンプレートをレビュー

開く *WordDocument02_.docx* onedriveのサンプルファイルから。 Word文書には、データが文書に入力される場所を表すいくつかの異なるテキストタグが含まれています。

### トリガーするパラメーターの追加

動的データをドキュメントにプッシュするには、値の入力を促すトリガー用にいくつかのパラメーターを作成する必要があります。

1. フローの編集時に、 **[!UICONTROL 手動によるフローのトリガー]** アクションを展開します。
1. 選択 **[!UICONTROL 入力を追加]**.
1. 選択 **[!UICONTROL Text]**.
1. フィールドに名前を付ける *名前（名）*.

手順2 ～ 4を繰り返して、次のフィールドを追加します。

* 姓
* 給与

![Power Automateのパラメーターフィールドでトリガー](assets/triggerParametersInPowerAutomate.png)

### テンプレートのファイルコンテンツを取得する

文書を生成するには、まずWordテンプレートのファイルコンテンツを取得する必要があります。

1. Power Automateで、「+」を選択します。 **[!UICONTROL 新しい手順]**.
1. 検索 *OneDrive* をクリックします。
1. 職場または個人のOneDriveアカウントを選択するには、 **[!UICONTROL OneDrive for Business]** または **[!UICONTROL OneDrive]**.
1. 検索 *ファイルコンテンツを取得* をクリックします。
1. を **[!UICONTROL ファイル]** フィールドで、フォルダーアイコンを選択して *WordDocument02.docx* onedrive内のファイルです。

![Microsoft Power AutomateのOneDriveからファイルコンテンツアクションを取得](assets/getFileContentAction02.png)

### テンプレートから文書を生成

1. Power Automateで、 **[!UICONTROL +新しい手順]**.
1. 検索 *Adobe PDF Services* をクリックします。
1. 選択 **[!UICONTROL Adobe PDF Services]**.
1. を選択します **[!UICONTROL Wordテンプレートから文書を生成]** アクション:
1. を **[!UICONTROL テンプレートファイル名]** フィールドに、必要に応じてファイルの名前を入力します。ただし、 *.docx*.

#### データを結合

を使用する *Wordテンプレートから文書を生成* アクションとして、動的コンテンツを使用して、以前フローにあった様々な変数から文書にデータを結合できます。

以下のJSONデータを **データを結合** フィールド：

```
{
    "FirstName": "",
    "LastName": "",
    "Salary": ""
}
```

1. フィールド内の2つの引用符の間にカーソルを置きます。 *FirstName* 値を返します。
1. を使用する **[!UICONTROL 動的コンテンツ]** パネル、挿入 *名前（名）* 「フローを手動でトリガー」アクションの値。

   ![JSONのデータタグを使用して文書を生成](assets/generateDocumentJSONAction.png)

1. 手順7 ～ 8を、 **[!UICONTROL LastName]** および **[!UICONTROL 給与]** フィールド。
1. を **[!UICONTROL テンプレートファイルコンテンツ]** フィールドで、 **[!UICONTROL 動的コンテンツ]** 挿入するパネル **[!UICONTROL ファイルコンテンツ]** 値を *ファイルコンテンツを取得* 手順

![すべての値が入力されたPower Automateの「Wordテンプレートから文書を生成」アクション](assets/generateDocumentJSONActionCompleted.png)

>[!TIP]
>
>この *Wordテンプレートから文書を生成* actionはAdobe Document Generation APIを使用します。 テンプレートの作成方法について詳しくは、いくつかのリソースをご覧ください。
>
>* [Adobe文書の生成の詳細](https://developer.adobe.com/document-services/apis/doc-generation/)
>* [Microsoft Word用Adobe文書生成タガー](https://appsource.microsoft.com/en-US/product/office/WA200002654)
>* [Adobeドキュメント生成APIドキュメント](https://developer.adobe.com/document-services/docs/overview/document-generation-api/)

### ファイルをOneDriveに保存

ドキュメントが生成されたら、ファイルをOneDriveに保存し直すことができます。

1. Power Automateで、 **+ [!UICONTROL 新しい手順]**.
1. 検索 *OneDrive* をクリックします。
1. 職場または個人のOneDriveアカウントを選択するには、 **[!UICONTROL OneDrive for Business]** または **[!UICONTROL OneDrive]**.
1. 検索 *ファイルを作成* をクリックします。
1. 選択 **[!UICONTROL ファイルを作成]**.
1. を **[!UICONTROL フォルダーパス]** フィールドで、フォルダーアイコンを選択して、OneDrive内のファイルの保存先を指定します。
1. を **[!UICONTROL ファイル名]** フィールドに、ファイルの名前を設定します。 出力はPDFなので、ファイル名の末尾は.pdf拡張子にする必要があります。
1. 次を使用します **[!UICONTROL 動的コンテンツ]** PDFファイルコンテンツ変数を **[!UICONTROL ファイルコンテンツ]** フィールドに入力します。

### 体験版のフロー

![入力を求めるMicrosoft Power Automateのフロー画面の実行](assets/runFlowParameters.png)

1. 「**[!UICONTROL 保存]**」を選択します。
1. 選択 **[!UICONTROL テスト]**.
1. 選択 **[!UICONTROL 手動]** その後 **[!UICONTROL 保存とテスト]**.
1. 「**[!UICONTROL 続行]**」を選択します。
1. 値を入力 *名前（名）*, *姓*、および *給与*.
1. 選択 **[!UICONTROL フローを実行]**.

OneDriveフォルダーに、Word文書から生成されたPDFが表示されるようになりました。 OneDriveでPDF文書を開くと、データがテキストタグの場所に結合されます。


## パート3:PDFを1つに組み合わせる

Word文書を生成してPDFに変換したので、次は複数のPDF文書を組み合わせることになりました。

>[!NOTE]
>
>前の操作では、文書のコピーをファイルとしてOneDriveに保存しました。 結合ツールなどのPDFを使用するには、ファイルをOneDriveに保存する必要はありません。 代わりに、1つのアクションから次のアクションに出力を直接渡すことができます。これは、各アクションの後にOneDriveに保存するよりも優れています。 ただし、デモの目的では、これらのファイルをOneDriveに保存します。

### 「結合PDFを追加」手順

1. フローの編集時に、 **[!UICONTROL +次のステップ]** をクリックして、フローの最後にアクションを追加します。
1. 検索 *Adobe PDF Services* をクリックします。
1. 選択 **[!UICONTROL Adobe PDF Services]**.
1. を選択します **[!UICONTROL PDFを結合]** アクション：
1. を **[!UICONTROL 結合PDFファイル名]** フィールドに、目的のファイル名を入力します(例：*CombinedDocument.pdf*)を参照してください。
1. を **[!UICONTROL ファイルコンテンツ – 1]** フィールドで、 **[!UICONTROL 動的コンテンツ]** 挿入するパネル *PDFファイルコンテンツ* 値を **[!UICONTROL WordをPDFに変換]** 手順
1. 次の文書を追加するには、 **+ [!UICONTROL 新しいアイテムを追加]**.
1. を **[!UICONTROL ファイルコンテンツ – 2]** フィールドで、 **[!UICONTROL 動的コンテンツ]** 挿入するパネル **[!UICONTROL 出力ファイルコンテンツ]** 値を *Wordテンプレートから文書を生成* 手順

![Microsoft Power Automateの「PDFを結合」アクション](assets/mergePDFAction.png)

### 結合したPDFをOneDriveに保存

文書が結合されたら、文書をOneDriveに保存し直すことができます。

1. Power Automateで、 **+ [!UICONTROL 新しい手順]**.
1. 検索 *OneDrive* をクリックします。
1. 職場または個人のOneDriveアカウントを選択するには、 **[!UICONTROL OneDrive for Business]** または **[!UICONTROL OneDrive]**.
1. 検索 *ファイルを作成* をクリックします。
1. 選択 **[!UICONTROL ファイルを作成]**.
1. を **[!UICONTROL フォルダーパス]** フィールドで、フォルダーアイコンを選択して、OneDrive内のファイルの保存先を指定します。
1. を **[!UICONTROL ファイル名]** フィールドに、ファイルの名前を設定します。 出力はPDFなので、ファイル名の末尾は.pdfにする必要があります。
1. を **[!UICONTROL ファイルコンテンツ]** フィールド、使用 **[!UICONTROL 動的コンテンツ]** 挿入するパネル *PDFファイルコンテンツ* 値を **[!UICONTROL PDFを結合]** 手順

   ![Microsoft Power Automateのフローの概要](assets/flowOverviewSavedMergedDocument.png)

### 体験版のフロー

1. 「**[!UICONTROL 保存]**」を選択します。
1. 選択 **[!UICONTROL テスト]**.
1. 選択 **[!UICONTROL 手動]** その後 **[!UICONTROL 保存とテスト]**.
1. 「**[!UICONTROL 続行]**」を選択します。
1. 値を入力 *名前（名）*, *姓*、および *給与*.
1. 選択 **[!UICONTROL フローを実行]**.

OneDriveフォルダーには、1つ目と2つ目の文書のページが結合されたPDFーが表示されます。

## パート4:Protect PDF文書

ドキュメントを生成した後、OneDriveに保存する前に追加の手順を追加することで、ドキュメントが編集されないように保護できます。

### PDF を保護

1. Power Automateでフローを編集する際に、 **+** 間に **[!UICONTROL PDFを結合]** actionおよび **[!UICONTROL ファイル3を作成]** アクション：

   ![新しいアクションを追加する2つのアクションの間のプラス記号](assets/addActionToProtect.png)

1. 選択 **[!UICONTROL アクションを追加]**.
1. 検索 *Adobe PDF Services* をクリックします。
1. 選択 **[!UICONTROL Adobe PDF Services]**.
1. を選択します **[!UICONTROL ProtectPDFの表示]** アクション：
1. を **[!UICONTROL ファイル名]** フィールドに、拡張子.pdfで終わる名前を希望する名前に設定します。
1. 設定する **[!UICONTROL Password]** をクリックし、指定したパスワードに文書を開きます。
1. を **[!UICONTROL ファイルコンテンツ]** フィールドで、 **[!UICONTROL 動的コンテンツ]** 挿入するパネル *PDFファイルコンテンツ* 値を **[!UICONTROL PDFを結合]** 手順

### OneDriveに保存を更新

ドキュメントが保護されたら、ファイルをOneDriveに保存し直すことができます。 この例では、既存 **ファイル3を作成** 新規アクション *ファイルコンテンツ* 値を返します。

1. カーソルを **[!UICONTROL ファイルコンテンツ]** フィールドを **[!UICONTROL ファイル3を作成]** アクション：
1. 次を使用します **[!UICONTROL 動的コンテンツ]** 挿入するパネル *PDFファイルコンテンツ* 値を **ProtectPDFの表示** 手順

### 体験版のフロー

1. 「**[!UICONTROL 保存]**」を選択します。
1. 選択 **[!UICONTROL テスト]**.
1. 選択 **[!UICONTROL 手動]** その後 **[!UICONTROL 保存とテスト]**.
1. 「**[!UICONTROL 続行]**」を選択します。
1. 値を入力 *名前（名）*, *姓*、および *給与*.
1. 選択 **[!UICONTROL フローを実行]**.

OneDriveフォルダーに、文書を閲覧するためのパスワードの入力を求める複合PDFが表示されます。

## 次の手順

このチュートリアルでは、Word文書をPDFに変換し、データに基づいて文書を生成し、文書を結合して、パスワードで保護しました。 詳しくは、Microsoft Power AutomateのAdobe PDFサービスコネクタで使用できるその他のアクションを確認してください。

* Microsoft Power Automateで使用可能な、作成済みのテンプレートを表示します。
* 学ぶ [記事](https://medium.com/adobetech/tagged/microsoft-power-automate) Adobeテクニカルブログで公開されています。
* レビュー [文書](https://developer.adobe.com/document-services/docs/overview/document-generation-api/) Adobe Document Generation API用。

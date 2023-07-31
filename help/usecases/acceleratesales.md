---
title: セールス・プロセスの促進
description: ドキュメントのエクスペリエンスを統合して販売を促進する方法を説明します [!DNL Adobe Acrobat Services]
role: Developer
level: Intermediate
type: Tutorial
feature: Use Cases
thumbnail: KT-10222.jpg
jira: KT-10222
exl-id: 9430748f-9e2a-405f-acac-94b08ad7a5e3
source-git-commit: b65ffa3efa3978587564eb0be0c0e7381c8c83ab
workflow-type: tm+mt
source-wordcount: '1755'
ht-degree: 0%

---

# 販売プロセスの迅速化

![ユースケースの英雄バナー](assets/UseCaseAccelerateSalesHero.jpg)

ホワイトペーパーから契約書、契約書まで、購入プロセス全体を通して数多くの文書が必要です。 このチュートリアルでは、以下の方法を学習します。 [[!DNL Adobe Acrobat Services]](https://developer.adobe.com/document-services/) このジャーニー全体のドキュメントエクスペリエンスを統合して、販売を促進できます。

## データから契約書と販売注文を生成

販売契約書、契約、およびその他の文書は、特定の条件に基づいて大幅に異なる場合があります。 例えば、販売契約書には、特定の国や州に居住する、または契約書の一部として特定の製品を含めるなど、固有の基準に基づいて特定の用語のみが含まれる場合があります。 これらの文書を手動で作成したり、多くの異なるテンプレートのバリエーションを維持したりすると、変更を手動で確認するための法律費用が大幅に増加する可能性があります。

[Adobe文書生成API](https://developer.adobe.com/document-services/apis/doc-generation/) crmやその他のデータシステムからデータを取得し、そのデータに基づいて販売ドキュメントを動的に生成できます。

## 資格情報の取得

まず、無料のAdobe PDFサービスの資格情報を登録します。

1. 移動 [こちら](https://documentcloud.adobe.com/dc-integration-creation-app-cdn/main.html) をクリックして資格情報を登録します。
1. Adobe IDを使用してログインします。
1. 資格情報名を設定します（例：販売契約書デモ）。

   ![資格情報名の設定のスクリーンショット](assets/accsales_1.png)

1. サンプルコードをダウンロードする言語を選択します（例：Node.js）。
1. チェックして同意 **[!UICONTROL デベロッパー条件]**.
1. 選択 **[!UICONTROL 資格情報の作成]**.
サンプルファイル、pdfservices-api-credentials.json、認証用のprivate.keyを含むZIPファイルがコンピューターにダウンロードされます。

   ![資格情報のスクリーンショット](assets/accsales_2.png)

1. 選択 **[!UICONTROL Microsoft Wordアドインを入手]** または [AppSource](https://appsource.microsoft.com/en-cy/product/office/WA200002654) をインストールします。

   >[!NOTE]
   >
   >Wordアドインをインストールするには、Microsoft 365内でアドインをインストールする権限が必要です。 権限がない場合は、Microsoft 365管理者に連絡してください。

## データ

特定のデータシステムからデータを取得する場合は、そのデータをJSONデータとして出力するか、独自のスキーマを生成する必要があります。 このシナリオでは、事前に作成された次のサンプルデータセットを使用します。

```
{
    "salesOrder": {
        "comment": "Make sure to call 555-555-1234 when you arrive. The front door is broken."
    },
    "company": {
        "name":"Home Services Co.",
        "address": {
            "city": "Homestead",
            "state": "NY",
            "zip": "14623",
            "streetAddress": "123 Demohome Street"
        }
    },
    "customer": {
        "address": {
            "city": "Seattle",
            "state": "WA",
            "zip": "98052",
            "streetAddress": "20341 Whitworth Institute 405 N. Whitworth"
        },
        "email": "mailto:jane-doe@xyz.edu",
        "jobTitle": "Professor",
        "name": "Jane Doe",
        "telephone": "(425) 123-4567",
        "url": "http://www.janedoe.com"
    },
    "tax": {
        "state":"WA",
        "rate": 0.08
    },
    "referencesOrder": [
        {
            "description": "Carpet Cleaning Service - 3BR 2BA",
            "totalPaymentDue": {
                "price": 359.54
            },
            "orderedItem": {
                "description": "Carpet Cleaning Service"
            }
        },
        {
            "description": "Home Cleaning Service - 3BR 2BA",
            "totalPaymentDue": {
                "price": 299.99
            },
            "orderedItem": {
                "description": "House Cleaning Service"
            }
        }
    ]
}
```

## 文書に基本的なタグを追加する

このシナリオでは、ダウンロード可能な受注文書が使用されます [こちら](https://github.com/benvanderberg/adobe-document-generation-samples/blob/main/SalesOrder/Exercise/SalesOrder_Base.docx?raw=true).

![サンプル販売注文文書のスクリーンショット](assets/accsales_3.png)

1. を開きます *SalesOrder.docx* Microsoft Wordのサンプル文書。
1. Document Generationプラグインがインストールされている場合は、 **[!UICONTROL 文書の生成]** をクリックします。 リボンに「ドキュメントの生成」が表示されない場合は、次の手順を実行します。
1. 選択 **[!UICONTROL 開始する]**.
1. 上記で記述したJSONサンプルデータを *JSONデータ* フィールドに入力します。

   ![JSONデータのコピーのスクリーンショット](assets/accsales_4.png)

次に、ドキュメント生成タガーパネルに移動して、ドキュメントにタグを配置します。

1. 置換するテキストを選択します(例： *会社名*)を参照してください。
1. を *文書生成タガー* パネルで、「name」を検索します。
1. タグのリストで、「company」の下の「name」を選択します。
1. 選択 **[!UICONTROL テキストを挿入]**.

   ![タグの挿入のスクリーンショット](assets/accsales_5.png)

   このプロセスによって、 {{company.name}} タグはJSON内のパスの下にあるため。

   ```
   {
   …
   "company": {
       "name":"Home Services Co.",
       …
   },
   …
   }
   ```

文書内の追加のタグの一部（番地、市区町村、都道府県、郵便番号など）について、これらのアクションを繰り返します。

## 生成された文書をプレビューする

Microsoft Word内で直接、サンプルJSONデータに基づいて生成された文書をプレビューできます。

1. を *文書生成タガー* パネル、選択 **[!UICONTROL 文書を生成]**. 初めてログインするときには、Adobe IDを使用してログインするように求められる場合があります。 選択 **[!UICONTROL ログイン]** プロンプトに入力し、資格情報を使用してログインします。

   ![生成されたドキュメントのプレビュー方法のスクリーンショット](assets/accsales_6.png)

1. 選択 **[!UICONTROL 文書を表示]**.

   ![「ドキュメントを表示」ボタンのスクリーンショット](assets/accsales_7.png)

1. ブラウザーウィンドウが開き、ドキュメントの結果をプレビューできます。

   ![ブラウザーウィンドウのドキュメントのスクリーンショット](assets/accsales_8.png)

元のサンプルデータのデータに置き換えられていた文書内のタグを確認できます。

![タグがデータに置き換えられたスクリーンショット](assets/accsales_9.png)

## テンプレートに表を追加する

次のシナリオでは、文書内の表に製品リストを追加します。

1. 表を配置する位置にカーソルを挿入します。
1. を *文書生成タガー* パネル、選択 **[!UICONTROL 詳細]**.
1. 展開 **[!UICONTROL テーブルとリスト]**.
1. を *テーブルレコード* フィールド、選択 *referencesOrder*&#x200B;は、すべての製品アイテムをリストする配列です。
1. 「列レコードの選択」フィールドで、含める値を入力します *description* および *totalPaymentDue.price* フィールドに入力します。
1. 選択 **[!UICONTROL 表を挿入]**.

   ![表の挿入のスクリーンショット](assets/accsales_10.png)

Microsoft Wordの表の場合と同様に、スタイル、サイズ、その他のパラメーターに合わせて表を編集します。

## 数値計算の追加

数値計算を使用すると、配列などのデータの集合に基づいて合計やその他の計算を計算できます。 このシナリオでは、小計を計算するフィールドを追加します。

1. を選択します *0.00ドル* 小計のタイトルの横に表示されます。
1. を *[!UICONTROL 文書生成タガー]* パネル、展開 **[!UICONTROL 数値計算]**.
1. 未満 *[!UICONTROL 計算タイプを選択]*&#x200B;を選択します **[!UICONTROL 集約]**.
1. 未満 *[!UICONTROL タイプを選択]*&#x200B;を選択します **[!UICONTROL 合計]**.
1. 未満 *[!UICONTROL レコードの選択]*&#x200B;を選択します **[!UICONTROL ReferencesOrder]**.
1. アンダー*[!UICONTROL 集計を実行する項目を選択します]**、次を選択 **[!UICONTROL totalPaymentsDue.price]**.
1. 選択 **[!UICONTROL 計算の挿入]**.

このプロセスでは、値の合計を提供する計算タグが挿入されます。 JSONata計算を使用すると、より高度な計算を行うことができます。 次に例を示します。

* 小計： `${{expr($sum(referencesOrder.totalPaymentDue.price))}}`
referencesOrder.totalPaymentDue.priceの合計を計算します。

* 消費税： `${{expr($sum(referencesOrder.totalPaymentDue.price)*0.08)}}`
価格を計算し、8%で乗算して税金を計算します。

* ご請求額： `${{expr($sum(referencesOrder.totalPaymentDue.price)*1.08)}}`
価格と1.08の倍数を計算して、小計+税を計算します。

## コンディショナル条件を追加

コンディショナルセクションを使用すると、特定の条件が満たされた場合にのみ文や段落を含めることができます。 このシナリオでは、特定の状態に一致するセクションのみが含まれます。

1. ドキュメント内で、というセクションを見つけます。 *カリフォルニア州のプライバシーに関する声明*.
1. カーソルでセクションを選択します。

   ![選択範囲のスクリーンショット](assets/accsales_11.png)

1. を *[!UICONTROL 文書生成タガー]*、選択 **[!UICONTROL 詳細]**.
1. 展開 **[!UICONTROL 条件付きコンテンツ]**.
1. を *[!UICONTROL レコードの選択]* フィールド、検索、選択 **[!UICONTROL customer.address.state]**.
1. を *[!UICONTROL 演算子の選択]* フィールド、選択 **=**.
1. を *[!UICONTROL 値フィールド]*、種類 *カルシウム*.
1. 選択 **[!UICONTROL コンディションを挿入]**.

「カリフォルニア」セクションは、customer.address.state = CAの場合にのみ生成されたドキュメントに表示されます。

次に、「WASHINGTON PRIVACY STATEMENTS」のセクションを選択して、上記の手順を繰り返し、値「CA」を「WA」に置き換えます。

## ダイナミック画像を追加する

Document Generation APIを使用すると、データから画像を動的に挿入できます。 これは、異なるサブブランドがあり、特定の業界でより関連性のあるロゴ、ポートレート画像、または画像を変更する場合に便利です。

画像は、dataまたはbase64コンテンツのURLで渡すことができます。 この例ではURLを使用しています。

1. 画像を含める場所にカーソルを置きます。
1. を *[!UICONTROL 文書生成タガー]* パネル、選択 **[!UICONTROL 詳細]**.
1. 展開 **[!UICONTROL 画像]**.
1. を *[!UICONTROL タグを選択]* フィールド、選択 **[!UICONTROL logo]**.
1. を *[!UICONTROL オプションの代替テキスト]* フィールドに、説明（ロゴ）を入力します。 このプロセスでは、次のような画像プレースホルダーが挿入されます。

   ![プレースホルダー画像のスクリーンショット](assets/accsales_12.png)

ただし、既にレイアウトにある画像に対して動的に画像を設定する場合は、次の操作を実行します。

1. 挿入されたプレースホルダー画像を右クリックします。

   ![プレースホルダー画像のスクリーンショット](assets/accsales_13.png)

1. 選択 **[!UICONTROL 代替テキストを編集]**.
1. パネルで、次のようなテキストをコピーします。
   `{ "location-path": "logo", "image-props": { "alt-text": "Logo" }}`
1. 動的にするドキュメント内の別の画像を選択します。

   ![ドキュメント内の新しい画像のスクリーンショット](assets/accsales_14.png)

1. 画像を右クリックし、 **[!UICONTROL 代替テキストを編集]**.
1. 値をパネルに貼り付けます。

この処理では、画像がデータ内のロゴ変数にある画像に置き換えられます。

## Acrobat Signのタグを追加する

Adobe Acrobat Signを使用すると、文書に電子サインを取り込むことができます。 Acrobat Signでは、webインターフェイス内でフィールドを簡単にドラッグ&amp;ドロップできますが、テキストタグを使用して、署名やその他のフィールドの配置を制御することもできます。 AdobeのDocument Generation Taggerを使用すると、これらのテキストタグフィールドを簡単に配置できます。

1. サンプル文書で署名が必要な場所に移動します。
1. 署名が必要な場所にカーソルを挿入します。
1. を *[!UICONTROL Adobe文書生成タガー]* パネル、選択 **[!UICONTROL Adobe Sign]**.
1. を *[!UICONTROL 受信者数を指定]* フィールドに受信者の数を設定します（この例では1です）。
1. を *[!UICONTROL 受信者]* フィールド、選択 **[!UICONTROL 署名者 – 1]**.
1. を *[!UICONTROL フィールド]* 文字を選択 **[!UICONTROL 署名]**.
1. 選択 **[!UICONTROL Adobe Signテキストタグを挿入]**.

タグが文書に挿入されます。

![文書内の署名タグのスクリーンショット](assets/accsales_15.png)

Acrobat Signには、日付フィールドなど、配置できるその他の種類のフィールドがいくつか用意されています。
1. を *フィールド* 文字を選択 **[!UICONTROL 日付]**.
1. 文書内の日付の位置の上にカーソルを移動します。
1. 選択 **[!UICONTROL Adobe Signテキストタグを挿入]**.

![文書内の日付タグのスクリーンショット](assets/accsales_16.png)

## 契約書を生成

これで文書にタグが付き、作業を開始できます。 次のセクションでは、Node.jsのDocument Generation APIサンプルを使用してドキュメントを生成する方法について説明します。ただし、これらは任意の言語で動作します。

資格情報の登録時にダウンロードしたpdfservices-node-sdk-samples-masterを開きます。 pdfservices-api-credentials.jsonとprivate.keyファイルが含まれている必要があります。

1. npm installを使用して依存関係をインストールするターミナルを開きます。
1. サンプルdata.jsonをresourcesフォルダーにコピーします。
1. Wordテンプレートをresourcesフォルダーにコピーします。
1. サンプルフォルダーのルートディレクトリに、 generate-salesOrder.jsという名前の新しいファイルを作成します。

```
const PDFServicesSdk = require('@adobe/pdfservices-node-sdk');
const fs = require('fs');
const path = require('path');

var dataFileName = path.join('resources', '<INSERT JSON FILE');
var outputFileName = path.join('output', 'salesOrder_'+Date.now()+".pdf");
var inputFileName = path.join('resources', '<INSERT DOCX>');

//Loads credentials from the file that you created.
const credentials =  PDFServicesSdk.Credentials
    .serviceAccountCredentialsBuilder()
    .fromFile("pdfservices-api-credentials.json")
    .build();

// Setup input data for the document merge process
const jsonString = fs.readFileSync(dataFileName),
jsonDataForMerge = JSON.parse(jsonString);

// Create an ExecutionContext using credentials
const executionContext = PDFServicesSdk.ExecutionContext.create(credentials);

// Create a new DocumentMerge options instance
const documentMerge = PDFServicesSdk.DocumentMerge,
documentMergeOptions = documentMerge.options,
options = new documentMergeOptions.DocumentMergeOptions(jsonDataForMerge, documentMergeOptions.OutputFormat.PDF);

// Create a new operation instance using the options instance
const documentMergeOperation = documentMerge.Operation.createNew(options)

// Set operation input document template from a source file.
const input = PDFServicesSdk.FileRef.createFromLocalFile(inputFileName);
documentMergeOperation.setInput(input);

// Execute the operation and Save the result to the specified location.
documentMergeOperation.execute(executionContext)
.then(result => result.saveAsFile(outputFileName))
.catch(err => {
    if(err instanceof PDFServicesSdk.Error.ServiceApiError
        || err instanceof PDFServicesSdk.Error.ServiceUsageError) {
        console.log('Exception encountered while executing operation', err);
    } else {
        console.log('Exception encountered while executing operation', err);
    }
});
```

1. 置換 `<INSERT JSON FILE>` /resources内のJSONファイルの名前を使用します。
1. 置換 `<INSERT DOCX>` にDOCXファイルの名前を付けます。
1. を実行するには、ターミナルを使用してノードgenerate-salesOrder.jsを実行します。

出力ファイルは、正しく生成された文書の/outputフォルダーにある必要があります。

## 詳細オプション

ドキュメントが生成されたら、次のような追加のアクションを実行できます。

* パスワードで文書を保護
* 画像が大きい場合はPDFを圧縮
* 文書で電子サインをキャプチャ

利用可能なその他のアクションについて詳しくは、サンプルファイルの/srcフォルダーにあるスクリプトを参照してください。 様々なアクションに関するドキュメントを確認して、詳細を確認することもできます。

## その他の使用例

[!DNL Adobe Acrobat Services] デジタルドキュメントワークフローにより、販売サイクルの多くの部分を合理化できます。

* Adobe PDF Embed APIを使用すると、Webサイトにホワイトペーパーやその他のコンテンツを埋め込むだけでなく、視聴者の分析結果を測定して収集することもできます
* Acrobat Signを使用して、生成された契約書の電子サインを取得
* Adobe PDF Extract APIを使用して、PDF文書から契約書データを抽出します

## さらなる学習

さらに学習したいですか？ その他の使用方法を確認する [!DNL Adobe Acrobat Services]:

* 詳細情報： [文書](https://developer.adobe.com/document-services/docs/overview/)
* Adobe Experience Leagueのその他のチュートリアルを見る
* PDFを活用する方法を確認するには、 /srcフォルダー内のサンプルスクリプトを使用します
* フォロー [Adobe技術ブログ](https://medium.com/adobetech/tagged/adobe-document-cloud) 最新のヒントとコツ
* 購入する [ペーパークリップ（月間ライブストリーム）](https://www.youtube.com/playlist?list=PLcVEYUqU7VRe4sT-Bf8flvRz1XXUyGmtF) を使用したオートメーションについて学習するには [!DNL Adobe Acrobat Services].=======
* 詳細情報： [文書](https://developer.adobe.com/document-services/docs/overview/)
* Adobe Experience Leagueのその他のチュートリアルを見る
* PDFを活用する方法を確認するには、 /srcフォルダー内のサンプルスクリプトを使用します
* フォロー [Adobe技術ブログ](https://medium.com/adobetech/tagged/adobe-document-cloud) 最新のヒントとコツ
* 購入する [ペーパークリップ（月間ライブストリーム）](https://www.youtube.com/playlist?list=PLcVEYUqU7VRe4sT-Bf8flvRz1XXUyGmtF) を使用したオートメーションについて学習するには [!DNL Adobe Acrobat Services]

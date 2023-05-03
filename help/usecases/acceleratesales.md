---
title: 販売プロセスの迅速化
description: 文書エクスペリエンスを [!DNL Adobe Acrobat Services]
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-10222.jpg
kt: 10222
exl-id: 9430748f-9e2a-405f-acac-94b08ad7a5e3
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1755'
ht-degree: 0%

---

# 販売プロセスを加速

![ユースケースのヒーローバナー](assets/UseCaseAccelerateSalesHero.jpg)

ホワイトペーパーから契約書、合意書まで、購入プロセス全体を通して数多くの文書が必要です。 このチュートリアルでは、 [[!DNL Adobe Acrobat Services]](https://developer.adobe.com/document-services/) では、このカスタマージャーニー全体を通じて文書のエクスペリエンスを統合し、販売を促進することができます。

## データからの契約書および販売注文の生成

売買契約、契約、およびその他の文書は、特定の条件によって大きく異なる場合があります。 例えば、販売契約には、特定の国や州に存在すること、または契約に特定の製品を含めることなど、一意の基準に基づいて特定の条件のみが含まれる場合があります。 これらの文書を手動で作成したり、多数の異なるテンプレートのバリエーションを保持したりすると、手動で変更をレビューする場合に関連する法的費用が大幅に増加する可能性があります。

[Adobeドキュメント生成 API](https://developer.adobe.com/document-services/apis/doc-generation/) では、CRM などのデータシステムからデータを取得し、そのデータに基づいて販売文書を動的に生成できます。

## 資格情報の取得

無料のAdobe PDF Services 資格情報に登録します。

1. 移動 [ここ](https://documentcloud.adobe.com/dc-integration-creation-app-cdn/main.html) 」をクリックして資格情報を登録します。
1. Adobe IDを使用してログインします。
1. 資格証明名を設定します（Sales Agreements Demo など）。

   ![資格証明名の設定のスクリーンショット](assets/accsales_1.png)

1. サンプルコードをダウンロードする言語を選択します（Node.js など）。
1. 確認して同意する **[!UICONTROL デベロッパー向け条件]**&#x200B;を選択します。
1. 選択 **[!UICONTROL 資格情報の作成]**を選択します。
認証用のサンプルファイル、pdfservices-api-credentials.json および private.key を含む ZIP ファイルがコンピューターにダウンロードされます。

   ![資格情報のスクリーンショット](assets/accsales_2.png)

1. 選択 **[!UICONTROL Microsoft Word アドインを入手]** または [AppSource](https://appsource.microsoft.com/en-cy/product/office/WA200002654) を選択します。

   >[!NOTE]
   >
   >Word アドインをインストールするには、Microsoft 365 内でアドインをインストールする権限が必要です。 権限がない場合は、Microsoft 365 管理者にお問い合わせください。

## データ

特定のデータシステムからデータを取得する場合は、そのデータを JSON データとして出力するか、独自のスキーマを生成する必要があります。 このシナリオでは、事前に作成された次のサンプルデータセットを使用します。

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

## ドキュメントへの基本タグの追加

このシナリオでは、ダウンロード可能な受注ドキュメントを使用します [ここ](https://github.com/benvanderberg/adobe-document-generation-samples/blob/main/SalesOrder/Exercise/SalesOrder_Base.docx?raw=true)を選択します。

![セールスオーダー文書のサンプルのスクリーンショット](assets/accsales_3.png)

1. パネルの「 *SalesOrder.docx* Microsoft Word のサンプル文書。
1. 文書生成プラグインがインストールされている場合は、 **[!UICONTROL 文書生成]** 」をクリックします。 リボンに「ドキュメントの生成」が表示されない場合は、次の手順に従ってください。
1. 選択 **[!UICONTROL はじめに]**&#x200B;を選択します。
1. 上記の JSON サンプルデータを *JSON データ* 」フィールドに入力します。

   ![JSON データのコピーのスクリーンショット](assets/accsales_4.png)

次に、文書生成タグパネルに移動して、文書にタグを配置します。

1. 置換するテキストを選択します ( *会社名*)。
1. 」を *文書生成タグ* パネルで、「名前」を検索します。
1. タグのリストで、「会社」の下の「名前」を選択します。
1. 選択 **[!UICONTROL テキストを挿入]**&#x200B;を選択します。

   ![タグの挿入のスクリーンショット](assets/accsales_5.png)

   このプロセスによって、 {{company.name}} タグは JSON のパスの下にあるからです。

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

住所、市区町村、都道府県、郵便番号など、文書内の他のタグに対してこれらのアクションを繰り返します。

## 生成されたドキュメントのプレビュー

Microsoft Word 内で直接、サンプルの JSON データに基づいて生成された文書をプレビューできます。

1. 」を *文書生成タグ* パネルで、 **[!UICONTROL ドキュメントの生成]**&#x200B;を選択します。 初めてAdobe IDでログインするように求められる場合があります。 選択 **[!UICONTROL サインイン]** 」をクリックし、資格情報を使用してログインするように求めるプロンプトに従います。

   ![生成されたドキュメントをプレビューする方法のスクリーンショット](assets/accsales_6.png)

1. 選択 **[!UICONTROL ドキュメントを表示]**&#x200B;を選択します。

   ![「文書を表示」ボタンのスクリーンショット](assets/accsales_7.png)

1. ブラウザーウィンドウが開き、ドキュメントの結果をプレビューできます。

   ![ブラウザーウィンドウでのドキュメントのスクリーンショット](assets/accsales_8.png)

元のサンプルデータのデータに置き換えられた文書内のタグを確認できます。

![データに置き換えられたタグのスクリーンショット](assets/accsales_9.png)

## テンプレートへのテーブルの追加

次のシナリオでは、ドキュメント内のテーブルに製品リストを追加します。

1. 表を配置する必要がある場所にカーソルを挿入します。
1. 」を *文書生成タグ* パネルで、 **[!UICONTROL 詳細]**&#x200B;を選択します。
1. 展開 **[!UICONTROL 表とリスト]**&#x200B;を選択します。
1. 」を *テーブルレコード* 」フィールドで、「 *referencesOrder*&#x200B;を使用します。これは、すべての製品アイテムをリストする配列です。
1. [ 列レコードの選択 ] フィールドに、含めるレコードを入力します *description* および *totalPaymentDue.price* 」フィールドに入力します。
1. 選択 **[!UICONTROL 表を挿入]**&#x200B;を選択します。

   ![挿入テーブルのスクリーンショット](assets/accsales_10.png)

Microsoft Word の他のテーブルと同様に、テーブルを編集してスタイル、サイズ、その他のパラメーターを調整します。

## 数値計算の追加

数値計算では、配列などのデータのコレクションに基づいて合計やその他の計算を計算できます。 このシナリオでは、小計を計算するフィールドを追加します。

1. ツールバーの「 *$0.00* をクリックします。
1. 」を *[!UICONTROL 文書生成タグ]* パネル、展開 **[!UICONTROL 数値計算]**&#x200B;を選択します。
1. Under *[!UICONTROL 計算タイプの選択]*&#x200B;で、 **[!UICONTROL 集約]**&#x200B;を選択します。
1. Under *[!UICONTROL タイプを選択]*&#x200B;で、 **[!UICONTROL 合計]**&#x200B;を選択します。
1. Under *[!UICONTROL レコードの選択]*&#x200B;で、 **[!UICONTROL ReferencesOrder]**&#x200B;を選択します。
1. *未満[!UICONTROL 集計を実行する項目を選択してください]**、 **[!UICONTROL totalPaymentsDue.price]**&#x200B;を選択します。
1. 選択 **[!UICONTROL 計算の挿入]**&#x200B;を選択します。

この処理により、値の合計を示す計算タグが挿入されます。 JSONata 計算を使用すると、より高度な計算を行うことができます。 次に例を示します。

* 小計： `${{expr($sum(referencesOrder.totalPaymentDue.price))}}`
referencesOrder.totalPaymentDue.price の合計を計算します。

* 消費税： `${{expr($sum(referencesOrder.totalPaymentDue.price)*0.08)}}`
価格を計算し、8%を乗算して税金を計算します。

* 合計期限： `${{expr($sum(referencesOrder.totalPaymentDue.price)*1.08)}}`
価格と 1.08 の倍数を計算して、小計+税を計算します。

## 条件項の追加

条件セクションを使用すると、特定の条件が満たされた場合にのみ文や段落を含めることができます。 このシナリオでは、特定の状態に一致するセクションのみが含まれます。

1. 文書内の「 *カリフォルニア州のプライバシーに関する声明*&#x200B;を選択します。
1. カーソルで断面を選択します。

   ![選択のスクリーンショット](assets/accsales_11.png)

1. 」を *[!UICONTROL 文書生成タグ]*&#x200B;で、 **[!UICONTROL 詳細]**&#x200B;を選択します。
1. 展開 **[!UICONTROL 条件付きコンテンツ]**&#x200B;を選択します。
1. 」を *[!UICONTROL レコードの選択]* 」フィールドに移動し、「 **[!UICONTROL customer.address.state]**&#x200B;を選択します。
1. 」を *[!UICONTROL 演算子の選択]* 」フィールドで、「 **=**&#x200B;を選択します。
1. 」を *[!UICONTROL 値フィールド]*、タイプ *CA*&#x200B;を選択します。
1. 選択 **[!UICONTROL 条件の挿入]**&#x200B;を選択します。

「California」セクションは、customer.address.state = CA の場合にのみ、生成されたドキュメントに表示されます。

次に、「ワシントンのプライバシーに関する声明」セクションを選択し、上記の手順を繰り返して、値「CA」を「WA」に置き換えます。

## ダイナミックイメージの追加

ドキュメント生成 API を使用すると、データから画像を動的に挿入できます。 これは、異なるサブブランドがあり、ロゴ、ポートレート画像、または画像を変更して、特定の業界に関連性を高めたい場合に便利です。

画像は、データまたは base64 コンテンツの URL によって渡すことができます。 この例では URL を使用しています。

1. 画像を含める場所にカーソルを置きます。
1. 」を *[!UICONTROL 文書生成タグ]* パネルで、 **[!UICONTROL 詳細]**&#x200B;を選択します。
1. 展開 **[!UICONTROL 画像]**&#x200B;を選択します。
1. 」を *[!UICONTROL タグの選択]* 」フィールドで、「 **[!UICONTROL ロゴ]**&#x200B;を選択します。
1. 」を *[!UICONTROL オプションの代替テキスト]* 」フィールドに、説明（ロゴなど）を入力します。 この処理により、次のようなイメージプレースホルダーが挿入されます。

   ![プレースホルダー画像のスクリーンショット](assets/accsales_12.png)

ただし、レイアウトに既に存在するイメージ上でイメージを動的に設定する場合は、次のようにします。

1. 挿入されたプレースホルダー画像を右クリックします。

   ![プレースホルダー画像のスクリーンショット](assets/accsales_13.png)

1. 選択 **[!UICONTROL 代替テキストの編集]**&#x200B;を選択します。
1. パネルで、次のようなテキストをコピーします。
   `{ "location-path": "logo", "image-props": { "alt-text": "Logo" }}`
1. ドキュメント内で動的にする別の画像を選択します。

   ![ドキュメント内の新しい画像のスクリーンショット](assets/accsales_14.png)

1. 画像を右クリックし、「 **[!UICONTROL 代替テキストの編集]**&#x200B;を選択します。
1. パネルに値をペーストします。

この処理により、画像がデータの logo 変数にある画像に置き換えられます。

## Acrobat Signへのタグの追加

Adobe Acrobat Signを使用すると、文書に電子サインをキャプチャできます。 Acrobat Signを使用すると、Web インターフェイス内でフィールドを簡単にドラッグ&amp;ドロップできますが、テキストタグを使用して、署名やその他のフィールドの配置を制御することもできます。 Adobe文書生成タグを使用すると、これらのテキストタグフィールドを簡単に配置できます。

1. サンプル文書で署名が必要な場所に移動します。
1. 署名が必要な場所にカーソルを挿入します。
1. 」を *[!UICONTROL Adobe文書生成タグ]* パネルで、 **[!UICONTROL Adobe Sign]**&#x200B;を選択します。
1. 」を *[!UICONTROL 受信者の数の指定]* 」フィールドで、受信者の数を設定します（この例では 1）。
1. 」を *[!UICONTROL 受信者]* 」フィールドで、「 **[!UICONTROL Signer-1]**&#x200B;を選択します。
1. 」を *[!UICONTROL フィールド]* 」タイプで、「 **[!UICONTROL 署名]**&#x200B;を選択します。
1. 選択 **[!UICONTROL 挿入/Adobe Sign/テキストタグ]**&#x200B;を選択します。

タグが文書に挿入されます。

![文書内の署名タグのスクリーンショット](assets/accsales_15.png)

Acrobat Signには、日付フィールドなど、他にも複数のタイプのフィールドを配置できます。
1. 」を *フィールド* 」タイプで、「 **[!UICONTROL 日付]**&#x200B;を選択します。
1. 文書内の日付の位置の上にカーソルを移動します。
1. 選択 **[!UICONTROL 挿入/Adobe Sign/テキストタグ]**&#x200B;を選択します。

![文書内の日付タグのスクリーンショット](assets/accsales_16.png)

## 契約書の生成

これで文書にタグが付き、作業を開始する準備が整いました。 次のセクションでは、Node.js のドキュメント生成 API サンプルを使用してドキュメントを生成する方法について説明します。ただし、どの言語でも動作します。

資格情報の登録時にダウンロードした pdfservices-node-sdk-samples-master を開きます。 pdfservices-api-credentials.json および private.key ファイルがこれらのファイルに含まれている必要があります。

1. ターミナルを開き、npm install を使用して依存関係をインストールします。
1. sample data.json を resources フォルダーにコピーします。
1. Word テンプレートを resources フォルダーにコピーします。
1. サンプルフォルダーのルートディレクトリに、generate-salesOrder.js という名前の新しいファイルを作成します。

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

1. 置換 `<INSERT JSON FILE>` の部分は、/resources にある JSON ファイルの名前を使用します。
1. 置換 `<INSERT DOCX>` に DOCX ファイルの名前を付けます。
1. 実行するには、ターミナルを使用してノード generate-salesOrder.js を実行します。

出力ファイルは、ドキュメントが正しく生成された/output フォルダー内にある必要があります。

## 詳細オプション

ドキュメントが生成されたら、次のような追加のアクションを実行できます。

* 文書をパスワードで保護
* 大きな画像がある場合はPDFを圧縮
* 文書への電子サインのキャプチャ

使用可能なその他のアクションについて詳しくは、サンプルファイルの/src フォルダーにあるスクリプトを参照してください。 各種アクションのドキュメントを確認して、詳細を確認することもできます。

## その他のユースケース

[!DNL Adobe Acrobat Services] デジタル文書ワークフローにより、販売サイクルの多くの部分を合理化できます。

* Adobe PDF Embed API を使用して、ホワイトペーパーやその他のコンテンツを Web サイトに埋め込むと同時に、視聴率に関する分析を測定して収集します
* Acrobat Signを使用して、生成された契約書に電子サインを取得します。
* Adobe PDF Extract API を使用したPDF文書からの契約書データの抽出

## 詳細

詳しくは、 その他の活用方法を見る [!DNL Adobe Acrobat Services]:

* さらに詳しく [文書](https://developer.adobe.com/document-services/docs/overview/)
* その他のAdobe Experience Leagueチュートリアル
* /src フォルダーにあるサンプルスクリプトを使用して、PDF
* フォロー [Adobe技術ブログ](https://medium.com/adobetech/tagged/adobe-document-cloud) 最新のヒントとテクニック
* サブスクリプション [Paper Clips（毎月のライブストリーム）](https://www.youtube.com/playlist?list=PLcVEYUqU7VRe4sT-Bf8flvRz1XXUyGmtF) を使用した自動化について学習するには [!DNL Adobe Acrobat Services]を選択します。=======
* さらに詳しく [文書](https://developer.adobe.com/document-services/docs/overview/)
* その他のAdobe Experience Leagueチュートリアル
* /src フォルダーにあるサンプルスクリプトを使用して、PDF
* フォロー [Adobe技術ブログ](https://medium.com/adobetech/tagged/adobe-document-cloud) 最新のヒントとテクニック
* サブスクリプション [Paper Clips（毎月のライブストリーム）](https://www.youtube.com/playlist?list=PLcVEYUqU7VRe4sT-Bf8flvRz1XXUyGmtF) を使用した自動化について学習するには [!DNL Adobe Acrobat Services]

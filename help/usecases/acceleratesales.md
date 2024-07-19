---
title: セールス・プロセスの促進
description: ドキュメントのエクスペリエンスと [!DNL Adobe Acrobat Services]を統合して販売を促進する方法を説明します
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-10222
thumbnail: KT-10222.jpg
exl-id: 9430748f-9e2a-405f-acac-94b08ad7a5e3
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1704'
ht-degree: 0%

---

# 販売プロセスの迅速化

![使用事例の英雄バナー](assets/UseCaseAccelerateSalesHero.jpg)

ホワイトペーパーから契約書、契約書まで、購入プロセス全体を通して数多くの文書が必要です。 このチュートリアルでは、このジャーニー全体で[[!DNL Adobe Acrobat Services]](https://developer.adobe.com/document-services/)がドキュメントエクスペリエンスを統合して、販売促進に役立つ方法について説明します。

## データから契約書と販売注文を生成

販売契約書、契約、およびその他の文書は、特定の基準によって大きく異なる場合があります。 例えば、販売契約書には、特定の国や州に居住する、または契約書の一部として特定の製品を含めるなど、固有の基準に基づいて特定の用語のみが含まれる場合があります。 これらの文書を手動で作成したり、多くの異なるテンプレートのバリエーションを維持したりすると、変更を手動で確認するための法律費用が大幅に増加する可能性があります。

[Adobeドキュメント生成API](https://developer.adobe.com/document-services/apis/doc-generation/)を使用すると、CRMやその他のデータシステムからデータを取得し、そのデータに基づいて売上ドキュメントを動的に生成できます。

## 資格情報の取得

まず、無料のAdobe PDFサービスの資格情報を登録します。

1. [ここ](https://documentcloud.adobe.com/dc-integration-creation-app-cdn/main.html)に移動して、資格情報を登録します。
1. Adobe IDを使用してログインします。
1. 資格情報名を設定します（例：販売契約書デモ）。

   ![資格情報の名前を設定したスクリーンショット](assets/accsales_1.png)

1. サンプルコードをダウンロードする言語を選択します（例：Node.js）。
1. **[!UICONTROL デベロッパー利用条件]**&#x200B;に同意する場合にオンにします。
1. **[!UICONTROL 資格情報の作成]**を選択します。
サンプルファイル、pdfservices-api-credentials.json、認証用のprivate.keyを含むZIPファイルがコンピューターにダウンロードされます。

   ![資格情報のスクリーンショット](assets/accsales_2.png)

1. 「**[!UICONTROL Microsoft Wordアドインを入手]**」を選択するか、[AppSource](https://appsource.microsoft.com/en-cy/product/office/WA200002654)に移動してインストールします。

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

このシナリオでは、販売注文文書を使用しています。この文書は[こちら](https://github.com/benvanderberg/adobe-document-generation-samples/blob/main/SalesOrder/Exercise/SalesOrder_Base.docx?raw=true)からダウンロードできます。

![サンプル販売注文文書のスクリーンショット](assets/accsales_3.png)

1. Microsoft Wordで&#x200B;*SalesOrder.docx*&#x200B;サンプル文書を開きます。
1. Document Generationプラグインがインストールされている場合は、リボンの&#x200B;**[!UICONTROL Document Generation]**&#x200B;を選択します。 リボンに「ドキュメントの生成」が表示されない場合は、次の手順を実行します。
1. 「**[!UICONTROL 使用を開始]**」を選択します。
1. 上記のJSONサンプルデータを&#x200B;*JSON Data*&#x200B;フィールドにコピーします。

   ![JSONデータのコピーのスクリーンショット](assets/accsales_4.png)

次に、ドキュメント生成タガーパネルに移動して、ドキュメントにタグを配置します。

1. 置換するテキストを選択します（例： *会社名*）。
1. *Document Generation Tagger*&#x200B;パネルで、「name」を検索します。
1. タグのリストで、「company」の下の「name」を選択します。
1. **[!UICONTROL [テキストの挿入]]**&#x200B;を選択します。

   ![タグを挿入したスクリーンショット](assets/accsales_5.png)

   このプロセスでは、タグがJSONのパスの下にあるため、{{company.name}}と呼ばれるタグが配置されます。

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

1. *Document Generation Tagger*&#x200B;パネルで、**[!UICONTROL Generate document]**&#x200B;を選択します。 初めてログインするときには、Adobe IDを使用してログインするように求められる場合があります。 「**[!UICONTROL ログイン]**」を選択し、画面の指示に従って資格情報でログインします。

   ![生成されたドキュメントをプレビューする方法のスクリーンショット](assets/accsales_6.png)

1. **[!UICONTROL ドキュメントの表示]**&#x200B;を選択します。

   ![[ドキュメントの表示]ボタンのスクリーンショット](assets/accsales_7.png)

1. ブラウザーウィンドウが開き、ドキュメントの結果をプレビューできます。

   ![ブラウザーウィンドウのドキュメントのスクリーンショット](assets/accsales_8.png)

元のサンプルデータのデータに置き換えられていた文書内のタグを確認できます。

![タグがデータに置き換えられたスクリーンショット](assets/accsales_9.png)

## テンプレートに表を追加する

次のシナリオでは、文書内の表に製品リストを追加します。

1. 表を配置する位置にカーソルを挿入します。
1. *Document Generation Tagger*&#x200B;パネルで、**[!UICONTROL Advanced]**&#x200B;を選択します。
1. **[!UICONTROL [テーブルとリスト]]**&#x200B;を展開します。
1. *テーブルレコード*&#x200B;フィールドで、*referencesOrder*&#x200B;を選択します。これは、すべての製品項目をリストする配列です。
1. 列レコードの選択フィールドに、*description*&#x200B;と&#x200B;*totalPaymentDue.price*&#x200B;フィールドを含めるように入力します。
1. **[!UICONTROL テーブルの挿入]**&#x200B;を選択します。

   ![テーブル挿入のスクリーンショット](assets/accsales_10.png)

Microsoft Wordの表の場合と同様に、スタイル、サイズ、その他のパラメーターに合わせて表を編集します。

## 数値計算の追加

数値計算を使用すると、配列などのデータの集合に基づいて合計やその他の計算を計算できます。 このシナリオでは、小計を計算するフィールドを追加します。

1. 小計タイトルの横にある&#x200B;*$0.00*&#x200B;を選択します。
1. *[!UICONTROL Document Generation Tagger]*&#x200B;パネルで、**[!UICONTROL 数値計算]**&#x200B;を展開します。
1. *[!UICONTROL 計算の種類の選択]*&#x200B;で、**[!UICONTROL 集計]**&#x200B;を選択します。
1. *[!UICONTROL 種類の選択]*&#x200B;で、**[!UICONTROL 合計]**&#x200B;を選択します。
1. *[!UICONTROL レコードの選択]*&#x200B;で、**[!UICONTROL 参照順序]**&#x200B;を選択します。
1. *[!UICONTROL 集計を実行する項目を選択]で**2}totalPaymentsDue.price ]**を選択します。**[!UICONTROL 
1. **[!UICONTROL 計算の挿入]**&#x200B;を選択します。

このプロセスでは、値の合計を提供する計算タグが挿入されます。 JSONata計算を使用すると、より高度な計算を行うことができます。 次に例を示します。

* 小計： `${{expr($sum(referencesOrder.totalPaymentDue.price))}}`
referencesOrder.totalPaymentDue.priceの合計を計算します。

* 消費税： `${{expr($sum(referencesOrder.totalPaymentDue.price)*0.08)}}`
価格を計算し、8%で乗算して税金を計算します。

* 期限の合計： `${{expr($sum(referencesOrder.totalPaymentDue.price)*1.08)}}`
価格と1.08の倍数を計算して、小計+税を計算します。

## コンディショナル条件を追加

コンディショナルセクションを使用すると、特定の条件が満たされた場合にのみ文や段落を含めることができます。 このシナリオでは、特定の状態に一致するセクションのみが含まれます。

1. ドキュメント内で、*カリフォルニア州のプライバシーに関する声明*&#x200B;というセクションを見つけます。
1. カーソルでセクションを選択します。

   ![選択範囲のスクリーンショット](assets/accsales_11.png)

1. *[!UICONTROL Document Generation Tagger]*&#x200B;で、**[!UICONTROL Advanced]**&#x200B;を選択します。
1. **[!UICONTROL 条件付きコンテンツ]**&#x200B;を展開します。
1. *[!UICONTROL レコードの選択]*&#x200B;フィールドで、**[!UICONTROL customer.address.state]**&#x200B;を検索して選択します。
1. *[!UICONTROL 演算子の選択]*&#x200B;フィールドで、**=**&#x200B;を選択します。
1. *[!UICONTROL 値フィールド]*&#x200B;に、「*CA*」と入力します。
1. **[!UICONTROL [条件の挿入]]**&#x200B;を選択します。

「カリフォルニア」セクションは、customer.address.state = CAの場合にのみ生成されたドキュメントに表示されます。

次に、「WASHINGTON PRIVACY STATEMENTS」のセクションを選択して、上記の手順を繰り返し、値「CA」を「WA」に置き換えます。

## ダイナミック画像を追加する

Document Generation APIを使用すると、データから画像を動的に挿入できます。 これは、異なるサブブランドがあり、特定の業界でより関連性のあるロゴ、ポートレート画像、または画像を変更する場合に便利です。

画像は、dataまたはbase64コンテンツのURLで渡すことができます。 この例ではURLを使用しています。

1. 画像を含める場所にカーソルを置きます。
1. *[!UICONTROL Document Generation Tagger]*&#x200B;パネルで、**[!UICONTROL Advanced]**&#x200B;を選択します。
1. **[!UICONTROL 画像]**&#x200B;を展開します。
1. 「*[!UICONTROL タグを選択]*」フィールドで、**[!UICONTROL ロゴ]**&#x200B;を選択します。
1. *[!UICONTROL オプションの代替テキスト]*&#x200B;フィールドに、説明（ロゴなど）を入力します。 このプロセスでは、次のような画像プレースホルダーが挿入されます。

   ![プレースホルダー画像のスクリーンショット](assets/accsales_12.png)

ただし、既にレイアウトにある画像に対して動的に画像を設定する場合は、次の操作を実行します。

1. 挿入されたプレースホルダー画像を右クリックします。

   ![プレースホルダー画像のスクリーンショット](assets/accsales_13.png)

1. **[!UICONTROL 代替テキストの編集]**&#x200B;を選択します。
1. パネルで、次のようなテキストをコピーします。
   `{ "location-path": "logo", "image-props": { "alt-text": "Logo" }}`
1. 動的にするドキュメント内の別の画像を選択します。

   ![ドキュメント内の新しい画像のスクリーンショット](assets/accsales_14.png)

1. 画像を右クリックして、**[!UICONTROL 代替テキストの編集]**&#x200B;を選択します。
1. 値をパネルに貼り付けます。

この処理では、画像がデータ内のロゴ変数にある画像に置き換えられます。

## Acrobat Signのタグを追加する

Adobe Acrobat Signを使用すると、文書に電子サインを取り込むことができます。 Acrobat Signでは、webインターフェイス内でフィールドを簡単にドラッグ&amp;ドロップできますが、テキストタグを使用して、署名やその他のフィールドの配置を制御することもできます。 AdobeのDocument Generation Taggerを使用すると、これらのテキストタグフィールドを簡単に配置できます。

1. サンプル文書で署名が必要な場所に移動します。
1. 署名が必要な場所にカーソルを挿入します。
1. *[!UICONTROL Adobeドキュメント生成タガー]*&#x200B;パネルで、**[!UICONTROL Adobe Sign]**&#x200B;を選択します。
1. *[!UICONTROL 受信者の数を指定]*&#x200B;フィールドで、受信者の数（この例では1人）を設定します。
1. 「*[!UICONTROL 受信者]*」フィールドで、「**[!UICONTROL 署名者–1]**」を選択します。
1. 「*[!UICONTROL フィールド]*」タイプで、「**[!UICONTROL 署名]**」を選択します。
1. **[!UICONTROL 「Adobe Signのテキストタグを挿入」]**&#x200B;を選択します。

タグが文書に挿入されます。

![ドキュメント内の署名タグのスクリーンショット](assets/accsales_15.png)

Acrobat Signには、日付フィールドなど、配置できるその他の種類のフィールドがいくつか用意されています。
1. *フィールド*&#x200B;の種類で、**[!UICONTROL 日付]**&#x200B;を選択します。
1. 文書内の日付の位置の上にカーソルを移動します。
1. **[!UICONTROL 「Adobe Signのテキストタグを挿入」]**&#x200B;を選択します。

![ドキュメント内の日付タグのスクリーンショット](assets/accsales_16.png)

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

1. `<INSERT JSON FILE>`を/resources内のJSONファイルの名前に置き換えます。
1. `<INSERT DOCX>`をDOCXファイルの名前に置き換えます。
1. を実行するには、ターミナルを使用してノードgenerate-salesOrder.jsを実行します。

出力ファイルは、正しく生成された文書の/outputフォルダーにある必要があります。

## 詳細オプション

ドキュメントが生成されたら、次のような追加のアクションを実行できます。

* パスワードで文書を保護
* 画像が大きい場合はPDFを圧縮
* 文書で電子サインをキャプチャ

利用可能なその他のアクションについて詳しくは、サンプルファイルの/srcフォルダーにあるスクリプトを参照してください。 様々なアクションに関するドキュメントを確認して、詳細を確認することもできます。

## その他の使用例

[!DNL Adobe Acrobat Services]は、デジタルドキュメントワークフローを使用して、販売サイクルの多くの部分を合理化するのに役立ちます。

* Adobe PDF Embed APIを使用すると、Webサイトにホワイトペーパーやその他のコンテンツを埋め込むだけでなく、視聴者の分析結果を測定して収集することもできます
* Acrobat Signを使用して、生成された契約書の電子サインを取得
* Adobe PDF Extract APIを使用して、PDF文書から契約書データを抽出します

## さらなる学習

さらに学習したいですか？ [!DNL Adobe Acrobat Services]を使用するその他の方法を確認してください：

* 詳細については、[ドキュメント](https://developer.adobe.com/document-services/docs/overview/)を参照してください
* Adobe Experience Leagueのその他のチュートリアルを見る
* PDFを活用する方法を確認するには、 /srcフォルダー内のサンプルスクリプトを使用します
* [Adobe技術ブログ](https://medium.com/adobetech/tagged/adobe-document-cloud)をフォローして、最新のヒントとコツを確認してください
* [ペーパークリップ（月間ライブストリーム）](https://www.youtube.com/playlist?list=PLcVEYUqU7VRe4sT-Bf8flvRz1XXUyGmtF)のサブスクリプションを購入すると、[!DNL Adobe Acrobat Services]を使用したオートメーションについて学習できます。
=======
* 詳細については、[ドキュメント](https://developer.adobe.com/document-services/docs/overview/)を参照してください
* Adobe Experience Leagueのその他のチュートリアルを見る
* PDFを活用する方法を確認するには、 /srcフォルダー内のサンプルスクリプトを使用します
* [Adobe技術ブログ](https://medium.com/adobetech/tagged/adobe-document-cloud)をフォローして、最新のヒントとコツを確認してください
* [ペーパークリップ（月間ライブストリーム）](https://www.youtube.com/playlist?list=PLcVEYUqU7VRe4sT-Bf8flvRz1XXUyGmtF)のサブスクリプションを購入すると、[!DNL Adobe Acrobat Services]を使用したオートメーションについて学習できます

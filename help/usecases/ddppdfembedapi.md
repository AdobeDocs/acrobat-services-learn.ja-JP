---
title: Digital Document Publishing
description: Adobe PDF Embed API を使用して、Web ページ内に埋め込まれたPDFドキュメントを表示する方法について説明します
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8090.jpg
kt: 8090
exl-id: 3aa9aa40-a23c-409c-bc0b-31645fa01b40
source-git-commit: 799b37e526073893fe7c078db547798d6c31d1b2
workflow-type: tm+mt
source-wordcount: '1903'
ht-degree: 0%

---

# デジタル文書パブリッシング

![ユースケースのヒーローバナー](assets/UseCaseDigitalHero.jpg)

電子文書は様々な場所にあります。実際には、多くの [数兆のPDF](https://itextpdf.com/en/blog/technical-notes/do-you-know-how-many-pdf-documents-exist-world) その数は毎日増加しています。 Web ページにPDFビューアを埋め込むと、HTMLや CSS のデザインを変更したり、Web サイトへのアクセスを妨害したりすることなく、ドキュメントを表示できるようになります。

人気のあるシナリオを探りましょう。 会社の役職 [web サイトのホワイトペーパー](https://www.adobe.io/apis/documentcloud/dcsdk/digital-content-publishing.html)
」をクリックします。 web サイトのマーケターは、利用者がPDFベースのコンテンツとやり取りする方法をより深く理解し、web ページやブランドと組み合わせたいと考えています。 彼らはホワイトペーパーを [ゲート含有量](https://whatis.techtarget.com/definition/gated-content-ungated-content#:~:text=Gated%20content%20is%20online%20materials,about%20their%20jobs%20and%20organizations.)を使用して、ダウンロードできるユーザーを制御できます。

## 学習内容

この実践チュートリアルでは、 [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html)を無料で利用できます。 これらの例では、JavaScript、Node.js、Express.js、HTML、CSS を使用しています。 完全なプロジェクトコードは、 [GitHub](https://www.google.com/url?q=https://github.com/marcelooliveira/EmbedPDF/tree/main/pdf-app&amp;sa=D&amp;source=editors&amp;ust=1617129543031000&amp;usg=AOvVaw2rzSwYuJ_JI7biVIgbNMw1)を選択します。

## 関連する API とリソース

* [PDF埋め込み API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [PDFサービス API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [プロジェクトコード](https://www.google.com/url?q=https://github.com/marcelooliveira/EmbedPDF/tree/main/pdf-app&amp;sa=D&amp;source=editors&amp;ust=1617129543031000&amp;usg=AOvVaw2rzSwYuJ_JI7biVIgbNMw1)

## ノード Web アプリケーションの作成

まず、Node.js と Express を使用して、見栄えの良いテンプレートを使用し、ダウンロード用の複数のPDFを提供するサイトを作成します。

まず [node.js のダウンロードとインストール](https://nodejs.org/en/download/)を選択します。

最小限の Web アプリケーション構造で Node.js プロジェクトを簡単に作成するには、アプリケーションジェネレータツールをインストールします `` `express-generator` ``を選択します。

```
npm install express-generator -g
```

次に、ビューエンジンとして選択して、pdf-app という名前の新しい Express アプリケーションを作成します。

```
express pdf-app --view=ejs
```

\\pdf-app ディレクトリに移動し、プロジェクトのすべての依存関係をインストールします。

```
cd pdf-app
npm install
```

次に、ローカル Web サーバーを起動し、アプリケーションを実行します。

```
npm start
```

最後に、 <http://localhost:3000>を選択します。

![基本 Web サイトのスクリーンショット](assets/ddp_1.png)

これで、基本的な Web サイトが完成しました。

## ホワイトペーパーデータのレンダリング

ホワイトペーパーを Web サイトに投稿するには、ホワイトペーパーデータを定義し、Web サイト上で準備してこれらのドキュメントを表示します。 まず、プロジェクトのルートに新しい\\data フォルダーを作成します。 利用可能なホワイトペーパーに関する情報は、 [data.json](https://github.com/marcelooliveira/EmbedPDF/blob/main/pdf-app/data/data.json)を選択します。

Web アプリを洗練された外観にするには、 [Bootstrap](https://getbootstrap.com/) および [Font Awesome](https://fontawesome.com/) フロントエンドライブラリ

```
npm install bootstrap
npm install font-awesome
```

app.js ファイルを開き、これらのディレクトリを静的ファイルのソースとして含め、既存の `` `express.static` `` 行

```
app.use(express.static(path.join(__dirname, '/node_modules/bootstrap/dist')));
app.use(express.static(path.join(__dirname, '/node_modules/font-awesome')));
```

PDFドキュメントを含めるには、プロジェクトの\\public フォルダーの下に\\pdfs という名前のフォルダーを作成します。 自分でサムネールとPDFを作成するのではなく、この [GitHub リポジトリフォルダー](https://github.com/marcelooliveira/EmbedPDF/tree/main/pdf-app/public) を\\pdfs フォルダーと\\image フォルダーにコピーします。

\\public\\pdfs フォルダーに、次のドキュメントのPDFが追加されました。

![PDFファイルアイコンのスクリーンショット](assets/ddp_2.png)

\\public\\images フォルダーには、各サムネール文書のサムネールを含める必要がありますが、次のPDFを行います。

![サムネールのPDF画像](assets/ddp_3.png)

次に、ホームページをルーティングするロジックを含む\\routes\\index.js ファイルを開きます。 data.json ファイルからホワイトペーパーデータを使用するには、ファイルシステムへのアクセスと操作を担当する Node.js モジュールを読み込む必要があります。 次に、 `fs` 定数を使用します。

```
const fs = require('fs');
```

次に、data.json ファイルを読み取って解析し、papers 変数に格納します。

```
let rawdata = fs.readFileSync('data/data.json');
let papers = JSON.parse(rawdata);
```

次に、この行を変更してインデックスビューのレンダリングメソッドを呼び出し、papers コレクションをインデックスビューのモデルとして渡します。

```
res.render('index', { title: 'Embedding PDF', papers: papers });
```

ホワイトペーパーのコレクションをホームページに表示するには、\\views\\index.ejs ファイルを開き、既存のコードをプロジェクトの [インデックスファイル](https://github.com/marcelooliveira/EmbedPDF/blob/main/pdf-app/views/index.ejs)を選択します。

ここで、npm start を再実行して開きます <http://localhost:3000> 」をクリックして、使用可能なホワイトペーパーのコレクションを表示します。

![ホワイトペーパーのサムネールのスクリーンショット](assets/ddp_4.png)

次のセクションでは、Web サイトの拡張と [PDF埋め込み API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html) を選択して、Web ページのPDFドキュメントを表示します。 PDF埋め込み API は無料で使用できます。API 資格情報を取得するだけです。

## PDF埋め込み API 資格情報の取得

無料のPDF埋め込み API 資格情報を取得するには、 [はじめに](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) 」ページが表示されます。

クリック **新しい資格情報の作成** その後 **はじめに：**

![新しい資格情報の作成方法のスクリーンショット](assets/ddp_5.png)

この時点で、無料アカウントをお持ちでない場合は、無料アカウントに登録するよう求められます。

選択 **PDF埋め込み API**」をクリックし、資格情報の名前とアプリケーションドメインを入力します。 次の **localhost** ドメインに保存されます。

![PDF埋め込み API の新しい資格情報を作成する画面](assets/ddp_6.png)

ツールバーの「 **資格情報の作成** 」ボタンをクリックして、PDF資格情報にアクセスし、クライアント ID（API キー）を取得します。

![新しい資格情報をコピーする方法のスクリーンショット](assets/ddp_7.png)

Node.js プロジェクトで、PDFのルートフォルダに.ENV という名前のファイルを作成し、アプリケーションの Embed Client ID の環境変数を、前の手順の API KEY 資格情報の値で指定します。

```
PDF_EMBED_CLIENT_ID=**********************************************
```

後でこのクライアント ID を使用して、PDF埋め込み API にアクセスします。 Node.js コードを使用してこの環境変数にアクセスするには、dotenv パッケージをインストールします。

```
npm install dotenv
```

app.js ファイルを開き、ファイルの先頭に次の行を追加して、Node.js が dotenv モジュールを読み込めるようにします。

```
require('dotenv').config();
```

## Web App でのPDFの表示

PDF埋め込み API を使用して、サイトにPDFを表示できるようになりました。 ライブを開く [PDF埋め込み API デモ](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf)を選択します。

![ライブPDF埋め込み API デモのスクリーンショット](assets/ddp_8.png)

左側のパネルで、Web サイトのニーズに最適な埋め込みモードを選択できます。

* **ウィンドウ全体**:このPDFは、すべての Web ページスペースをカバーします

* **サイズコンテナ**:PDFは、Web ページ内に、サイズが限られた Div で一度に 1 ページずつ表示されます

* **インライン**:PDF全体が web ページ内の div に表示されます

* **ライトボックス**:PDFは、web ページの上にレイヤーとして表示されます

ホワイトペーパーにはインライン埋め込みモードを使用し、後でコードジェネレーターを使用してアプリケーションにPDFを埋め込むことをお勧めします。

## インライン埋め込みモードページの作成

Web ページにPDFビューアを埋め込んですべてのページを同時に表示するには、インライン埋め込みモードを使用して新しいページを作成します。

EJS ビューエンジンを使用して、\\views\\in-line.ejs ファイルに新しいビューを作成します。

```
<! html DOCTYPE >
<html>
<head>
<title>
<%= title %>
</title>
<link rel='stylesheet' href='/stylesheets/style.css' />
<link rel='stylesheet' href='/css/bootstrap.min.css'/>
<link rel='stylesheet' href='/css/font-awesome.min.css' />
<style type="text/css">
p {
font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif
}
</style>
</head>
<body class="m-0">
<div>
<main>
<div class="row">
<div class="col-sm-3"></div>
<div class="col-sm-6">
<h3>
<p class="text-center">Grow your business, establish your brand,<br
/>
```

顧客を第一に

```
</p>
</h3>
<div>
<p class="text-center">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do<br />
eiusmod tempor incididunt ut labore et dolore</p>
</div>
</div>
</main>
<footer>
<div class="row">
<div class="col-sm-3"></div>
<div class="col-sm-6">
<p class="text-center">Bodea Inc. Your trusted partner since 2008</p>
</div>
</div>
</footer>
</div>
</div>
</body>
</html>
```

次に、\\views\\index.ejs を変更して、インラインビューを開くボタンを作成します。

```
<div class="card-body">
<h5 class="card-title">
<span>
<%= paper.title %>
</span>
</h5>
<p>
<a class="btn btn-sm btn btn-danger" href="/in-line/<%=
paper.id %>">
<span type="button"></span>
<span class="fa fa-file-pdf-o"></span>&nbsp;View Document</button>
</a>
</p>
</div>
```

app.js ファイルを開き、indexRouter 宣言の後に新しいルーターを宣言します。

```
var indexRouter = require('./routes/index');
var inLineRouter = require('./routes/in-line');
```

次に、このコードを app.use(&#39;/&#39;, indexRouter)；の後に追加します。インライン埋め込みモードビューをルータに関連付けるには：

```
app.use('/', indexRouter);
app.use('/in-line', inLineRouter);
```

ここで、\\routes の下に新しい in-line.js ファイルを作成して、新しいルータロジックを作成します。 Web アプリケーションのバックエンドを有効にするノードモジュールである Express を含めます。

```
var express = require('express');
const fs = require('fs');
var router = express.Router();
```

次に、特定のホワイトペーパー ID のGET要求を処理し、in-line.ejs ビューをレンダリングするエンドポイントを作成します。

```
router.all('/:id', function(req, res, next) {
let rawdata = fs.readFileSync('data/data.json');
let papers = JSON.parse(rawdata);
let paper = papers.filter(p => p.id == parseInt(req.params.id))[0];
res.render('in-line', { title: paper.title, paper: paper });
});
module.exports = router;
```

もう一度 [ライブデモ](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf) を使用して、PDF埋め込み API コードを自動的に生成します。 クリック **インライン** 左側のパネルから：

![ライブPDF埋め込み API デモのスクリーンショット](assets/ddp_8.png)

クリック **コードを生成** 」をクリックし、サイズ変更HTMLビューアの表示に必要なコンテナコードをPDFします。

![コードプレビューのスクリーンショット](assets/ddp_9.png)

クリック **コードをコピー** コードを in-line.ejs ファイルにペーストします。

```
<div>
<p class="text-center">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do<br />
eiusmod tempor incididunt ut labore et dolore</p>
</div>
<div class="row align-items-center border border-primary">
<div id="adobe-dc-view" style="width: 800px;"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
document.addEventListener("adobe_dc_view_sdk.ready", function(){
var adobeDCView = new AdobeDC.View({clientId: "<YOUR_CLIENT_ID>", divId: "adobe-dc-view"});
adobeDCView.previewFile({
content:{location: {url: "https://documentcloud.adobe.com/view-sdk-demo/PDFs/Bodea Brochure.pdf"}},
metaData:{fileName: "Bodea Brochure.pdf"}
}, {embedMode: "IN_LINE"});
});
</script>
</div>
```

ただし、ドキュメントパラメーターはまだハードコーディングされています。 これらを EJS 括弧構文 (\&lt;%= someValue %\>) に置き換えて、ホワイトペーパーモデルデータに従ってページをレンダリングします。

```
<div id="adobe-dc-view" style="width: 800px;"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
document.addEventListener("adobe_dc_view_sdk.ready", function () {
var adobeDCView = new AdobeDC.View({ clientId: "<%=process.env.PDF_EMBED_CLIENT_ID %>", divId: "adobe-dc-view" });
adobeDCView.previewFile({
content: { location: { url: "<%= paper.pdf %>" } },
metaData: { fileName: "<%= paper.fileName %>" }
}, {
embedMode: "IN_LINE"
});
});
</script>
```

次に、npm start コマンドを使用してアプリケーションを実行し、 <http://localhost:3000>を選択します。

![白紙のサムネールPDFのスクリーンショット](assets/ddp_10.png)

最後に、ホワイトペーパーを 1 つ選択して「 **ドキュメントを表示** インライン埋め込みページを使用して新しいページを開くには、次のPDFを実行します。

![ホワイトペーパーPDFの画面 ](assets/ddp_11.png)

「ダウンロード」オプションと「プリントPDF」オプションがPDFされていることに注意してください。

![ダウンロードと印刷オプションのスクリーンショット](assets/ddp_12.png)

これらのフラグをバックエンドで制御する場合。 後で、ユーザー ID に基づいて承認制御を実装し、ビジネスルールに従ってアクセスを制限できます。 ここでは、この複雑さを考慮する必要はないので、\\routes\\in-line.js を変更して、モデルオブジェクトに authenticated プロパティと permissions プロパティを含めます。

```
let authenticated = false;
res.render('in-line', {
title: paper.title,
paper: paper,
authenticated: authenticated,
permissions: {
showDownloadPDF: true,
showPrintPDF: true,
showFullScreen: true
}
});
```

次に、\\views\\in-line.ejs を変更して、Web ページがバックエンドから送信されるフラグ値をレンダリングできるようにします。

```
embedMode: "IN_LINE",
showDownloadPDF: <%= permissions.showDownloadPDF %>,
showPrintPDF: <%= permissions.showPrintPDF %>,
showFullScreen: <%= permissions.showFullScreen %>
Now, open the in-line.js route file and modify it to disallow the printing, downloading, and full-screen controls.
permissions: {
showDownloadPDF: false,
showPrintPDF: false,
showFullScreen: false
}
```

次に、アプリケーションを再実行して、この変更がアプリケーションビューアにどのように反映されるかをPDFしてください。

![スクリーンショット、PDFファイル](assets/ddp_13.png)

## ゲートコンテンツの作成

エンドユーザーの考え方によると、同社の web サイトのマーケターは、利用者がPDFベースのコンテンツとやり取りする方法をより深く理解し、そのコンテンツを他の web ページやブランドと組み合わせることを望んでいます。

ここではPDFの埋め込みに重点を置いているため、ユーザー認証機能は作成しません。 代わりに、ユーザー情報を受け入れ、ユーザーがフォームを送信するとPDF文書を表示する Web フォームを使用して、シンプルで偽のペイウォールを実装するだけです。

\\routes\\in-line.js ファイルを以下の内容に置き換えて、ビューモデルにユーザー情報を提供します。

```
var express = require('express');
const fs = require('fs');
var router = express.Router();
router.all('/:id', function(req, res, next) {
let rawdata = fs.readFileSync('data/data.json');
let papers = JSON.parse(rawdata);
let paper = papers.filter(p => p.id == parseInt(req.params.id))[0];
let authenticated = false;
let user = {};
if (req.body.firstName) {
user = {
firstName: req.body.firstName,
lastName: req.body.lastName,
jobTitle: req.body.jobTitle,
email: req.body.email,
};
authenticated = true;
}
res.render('in-line', {
title: paper.title,
paper: paper,
user: user,
authenticated: authenticated,
permissions: {
showDownloadPDF: false,
showPrintPDF: false,
showFullScreen: false
}
});
});
module.exports = router;
```

次に、\\views\\in-line.ejs の内容を次のコードに置き換えます。 認証されたユーザーかどうかに応じて、PDF・データ・フォームまたはユーザー・ビューアが表示されます。

```
<!DOCTYPE html>
<html>
<head>
<title>
<%= title %>
</title>
<link rel='stylesheet' href='/css/bootstrap.min.css'/>
<link rel='stylesheet' href='/css/font-awesome.min.css' />
<style type="text/css">
p {
font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif
}
</style>
</head>
<body class="m-0">
<% if (authenticated) { %>
<header class="bg-dark text-white">
<div class="text-right mr-4">Hello, <%= user.firstName %> <%= user.lastName%></div>
</header>
<% } %>
<div>
<main>
<div class="row">
<div class="col-sm-3"></div>
<div class="col-sm-6">
<h3>
<p class="text-center">Grow your business, establish your brand,<br
/>
```

顧客を第一に

```
</p>
</h3>
<div>
<p class="text-center">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do<br />
eiusmod tempor incididunt ut labore et dolore</p>
</div>
<% if (!authenticated) { %>
<div class="row">
<form method="POST" class="center-panel text offset-md-3 col-md-6 border">
<fieldset class="offset-md-1">
<legend>Submit your info to<br/>access the whitepaper</legend>
<p><input name="firstName" placeholder="first name"/></p>
<p><input name="lastName" placeholder="last name"/></p>
<p><input name="jobTitle" placeholder="job title"/></p>
<p><input name="email" placeholder="email"/></p>
<p><button type="submit" class="btn btn-sm btn btn-primary">Submit</button></p>
</fieldset>
</form>
</div>
<% } %>
<% if (authenticated) { %>
<div class="row align-items-center border border-primary">
<div id="adobe-dc-view" style="width: 800px;"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
document.addEventListener("adobe_dc_view_sdk.ready", function () {
var adobeDCView = new AdobeDC.View({ clientId: "<%=process.env.PDF_EMBED_CLIENT_ID %>", divId: "adobe-dc-view" });
adobeDCView.previewFile({
content: { location: { url: "<%= paper.pdf %>" } },
metaData: { fileName: "<%= paper.fileName %>" }
}, {
embedMode: "IN_LINE",
showDownloadPDF: <%= permissions.showDownloadPDF %>,
showPrintPDF: <%= permissions.showPrintPDF %>,
showFullScreen: <%= permissions.showFullScreen %>
});
});
</script>
<% } %>
</div>
</div>
</main>
<footer>
<div class="row">
<div class="col-sm-3"></div>
<div class="col-sm-6">
<p class="text-center">Bodea Inc. Your trusted partner since 2008</p>
</div>
</div>
</footer>
</div>
</div>
</body>
</html>
```

![ゲーティングコンテンツのスクリーンショット](assets/ddp_14.png)

サイト訪問者は、情報を送信した後にのみPDFにアクセスできるようになりました。

![埋め込み型ビューアのPDFコンテンツのスクリーンショット](assets/ddp_15.png)

## イベントの有効化

マーケター向けに分析データを収集するために、PDFビューアーのイベントをアプリケーションと簡単に統合する方法を見てみましょう。 PDFEmbedAPI を使用してビューアを拡張するには、adobeDCView 変数を宣言した後で previewFile メソッドを呼び出す前に、次のコード行を追加します。

```
var adobeDCView = new AdobeDC.View({ clientId: "<%=process.env.PDF_EMBED_CLIENT_ID %>", divId: "adobe-dc-view" });
adobeDCView.registerCallback(
AdobeDC.View.Enum.CallbackType.EVENT_LISTENER,
function(event) {
console.log(event);
},
{ enablePDFAnalytics: true }
);
```

アプリケーションを再実行し、Web ブラウザーの開発者ツールを開いてイベントデータを確認します。

![コードのスクリーンショット](assets/ddp_16.png)

このデータを [Adobe Analytics](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=view) その他の分析ツールを活用できます。

## 次の手順

[!DNL Acrobat Services] API を使用すれば、開発者はアプリケーション中心のワークフローでデジタルパブリッシングのPDFを容易に解決することができます。 ここまで、ホワイトペーパーのコレクションを表示するサンプルの Node Web アプリの作成方法を見てきました。 次に、 [無料 API 資格情報](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) ホワイトペーパーへのアクセスを制限しています。このホワイトペーパーは、次の 4 つのいずれかの形式で表示できます。 [埋め込みモード](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf)を選択します。

このワークフローを組み合わせることで、 [仮説のマーケター](https://www.adobe.io/apis/documentcloud/dcsdk/digital-content-publishing.html) ホワイトペーパーのダウンロードと引き換えにリードの連絡先情報を収集し、誰がPDFと対話しているかの統計を表示します。 これらの機能を Web サイトに組み込んで、ユーザーエンゲージメントを推進および監視できます。

angularや React の開発者は、ぜひ試してみてください [追加サンプル](https://github.com/adobe/pdf-embed-api-samples) PDFの Embed API を React プロジェクトやAngularプロジェクトと統合

Adobeは、革新的なソリューションでエンドツーエンドの顧客体験を構築することを可能にします。 チェックアウト [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/viesdk) 無料で体験 他に何ができるかを調べるには、 Adobe PDF Services API を [従量制](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)[着氷](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)を選択します。

[今すぐ始める](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) を [!DNL Adobe Acrobat Services] 今日の API

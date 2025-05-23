---
title: Javaでの人事文書ワークフロー
description: '[!DNL Adobe Acrobat Services]個のAPIで、PDF機能を人事Webアプリケーションに簡単に組み込むことができます'
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-7474
thumbnail: KT-7474.jpg
exl-id: add4cc5c-06e3-4ceb-930b-e8c9eda5ca1f
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '1777'
ht-degree: 0%

---

# Javaでの人事文書ワークフロー

![使用事例の英雄バナー](assets/UseCaseHRHero.jpg)

多くの企業では、新入社員に関する文書（在宅勤務者の労働協約など）が必要です。 従来、企業は、これらの文書を管理および保存が困難な形式で物理的に管理していました。 電子ドキュメントに切り替える場合、PDFファイルは他の種類のファイルよりも安全で、変更が難しいため、最適な選択肢です。 さらに、デジタル署名もサポートしています。

## 学習内容

この実践チュートリアルでは、簡単なJava Spring MVCアプリケーションでPDFへのサインオンを行うワークプレース契約書を保存する、webベースのHRフォームの実装方法について説明します。

## 関連APIとリソース

* [PDFサービスAPI](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Adobe Sign API](https://developer.adobe.com/adobesign-api/)

* [プロジェクトコード](https://github.com/dawidborycki/adobe-sign)

## API資格情報を生成中

まず、Adobe PDFサービスAPIの無料体験版にサインアップします。 [Adobe](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html?ref=getStartedWithServicesSDK) [Webサイト](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html?ref=getStartedWithServicesSDK)に移動し、*新しい資格情報の作成*&#x200B;の下にある&#x200B;*開始*&#x200B;ボタンをクリックします。 無料体験版では、6か月間で使用できる1,000件の文書トランザクションを提供します。 次のページ（下記参照）で、サービス(PDFサービスAPI)を選択し、資格情報名（HRDocumentWFCredentialsなど）を入力して、説明を入力します。

言語（この例ではJava）を選択し、[*パーソナライズされたコードサンプルの作成*]をオンにします。 最後の手順では、コードサンプルに、使用する事前入力されたpdftools-api-credentials.jsonファイルと、API内でアプリを認証するためのプライベートキーが既に含まれていることを確認します。

最後に、*資格情報の作成*&#x200B;ボタンをクリックします。 これにより、資格情報が生成され、サンプルのダウンロードが自動的に開始されます。

![資格情報の新規作成のスクリーンショット](assets/HRWJ_1.png)

資格情報が機能していることを確認するには、ダウンロードしたサンプルを開きます。 ここでは、IntelliJ IDEAを使用しています。 ソースコードを開くと、統合開発環境(IDE)によってビルドエンジンの入力が求められます。 このサンプルではMavenを使用していますが、環境設定によってはGradleを使用して作業することもできます。

次に、`mvn clean install` Maven目標を実行してjarファイルをビルドします。

最後に、次に示すようにCombinePDFサンプルを実行します。 このコードは、出力フォルダー内にPDFを生成します。

![CombinePDFサンプルスクリーンショットを実行するメニュー](assets/HRWJ_2.png)

## Spring MVCアプリケーションの作成

資格情報を指定して、アプリケーションを作成します。 この例ではSpring Initializerを使用しています。

まず、Java 8言語とJarパッケージを使用するようにプロジェクト設定を構成します（以下のスクリーンショットを参照）。

![Spring Initializrのスクリーンショット](assets/HRWJ_3.png)

次に、Spring Web（Webから）とThymeleaf（テンプレートエンジンから）を追加します。

![Spring WebとThymeleafを追加するスクリーンショット](assets/HRWJ_4.png)

プロジェクトの作成後、pom.xmlファイルに移動し、pdftools-sdkとlog4j-slf4j-implを使用して「依存関係」セクションを補完します。

```
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>

</dependencies>
```

次に、サンプルコードでダウンロードした2つのファイルでプロジェクトのルートフォルダーを補完します。

* pdftools-api-credentials.json

* private.key

## Webフォームのレンダリング

Webフォームをレンダリングするには、個人データフォームをレンダリングし、フォームの投稿を処理するコントローラでアプリケーションを変更します。 まず、PersonFormモデルクラスを使用してアプリケーションを変更します。

```
package com.hr.docsigning;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;

public class PersonForm {
    @NotNull
    @Size(min=2, max=30)
    private String firstName;

    @NotNull
    @Size(min=2, max=30)
    private String lastName;

    public String getFirstName() {
            return this.firstName;
    }


    public void setFirstName(String firstName) {
            this.firstName = firstName;
    }

    public String getLastName() {
           return this.lastName;
    }

    public void setLastName(String lastName) {
            this.lastName = lastName;
    }

    public String GetFullName() {
           return this.firstName + " " + this.lastName;
    }
}
```

このクラスには2つのプロパティが含まれています： `firstName`と`lastName`。 また、この簡単な検証を使用して、2文字から30文字の間にあるかどうかを確認します。

モデルクラスを指定すると、コントローラを作成できます（コンパニオンコードのPersonController.javaを参照）。

```
package com.hr.docsigning;
import org.springframework.stereotype.Controller;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import javax.validation.Valid;


@Controller
public class PersonController {
    @GetMapping("/")
    public String showForm(PersonForm personForm) {
        return "form";
    }
}
```

コントローラにはshowFormメソッドが1つしかありません。 resources/templates/form.htmlにあるHTMLテンプレートを使用してフォームをレンダリングします。

```
<html>
<head>
    <link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css">
</head>
 
<body>
<div class="w3-container">
    <h1>HR Department</h1>
</div>
 
<form class="w3-panel w3-card-4" action="#" th:action="@{/}"
        th:object="${personForm}" method="post">
    <h2>Personal data</h2>
    <table>
        <tr>
            <td>First Name:</td>
            <td><input type="text" class="w3-input"
                placeholder="First name" th:field="*{firstName}" /></td>
            <td class="w3-text-red" th:if="${#fields.hasErrors('firstName')}"
                th:errors="*{firstName}"></td>
        </tr>
        <tr>
            <td>Last Name:</td>
            <td><input type="text" class="w3-input"
                placeholder="Last name" th:field="*{lastName}" /></td>
            <td class="w3-text-red" th:if="${#fields.hasErrors('lastName')}"
                th:errors="*{lastName}"></td>
        </tr>
        <tr>
            <td><button class="w3-button w3-black" type="submit">Submit</button></td>
        </tr>
    </table>
</form>
</body>
</html>
```

ダイナミックコンテンツをレンダリングするには、Thymeleafテンプレートレンダリングエンジンを使用します。 そのため、アプリケーションを実行すると、次のように表示されます。

![レンダリングされたコンテンツのスクリーンショット](assets/HRWJ_5.png)

## ダイナミックコンテンツを使用したPDFの生成

ここで、個人のデータフォームをレンダリングした後に、選択したフィールドに動的に値を入力して、バーチャルコントラクトを含むPDF文書を生成します。 具体的には、個人データをあらかじめ作成された契約に入力する必要があります。

ここでは、簡略化のために、「この契約は\&lt;人のフルネーム\>に対して用意されました」というヘッダー、サブヘッダー、および文字列定数のみを使用します。

この目標を達成するには、Adobeの[動的HTMLからPDFを作成する](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf-from-dynamic-html)の例から始めます。 このサンプルコードを分析すると、動的HTMLフィールドの生成プロセスは次のように動作することがわかります。

まず、静的なコンテンツと動的なコンテンツを含むHTMLページを準備する必要があります。 ダイナミックパーツは、JavaScriptを使用して更新されます。 つまり、PDFサービスAPIは、JSONオブジェクトをHTMLに注入します。

その後、HTMLドキュメントが読み込まれたときに呼び出されるJavaScript関数を使用して、JSONプロパティを取得します。 このJavaScript関数は、選択されたDOM要素を更新します。 個人のデータを保持してspan要素を設定する例を次に示します(コンパニオンコードのsrc\\main\\resources\\contract\\index.htmlを参照)。

```
<html>
<head>
    <link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css">
</head>
 
<body onload="updateFullName()">
    <script src="./json.js"></script>
    <script type="text/javascript">
        function updateFullName()
        {
            var document = window.document;
            document.getElementById("personFullName").innerHTML = String(
                window.json.personFullName);
        }
    </script>
 
    <div class="w3-container ">
        <h1>HR Department</h1>
 
        <h2>Contract details</h2>
 
        <p>This contract was prepared for:
            <strong><span id="personFullName"></span></strong>
        </p>
    </div>
</body>
</html>
```

次に、依存するすべてのJavaScriptファイルとCSSファイルを含むHTMLーを圧縮する必要があります。 PDFサービスAPIは、HTMLファイルを受け付けません。 代わりに、入力としてzipファイルが必要です。 この場合、zipファイルをsrc\\main\\resources\\contract\\index.zipに保存します。

後で、`PersonController`を追加して、POST要求を処理する別の方法を使用できます。

```
@PostMapping("/")
public String checkPersonInfo(@Valid PersonForm personForm,
    BindingResult bindingResult) {
    if (bindingResult.hasErrors()) {
        return "form";
    }
 
    CreateContract(personForm);
 
    return "contract-actions";
}
```

提供された個人情報を使用してPDF契約書を作成し、contract-actionsビューをレンダリングします。 後者は、生成されたPDFへのリンクとPDFへの署名を提供します。

`CreateContract`メソッドがどのように機能するかを見てみましょう（完全なリストは以下のとおりです）。 このメソッドは、次の2つのフィールドを使用します。

* log4jの`LOGGER`から、例外に関するデバッグ情報

* 生成されたPDFへのファイルパスを含む`contractFilePath`

`CreateContract`メソッドは資格情報を設定し、HTMLからPDFを作成します。 コントラクトにユーザーのデータを渡して入力するには、`setCustomOptionsAndPersonData`ヘルパーを使用します。 このメソッドは、フォームから個人のデータを取得し、前述のJSONオブジェクトを介して生成されたPDFに送信します。

また、`setCustomOptionsAndPersonData`では、ヘッダーとフッターを無効にしてPDFの表示を制御する方法も示されています。 これらの手順が完了したら、PDFファイルをoutput/contract.pdfに保存し、最終的に以前に生成されたファイルを削除します。

```
private static final Logger LOGGER = LoggerFactory.getLogger(PersonController.class);
private String contractFilePath = "output/contract.pdf"; 
private void CreateContract(PersonForm personForm) {
    try {
        // Initial setup, create credentials instance.
        Credentials credentials = Credentials.serviceAccountCredentialsBuilder()
                .fromFile("pdftools-api-credentials.json")
                .build();

        //Create an ExecutionContext using credentials 
       //and create a new operation instance.
        ExecutionContext executionContext = ExecutionContext.create(credentials);
        CreatePDFOperation htmlToPDFOperation = CreatePDFOperation.createNew();

        // Set operation input from a source file.
        FileRef source = FileRef.createFromLocalFile(
           "src/main/resources/contract/index.zip");
       htmlToPDFOperation.setInput(source);

        // Provide any custom configuration options for the operation
        // You pass person data here to dynamically fill out the HTML
        setCustomOptionsAndPersonData(htmlToPDFOperation, personForm);

        // Execute the operation.
        FileRef result = htmlToPDFOperation.execute(executionContext);

        // Save the result to the specified location. Delete previous file if exists
        File file = new File(contractFilePath);
        Files.deleteIfExists(file.toPath());

        result.saveAs(file.getPath());

    } catch (ServiceApiException | IOException | 
             SdkException | ServiceUsageException ex) {
        LOGGER.error("Exception encountered while executing operation", ex);
    }
}
 
private static void setCustomOptionsAndPersonData(
    CreatePDFOperation htmlToPDFOperation, PersonForm personForm) {
    //Set the dataToMerge field that needs to be populated 
    //in the HTML before its conversion
    JSONObject dataToMerge = new JSONObject();
    dataToMerge.put("personFullName", personForm.GetFullName());
 
    // Set the desired HTML-to-PDF conversion options.
    CreatePDFOptions htmlToPdfOptions = CreatePDFOptions.htmlOptionsBuilder()
        .includeHeaderFooter(false)
        .withDataToMerge(dataToMerge)
        .build();
    htmlToPDFOperation.setOptions(htmlToPdfOptions);
}
```

契約の生成時に、動的な個人固有のデータを固定契約条件とマージすることもできます。 これを行うには、[静的HTMLからPDFを作成する](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf-from-dynamic-html)の例に従ってください。 または、[2つのPDFを結合](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf-from-static-html)することもできます。

## ダウンロード用のPDFファイルの提示

これで、生成されたPDFへのリンクを表示して、ダウンロードできるようになりました。 これを行うには、最初にcontract-actions.htmlファイルを作成します（コンパニオンコードのresources/templates contract-actions.htmlを参照）。

```
<html>
<head>
    <link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css">
</head>
 
<div class="w3-container ">
    <h1>HR Department</h1>
 
    <h2>Contract file</h2>
 
    <p>Click <a href="/pdf">here</a> to download your contract</p>
</div>
</body>
</html>
```

次に、`PersonController`クラス内で`downloadContract`メソッドを次のように実装します。

```
@RequestMapping("/pdf")
public void downloadContract(HttpServletResponse response)
{
    Path file = Paths.get(contractFilePath);
 
    response.setContentType("application/pdf");
    response.addHeader(
        "Content-Disposition", "attachment; filename=contract.pdf");

    try
    {
        Files.copy(file, response.getOutputStream());
        response.getOutputStream().flush();
    }
    catch (IOException ex) 
    {
        ex.printStackTrace();
    }
}
```

アプリを実行すると、次のフローが表示されます。 最初の画面には、個人データ・フォームが表示されます。 テストするには、2文字から30文字の値を入力します。

![データ値のスクリーンショット](assets/HRWJ_6.png)

[*送信*]ボタンをクリックすると、フォームが検証され、HTML (resources/contract/index.html)に基づいてPDFが生成されます。 PDFをダウンロードできる別のビュー(contract-details)が表示されます。

![PDFをダウンロードできるスクリーンショット](assets/HRWJ_7.png)

Webブラウザーでレンダリングすると、PDFは次のようになります。 つまり、入力した個人情報がPDFに伝播されます。

![個人情報を使用してレンダリングされたPDFのスクリーンショット](assets/HRWJ_8.png)

## 署名とセキュリティの有効化

契約書の準備が整うと、Adobe Signは承認を表すデジタル署名を追加できます。 Adobe Sign認証は、OAuthとは少し異なる仕組みです。 アプリケーションとAdobe Signを連携させる方法を見てみましょう。 そのためには、アプリケーションのアクセストークンを準備する必要があります。 次に、Adobe Sign Java SDKを使用してクライアントコードを記述します。

認証トークンを取得するには、いくつかの手順を実行する必要があります。

まず、[開発者アカウント](https://acrobat.adobe.com/jp/ja/sign/developer-form.html)を登録します。

[Adobe Signポータル](https://opensource.adobe.com/acrobat-sign/developer_guide/index.html#!adobedocs/adobe-sign/master/gstarted/create_app.md)でCLIENTアプリケーションを作成します。

[こちら](https://opensource.adobe.com/acrobat-sign/developer_guide/index.html#!adobedocs/adobe-sign/master/gstarted/configure_oauth.md)と[こちら](https://secure.eu1.adobesign.com/public/static/oauthDoc.jsp)で説明されているように、アプリケーションのOAuthを構成します。 クライアント識別子とクライアントシークレットを書き留めます。 その後、`https://www.google.com`をリダイレクトURIおよび次のスコープとして使用できます。

* user_login: self

* agreement_read: account

* agreement_write: account

* agreement_send: account

\&lt;CLIENT_ID\>の代わりにクライアントIDを使用して、次のようにURLを準備します。

```
https://secure.eu1.adobesign.com/public/oauth?redirect_uri=https://www.google.com
&response_type=code
&client_id=<CLIENT_ID>
&scope=user_login:self+agreement_read:account+agreement_write:account+agreement_send:account
```

Webブラウザーに上記のURLを入力します。 google.comにリダイレクトされ、コードがアドレスバーにcode=\&lt;YOUR_CODE\>として表示されます。
例：

```
https://www.google.com/?code=<YOUR_CODE>&api_access_point=https://api.eu1.adobesign.com/&web_access_point=https://secure.eu1.adobesign.com%2F
```

\&lt;YOUR_CODE\>とapi_access_pointに指定した値に注意してください。

アクセストークンを提供するHTTP POSTリクエストを送信するには、クライアントID、\&lt;YOUR_CODE\>、およびapi_access_pointの値を使用します。 [Postman](https://helpx.adobe.com/jp/sign/kb/how-to-create-access-token-using-postman-adobe-sign.html)またはcURLを使用できます：

```
curl --location --request POST "https://**api.eu1.adobesign.com**/oauth/token"
\\

\--data-urlencode "client_secret=**\<CLIENT_SECRET\>**" \\

\--data-urlencode "client_id=**\<CLIENT_ID\>**" \\

\--data-urlencode "code=**\<YOUR_CODE\>**" \\

\--data-urlencode "redirect_uri=**https://www.google.com**" \\

\--data-urlencode "grant_type=authorization_code"
```

サンプルの応答は次のようになります。

```
{
    "access_token":"3AAABLblqZhByhLuqlb-…",
    "refresh_token":"3AAABLblqZhC_nJCT7n…",
    "token_type":"Bearer",
    "expires_in":3600
}
```

access_tokenを書き留めます。 クライアントコードを認証するために必要です。

## Adobe Sign Java SDKの使用

アクセストークンを取得すると、REST API呼び出しをAdobe Signに送信できます。 このプロセスを簡単にするには、Adobe Sign Java SDKを使用します。 ソースコードは、[Adobe GitHubリポジトリ](https://github.com/adobe-sign/AdobeSignJavaSdk)で利用できます。

このパッケージをアプリケーションと統合するには、コードを複製する必要があります。 次に、Mavenパッケージ（mvnパッケージ）を作成し、次のファイルをプロジェクトにインストールします（これらのファイルは、adobe-sign-sdkフォルダーのコンパニオンコードにあります）。

* target/swagger-java-client-1.0.0.jar

* target/lib/gson-2.8.1.jar

* target/lib/gson-fire-1.8.0.jar

* target/lib/hamcrest-core-1.3.jar

* target/lib/junit-4.12.jar

* target/lib/logging-interceptor-2.7.5.jar

* target/lib/okhttp-2.7.5.jar

* target/lib/okio-1.6.0.jar

* target/lib/swagger-annotations-1.5.15.jar

IntelliJ IDEAでは、*プロジェクト構造* （ファイル/プロジェクト構造）を使用して、これらのファイルを依存関係として追加できます。

## PDFを署名用に送信しています

これで、署名用に契約書を送信する準備ができました。 これを行うには、まずcontract-details.htmlを補完し、送信リクエストへの別のハイパーリンクを追加します。

```
<html>
<head>
    <link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css">
</head>
 
<div class="w3-container ">
    <h1>HR Department</h1>
 
    <h2>Contract file</h2>
 
    <p>Click <a href="/pdf"> here</a> to download your contract</p>
 
    
</div>
</body>
</html>
```

次に、`sendContractMethod`を実装する別のコントローラー`AdobeSignController`を追加します（コンパニオンコードを参照）。 この方法は次のように動作します。

まず、`ApiClient`を使用してAPIエンドポイントを取得します。

```
ApiClient apiClient = new ApiClient();

//Default baseUrl to make GET /baseUris API call.
String baseUrl = "https://api.echosign.com/";
String endpointUrl = "/api/rest/v6";
apiClient.setBasePath(baseUrl + endpointUrl);

// Provide an OAuth Access Token as "Bearer access token" in authorization
String authorization = "Bearer ";

// Get the baseUris for the user and set it in apiClient.
BaseUrisApi baseUrisApi = new BaseUrisApi(apiClient);
BaseUriInfo baseUriInfo = baseUrisApi.getBaseUris(authorization);
apiClient.setBasePath(baseUriInfo.getApiAccessPoint() + endpointUrl);
```

次に、メソッドはcontract.pdfファイルを使用して一時的なドキュメントを作成します。

```
// Get PDF file
String filePath = "output/";
String fileName = "contract.pdf";
File file = new File(filePath + fileName);
String mimeType = "application/pdf";
 
//Get the id of the transient document.
TransientDocumentsApi transientDocumentsApi =
    new TransientDocumentsApi(apiClient);
TransientDocumentResponse response = transientDocumentsApi.createTransientDocument(authorization,
    file, null, null, fileName, mimeType);
String transientDocumentId = response.getTransientDocumentId();
```

次に、契約書を作成する必要があります。 そのためには、 contract.pdfファイルを使用し、契約書の状態をIN_PROCESSに設定して、ファイルをすぐに送信します。 また、電子サインを選択します。

```
// Create AgreementCreationInfo
AgreementCreationInfo agreementCreationInfo = new AgreementCreationInfo();
 
// Add file
FileInfo fileInfo = new FileInfo();
fileInfo.setTransientDocumentId(transientDocumentId);
agreementCreationInfo.addFileInfosItem(fileInfo);
 
// Set state to IN_PROCESS, so the agreement is be sent immediately
agreementCreationInfo.setState(AgreementCreationInfo.StateEnum.IN_PROCESS);
agreementCreationInfo.setName("Contract");
agreementCreationInfo.setSignatureType(AgreementCreationInfo.SignatureTypeEnum.ESIGN);
```

次に、次のように契約書の受信者を追加します。 ここでは、2人の受信者を追加します（「従業員」セクションと「マネージャー」セクションを参照）。

```
// Provide emails of recipients to whom agreement is be sent
// Employee
ParticipantSetInfo participantSetInfo = new ParticipantSetInfo();
ParticipantSetMemberInfo participantSetMemberInfo = new ParticipantSetMemberInfo();
participantSetMemberInfo.setEmail("");
participantSetInfo.addMemberInfosItem(participantSetMemberInfo);
participantSetInfo.setOrder(1);
participantSetInfo.setRole(ParticipantSetInfo.RoleEnum.SIGNER);
agreementCreationInfo.addParticipantSetsInfoItem(participantSetInfo);
 
// Manager
participantSetInfo = new ParticipantSetInfo();
participantSetMemberInfo = new ParticipantSetMemberInfo();
participantSetMemberInfo.setEmail("");
participantSetInfo.addMemberInfosItem(participantSetMemberInfo);
participantSetInfo.setOrder(2);
participantSetInfo.setRole(ParticipantSetInfo.RoleEnum.SIGNER);
agreementCreationInfo.addParticipantSetsInfoItem(participantSetInfo);
```

最後に、Adobe Sign Java SDKから`createAgreement`メソッドを使用して契約書を送信します。

```
// Create agreement using the transient document.
AgreementsApi agreementsApi = new AgreementsApi(apiClient);
AgreementCreationResponse agreementCreationResponse = agreementsApi.createAgreement(
    authorization, agreementCreationInfo, null, null);
 
System.out.println("Agreement sent, ID: " + agreementCreationResponse.getId());
```

このコードを実行すると、契約書の署名要求を含む電子メール（コードで`<email_address>)`として指定されたアドレス宛）が送信されます。 電子メールにはハイパーリンクが含まれており、受信者はAdobe Signポータルにアクセスして、署名を実行できます。 Adobe Sign Developer Portal（以下の図を参照）に文書が表示されます。また、[getAgreementInfo](https://github.com/adobe-sign/AdobeSignJavaSdk/blob/master/docs/AgreementsApi.md#getAgreementInfo)メソッドを使用して、プログラムで署名プロセスをトラックすることもできます。

最後に、これらの[例](https://github.com/adobe/pdfservices-java-sdk-samples/tree/master/src/main/java/com/adobe/pdfservices/operation/samples/protectpdf)に示すように、PDFサービスAPIを使用してPDFをパスワードで保護することもできます。

![契約の詳細のスクリーンショット](assets/HRWJ_9.png)

## 次の手順

このように、quickstartsを活用することで、簡単なWebフォームを実装し、Adobe PDF Services APIを使用してJavaで承認済みPDFを作成できます。 Adobe PDF APIは、既存のクライアントアプリケーションとシームレスに連携します。

さらに例を挙げると、フォームの受信者がリモートで安全に署名できるように作成できます。 複数の署名が必要な場合は、ワークフロー内の一連のユーザーにフォームを自動的に送信することもできます。 従業員のオンボーディングが改善され、人事部があなたを気に入ってくれるでしょう。

今すぐ[[!DNL Adobe Acrobat Services]](https://developer.adobe.com/document-services/homepage/)をチェックして、アプリケーションに多くのPDF機能を追加してください。

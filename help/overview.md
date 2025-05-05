---
title: '[!DNL Adobe Acrobat Services]個のAPI Tutorials'
description: ' [!DNL Adobe Acrobat Services]の概要ページ'
feature: Acrobat Sign API, PDF Services API, PDF Embed API, Document Generation API, PDF Electronic Seal API, PDF Extract API, PDF Accessibility Auto-Tag API
role: Developer
level: Beginner, Intermediate, Experienced
jira: KT-7463
type: Tutorial
thumbnail: KT-7463.jpg
exl-id: c73feb77-4057-42fd-831c-a5004c7637c1
source-git-commit: 2e23ffef7e4438a9287c909d2fdb13968195c31f
workflow-type: tm+mt
source-wordcount: '353'
ht-degree: 2%

---

# [!DNL Adobe Acrobat Services] APIチュートリアル

[!DNL Adobe Acrobat Services]には6つの主なAPIがあります：

* [!DNL Adobe PDF Services API]
* [!DNL Adobe PDF Embed API]
* [!DNL Adobe Document Generation API]
* [!DNL Adobe PDF Electronic Seal API]
* [!DNL Adobe PDF Extract API]
* [!DNL Adobe PDF Accessibility Auto-Tag API]

後の2つのAPIとそのSDKは、有料サービスの一部として[!DNL Adobe PDF Services API]にバンドルされています。 [!DNL PDF Embed API]は無料サービスです。 これらのAPIは、最新のクラウドベースのWebサービスのセットを介して、ドキュメントコンテンツの生成、操作、変換を自動化します。 これらは、ドキュメントに対するユーザー操作の制御、PDFワークフローの合理化、使用と保持の促進など、シンプルで迅速なブランドエクスペリエンスを提供するのに役立ちます。 これらのチュートリアルでは、[!DNL Adobe Acrobat Services] APIを使用して、ブランド化されたエクスペリエンスをよりシンプルかつ迅速に提供します。

<!-- Comment -->
<!-- CARDS

* https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/pdfservices/overview-pdfservices
  {target = _self}
  {title = PDF Services API}
  {description = PDF APIs with SDKs for node.js, .Net, and Java to create, convert, OCR PDFs, and more}
  {image = https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/media_1a7b3ae4fc2b8c33c920f81a3eee05dc358108a74.png?width=400&format=webply&optimize=medium}
  {cta = Browse tutorials}
* https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/docgen/overview-docgen
  {target = _self}
  {title = Document Generation API}
  {description = Generate PDF and Word documents from Word templates and JSON data}
  {image = https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/media_1e4df708e549cf00ce0fb7fa1782957786ad4886b.png?width=400&format=webply&optimize=medium}
  {cta = Browse tutorials}
* https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/pdfaccessibility/overview-accessibility
  {target = _self}
  {title = Adobe PDF Accessibility Auto-Tag API tutorials}
  {description = This AI-powered API automatically tags documents making it easy to scale PDF accessibiity}
  {image = https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/media_1441e8f0ef7b4a044f3e6355d66fa8f88146f9394.png?width=400&format=webply&optimize=medium}
  {cta = Browse tutorials}
* https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/pdfextract/overview-extract
  {target = _self}
  {title = PDF Extract API}
  {description = Unlock the structure and content elements of any PDF with a web service powered by Adobe Sensi's machine learning}
  {image = https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/media_1441e8f0ef7b4a044f3e6355d66fa8f88146f9394.png?width=400&format=webply&optimize=medium}
  {cta = Browse tutorials}
* https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/eseal/overview-electronic-seal
  {target = _self}
  {title = PDF Electronic Seal API}
  {description = Learn how to apply a tamper-evident electronic seal to PDFs at scale}
  {image = https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/media_1144424efd29c8faaf22aceff3f73d80fb7fb3ac1.png?width=400&format=webply&optimize=medium}
  {cta = Browse tutorials}
* https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/pdfembed/overview-embed
  {target = _self}
  {title = PDF Embed API}
  {description = Free Javascript API to embed high-fidelity PDFs, enable collaboration, and see analytics}
  {image = https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/media_100c64db6899f092f8a30e0d153091398242f8abc.png?width=400&format=webply&optimize=medium}
  {cta = Browse tutorials}
* https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/acrobatsign/overview-sign
  {target = _self}
  {title = Acrobat Sign API}
  {description = Integrate e-signatures into your platform or application}
  {image = https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/media_1f579a346e7d3a7647236d7b81daf49d45dafde35.png?width=400&format=webply&optimize=medium}
  {cta = Browse tutorials}
* https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/usecases/overview-usecases
  {target = _self}
  {title = Adobe Acrobat Services API use cases}
  {description = A wide variety of Acrobat Services API use cases}
  {image = https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/media_13219295abc6d94806a7cccf552effa9b54f25ecf.png?width=400&format=webply&optimize=medium}
  {cta = Browse tutorials}

-->
<!-- End Comment -->

<!-- START CARDS HTML - DO NOT MODIFY BY HAND -->
<div class="columns">
    <div class="column is-half-tablet is-half-desktop is-one-third-widescreen" aria-label="PDF Services API">
        <div class="card" style="height: 100%; display: flex; flex-direction: column; height: 100%;">
            <div class="card-image">
                <figure class="image x-is-16by9">
                    <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/pdfservices/overview-pdfservices" title="PDFサービスAPI" target="_self" rel="referrer">
                        <img class="is-bordered-r-small" src="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/media_1a7b3ae4fc2b8c33c920f81a3eee05dc358108a74.png?width=400&format=webply&optimize=medium" alt="PDFサービスAPI"
                             style="width: 100%; aspect-ratio: 16 / 9; object-fit: cover; overflow: hidden; display: block; margin: auto;">
                    </a>
                </figure>
            </div>
            <div class="card-content is-padded-small" style="display: flex; flex-direction: column; flex-grow: 1; justify-content: space-between;">
                <div class="top-card-content">
                    <p class="headline is-size-6 has-text-weight-bold">
                        <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/pdfservices/overview-pdfservices" target="_self" rel="referrer" title="PDFサービスAPI">PDFサービスAPI</a>
                    </p>
                    <p class="is-size-6">node.js、.Net、Java用のSDKを使用して、PDFの作成、変換、OCRなどをPDFするAPI</p>
                </div>
                <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/pdfservices/overview-pdfservices" target="_self" rel="referrer" class="spectrum-Button spectrum-Button--outline spectrum-Button--primary spectrum-Button--sizeM" style="align-self: flex-start; margin-top: 1rem;">
                    <span class="spectrum-Button-label has-no-wrap has-text-weight-bold">チュートリアルを参照</span>
                </a>
            </div>
        </div>
    </div>
    <div class="column is-half-tablet is-half-desktop is-one-third-widescreen" aria-label="Document Generation API">
        <div class="card" style="height: 100%; display: flex; flex-direction: column; height: 100%;">
            <div class="card-image">
                <figure class="image x-is-16by9">
                    <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/docgen/overview-docgen" title="Document Generation API" target="_self" rel="referrer">
                        <img class="is-bordered-r-small" src="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/media_1e4df708e549cf00ce0fb7fa1782957786ad4886b.png?width=400&format=webply&optimize=medium" alt="Document Generation API"
                             style="width: 100%; aspect-ratio: 16 / 9; object-fit: cover; overflow: hidden; display: block; margin: auto;">
                    </a>
                </figure>
            </div>
            <div class="card-content is-padded-small" style="display: flex; flex-direction: column; flex-grow: 1; justify-content: space-between;">
                <div class="top-card-content">
                    <p class="headline is-size-6 has-text-weight-bold">
                        <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/docgen/overview-docgen" target="_self" rel="referrer" title="Document Generation API">Document Generation API</a>
                    </p>
                    <p class="is-size-6">WordテンプレートおよびJSONデータからPDF文書およびWord文書を生成</p>
                </div>
                <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/docgen/overview-docgen" target="_self" rel="referrer" class="spectrum-Button spectrum-Button--outline spectrum-Button--primary spectrum-Button--sizeM" style="align-self: flex-start; margin-top: 1rem;">
                    <span class="spectrum-Button-label has-no-wrap has-text-weight-bold">チュートリアルを参照</span>
                </a>
            </div>
        </div>
    </div>
    <div class="column is-half-tablet is-half-desktop is-one-third-widescreen" aria-label="Adobe PDF Accessibility Auto-Tag API tutorials">
        <div class="card" style="height: 100%; display: flex; flex-direction: column; height: 100%;">
            <div class="card-image">
                <figure class="image x-is-16by9">
                    <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/pdfaccessibility/overview-accessibility" title="Adobe PDFアクセシビリティ自動タグ付けAPIチュートリアル" target="_self" rel="referrer">
                        <img class="is-bordered-r-small" src="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/media_1441e8f0ef7b4a044f3e6355d66fa8f88146f9394.png?width=400&format=webply&optimize=medium" alt="Adobe PDFアクセシビリティ自動タグ付けAPIチュートリアル"
                             style="width: 100%; aspect-ratio: 16 / 9; object-fit: cover; overflow: hidden; display: block; margin: auto;">
                    </a>
                </figure>
            </div>
            <div class="card-content is-padded-small" style="display: flex; flex-direction: column; flex-grow: 1; justify-content: space-between;">
                <div class="top-card-content">
                    <p class="headline is-size-6 has-text-weight-bold">
                        <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/pdfaccessibility/overview-accessibility" target="_self" rel="referrer" title="Adobe PDFアクセシビリティ自動タグ付けAPIチュートリアル">Adobe PDFアクセシビリティ自動タグ付けAPIチュートリアル</a>
                    </p>
                    <p class="is-size-6">このAIを活用したAPIにより、文書に自動的にタグ付けされるため、PDFのアクセシビリティを簡単に拡大・縮小できます</p>
                </div>
                <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/pdfaccessibility/overview-accessibility" target="_self" rel="referrer" class="spectrum-Button spectrum-Button--outline spectrum-Button--primary spectrum-Button--sizeM" style="align-self: flex-start; margin-top: 1rem;">
                    <span class="spectrum-Button-label has-no-wrap has-text-weight-bold">チュートリアルを参照</span>
                </a>
            </div>
        </div>
    </div>
    <div class="column is-half-tablet is-half-desktop is-one-third-widescreen" aria-label="PDF Extract API">
        <div class="card" style="height: 100%; display: flex; flex-direction: column; height: 100%;">
            <div class="card-image">
                <figure class="image x-is-16by9">
                    <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/pdfextract/overview-extract" title="PDFエクストラクトAPI" target="_self" rel="referrer">
                        <img class="is-bordered-r-small" src="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/media_1441e8f0ef7b4a044f3e6355d66fa8f88146f9394.png?width=400&format=webply&optimize=medium" alt="PDFエクストラクトAPI"
                             style="width: 100%; aspect-ratio: 16 / 9; object-fit: cover; overflow: hidden; display: block; margin: auto;">
                    </a>
                </figure>
            </div>
            <div class="card-content is-padded-small" style="display: flex; flex-direction: column; flex-grow: 1; justify-content: space-between;">
                <div class="top-card-content">
                    <p class="headline is-size-6 has-text-weight-bold">
                        <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/pdfextract/overview-extract" target="_self" rel="referrer" title="PDFエクストラクトAPI">PDFエクストラクトAPI</a>
                    </p>
                    <p class="is-size-6">AdobeSensiの機械学習を活用したWebサービスで、あらゆるPDFの構造とコンテンツ要素を利用できます</p>
                </div>
                <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/pdfextract/overview-extract" target="_self" rel="referrer" class="spectrum-Button spectrum-Button--outline spectrum-Button--primary spectrum-Button--sizeM" style="align-self: flex-start; margin-top: 1rem;">
                    <span class="spectrum-Button-label has-no-wrap has-text-weight-bold">チュートリアルを参照</span>
                </a>
            </div>
        </div>
    </div>
    <div class="column is-half-tablet is-half-desktop is-one-third-widescreen" aria-label="PDF Electronic Seal API">
        <div class="card" style="height: 100%; display: flex; flex-direction: column; height: 100%;">
            <div class="card-image">
                <figure class="image x-is-16by9">
                    <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/eseal/overview-electronic-seal" title="PDFeシールAPI" target="_self" rel="referrer">
                        <img class="is-bordered-r-small" src="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/media_1144424efd29c8faaf22aceff3f73d80fb7fb3ac1.png?width=400&format=webply&optimize=medium" alt="PDFeシールAPI"
                             style="width: 100%; aspect-ratio: 16 / 9; object-fit: cover; overflow: hidden; display: block; margin: auto;">
                    </a>
                </figure>
            </div>
            <div class="card-content is-padded-small" style="display: flex; flex-direction: column; flex-grow: 1; justify-content: space-between;">
                <div class="top-card-content">
                    <p class="headline is-size-6 has-text-weight-bold">
                        <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/eseal/overview-electronic-seal" target="_self" rel="referrer" title="PDFeシールAPI">PDF eシールAPI</a>
                    </p>
                    <p class="is-size-6">改ざんの跡がすぐわかるeシールを大規模なPDFに適用する方法を説明します</p>
                </div>
                <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/eseal/overview-electronic-seal" target="_self" rel="referrer" class="spectrum-Button spectrum-Button--outline spectrum-Button--primary spectrum-Button--sizeM" style="align-self: flex-start; margin-top: 1rem;">
                    <span class="spectrum-Button-label has-no-wrap has-text-weight-bold">チュートリアルを参照</span>
                </a>
            </div>
        </div>
    </div>
    <div class="column is-half-tablet is-half-desktop is-one-third-widescreen" aria-label="PDF Embed API">
        <div class="card" style="height: 100%; display: flex; flex-direction: column; height: 100%;">
            <div class="card-image">
                <figure class="image x-is-16by9">
                    <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/pdfembed/overview-embed" title="PDF埋め込みAPI" target="_self" rel="referrer">
                        <img class="is-bordered-r-small" src="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/media_100c64db6899f092f8a30e0d153091398242f8abc.png?width=400&format=webply&optimize=medium" alt="PDF埋め込みAPI"
                             style="width: 100%; aspect-ratio: 16 / 9; object-fit: cover; overflow: hidden; display: block; margin: auto;">
                    </a>
                </figure>
            </div>
            <div class="card-content is-padded-small" style="display: flex; flex-direction: column; flex-grow: 1; justify-content: space-between;">
                <div class="top-card-content">
                    <p class="headline is-size-6 has-text-weight-bold">
                        <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/pdfembed/overview-embed" target="_self" rel="referrer" title="PDF埋め込みAPI">PDF埋め込みAPI</a>
                    </p>
                    <p class="is-size-6">無料のJavascript APIにより、忠実度の高いPDFを埋め込み、共同作業を可能にし、分析を表示</p>
                </div>
                <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/pdfembed/overview-embed" target="_self" rel="referrer" class="spectrum-Button spectrum-Button--outline spectrum-Button--primary spectrum-Button--sizeM" style="align-self: flex-start; margin-top: 1rem;">
                    <span class="spectrum-Button-label has-no-wrap has-text-weight-bold">チュートリアルを参照</span>
                </a>
            </div>
        </div>
    </div>
    <div class="column is-half-tablet is-half-desktop is-one-third-widescreen" aria-label="Acrobat Sign API">
        <div class="card" style="height: 100%; display: flex; flex-direction: column; height: 100%;">
            <div class="card-image">
                <figure class="image x-is-16by9">
                    <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/acrobatsign/overview-sign" title="Acrobat Sign API" target="_self" rel="referrer">
                        <img class="is-bordered-r-small" src="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/media_1f579a346e7d3a7647236d7b81daf49d45dafde35.png?width=400&format=webply&optimize=medium" alt="Acrobat Sign API"
                             style="width: 100%; aspect-ratio: 16 / 9; object-fit: cover; overflow: hidden; display: block; margin: auto;">
                    </a>
                </figure>
            </div>
            <div class="card-content is-padded-small" style="display: flex; flex-direction: column; flex-grow: 1; justify-content: space-between;">
                <div class="top-card-content">
                    <p class="headline is-size-6 has-text-weight-bold">
                        <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/acrobatsign/overview-sign" target="_self" rel="referrer" title="Acrobat Sign API">Acrobat Sign API</a>
                    </p>
                    <p class="is-size-6">電子サインをプラットフォームまたはアプリケーションに統合</p>
                </div>
                <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/acrobatsign/overview-sign" target="_self" rel="referrer" class="spectrum-Button spectrum-Button--outline spectrum-Button--primary spectrum-Button--sizeM" style="align-self: flex-start; margin-top: 1rem;">
                    <span class="spectrum-Button-label has-no-wrap has-text-weight-bold">チュートリアルを参照</span>
                </a>
            </div>
        </div>
    </div>
    <div class="column is-half-tablet is-half-desktop is-one-third-widescreen" aria-label="Adobe Acrobat Services API use cases">
        <div class="card" style="height: 100%; display: flex; flex-direction: column; height: 100%;">
            <div class="card-image">
                <figure class="image x-is-16by9">
                    <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/usecases/overview-usecases" title="Adobe AcrobatサービスAPIのユースケース" target="_self" rel="referrer">
                        <img class="is-bordered-r-small" src="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/media_13219295abc6d94806a7cccf552effa9b54f25ecf.png?width=400&format=webply&optimize=medium" alt="Adobe AcrobatサービスAPIのユースケース"
                             style="width: 100%; aspect-ratio: 16 / 9; object-fit: cover; overflow: hidden; display: block; margin: auto;">
                    </a>
                </figure>
            </div>
            <div class="card-content is-padded-small" style="display: flex; flex-direction: column; flex-grow: 1; justify-content: space-between;">
                <div class="top-card-content">
                    <p class="headline is-size-6 has-text-weight-bold">
                        <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/usecases/overview-usecases" target="_self" rel="referrer" title="Adobe AcrobatサービスAPIのユースケース">Adobe AcrobatサービスAPIの使用例</a>
                    </p>
                    <p class="is-size-6">様々なAcrobatサービスAPIのユースケース</p>
                </div>
                <a href="https://experienceleague.adobe.com/ja/docs/acrobat-services-learn/tutorials/usecases/overview-usecases" target="_self" rel="referrer" class="spectrum-Button spectrum-Button--outline spectrum-Button--primary spectrum-Button--sizeM" style="align-self: flex-start; margin-top: 1rem;">
                    <span class="spectrum-Button-label has-no-wrap has-text-weight-bold">チュートリアルを参照</span>
                </a>
            </div>
        </div>
    </div>
</div>
<!-- END CARDS HTML - DO NOT MODIFY BY HAND -->

---
layout: 'layouts/doc-post.njk'
title: マニフェストにマスカブルアイコンがない
description: PWA にマスカブルアイコンのサポートを追加する方法について学びます。
date: 2020-05-06
---

[マスカブルアイコン](https://web.dev/articles/maskable-icon)は、すべての Android デバイスにおいて PWA アイコンが適切に表示されることを保証する新しいアイコン形式です。新しい Androidデバイスでは、マスカブルアイコンの形式に従わない PWA アイコンは、背景色が白になります。マスカブルアイコンを使用すると、Android がそのアイコン用に提供するスペース全体にそのアイコンが表示されます。

## Lighthouse によるマスカブルアイコンの監査が失敗する原因

[Lighthouse](https://developers.google.com/web/tools/lighthouse/) は、マスカブルアイコンをサポートしていないページをフラグします。

<figure>{% Img src="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/w0lXCcsZdOeLZuAw3wbY.jpg", alt="Lighthouse レポートの UI に表示されているマスカブルアイコンの監査。", width="800", height="110" %}</figure>

監査に合格するには、以下が必要です。

- ウェブアプリのマニフェストがある。
- ウェブアプリのマニフェストに `icons` 配列がある。
- `icons` 配列に `purpose` プロパティを持つオブジェクトが 1 つ含まれている。また、その `purpose` プロパティに `maskable` が含まれている。

{% Aside 'caution' %} Lighthouse は、マスカブルアイコンとして指定された画像を検査しません。画像が適切に表示されることは、自分で確認する必要があります。{% endAside %}

{% Partial 'lighthouse-best-practices/scoring.njk' %}

## PWA にマスカブルアイコンのサポートを追加する方法

1. [Maskable.app Editor](https://maskable.app/editor) を使用して、既存のアイコンをマスカブルアイコンに変換します。

2. [ウェブアプリのマニフェスト](https://web.dev/articles/add-manifest)の `icons` オブジェクトの1つに `purpose` プロパティを追加します。`purpose` の値を `maskable{/code4 } もしくは <code data-md-type="codespan">any maskable` に設定します。[Values](https://developer.mozilla.org/docs/Web/Manifest/icons#Values) (値) を参照してください。

    ```json/8
    {
      …
      "icons": [
        …
        {
          "src": "path/to/maskable_icon.png",
          "sizes": "196x196",
          "type": "image/png",
          "purpose": "any maskable"
        }
      ]
      …
    }
    ```

3. Chrome DevTools を使用して、マスカブルアイコンが正しく表示されていることを確認します。[Are my current icons ready? (私の現在のアイコンは使用できるか？)](https://web.dev/articles/maskable-icon#are_my_current_icons_ready) を参照してください。

## リソース

- [**Manifest doesn't have a maskable icon (マニフェストにマスカブルアイコンがない)** 監査のソースコード](https://github.com/GoogleChrome/lighthouse/blob/master/lighthouse-core/audits/maskable-icon.js)
- [Adaptive icon support in PWAs with maskable icons (マスカブルアイコンを使用した PWA におけるアダプティブアイコンサポート)](https://web.dev/articles/maskable-icon)
- [Maskable.app エディター](https://maskable.app/editor)
- [Add a web app manifest (ウェブアプリのマニフェストを追加する)](https://web.dev/articles/add-manifest)
- [MDNの `icons` プロパティ](https://developer.mozilla.org/docs/Web/Manifest/icons)

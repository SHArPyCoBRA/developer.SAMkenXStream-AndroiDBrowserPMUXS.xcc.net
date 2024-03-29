---
layout: layouts/doc-post.njk
title: フィードバック
subhead: 開発者のみなさんがプライバシー サンドボックスの提案に対してフィードバックを送る場所と方法をご紹介します。
description: 開発者のみなさんがプライバシー サンドボックスの提案に対してフィードバックを送る場所と方法をご紹介します。
date: 2022-02-28
updated: 2023-06-05
authors:
  - rowan_m
---

フィードバック 開発者のみなさんがプライバシーサンドボックスの提案に対してフィードバックを送る場所と方法をご紹介します。 プライバシー サンドボックスでは、ウェブ エコシステム全体の多様な関係者からフィードバックを得ることが必要不可欠です。ここでは、開発の情報を提供するさまざまな公開チャネルについて説明し、個人または組織が開発の各段階でフィードバックを提供するための方法をご案内します。こうしたフィードバックには、Chrome のプロダクト マネージャーとエンジニアが積極的に対処します。すでに数百名の業界代表者からご協力をいただいています。

さまざまなフィードバック チャネルをご利用になれます。個々のやり取りは多くの場合一般公開されるので、ディスカッションの進行を追いかけ、どこで開発に貢献するかを決めることができます。**[フィードバック フォーム](#feedback-form)** もご利用いただけます。フォームを使用すると、関係者は公開フォーラムの外で Chrome チームと直接フィードバックを共有する機会を得られます。フィードバック フォーム経由で届いたフィードバックは、属性情報なしで Chrome チームの公開レポートに含まれるものとして集計される場合があります。

## フィードバックが検討されたことを確認するにはどうすればよいでしょうか。 {: #reports}

個々の Privacy Sandbox API に関する最新情報は、定期的にこのサイトで公開されています。特に、この最新情報では、API 固有の一般的なフィードバック テーマの概要を紹介します。

- [2023 年第 1 四半期のフィードバック レポート](/docs/privacy-sandbox/feedback/report-2023-q1/)
- [2022 年第 4 四半期のフィードバック レポート](/docs/privacy-sandbox/feedback/report-2022-q4/)
- [2022 年第 3 四半期のフィードバック レポート](/docs/privacy-sandbox/feedback/report-2022-q3/)
- [2022 年第 2 四半期のフィードバック レポート](/docs/privacy-sandbox/feedback/report-2022-q2/)
- [2022 年第 1 四半期のフィードバック レポート](/docs/privacy-sandbox/feedback/report-2022-q1/)

基本的に公開のフィードバック チャネルを推奨しますが、公開チャネル（GitHub など）とダイレクト チャネル（フィードバック フォームなど）の両方が利用可能です。Chrome チームは、関係者から得られたフィードバックと関心が各 API の設計と開発に反映されるかどうか、またどのように反映されるかを説明します。

## フィードバックのルート

{% Details %} {% DetailsSummary %}

### 個々の提案に共同で取り組む {: #proposal-feedback}

***プライバシー サンドボックスに関する提案はすべて公開ディスカッションの対象となります。提案の作成者とウェブの関係者は、機能が確定される前に協力して未解決の質問に回答し、実装の詳細を明確化します。***

{% endDetailsSummary %}

提案は Explainer から始まります。Explainer とは、提案する仕様の機能の大まかな技術概要です。未解決の質問と明確化が必要な詳細情報は常に存在するので、Explainer が投稿されてフィードバック プロセスが開始されます。この共同作業プロセスは、提案のライフサイクルを通じて進行します。これは、アイデアの初期のディスカッションで始まり、正式な仕様の改訂の反復で終わります。

{% Aside %}

このような大まかな概要と未解決の質問のパターンについては、[Topics API の Explainer](https://github.com/jkarlin/topics) をご覧ください。

{% endAside %}

Explainer と補足コンテンツは GitHub でホストされます。GitHub では、GitHub アカウントを持っている方はどなたでも、リポジトリ（repo）で問題を報告（質問を投稿またはコメントを追加）して、ディスカッションを開始するかディスカッションに参加することができます。提案の作成者（Chrome プロダクト マネージャーとエンジニアを含む）は積極的にディスカッションに参加します。GitHub には、新しいアクティビティのアラートを受け取るオプションがあります。GitHub のフィードバックについては、特定の提案に関心を持つコミュニティと直接交流できます。GitHub アカウントを持っていなくても、個々の提案に関するコミュニティのコメントをすべて閲覧できます。

リポジトリにおけるディスカッションでは、提案の中で解決すべきものとされているユースケースになぜ対処するのか、またどのように対処するかに焦点を絞る必要があります。個々の提案に関する問題を閲覧および報告するためのリンクは、[**提案**セクション](#proposals)の表の**フィードバック**の列にあります。

{% endDetails %}

{% Details %} {% DetailsSummary %}

### Chromium 機能の開発を追跡して対応する

***機能開発の各段階はすべて公開メーリング リストで発表されます。これにより、技術実装に関するディスカッションがさらに促進されます。***

{% endDetailsSummary %}

各提案の結果として、Chromium に 1 つ以上の機能が組み込まれる可能性があります。提案したデベロッパーは、[一般公開されている `blink-dev` メーリング リスト](https://groups.google.com/a/chromium.org/g/blink-dev)で、機能開発の各段階を開始するリクエストを送信します。[各段階には、](https://blog.chromium.org/2019/11/intent-to-explain-demystifying-blink.html)Intent to Prototype（I2P）、Intent to Experiment（I2E）、Intent to Ship（I2S）、Intent to Remove（I2R）があります。

- **Intent to Prototype（I2P）:** デベロッパーは、Chromium の最初の実装を開始したいと考えています。これにより、デベロッパー テストで初期の機能をテストできる機会が増えます。ほとんどの場合、この段階での目的は提案されたアイデアを開発中のコードで検証することなので、この段階で役立つフィードバックは GitHub に投稿することが適切です。
- **Intent to Experiment（I2E）:** デベロッパーは、広範囲を対象としてテストをオリジン トライアルの形で実施したいと考えています。これにより、デベロッパーのサイトでデベロッパー自身のトラフィックの一部を使用して初期の機能をテストできます。この段階で役立つフィードバックとしては、テストへの参加希望の表明や、提案されたテストが動作の検証のニーズを満たしているかどうかなどがあります。
- **Intent to Ship（I2S）**: デベロッパーは、完全な機能を Chromium にデプロイしたいと考えています。これにより、すべてのユーザーが機能を利用できるようになります。この段階で役立つフィードバックは、未解決だった問題が解決されて機能を一般提供する準備ができたという確認です。
- **Intent to Remove（I2R）**: デベロッパーは、機能を非推奨にして Chromium から削除したいと考えています。この段階で役立つフィードバックとしては、この削除によって開発チームが把握していない影響がユースケースに及ぶかどうかの確認などがあります。

各段階には標準テンプレートがあり、デベロッパーはそれを通して関連性の高い情報を厳選して提供します。段階によっては、Chromium プロジェクト オーナーの承認が必要です。オーナーは、投稿に「Looks Good To Me」（LGTM）と回答することにより承認を行います。

メーリング リストは一般公開されているので、どなたでも各マイルストーンでディスカッションを追いかけたり、メーリング リストに参加して質問を追加したりすることができます。このメーリング リストは Chromium プロジェクトで扱われるすべての機能をカバーしているため、そこでは広範なアクティビティが行われています。[Chrome ステータスで個々の機能の進捗状況](https://chromestatus.com/features)をご覧ください。

メーリング リストのスレッドでは、Chromium における特定の機能の実装の詳細に焦点を絞ってディスカッションする必要があります。提案自体の有効性に関するディスカッションは、[GitHub で行うことが適切](#proposal-feedback)です。個々のお知らせを閲覧してコメントを投稿するためのリンクは、[**提案**セクション](#proposals)の表の**インテント**の列にあります。

{% endDetails %}

{% Details %} {% DetailsSummary %}

### 個々の機能開発を追跡してディスカッションする

***特定の提案に焦点を絞ったディスカッションを行うため、提案の実装の進捗に応じて特定のメーリング リストが作成されることがあります。***

{% endDetailsSummary %}

個々の提案の Chromium への実装が進むにつれて、特定の提案に焦点を絞ったディスカッションを行うため、提案固有のメーリング リストが作成されることがあります。

それにより、オリジン トライアルの最新情報、必要なコードの更新、開発に影響する可能性がある既知の問題などに関する通知とディスカッションが可能になります。そのようなメーリング リストは、`blink-dev` と同様に一般公開されます。提案を直接追跡している場合や提案に取り組んでいる場合は、提案固有のメーリング リストに参加して、開発チームから最新情報を直接入手する必要があります。

これらのメーリング リストのスレッドでは、Chromium における進行中の実装の詳細に焦点を絞ってディスカッションする必要があります。メーリング リストの対象読者はお知らせ全般に興味がある一般ユーザーではなく、特定の機能のコードを作成するデベロッパーであるからです。メーリング リストで閲覧と投稿を行うためのリンクは、[**提案**セクション](#proposals)の表の**メーリング リスト**の列にあります。

{% endDetails %}

{% Details %} {% DetailsSummary %}

### 機能に関する問題を報告および追跡する

***実装の進行に伴い、機能の動作に関する問題が発生した場合、Chromium の Issue Tracker に報告することができます。***

{% endDetailsSummary %}

報告できる問題には、Chromium の動作が提案された仕様と一致しない実装バグのほかに、ブラウザ固有の機能（提案された機能が DevTools やユーザー設定とどのように相互作用するかなど）に関する問題や、単にエラーを報告するだけのものもあります。デベロッパー テスト用に新しく追加された試験運用機能の問題であれ、安定版リリースで検出された問題であれ、問題は Chromium 機能のライフサイクルのどの時点でも報告できます。

Chromium の問題については、Chromium の想定される機能実装の詳細に焦点を絞ってディスカッションする必要があります。提案自体の有効性に関するディスカッションは、[GitHub で行うことが適切](#proposal-feedback)です。問題を閲覧または報告するためのリンクは、[**提案**セクション](#proposals)の表の **Chromium コンポーネント**の列にあります。

{% endDetails %}

{% Details %} {% DetailsSummary %}

### 標準化団体をフォローして参加する

***[ワールドワイド ウェブ コンソーシアム（W3C）](https://www.w3.org/)と[インターネット技術特別調査委員会（IETF）](https://www.ietf.org/)は、すべてのウェブ プラットフォーム向けのオープン標準を開発しています。それらの団体は、関係者が個々の基準と大規模なウェブ エコシステムについて議論し、知識を深めることを奨励しています。***

{% endDetailsSummary %}

W3C と IETF は、ウェブとインターネットのためのオープン標準を開発し、オープン プラットフォームの長期的な成長を促進する国際団体です。それらの標準化団体のさまざまなフォーラムでは、プライバシー サンドボックス技術のような新しいウェブ プラットフォーム技術が提案され、議論されています。フォーラムは、技術の設計と開発に積極的に参加することを希望するすべての人々に開放されています。

各標準団体は、関係者にさまざまなメンバーシップおよび貢献のオプションを提供しており、ウェブエコシステムや関連業界全体のメンバーが含まれるコミュニティグループやビジネスグループが存在します。提案作成者は、関連する会議で概要や進捗状況の最新情報を発表することが多く、直接質問したり、他の関係者の意見を聞く機会を提供しています。ほとんどのグループの会議議事録は公開されています。

標準団体でのディスカッションは多岐にわたりますが、一般的には、提案がエコシステムのニーズをどのように満たしているか、および受け入れられる標準に向けた進捗に焦点が当てられています。[**Proposals**（提案）セクション](#proposals)に記載される表の **Standards groups** （標準グループ）列に、フォローまたは参加するためのリンクがあります。

{% endDetails %}

{% Details %} {% DetailsSummary %}

### Chrome を利用したテスト

***私たちは、最初のサード パーティ廃止から得たイシューのトラッキングに沿った [Chrome でのテスト計画](/docs/privacy-sandbox/chrome-testing/)に関するフィードバックを求めています。***

{% endDetailsSummary %}

サイトの機能にサード パーティ Cookie データを使用している場合は、[公開イシュートラッカー](https://goo.gle/report-3pc-broken)でサード パーティ Cookie の廃止に起因するサイトのイシューを報告できるようになりました。

さらに、Chrome は 2024 年第 1 四半期にサードパーティ Cookie の 1% を廃止する予定であり、廃止対象を拡大するためのさらなる措置を講じる前に CMA と緊密に連携する予定です。[Chrome を使用したテスト](https://github.com/GoogleChromeLabs/privacy-sandbox-dev-support/labels/chrome-testing)のこのサブセットに割り当てる適切な部分のトラフィックについて、[GitHub にフィードバック](/docs/privacy-sandbox/chrome-testing/)を送信できます。

{% endDetails %}

{% Details %} {% DetailsSummary %}

### フィードバック フォームを使ってフィードバックを送信する {: #feedback-form}

***すべての問題が上記のカテゴリに分類されるわけではありません。これらのルートは、最も関連性の高い相手と公開のディスカッションを開始する最良の方法ですが、フィードバック フォームを使用して、いつでも Chrome チームに直接問い合わせることができます。***

{% endDetailsSummary %}

以下の点をお知りになりたい場合は、このフォームをご使用ください。

- 複数の提案によって特定の状況にどのような影響があるか。
- ユースケースが提案の対象となるかどうか。

このフォームによって、関係者は Chrome チームとフィードバックを直接共有できますが、フィードバックのテーマや問題は、関連付けなしで Chrome チームの公開レポートに含められる場合があります。

{% endDetails %}

{% Aside %}

[**フィードバック フォームを使ってフィードバックをお送りいただけます**](https://goo.gle/privacy-sandbox-feedback-ja)。

{% endAside %}

## 提案

個々のプライバシー サンドボックス提案に対するフィードバックとディスカッションのオプションは、[API ステータスと機能リリース](/docs/privacy-sandbox/status/)をご覧ください。

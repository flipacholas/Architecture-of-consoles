---
title: ゲーム機のアーキテクチャ
description: コンソールのアーキテクチャの進化を理解する
id: consoles
layout: consoles
seo_image: banner.png
cascade:
  description: "ゲーム機が内部の動作を説明する詳細な分析。"
  second_title: 実用的な分析
  layout: console
  type: console
  image_dir: consoles
  icons:
    - "resources"
  seo_image: diagram.png
  seo_type: TechArticle
#Historical
aliases:
  - ../projects/consoles
---

{{< linked_img src="banner.png" alt="Architecture of Consoles" no_link="true" no_lazyload="true" >}}

ビデオゲーム機の進化は興味深い。 従来のPCは「漸進的に」進化する傾向がありますが、ゲーム機は次世代機になるとまったく新しい機能を導入します。 ここでご覧いただけるのは、テクノロジーの最新トレンドの背後にある要素技術を開設する一連の記事です。 また、各システムがその「特徴」や、メガヘルツ、RAMの量などによって要約できないことを実証します。

これは開発者用のマニュアルではありません。各システムが内部でどのように機能しているかを単に*深く* 説明しただけのものです。 近年のテクノロジーはとても複雑になりつつあることを覚えておいてください。最新の記事を読むのに苦労しているなら、まずは初期のものを読んでみてください。 常に改訂されている多くの概念や定義をご紹介します。

この記事を読むには、コンピューティングの基礎知識があることが望ましいです。 しかし、私はこのコンテンツをより多くの人に届けることに一生懸命取り組んでいますので、ぜひ試してみてください！ しかし、それでも大変な場合は、[付記]({{< ref "readings">}})をご覧になるとよいでしょう。

私は可能な限り正確に記述することを心掛けていますが、 もし何か間違いが見つかったら{{< email "give a shout" >}} または{{< console/repo_link "proposing changes" >}} ください。 最後に、eBook リーダーを使用して読む場合は、 [eBook edition]({{< ref "ebook" >}}) をチェックしてください。

前置きはこのくらいにして、さっそく読んでみましょう！

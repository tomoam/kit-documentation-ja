---
title: Introduction
---

### 始める前に(Before we begin)

> SvelteKitは初期の開発段階で、バージョン1.0になるまでに様々なところが変更される可能性があります。このドキュメントもまだ進行中です。もし行き詰まった場合は、[Discordチャットルーム](https://svelte.dev/chat)で助けを求めてください。
>
> Sapperからのアップグレードについては [migrationガイド](migrating) をご参照ください。

### SvelteKitとは?(What is SvelteKit?)

SvelteKitは非常にハイパフォーマンスなWebアプリを構築するためのフレームワークです。今見ているこのサイトもそうです! 基本的なコンセプトは2つあります:

* アプリの各ページは [Svelte](https://svelte.dev) コンポーネントです
* プロジェクトの `src/routes` ディレクトリにファイルを追加してページを作成します。これらはユーザーが初めてアクセスしたときにできるだけ速くアクセスできるようサーバーでレンダリングされ、その後クライアントサイドのアプリに引き継がれます

最新のベストプラクティス — コード分割、オフラインサポート、クライアントサイドハイドレーションとサーバーサイドレンダリングビューなど — を全て用いてアプリを構築するのは気が遠くなるほど複雑です。SvelteKitが退屈な作業を全て行ってくれるので、あなたはクリエイティブな作業に専念することができます。

このガイドの残りの部分を理解するのにSvelteを知っている必要はありませんが、助けにはなります。一言で言えば、Svelteはコンポーネントを高度に最適化されたvanilla JavaScriptにコンパイルしてくれるUIフレームワークです。詳しくは [紹介ブログ記事](https://svelte.dev/blog/svelte-3-rethinking-reactivity) や [チュートリアル](https://svelte.dev/tutorial) をお読みください。

> 日本語翻訳版 追記 :  
> 上記のブログポストとチュートリアルは英語版です。日本語翻訳版はこちらからどうぞ。  
> - [紹介ブログ記事](https://sveltejp.dev/blog/svelte-3-rethinking-reactivity)
> - [チュートリアル](https://sveltejp.dev/tutorial)

### Getting started

SvelteKitアプリの構築を始める最も簡単な方法は `npm init` を実行することです:

```bash
mkdir my-app
cd my-app
npm init svelte@next
npm install
npm run dev
```

これを実行すると `my-app` ディレクトリに新しいプロジェクトが生成され、依存関係がインストールされ、サーバーが [localhost:3000](http://localhost:3000) で開始されます。ファイルを編集して、全てがどのように機能するか感触を確かめてください – もうこのガイドの残りをわざわざ読む必要はないかもしれません!

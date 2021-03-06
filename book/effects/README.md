<!--
# Commands and Subscriptions
-->
# コマンドとサブスクリプション

<!--
Earlier in this book we saw The Elm Architecture handle mouse and keyboard interactions, but what about talking to servers? Generating random numbers?
-->
これまでに本書ではThe Elm Architectureがマウスやキーボードとのやり取りをどのように扱うかを見てきました。しかしサーバーと話したり、乱数を生成するにはどうしたら良いでしょうか？

<!--
To answer these questions, it helps to learn more about how The Elm Architecture works behind the scenes. This will explain why things work a bit differently than in languages like JavaScript, Python, etc.
-->
これらの疑問に答えるために、The Elm Architectureが背後でどの様に働くのかをもっと学ぶことが理解の助けになります。そしてなぜJavaScript、Pythonやその他の言語と少し異なった働きをするのかを説明してくれるでしょう。

<!--
## `sandbox`
-->
## `sandbox`

<!--
I have not made a big deal about it, but so far all of our programs were created with [`Browser.sandbox`][sandbox]. We gave an initial `Model` and describe how to `update` and `view` it.
-->
今まで多くは触れませんでしたが、これまでのプログラムは全て [`Browser.sandbox`][sandbox] から生成されていました。初期値としての`Model`を与え、どのようにそれを`update`して`view`するのかを説明してきました。

<!--
You can think of `Browser.sandbox` as setting up a system like this:
-->
`Browser.sandbox`ではシステムを下記のように構成すると考えることができます:

![](diagrams/sandbox.svg)

<!--
We get to stay in the world of Elm, writing functions and transforming data. This hooks up to Elm&rsquo;s **runtime system**. The runtime system figures out how to render `Html` efficiently. Did anything change? What is the minimal DOM modification needed? It also figures out when someone clicks a button or types into a text field. It turns that into a `Msg` and feeds it into your Elm code.
-->

我々はElmの世界（上図のブルーの部分）の中にとどまり、いろいろな関数を書いたりデータを変換したりします。そしてこの世界がElmのランタイムシステムとつながります。このランタイムシステムは`Html`をどのように効率的に描画するのかを計算してくれます。例えば、なんらかの変化があったら？必要とされる最小限のDOMの変更はなんでしょうか？また、誰かがボタンをクリックした時や、テキストフィールドになにかをタイプした際にも計算し、その結果を`Msg`に変換した上であなたのElmコードに送り出します。

<!--
By cleanly separating out all the DOM manipulation, it becomes possible to use extremely aggressive optimizations. So Elm&rsquo;s runtime system is a big part of why Elm is [one of the fastest options available][benchmark].
-->
全てのDOM操作を明確に分離することにより、極めて積極的な最適化が実現可能となります。そしてElmが[利用可能なフレームワークの中で最速の選択肢の一つ][benchmark]となる大きな理由がElmのこのランタイムシステムです。

[sandbox]: https://package.elm-lang.org/packages/elm/browser/latest/Browser#sandbox
[benchmark]: https://elm-lang.org/blog/blazing-fast-html-round-two

<!--
## `element`
-->
## `element`
<!--
In the next few examples, we are going to use [`Browser.element`][element] to create programs. This will introduce the ideas of **commands** and **subscriptions** which allow us to interact with the outside world.
-->
続くいくつかの例ではプログラムを作るために[`Browser.element`][element]を使っていきます。そこでは外の世界とのやり取りを可能とするための**コマンド**と**サブスクリプション**の概念を導入していきます。

<!--
You can think of `Browser.element` as setting up a system like this:
-->
`Browser.element`ではシステムを下記のように構成すると考えることができます:

![](diagrams/element.svg)

<!--
In addition to producing `Html` values, our programs will also send `Cmd` and `Sub` values to the runtime system. In this world, our programs can **command** the runtime system to make an HTTP request or to generate a random number. They can also **subscribe** to the current time.
-->
このプログラムでは`Html`という値に加えて、`Cmd`と`Sub`なる値をランタイムシステムに送り出します。ここでは我々のプログラムがランタイムシステムに対してHTTPリクエストを送ったり乱数を生成するよう**指示**することができます。また現在時刻をランタイムシステムから待ち受けて我々のプログラムで使うこともできます。

<!--
I think commands and subscriptions make more sense when you start seeing examples, so let&rsquo;s do that!
-->
いくつかの例を見ていくと、コマンドとサブスクリプションについての理解が深まると思いますので、やってみましょう！

[element]: https://package.elm-lang.org/packages/elm/browser/latest/Browser#element

<!--
> **Note 1:** Some readers may be worrying about asset size. &ldquo;A runtime system? That sounds big!&rdquo; It is not! In fact, Elm assets are [exceptionally small](https://elm-lang.org/blog/small-assets-without-the-headache) when compared to popular alternatives.
-->
> **Note 1:** アセットの大きさについて心配する方がいるかもしれません。&ldquo;ランタイムシステムって大きいもんでしょ！&rdquo; いいえ、そんな事はありません。実際には競合となる他のフレームワークと比べてもElmのアセットは[非常に小さい](https://elm-lang.org/blog/small-assets-without-the-headache)のです。
>
<!--
> **Note 2:** We are going to use packages from [`package.elm-lang.org`](https://package.elm-lang.org) in the upcoming examples. We have already been working with a couple:
-->
> **Note 2:** 続く例ではパッケージを[`package.elm-lang.org`](https://package.elm-lang.org)から利用していきましょう。すでに次の二つを使用しています:
>
> - [`elm/core`](https://package.elm-lang.org/packages/elm/core/latest/)
> - [`elm/html`](https://package.elm-lang.org/packages/elm/html/latest/)
>
<!--
- But now we will start getting into some fancier ones:
-->
> 引き続きいくつかのさらにイカしたパッケージについて取り組んで行きましょう:
>
> - [`elm/http`](https://package.elm-lang.org/packages/elm/http/latest/)
> - [`elm/json`](https://package.elm-lang.org/packages/elm/json/latest/)
> - [`elm/random`](https://package.elm-lang.org/packages/elm/random/latest/)
> - [`elm/time`](https://package.elm-lang.org/packages/elm/time/latest/)
>
<!--
> There are tons of other packages on `package.elm-lang.org` though! So when you are making your own Elm programs locally, it will probably involve running some commands like this in the terminal:
-->
> その他にも非常に沢山のパッケージが`package.elm-lang.org`に存在します。ローカル環境で自分のElmプログラムを作る際には、次のようなコマンドをターミナル上で実行することになるでしょう:
>
>```bash
elm init
elm install elm/http
elm install elm/random
```
<!--
>
> That would set up an `elm.json` file with `elm/http` and `elm/random` as dependencies.
-->
>
> これにより`elm/http`と`elm/random`を依存関係として持つ`elm.json`ファイルが用意されます。
<!--
> I will be mentioning the packages we are using in the following examples, so I hope this gives some context on what that is all about!
-->
>
> 続く例の中で我々が使用するパッケージについて触れていきますので、これらがどんな役割を果たすか判明するでしょう！
>
> **Note 3:**「指示する」を意味する英語のCommandが`Cmd`の語源です。英語のSubscriptionが`Sub`の語源で、主に定期購読と訳されますが、Elmでのイメージは外部から来るメッセージを定期的に待ち受けるところからから来ています 。

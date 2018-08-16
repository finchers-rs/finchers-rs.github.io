---
layout: post
title: Hello, new Finchers!
---

Finchers は Finch インスパイアドな非同期 Web フレームワークです。
`Endpoint` というトレイトを実装したコンポーネントを組み合わせていくことで
静的に型付けされた Web アプリケーションを簡単に構築出来るよう設計されています。

`warp` の登場を受け，Finchers の次期バージョンでは `async/await` 構文への積極的な対応を
するように方針を変更しました。

# Finchers 0.12

現在の開発版から futures-0.3 を使用するようになっており，
現時点では不安定機能である async/await 構文をスムーズに使用することが出来るようになっています。

* Migrate to the new futures and async/await syntax.
* improve helper macros.

サンプルコードは以下の通りです。
ここで`route!(...)` は新たに導入されたマクロであり，
セグメントの系列から与えられたパスにマッチしパラメータを取り出すエンドポイントを構築します。

```rust
#![feature(rust_2018_preview)]

use finchers::{route, routes};
use finchers::endpoint::EndpointExt;
use finchers::endpoints::body;

fn main() -> finchers::rt::LaunchResult<()> {
    let get_post = route!(@get / u32 /)
        .and_then(get_post);

    let create_post = route!(@post /)
        .and(body::json())
        .and_then(create_post);

    let create_post = route!(/ "api" / "v1" / "posts")
        .and(routes![
            get_post,
            create_post,
        ]);

    finchers::rt::launch(endpoint)
}

async fn get_post(id: u32) -> Result<impl Responder, Error> {
    ...
}

async fn create_post(new_post: NewPost) -> Result<impl Responder, Error> {
    ...
}
```

# Comparison to Warp

今月のはじめに @seanmonstar 氏が `warp` という新しい Web フレームワークをリリースしました。 [^1]
このフレームワークのコンセプトは Finchers と非常に近いですが，
組み込みのコンポーネントが充実しているなどより洗練されたライブラリなようです。

現時点では明らかに warp のほうが人気があるようですが，それでも Finchers の開発は今後も継続して行う予定です
（そもそもの話として，似たようなライブラリが発表されることで開発を自粛しなければならないという理由はない）。

@seanmonstar によるアナウンスの後 warp と Finchers の実装を比較し，
それまでの Finchers に不足していた明らかな改善点が存在することに気づきました。

* パスを構築するためのマクロの導入
* Heterogeneous list に基づく戻り値型の平滑化

エルゴノミクスの観点から見るとこれらは明らかに改善すべき問題点であるため，
Finchers 0.12 では修正を行いました（ベースの実装は warp のものを参考にしました）。
現時点ではパス構築用のマクロを `macro_rules!` で実装しますが，
より柔軟な構文を取れるよう手続き的マクロに切り替えることを検討しています。

# Current Status

次期バージョン (0.12) は現在活発的に開発している最中ですが，開発版を使用することが出来ます。
Warp と比較したときの明らかな欠点が組み込みのコンポーネントの少なさであり，正式版をリリースする前に実装を完了させる必要があります。

* 静的ファイル
* WebSocket
* ロギング

[^1]: https://seanmonstar.com/post/176530511587/warp

---
title: "Bpaf : Rustにおける軽量かつ柔軟なCommand Line Argument Parser"
emoji: "⛓️"
type: "tech"
topics: ["Rust", "CLI"]
published: true
---

[Rust Advent Calendar 2023 Series 2 18日目](https://qiita.com/advent-calendar/2023/rust)の本記事では、[Bpaf](https://github.com/pacak/bpaf) という軽量かつ柔軟（Lightweight and flexible）なCommand Line Argument Parserを紹介します。

https://github.com/pacak/bpaf

RustのCommand Line Argument Parserといえば[Clap](https://github.com/clap-rs/clap)が有名です。特にこだわりがない場合はClapを使えばよいと思いますが、バイナリサイズやビルド時間を削減したい場合にはBpafなどの軽量なcrateが選定候補に挙げられます。

## Bpaf

Bpafは、以下のような特徴を持ちます。

- Derive style API と Combinatoric style API を提供
    - Derive style API：`derive` feature （bpaf_derive crateを利用して、re-exportする）
    - Clap v3で実装された Derive APIに近い
- UTF-8以外の文字列を含むコマンドライン引数のフルサポート
- bash, zsh, fish, elvishに対する補完を提供
    - `autocomplete` feature
- helpオプション生成・カスタマイズ
    - 色付け：`bright-color` , `dull-color` feature
    - UX向上：[doc commentsを利用したhelpのカスタマイズ](https://docs.rs/bpaf/latest/bpaf/index.html#improving-user-experience)
- 可能な限り、 [parse, don’t validate](https://lexi-lambda.github.io/blog/2019/11/05/parse-don-t-validate/) のアプローチに従う
    - 通常は値を一度だけパースして、その結果を厳密な型で取得する
- 柔軟性と再利用性
    - 特定のパース要件のためのバックドアを提供
    - 任意の時点で、各サブパーサの現在の解析状態に基づいて追加の検証やフォールバック値の適用が可能
    - BpafのParserはmonolithicではないため、複数のバイナリ・workspaceメンバー・別プロジェクトにおいても共有可能
- ドキュメントが豊富
    - チュートリアルやガイドなどが、丁寧に書かれている
    - [https://docs.rs/bpaf/latest/bpaf/index.html](https://docs.rs/bpaf/latest/bpaf/index.html)

## Exampleコード (Derive API)

Bpafでは、Derive APIとCombinatoric APIの２つが提供されていますが、これは意図的です。どちらのAPIも同時に使うことができます。
Derive APIを使う場合は、エディタからの補完は少ないですがタイプ数も少ないです。Combinatori APIを使う場合は、タイプ数が多いですが、procマクロ（bpaf_derive crate）に依存せず、エディタからの補完が増えます。

Derive APIのほうが可読性が高いと思うので、ここでは[Bpaf公式のExamplesのDerive APIの例](https://github.com/pacak/bpaf/blob/master/examples/derive.rs)を載せます。

```rust
// https://github.com/pacak/bpaf/blob/master/examples/derive.rs
//! pretty basic derive example with external function

use bpaf::{short, Bpaf, Parser};
use std::path::PathBuf;

#[derive(Debug, Clone, Bpaf)]
#[bpaf(options, version)]
#[allow(dead_code)]
struct Opts {
    /// Activate debug mode
    #[bpaf(short, long)]
    debug: bool,
    /// this comment is ignored
    #[bpaf(external(verbose))]
    verbose: usize,
    /// Set speed
    #[bpaf(argument("SPEED"), fallback(42.0))]
    speed: f64,
    /// Output file
    output: PathBuf,

    #[bpaf(guard(positive, "must be positive"), fallback(1))]
    number_of_cars: u32,
    files_to_process: Vec<PathBuf>,
}

fn verbose() -> impl Parser<usize> {
    // number of occurrences of the v/verbose flag capped at 3
    short('v')
        .long("verbose")
        .help("Increase the verbosity\nYou can specify it up to 3 times\neither as -v -v -v or as -vvv")
        .req_flag(())
        .many()
        .map(|xs| xs.len())
        .guard(|&x| x <= 3, "It doesn't get any more verbose than this")
}

/// Guard for nb_cars
fn positive(input: &u32) -> bool {
    *input > 1
}

fn main() {
    println!("{:#?}", opts().run());
}
```

### コマンド実行結果

```rust
▶ cargo run -- --debug -vvv --speed 60.5 --output /path/to/output.txt --number-of-cars 3 --files-to-process file1.txt
Opts {
    debug: true,
    verbose: 3,
    speed: 60.5,
    output: "/path/to/output.txt",
    number_of_cars: 3,
    files_to_process: [
        "file1.txt",
    ],
}
```

パース後、型安全にパース結果を利用可能です。

### versionの出力結果

```rust
▶ cargo run -- --version
Version: 0.1.0
```

デフォルトでは、Cargo.tomlから抽出されます。

### helpの出力結果

```rust
▶ cargo run -- --help
Usage: bpaf-example [-d] [-v]... [--speed=SPEED] --output=ARG [--number-of-cars=ARG] [--files-to-process
=ARG]...

Available options:
    -d, --debug               Activate debug mode
    -v, --verbose             Increase the verbosity You can specify it up to 3 times either as -v -v
                              -v or as -vvv
        --speed=SPEED         Set speed
        --output=ARG          Output file
        --number-of-cars=ARG
        --files-to-process=ARG
    -h, --help                Prints help information
    -V, --version             Prints version information
```

`bright-color` featureなどを利用すれば、helpに色付けできます。

## Bpafを利用しているプロジェクト

Bpafは、以下に挙げられるプロジェクトなどのCLIツールで利用されています（[bpaf の dependency graph](https://github.com/pacak/bpaf/network/dependents)からpick up）。

- [biomejs/biome](https://github.com/biomejs/biome)
- [oxc-project/oxc](https://github.com/oxc-project/oxc)
- [nvarner/typst-lsp](https://github.com/nvarner/typst-lsp)
- [NixOS/nixpkgs](https://github.com/NixOS/nixpkgs)（一部のcrate）

Biomeは以前はpico_argsを利用していましたが、@ematipicoによる[こちらのPR](https://github.com/rome/tools/pull/4405) でbpafを利用するように変更されました。変更の目的はPRに書かれているように、CLIのhelpやdocumentationの機能をより簡単にメンテナンス可能にできるようにしたかったためです。利用しているcargo featuresは、`derive` と `bright-color` です（`autocomplete` も使っても良いかも）。

Derive API を利用したCommand実装として、以下のファイルにあります。実践的な例として参考になると思います。

https://github.com/biomejs/biome/blob/main/crates/biome_cli/src/commands/mod.rs#L29-L44

## Alternatives

本記事では筆者の経験の都合上、他のCommand Line Argument Parserとの比較は行っていません。ですので、その代わりに、知る限りのbpafの代替となり得るcrateを列挙しておきます。

- [Clap（clap-rs/clap）](https://github.com/clap-rs/clap)
- [Argh（google/argh）](http://github.com/google/argh)
- [Lexopt（blyxxyz/lexopt）](https://github.com/blyxxyz/lexopt)
- [getopts（rust-lang/getopts）](http://github.com/rust-lang/getopts)
- pico-args（RazrFalcom/pico-args）

以下のRepositoryでは、いくつかのCommand Line Argument Parserを対象としたベンチーマークが記録されており、選定の際に役立つと思います。

[https://github.com/rosetta-rs/argparse-rosetta-rs](https://github.com/rosetta-rs/argparse-rosetta-rs)

上記のベンチマークは、Clapのメンテナ（[@epage](https://github.com/epage)）によって、メンテナンスされているようです。ちなみにLexoptは、ripgrepで採用されています。

- [various rollup + move off of Clap to lexopt by BurntSushi · Pull Request #2626 · BurntSushi/ripgrep](https://github.com/BurntSushi/ripgrep/pull/2626)

## 終わりに

本記事では、Rustにおける軽量かつ柔軟なCommand Line Argument ParserのBpafを紹介しました。Clapほど機能が豊富でなくてもよい・バイナリサイズ・ビルド時間などを削減したいという場合の選択肢となればよいと思います。

- [pacak/bpaf: Command line parser with applicative interface](https://github.com/pacak/bpaf)

今回はBpafの内部のコードについて触れていません。以下のページなどでは、Bpafで利用されている抽象的な概念について書かれているので、内部に興味がある方は読んでみると面白いと思います。

- [bpaf::_documentation::_4_explanation - Rust](https://docs.rs/bpaf/latest/bpaf/_documentation/_4_explanation/index.html)
- [Using Category Theory to parse command line options](https://rustmagazine.org/issue-2/applicative-parsing/)

ちなみに[Bpafの “af” の部分は、作者いわくApplicative Functorの略](https://www.reddit.com/r/rust/comments/xlzx3v/comment/ipnf3og/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button)とのことです（”Bp”は何を表していたか忘れたらしい）。

上記のExplanationのドキュメントなどを読んでいると、Bpafの内部実装に興味が湧きました。利用しているツールでもあるので、どこかの機会でちゃんとコードリーディング（及び コントリビューション）したくなりました。

## 参考資料

- [bpaf - Rust](https://docs.rs/bpaf/latest/bpaf/)
- [rosetta-rs/argparse-rosetta-rs: Collected benchmarks for arg parsing crates written in Rust](https://github.com/rosetta-rs/argparse-rosetta-rs)

---
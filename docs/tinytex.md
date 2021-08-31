---
title: "TinyTeX 非公式日本語版ドキュメント"
date: "2021/08/31"
site: bookdown::bookdown_site
author: 
  - Xie, Yihui (Author)
  - "Katagiri, Satoshi (片桐 智志)^[twitter id: ill_identified] (Translator)"
subtitle: 軽量・クロスプラットフォーム・ポータブル・メンテナンスも容易な TeX Live ベースの LaTeX ディストリビューション
documentclass: bxjsreport
link-citations: yes
linkcolor: blue
citecolor: blue
urlcolor: magenta
github-repo: Gedevan-Aleksizde/tinytex-doc-ja
---





---
date: "ver. 1.2.3 (2021/08/31 21:49:45 JST, 本家の更新確認時刻: [2021/06/06 22:28:33 JST](https://github.com/rbind/yihui/tree/master/content/tinytex))"
---

# TinyTeX {#tinytex}

::::::{.infobox .caution data-latex="{caution}"}

**注意**

このドキュメントは Xie, Yihui 氏による TinyTeX のドキュメントの翻訳であり, CC BY-NC-SA 4.0 (クリエイティブ・コモンズ 表示 - 非営利 - 継承 4.0 国際) ライセンスで提供されています. Yihui 氏によるオリジナルは https://yihui.org/tinytex/ で読むことができます.

:::{}

This is an unofficial Japanese translation of Yihui’s TinyTeX documentation, which is licensed under [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). The original documentation by Yihui is [here](https://yihui.org/tinytex/).

:::

::::::

\newpage

TinyTeX は TeX Live を元にした軽量 (macOS/Linux ではおよそ 61 Mb, Windows では(g)zip圧縮して 94 Mb) なカスタム LaTeX ディストリビューションですが, ほとんどの場面, 特にRユーザーにとってよく機能します. LaTeX パッケージが足りないという問題に直面しても, あなたが何をすべきかはとても明快です (実際Rユーザは何もする必要がありません). **あなたが実際に必要な LaTeX パッケージをインストールするだけです**.

<center>

![Hao Zhu \@haozhu233 による TinyTeX ロゴ](https://github.com/rbind/yihui/blob/e89f32e9f23d5271ad48261767b2326b87e83d9c/static/images/logo-tinytex.png?raw=true)

</center>

現在 TinyTeX はRユーザーにとって最善です. その他のユーザも使うことができます. LaTeX パッケージが自動的にインストールされず, 手動でする必要がありますが. あるいはありとあらゆるパッケージをインストールすることもできます (やり方は FAQ \@ref(faq-tinytex-size) 参照) が, その数は数千にもなることに注意してください.

TinyTeX のインストールまたは動作には管理者権限が**不要**です. これは `sudo` も 情シス担当者の助けもなくてよいということです. フラッシュドライブから TinyTeX を起動することすら可能です.



## R ユーザへ

R パッケージの **tinytex** (\@ref(r)節) がラッパ関数を提供しているので R ユーザにとって TinyTeX のインストールと保守は簡単です (備考: 太い小文字で **tinytex** といえば R パッケージをさし, キャメルケースで TinyTeX といえば LaTeX ディストリビューションを意味することとします). **tinytex** を使って TinyTeX をインストールできます.


```{.r .numberLines .lineAnchors}
install.packages("tinytex")
tinytex::install_tinytex()
# アンインストールするときは tinytex::uninstall_tinytex() を実行
```

R Markdown 文書を PDF にコンパイルするために, 以下の他に知るべきことは何もありません.

R Markdown 文書を PDF にコンパイルするためには, 使用したい LaTeX エンジンに応じて, **tinytex** の `pdflatex()`, `xelatex()`, `lualatex()` のいずれかの関数を呼び出します. これらの関数が, 必要なのにインストールされていない LaTeX パッケージを検出すると, デフォルトでは自動的に欠落したパッケージをインストールします.


```{.r .numberLines .lineAnchors}
writeLines(c(
  "\\documentclass[lualatex,ja=standard,haranoaji]{bxjsarticle}",
  "\\begin{document}", "ハロー日本語文書!", "\\end{document}"
), "test.tex")
tinytex::lualatex("test.tex")
```

普通の R ユーザーが知るべきことは以上です. あなたが開発者なら, `tinytex:::install_yihui_pkgs()` で[いくつかのパッケージ](https://github.com/yihui/tinytex/blob/master/tools/pkgs-yihui.txt)をインストールしたいと思うかもしれません. この関数は CRAN のパッケージの多くの PDF ビネットをビルドするのに必要な関数をインストールすることで, あなたがパッケージを探す手間を[省いてくれます](https://github.com/yihui/tinytex/issues/135).

R を使用していないなら, 知る必要があることがもう1つあります. `tlmgr` コマンドです.

## Rユーザ以外へ

TinyTeX はあなたがコマンドラインを使用することを怖がっていないことを想定しています. もし苦手意識があるなら, 代わりに他の普及している LaTeX ディストリビューションを使ってください. 実際のところ, 知る必要があるのは `tlmgr` コマンドだけです. どうか過剰に恐れないでください.


### インストール

TinyTeX のバイナリパッケージは1月ごとに, GitHub レポジトリ https://github.com/yihui/tinytex-releases としてリリースされています.

Linux ユーザの場合は TinyTeX は `$HOME/.TinyTeX` にインストールされ, 実行ファイル (`xelatex`, `lualatex` など) のシンボリックリンクは `$HOME/bin` に作成されます. このパスは通常 `PATH` 環境変数に存在するはずです.^[もしそうでなかったらどうするか, Linux ユーザなら `PATH` に追加する方法を知っていることでしょう!]

```sh
wget -qO- "https://yihui.org/tinytex/install-bin-unix.sh" | sh
```

macOS ユーザの場合は, `/usr/local/bin` への書き込み権限がないなら, TeX Live の実行ファイルのシンボリックリンクを `/usr/local/bin` に貼るために以下のコマンドを実行したほうがいいかもしれません(理由は[これ](https://github.com/yihui/tinytex/issues/188)).^[**訳注**: リンク先はこの記述の経緯が書かれているだけです.]

```sh
sudo chown -R $(whoami) /usr/local/bin
```

TinyTeX は macOS では `~/Library/TinyTeX` にインストールされます.

```sh
curl -sL "https://yihui.org/tinytex/install-bin-unix.sh" | sh
```

Windows ユーザの場合, バッチファイル [install-bin-windows.bat](https://yihui.org/tinytex/install-bin-windows.bat) を保存し (リンクを開いて `Ctrl + S`), ファイルをダブルクリックしてください.^[アンチウィルスソフトがいずれかの `*.dll` ファイルに対して警告を発するかもしれませんが, 私は Windows の専門家ではありません. 決断はあなたに委ねます.] PowerShell ([少なくともバージョン 3.0 以降](https://docs.microsoft.com/en-us/powershell/scripting/setup/starting-windows-powershell-on-earlier-versions-of-windows)) であることに注意してください. あなたの Windows のバージョンが古すぎてパワーシェルをインストールできない場合, [この記事](https://docs.microsoft.com/en-us/powershell/scripting/setup/starting-windows-powershell-on-earlier-versions-of-windows)に従ってインストールしてください. インストールフォルダは `%APPDATA%/TinyTeX` で, `APPDATA` は典型的には `C:\Users\Your Name\AppData\Roaming` を指す環境変数です.^[興味があるなら, スタートメニューをクリックし, `cmd` とタイプして実行してコマンドウィンドウを開き, `echo %APPDATA%` とタイプして実行すれば実際のファイルパスが分かります.]

:::{.infobox .memo data-latex="{memo}"}

**訳注**: 昔の話だからかリンクが切れています. 古いバージョンの互換性に詳しい方がいたら教えてください. 最近の市販品はこれより新しいバージョンの PowerShell が最初から使えることが多いためあまり気にする必要がないとは思いますが, 現在の日本語の PowerShell のインストールに関するドキュメントは[こちら](https://docs.microsoft.com/ja-jp/powershell/scripting/install/installing-powershell?view=powershell-7.1)になります.

:::

代わりに Chocolatey か Scoop を使って TinyTeX をインストールすることもできます. 手順は[TinyTeX のリリース情報](https://github.com/yihui/tinytex-releases)を確認してください.

TinyTeX をアンインストールするなら, ファイルマネージャ/ブラウザ等でフォルダごと消すだけです. あるいはコマンドラインで以下を実行するだけです.

```sh
# Linux の場合
tlmgr path remove
rm -r "~/.TinyTeX"

# macOS の場合
tlmgr path remove
rm -r ~/Library/TinyTeX

# Windows の場合
tlmgr path remove
rd /s /q "%APPDATA%\TinyTeX"
```

他の LaTeX ディストリビューションと TinyTeX のアンインストール方法を比べてみてください. 他のディストリビューションと比べ TinyTeX がいとも簡単にアンインストールできることをあなたはきっと評価するでしょう. これは TinyTeX が自己完結的なフォルダ構成になっているからです.


### メンテナンス

この節は R Markdown ユーザー**以外**を対象としています. R Markdown ユーザにとっては何もかも自動的に行われ, 欠落した LaTeX パッケージを手作業で探したりインストールしたりする必要はありません.

LaTeX 文書をコンパイルして, このようなエラー文が流れてくるかもしれません.

```
! LaTeX Error: File `times.sty' not found.

Type X to quit or <RETURN> to proceed,
or enter new name. (Default extension: sty)
```

これは基本的にはある LaTeX パッケージが足りないことを意味しています. 慌てないでください. コマンドウィンドウを開き, `tlmgr search --global --file` タイプし, その後に続けてファイル名をタイプしてください.^[ファイル名の前のスラッシュも正確に合わせてください. スラッシュがない場合, 他のパッケージがマッチするかもしれません. たとえば `chemtimes.sty` とか.] 例えば, 以下のようになります.^[**訳注**: TeX Live に登録されているファイルパスに対して正規表現マッチで検索するため, ファイル名を完全一致で検索したい場合はこのようにスラッシュを頭に付けます. 正規表現では `.` は任意の1字のマッチングと解釈されるため, さらに万全を期すならば, `/times\.sty` となります.]


```sh
$ tlmgr search --global --file "/times.sty"
psnfss:
        texmf-dist/tex/latex/psnfss/times.sty
...
```

上記のエラーログに現れた名前のファイルを含むパッケージを見つけてください. 今回は足りないパッケージは `psnfss` で,  `tlmgr install` でインストールすることができます.

```sh
tlmgr install psnfss
```

代わりの方法として, GitHub レポジトリの [yihui/latex-pass](https://github.com/yihui/latex-pass) にエラーログをアップロードするだけで, クラウド上で `tlmgr search` が実行され不足しているパッケージを教えてくれます.

理解するのが難しいエラーメッセージを見つけたなら, 全てアップデートしてしまうとよいかもしれません.

```sh
tlmgr update --self --all
tlmgr path add
fmtutil-sys --all
```

R ユーザにとっては, これらに対応する便利関数が使用できます. いくつか例を挙げます.

```r
library(tinytex)
tlmgr_search('/times.sty')   # times.sty を検索する
tlmgr_install('psnfss')      # psnfss パッケージを検索する
tlmgr_update()               # 全て更新する
```

"Remote repository newer than local" というエラーメッセージを見たら, それは TinyTeX を手動でアップグレード (再インストール) する時であることを意味しています.

```r
tinytex::reinstall_tinytex()
```

以下のようなメッセージを見ても慌てないでください. 数ヶ月待ってから, TinyTeX を再インストールするだけです.

> TeX Live 2018 is frozen forever and will no longer be updated.  This happens in preparation for a new release.

> If you're interested in helping to pretest the new release (when pretests are available), please read https://tug.org/texlive/pretest.html. Otherwise, just wait, and the new release will be ready in due time.


普通のユーザにとって知るべきことは以上です. TinyTeX に関するより技術的に詳細なことを知りたいならば, \@ref(faq) 節を読んでください.

## モチベーション

TinyTeX の背景のモチベーションとして, LaTeX ディストリビューションとインストールと維持に関する共通の2つの問題がありました.

1. 比較的小さい (数百 Mb) の基本バージョンをインストールしなければならないが, そうするとはおそらくきっと頻繁に使われる LaTeX パッケージが欠落しているせいで動作しない. しかし数GBのフルバージョンをインストールしたとして, 生涯できっとそのうち1%も使わない.

1. インストールとメンテナンスに関するドキュメントはしばしば初心者にとっては長すぎる. 例えば[`tlmgr` のマニュアル](https://www.tug.org/texlive/doc/tlmgr.html) は包括的な内容で有用ですが, ある `.sty` ファイルが見つからないという問題が生じた時にどうすればいいのか解決するのが難しい.

1. 既存の LaTeX ディストリビューションはインストールやメンテナンスにしばしば管理者権限を要求しています. `sudo` や情シス担当に問い合わせることは大いに面倒を生んでいました.

幸いにも TeX Live ベースのよい突破口がありました. TeX Live はすばらしい. クロスプラットフォームであり (そう Windows 上でも動作します), ポータブルにすることも**可能**です. 我々は単に軽量で, メンテナンスも簡単にする必要があるだけです. そこで TinyTeX の出番です.

## 謝辞

TinyTeX は TeX Live チームの偉大な業績なくしてありえませんでした. これに感謝したいと思います. 私は特に, ソースコードとドキュメントをインストールから省くオプションを提案してくれたことをうれしく思っています.

極めて注意深く, そして忍耐強く[テスト](https://d.cosx.org/d/419672)し, さらにはとても有意義なフィードバックをくれた [Peng Zhao](http://www.pzhao.org) と [Miao Yu](https://yufree.cn) には感謝してもしきれません. [Carl Boettiger](https://www.carlboettiger.info), [TC](http://tc.rbind.io), [Ce Gao](https://github.com/gaocegege), [Xiangyun Huang](https://github.com/XiangyunHuang) にもベータテストを手伝ってもらいました. 六角形ロゴは [Hao Zhu](https://github.com/haozhu233) によるデザインです.

<!--chapter:end:index.Rmd-->

# R パッケージとしての **tinytex** {#r}

TinyTeX を管理し, LaTeX 文書をコンパイルするヘルパー関数について

オリジナルのページ: https://yihui.org/tinytex/r/

オリジナルの更新日: 2017/12/03

----

CRAN または GitHub から **tinytex** パッケージをインストールできます.


```{.r .numberLines .lineAnchors}
# CRAN 版
install.packages("tinytex")

# Github 上の開発版
remotes::install_github("yihui/tinytex")
```

パッケージは主に2種類の関数の集合からなります. TinyTeX を管理する関数と, LaTeX 文書を PDF にコンパイルするのを助ける関数です.

## TinyTeX を管理する

`tlmgr_*` シリーズの関数は TeX Live を管理するためのユーティリティコマンドである `tlmgr`  を呼び出します. 例えば LaTeX パッケージのインストールと更新です. `?tinytex::tlmgr` でドキュメントの完全版を見てください. `tlmgr_search()`, `tlmgr_install()`, `tlmgr_update()` は TeX Live を自分で管理したい場合に最もよく使う関数です.

## LaTeX 文書をコンパイルする

LaTeX の「最高に愛すべき」機能は, PDFを取得するのにしょっちゅう,  相互参照 (例えば索引や引用文献リスト) があるときに文書を複数回コンパイルしなければならないことです.

LaTeX の失敗のよくある原因は LaTeX パッケージが足りないことです. どのパッケージが欠けているかがすぐには分からないことがあります (いくつかのパッケージは複数の `*.sty` や `*.cls` ファイルを含んでいます). あなたがパッケージ化された TeX Live を使っているなら,  この問題は悪化する可能性があります. 例えば Debian/Ubuntu では LaTeX パッケージはグループ分けされ, それぞれ異なる Debian パッケージとしてビルドされるので LaTeX パッケージが1つ欠けていただけでも, あなたが必要としているものに加え, 不要な多くのパッケージを含んでいるかもしれない巨大な Debian パッケージをインストールしなければなりません.

**tinytex** の `latexmk()` 関数はこれら2つの問題を自動的に解決しようとします. 指定された LaTeX エンジン (`pdflatex`, `xelatex`, `lualatex`)^[**訳注**: 日本語ユーザはまだあまり使うことがないかもしれませんが, 最近 (v0.3.1) は `tectonic` もサポートしています.] を使用し, デフォルトで LaTeX 文書を3回コンパイルします^[**訳注**: 文献引用と数式やセクション番号といった相互参照を含んだ, 典型的な LaTeX によるレポート・学術論文ならば3回で十分です.]. また, デフォルトで不足しているパッケージをインストールしようとします. この関数は足りないパッケージを `tinytex::parse_packages()` 関数を介して LaTeX ログ からインストールしようとします.

文書をコンパイルする最大回数を変えることも, 不足パッケージの自動インストールを無効化することもできます. 詳しくは `?tinytex::latexmk` を見てください.^[**訳注**: それぞれ `min_times`/`max_times` と `install_packages` で指定できます.]

`pdflatex()`, `xelatex()`, `lualatex()` という3つの関数は `latexmk()` ベースのラッパ関数です. 例えば `pdflatex()` は基本的に `latexmk(engine = 'pdflatex', emulation = TRUE)` と同じです.

その実装はぎこちないです. ソースコードを読まないでください. 将来的にも改善されることはないでしょう. LaTeX に対して十分に時間を費やしてきましたし, この洗練されていない実装によって多くの時間を節約するべきでしょう. 「最善は善の敵」というやつです.

## デバッグ

LaTeX を PDF にコンパイルするときにエラーが起こったなら, 問題が解消されるまで以下のような段階を1つづつ踏んでください.

1. [R と LaTeX パッケージを全て更新する](https://yihui.org/en/2017/05/when-in-doubt-upgrade/):

    
    ```{.r .numberLines .lineAnchors}
    update.packages(ask = FALSE, checkBuilt = TRUE)
    tinytex::tlmgr_update()
    ```

1. LaTeX パッケージの更新時に "tlmgr: Remote repository is newer than local (2018 < 2019)" のようなエラーが発生したら, それはあなたの TinyTeX が古すぎることを意味します. 以下のようにして新しいバージョンを再インストールしてください.

    
    ```{.r .numberLines .lineAnchors}
    tinytex::reinstall_tinytex()
    ```

1. 更新でも再インストールでも問題を解決できないなら, 文書をコンパイルする前に以下のように R のオプションを設定してください:

    
    ```{.r .numberLines .lineAnchors}
    options(tinytex.verbose = TRUE)
    ```

    これで問題のデバッグをする助けになる追加のメッセージが表示されます. R Markdown と RStudio の `knit` ボタンを使用しているなら R Markdown 文書のコードチャンク内でこのオプションを設定すべきです.
    
    ````md
    
    ````

    あなたができること, そしてもしかするとすべきことなのは, デバッグを終えたあとにこのオプションを削除することです (もはや完全なログを読む必要はなく, LaTeX を静かにさせるため

1. 上記いずれも効果がなかったら, [このガイド](https://yihui.org/issue/)に従って問題を報告してください.^[**訳注**: こちらの窓口は当然ながら英語 (中国語も可?) での対応となります. 日本語の質問フォーラムとして[スタック・オーバーフロー日本語版](https://ja.stackoverflow.com/questions/tagged/latex)や, [TeX Wiki のフォーラム](https://oku.edu.mie-u.ac.jp/tex/), [teratail ](https://teratail.com/tags/LaTeX) がありますが, 日本語ユーザの間でかならずしもTinyTeXが知られているとは限らないことに注意してください.] あなたがとても忙しい場合, 問題の報告に際してもっとも重要なもの, 「**必要最小限の再現例**」を忘れないでください.

## その他の LaTeX ディストリビューション

(当然ながら) **tinytex** パッケージは TinyTeX との組み合わせで最もよく動いてくれるはずですが, TinyTeX と完全に不可分なわけではありません. 特に以下に注意してください.

1. LaTeX 文書の相互参照が解決するまで数回にわたって自動コンパイルする機能はどの LaTeX ディストリビューションでも有効です. お望みなら Windows 上で MiKTeX を使うこともできます.^[**訳注**: 日本語コミュニティでは Windows に対しては従来 [W32TeX](http://w32tex.org/) がよく使われていると思います. これも最近のバージョンは TeX Live をベースにしているため, 概ねうまく動作するはずです. Windows 上での従来の TeX 事情はこちらを参考に: https://texwiki.texjp.org/?Microsoft%20Windows]

1. LaTeX の不足しているパッケージを自動インストールする機能はディストリビューションが TeX Live であることが必要です (TinyTeX は TeX Live のカスタム版なので TinyTeX でも動作することを思い出してください). パッケージをインストールできるようにするには `tlmgr install` も必要です. システム管理者によってインストールされた TeX Live や TinyTeX のコピーを使用しているなら, あなたには変更する権限が与えられておらず, この機能は使えません. あなたが自分のPCにTinyTeXをインストールしているなら, できる公算が大きいです. この機能は MiKTeX ユーザには使えませんが, MikTeX 自体も不足パッケージを自動インストールできるため, そもそも必要ないでしょう.

<!--chapter:end:r.Rmd-->

# 訳注: 日本語ユーザへの補足説明 {#addendum-ja}

:::{.infobox .important data-latex="{important}"}

このページは日本語ユーザ特有の事情を考慮し, 翻訳者の独断で補足説明として挿入しています.

:::

## TeX Live について

公式ガイドの和訳版をご覧ください.

https://www.tug.org/texlive/doc/texlive-ja/texlive-ja.pdf

## TinyTeX の軽量さについて

日本語文書作成には独自のパッケージをそれなりに必要とするため, 残念ながら意外とファイルサイズが大きくなることがあります. しかし\@ref(faq-size) でも注記したように, せいぜい 500 MB 前後にとどまることが多いと思いますし, TeX Live のように過年度版が不要であることに気づかないでいると年々占有領域が肥大化する, ということもありません. 

## (u)pLaTeX や (u)pBibTeX 使用者へ

LaTeX ディストリビューションとしては TinyTeX でも TeX Live と同様に (u)pLaTeX や (u)pBibTeX を使うことができます. しかしこれらの LaTeX エンジン (および, 頭に `up` とついているもの概ね全て) は日本で独自に開発されてきたものであるため, **tinytex** や **rmarkdown** パッケージではオプションとしてあまり考慮されていません. 

**rmarkdown** を使用する場合, 基本的に XeLaTeX または LuaLaTeX を使用して日本語文書をコンパイルしたほうが簡単です. ただし従来の pLaTeX と異なり, bxjs/ltjs シリーズの文書クラスを使用したほうが品質の良い日本語文書を作成できます. 日本語文書のための設定に関する詳細は翻訳者の開発した [**rmdja** パッケージ](https://github.com/Gedevan-Aleksizde/rmdja)のヘルプやドキュメントや, https://rpubs.com/ktgrstsh/755893 を参考にしてください. **rmdja** には試験的ですが (u)pLaTeX を使用する機能もあります.

一方で, 以降は **rmarkdown** ではなく従来の LaTeX ディストリビューションの代わりとして **tinytex** を使いたい場合の解説をします. まず, 日本語環境を想定していないため文献引用もデフォルトでは BibTeX が呼び出されるため, (u)pBibTeX を想定したファイルを使用しているとハングアップの原因となります. **tinytex** パッケージで (u)pLaTeX/(u)pBibTeX による処理を `xelatex()` や `pdflatex()` のように呼び出したい場合, `latexmk()` を使用します. TeX Wiki の[このページ](https://texwiki.texjp.org/?Latexmk) に掲載されている `.latexmkrc` ファイルを作成し^[もしうまく動作しない場合は **rmdja** パッケージに内蔵してある[ファイル](https://github.com/Gedevan-Aleksizde/rmdja/blob/master/inst/resources/latexmk/.latexmkrc) をコピーしてください.], 作業フォルダに置いた上で, 以下のようなコマンドを実行します.


```{.r .numberLines .lineAnchors}
tinytex::latexmk(
  "<あなたのファイル>.tex",
  engine = "latex",
  bib_engine = "bibtex",
  engine_args = "-latex=uplatex -gg -pdfdvi",
  emulation = F,
  clean = F
)
```

ここで `.latexmkrc` の設定を忘れていると, `bibtex` の処理でハングアップしてしまうため, おかしいと思ったら早めに `bibtex` を強制終了してください (最初はタスクスケジューラなどを見ながら試すといいかもしれません).

また, この方法では **tinytex** の**不足パッケージ自動インストール機能が無効**であることに注意してください.

これは現在一般的な `dvipdfmx` を利用して PDF を生成する場合です. 例えば `dvips` を使用する場合は別のオプションが必要になりますが, それは latexmk の使い方としてご自身で調べてください.

また, BibTeX 問題を回避する別の方法として `bib_engine = "biber"` を指定することで Biber (BibLaTeX) エンジンを使えばこの問題は起こりませんが, BibLaTeX には日本語に適した文献スタイルファイルがまだほとんど用意されていない, という問題があります (エラーが発生することは少ないと思いますが姓名の表記順などが考慮されていません).

## 既存の TeX Live 使用者と後方互換性について

本文でも述べられているように, TeX Live をすでにインストールしている方は, R パッケージの **tinytex** で提供される関数をそのまま使うことができます. むしろこの状態で `tinytex::install_tinytex()` を使用してインストールすると競合が発生してしまいます. もし誤ってインストールしてしまった場合は, TinyTeX のインストールフォルダ　(場所がわからない場合は FAQ \@ref(change-directory)参照) をまるごと削除してください.

TinyTeX のディストリビューションは TeX Live と同様, 年々更新されています. よって古いバージョンは自動的に凍結されていくため, 環境を維持したい場合は注意してください. 例えば最近ですと, 次のような問題が取り沙汰されています. https://acetaminophen.hatenablog.com/entry/2021/06/18/022108

このような場合は, 究極的には自衛手段として TinyTeX のフォルダのバックアップを取ることになると思います. 

## パッケージの自動インストール機能の限界について

TinyTeX のパッケージ自動インストール機能は, LaTeX 文書のコンパイル時のエラーログから, ファイルが足りないというエラーメッセージをパターンマッチングで検出し, 必要なパッケージを探索しています. よってエラーメッセージが特殊なものである場合はうまく動作しません.

日本語ユーザでありがちなケースとして, 例えば以下のようなケースがあります.

1. `ltjs*` および `bxjs*` シリーズの文書クラスを使用している場合,
1. `haranoaji` などの日本語フォントパッケージを使用している (かつ通常のフォントとしてインストールしていない) 場合
1. 先述の (u)pBibTeX を使用する場合

(1, 2) はうまくいかなかったという報告が以前ありましたが, 正確な原因は特定できていません. 現在は問題なく動作するかもしれません. 最後の (3) は残念ながら根本的な解決はできませんが, エラーログファイルを `tinytex::parse_install()` に与えることで不足パッケージの自動解析および自動インストールをさせることができます.

もしうまく動作しない場合のために, 以下に XeLaTeX/LuaLaTeX を使用した日本語文書作成によく使用されるパッケージをまとめてインストールするコマンドを用意しました.


```{.r .numberLines .lineAnchors}
tinytex::tlmgr_install(c("bxjscls", "luatexja", "zxjatype", "zxjafont", "haranoaji", "jlreq"))
```

また, 本文 (FAQ\@ref(faq-tikz)) でも触れられているように, R の **tikzDevice** を使用する際もこのエラーにハマりがちです.


```{.r .numberLines .lineAnchors}
tinytex::tlmgr_install(c("pgf", "preview", "xcolor"))
```

## CTAN や TeX Live に登録されていないパッケージのインストール方法について

TeX Live に登録されていないパッケージは `tlmgr` コマンドでも **tinytex** の関数でも検索対象ではないため, ダウンロードおよびインストールすることもできません. 例えば [**BXcoloremoji**](https://github.com/zr-tex8r/BXcoloremoji) パッケージ, [**pxrubrica**](https://github.com/zr-tex8r/PXrubrica) パッケージ, あるいは日本国内の学会発表・学術論文用フォーマットなどが該当します. また, 登録されているものと, GitHub などでダウンロードできる開発版とでバージョンが異なることもありえます.

このような場合, `.sty` や `.cls` といったファイルを手動でダウンロードし, 適切な場所に置く必要があります. プロジェクトのフォルダに直接置いても良いですが, それだと新たに文書を作成するたびにファイルをコピーする必要があります.

TinyTeX および TeX Live の仕様として, ファイルを配置する場所はある程度自由ですが, 管理のしやすさを考慮して FAQ \@ref(non-root-package-installation) にあるような, `texmf-home` フォルダまたは, `texmf-local` 以下に配置することをお勧めします.^[https://tex.stackexchange.com/questions/583006/where-do-i-place-my-own-sty-or-cls-files-to-get-them-working-with-tinytex では 2021/3/3 以降は `texmf-home` に配置すべきという回答が書かれていますが, v0.33 で確認したところ `texmf-home` はデフォルトでは認識されず, `texmf-local` 以下に置く必要があります.] ターミナルで `kpsewhich --var-value=TEXMFHOME` を実行し出力されたフォルダに配置します. このフォルダ以下に配置したファイルであれば認識されますが, 慣例的に `.sty`/`.cls` ファイルは主に `tex/latex/` 以下に, `.bst` ファイルは `bibtex/bst` 以下に置かれます.

 (システム管理者の方は適当に工夫してください). root でインストールしていない場合は `texmf-local` になると思います.

TinyTeX は ls-R を使用しているため[^ls-r] その後 `mktexlsr` を実行します. R上では `system()` で呼び出すことができます.

[^ls-r]: ls-R 不要論を唱える方もいますし私もその主張には一理あると思いますが, TinyTeX では残念ながら使用しているので必要です.


```{.r .numberLines .lineAnchors}
system("mktexlsr")
```

ただし, 例に挙げた **BXcoloremoji** パッケージは, `$TEXMF/tex/latex/BXcoloremoji` 以下に配置するように指示があります. 今回は `$TEXMF` は `texmf-home`/`texmf-local` に対応させることができます. このように, パッケージによってはフォルダ構造に制約があるため, 手動インストール時には注意してください.

TeX のフォルダ構造の仕組みについては TeX Wiki の『[LaTeX入門/各種パッケージの利用](https://texwiki.texjp.org/?LaTeX%E5%85%A5%E9%96%80%2F%E5%90%84%E7%A8%AE%E3%83%91%E3%83%83%E3%82%B1%E3%83%BC%E3%82%B8%E3%81%AE%E5%88%A9%E7%94%A8)』と『[TeX のフォルダ構成](https://texwiki.texjp.org/?TeX%20%E3%81%AE%E3%83%87%E3%82%A3%E3%83%AC%E3%82%AF%E3%83%88%E3%83%AA%E6%A7%8B%E6%88%90)』も参考になります.

**どうしてもうまく行かなかったら作業フォルダに毎回配置してください**.

<!--chapter:end:addendum-ja.Rmd-->

# FAQ {#faq}

よくある (?) 質問 

オリジナルのページ: https://yihui.org/tinytex/faq/

オリジナルの最終更新日: 2017/12/02

----

このよくある質問リストでは, 好奇心のある方に TinyTeX の技術的詳細について説明したり, システム管理者でない人に向けて TinyTeX をインストールしたり使用したりする代わりの方法を説明したりします.


## TeX Live のどのバージョンがインストールされるのですか?

デフォルトでは, Windows, macOS, Linux の最新の動作バージョン, そして他のプラットフォームでも (動作の有無にかかわらず) 最新のバージョンです. ここでの「動作」とは, いくつかの R Markdown 文書を PDF にコンパイルする基本的なテストをパスした TinyTeX のバージョンです. もし古いバージョンをインストールしている場合, 次の FAQ を読んでください.

現在の最新バージョンをインストールできて, このバージョンが十分に安定しているなら将来もアップグレードしない選択ができることを覚えておいてください. ただし, これは現在の TeX Live バージョンが凍結 (年単位で凍結されます) されて以降は LaTeX パッケージをインストールすることもアップデートすることもできないことに注意してください.

TODO: 日本語ユーザにとって後方互換性は重要. なにか追加サポートが必要

## TinyTeX のビルド済みバイナリはありますか?

はい, あります. 2020年9月から TinyTeX のビルド済みバイナリパッケージの提供を始めています. それらは [`yihui/tinytex-releases`](https://github.com/yihui/tinytex-releases) で見ることができます. 2名の TeX Live 開発者に TeX Live のライセンスについて教えてもらったのでこれが可能となりました. それ以前はこのライセンスは私にとって複雑で, バイナリパッケージとして再配布することをためらっていました. これが可能であると知ってからは AppVeyor 上でビルドし GitHub リリースとして公開しています.


## TinyTeX のサイズはどれくらいですか? {#faq-tinytex-size}

macOS と Linux では gzip 圧縮で 61 MB, Windows では zip 圧縮で 94 MBです. これでもまだ大きすぎると思うかもしれませんが, [Basic TeX インストーラ](https://www.tug.org/mactex/morepackages.html) のサイズが macOS ではおよそ 80 MB に, Windows では [MiKTeX インストーラ](https://miktex.org/download) がおよそ 235 MB になることを考えてみてください.

TinyTeX の軽量さはクラウド上で (たとえば Travis CI で意図的にテストするとき) インストールするときにとても便利でしょう. ダウンロードとインストールはほんの数秒しかかかりません.

もちろん, TinyTeX のサイズは追加で LaTeX パッケージをインストールするたびに増加します. 極端な話, 全てのパッケージをインストールしたいかもしれません (どのパッケージが足りないのかを把握する必要を排除してしまうために). そうしたい場合でも, R ではこのように実行します.


```{.r .numberLines .lineAnchors}
tinytex::tlmgr_install("scheme-full")
```

コマンドラインで同様の操作はこうなります

```sh
tlmgr install scheme-full
tlmgr path add
```

数ギガバイトぶんのパッケージをダウンロードしインストールする必要があるため, これはかなり時間がかかります

## TinyTeX はどうやって作られたのですか? 巨大な TeX Live のサイズをどうやって削減したのですか? {#faq-size}

TinyTeX を理解する最善の方法は TUGBoat の "[TinyTeX: A lightweight, cross-platform, and easy-to-maintain LaTeX distribution based on TeX Live](https://tug.org/TUGboat/Contents/contents40-1.html)" を読むことです. あるいは代わりに `tools` フォルダにある[ソースを読む](https://github.com/yihui/tinytex/)こともできます. 基本的に TinyTeX は, [tinytex.profile](https://github.com/yihui/tinytex/blob/master/tools/tinytex.profile) (`./install-tl -profile=tinytex.profile`) というプロファイルで TeX Live のインストールを自動化しています. このファイルは最初にインストールされる `infraonly` スキームを指定しているだけです. このスキームがあるので LaTeX のドキュメントは一切コンパイルできませんが, 最も重要なユーティリティである `tlmgr` (TeX Live マネージャ) だけは含まれます. この時点での全体のサイズは (UNIX系では) 1 MB 未満です.

それから `tlmgr` で基本的なパッケージをさらにいくつかインストールします (これは [pkgs-custom.txt](https://github.com/yihui/tinytex/blob/master/tools/pkgs-custom.txt) で定義されています). これらのパッケージがあればほとんどの R Markdown 文書を PDF にコンパイルすることができます. 全体のサイズはおよそ 61 MB になります.

TinyTeX には少数の LaTeX パッケージを含めているだけであるという点は, TinyTeX が比較的小さなサイズである2つの理由のうちの1つです. もう1つの理由は, パッケージのソースファイルとドキュメントファイルを除外している点です. `tinytex.profile` にこのような2つオプションが書かれているのがわかるでしょう.

```config
option_doc 0
option_src 0
```

なぜソースファイルを排除したのか? これらはエンドユーザにとって役に立つ場面がなさそうだからです. あなたは LaTeX のソースコードを読みますか? あなたが開発者や上級者ユーザでないならおそらく読んでないでしょう.

なぜドキュメントを排除したのか? 正直に答えてください, あなたは [StackExchange](https://tex.stackexchange.com) で答えを探したことが何度ありますか, パッケージのドキュメントを読んだことが何度ありますか? 完全なドキュメントがあっても, あなたはコンピュータのどこにそのファイルがあるのかすら知らないでしょう. ドキュメントのファイルはディスクの容量を多く占有し, 平均レベルのユーザはほとんど読まないと私は思っています. ゆえに同梱しません. web ブラウザのアドレスバーが最も便利なドキュメントです. キーワードをタイプし, 検索してください.

パッケージのソースとドキュメントを本当に欲しているのなら, [CTAN](https://ctan.org) で見つけることができます. あるいは `tlmgr installl --reinstall --with-doc --with-src <パッケージ名>` というコマンドでソースとドキュメントを再インストールできます. ([Norbert Preining の指摘](https://github.com/yihui/tinytex/pull/236#issuecomment-690789472) に感謝します)

TeX Live のサイズに影響する他の主な要因としてフォントパッケージがあります. フォントパッケージは大抵の場合は他の LaTeX パッケージよりずっと大きいのですが, ほとんど使うことがありません. あなたが `pdflatex` を使わないというのなら話は別ですが. こういった場合では, サイズの小さな TeX Live ディストリビューションをさらに削減することができます.

インストールされたパッケージ全てのサイズを `tlmgr info --list --only-installed --data name,size` というコマンドで得ることができます. R パッケージの **tinytex** をインストールしているなら, `tinytex::tl_sizes()` を使うこともできます. 例えばフォントパッケージの **lm** (Latin modern) はおよそ 42 MB もあります!

:::{.infobox .memo data-latex="{memo}"}

**訳注**: 日本語ユーザの場合, `pdflatex` で日本語文書を作成するのは大変なので, ほとんどの日本語ユーザはフォントパッケージが必要であり, また xelatex, lualatex といった多言語に対応した処理系を使うことになるため, ここで書かれているよりもサイズが大きくなります. しかしそれでも, 典型的な日本語文書を作成するのに必要なサイズは, 例えば Linux 系では 500 MB 程度に収まると思います.

:::

## 私は Linux システムの管理者です. システム使用者全員が使えるように TinyTeX をインストールするにはどうしたらいいですか? {#sys-install}

まずインストールスクリプトに2つのオプション `--admin --no-path` を追加してください.

```sh
wget -qO- "https://yihui.org/tinytex/install-unx.sh" | sh -s - --admin --no-path
```

これは TinyTeX を `~/.TinyTeX` にインストールします. この時点では root 権限は**不要**です. それからシステムの利用者全員が TeX Live の実行ファイル (例: `xelatex` や `lualatex`) を使えるように `sudo` で `/usr/local/bin` にシンボリックリンクを追加する必要があります.
    
```sh
sudo ~/.TinyTeX/bin/*/tlmgr path add
```

`~/.TinyTex` にデフォルトで `700` のパーミッションが設定される[システムも中にはあります](https://github.com/yihui/tinytex/issues/37). これは他のユーザは TinyTeX を使用できないことを意味します (このフォルダ内では読み込み・書き込み・実行いずれの権限もありません). `chown` を使用してこのフォルダのオーナやグループを変更し, 書き込み権限 (さらに `bin` への実行権限) を付与するとよいでしょう. これが例です.

```sh
chown -R root:staff ~/.TinyTeX
chmod -R g+w ~/.TinyTeX
chmod -R g+wx ~/.TinyTeX/bin
```

`~/.TinyTeX` を他の場所に移動したいなら, FAQ\@ref(change-directory) を見てください. そしてフォルダを移動したあとに,  `/usr/local/bin` にあるシンボリックリンクが新しい場所を正しく参照するように `sudo` で `tlmgr path add` を実行することを忘れないでください.


## 私は Linux 管理者です. 上記のアプローチで TinyTeX をインストールし `/usr/local/bin` にシンボリックリンクを追加しました. root 権限のないユーザーに自分で LaTeX パッケージをインストールしてもらうにはどうしたよいですか? {#non-root-package-installation}

root 権限のないユーザは通常の方法では, TinyTeX のルートフォルダ (デフォルトでは `~/.TinyTeX`, `~` は**あなた自身の**ホームフォルダを意味します) に書き込み権限を持つグループに登録されない限り, `tlmgr install` コマンドでパッケージをインストールすることができません. このような場合は TinyTeX のシステム全体に対してインストールされた TinyTeX を修正することができます.

TinyTeX フォルダへの書き込み権限がない人たちには, TeX Live の[ユーザーモード](https://www.tug.org/texlive/doc/tlmgr.html#USER-MODE) があり, これはユーザーごとに texmf ツリーを管理することができます. 例えばシステムフォルダの代わりに, そのユーザーのホームフォルダにパッケージをインストールできます. TinyTeX があれば, ユーザーレベルの texmf ツリーは `~/.TinyTeX/texmf-home` にあります (TeX Live の用語としては, `TEXMFHOME` 環境変数となります. `~` は**あなた自身の**ホームフォルダを意味します).

ユーザが最初にすべきことは, このツリーを初期化すること (`TEXMFHOME` がまだ作成されていないなら作ること) です. 以下を一度実行するだけです.
    
```sh
tlmgr init-usertree
```

それからパッケージをインストールする時には, ユーザは常に `--usermode` オプションを付けなければなりません. 例えば以下のように.

```sh
tlmgr --usermode install koma-script xcolor
```

R ユーザなら, 上記のコマンドは以下の R の命令文と同じです.
    

```{.r .numberLines .lineAnchors}
tinytex::tlmgr("init-usertree") # これも最初に一度実行するだけです
tinytex::tlmgr_install(c("koma-script", "xcolor"), usermode = TRUE)
```
    
しかし, TeX Live のユーザーモードは実際のところかなり複雑になる可能性があり, 残念なことに私はサポートしきれません. 少しだけ注意事項を挙げておきます.

* 最悪のやり方はユーザが一切のパッケージをインストールできないことです. TeX Live では**再配置可能**なパッケージとそうでないものがあります. 例えば実行ファイルを含むパッケージは再配置できません (例: **metafont** パッケージは `mf` という実行ファイルを含んでいます). ユーザがこれらのパッケージを使わなければならないなら, システム管理者だけが頼りです. 良い知らせとして, こういったパッケージは小規模なので, これらを全てをシステム管理者が最初からインストールしておくという保守的な戦略がある, ということがあります. R 関数の `tinytex::tl_unrelocatable()` は再配置不可能な全てのパッケージの名前を返します. これは内部で `tlmgr info --list --data name,relocatable` を呼び出しています.

* パッケージの中にはインストール後に [`updmap`](https://www.tug.org/texlive/doc/updmap.html) を実行することが必要なものもあります (例: `pdflatex` に使用するフォントパッケージ). 良い知らせは  `updmap-user` を実行できることで, 悪い知らせは (私のドキュメントの理解が正しければ) システム管理者が `updmap-sys` を実行するたびに, ユーザは `updmap-user` を再実行せねばならないことです. ユーザにとっての保守的な戦略は, 以前にはなかったフォントの問題に出くわすたびに, `updmap-user` を実行することです (R ユーザは `system2('updmap-user')` を実行できます).

## 私は Debian/Ubuntu ユーザです. TeX Live が依存している他のパッケージをインストールすることを止めるにはどうしたらいいですか? TinyTeX と公式の TeX Live のパッケージを両方ともインストールしたくない (あるいは, 必要がない) です.

はい, `apt-get install` は依存パッケージをインストールする時, なんらかの `texlive-*` をインストールするかもしれません (例えば, `apt-get install dvipng` は `texlive-base` もインストールするかもしれません). あなたが `apt-get` に「だまされて」 [texlive-local.deb](https://travis-bin.yihui.org/texlive-local.deb) をダウンロードしインストールしてしまう可能性があります.

```sh
wget "https://travis-bin.yihui.org/texlive-local.deb"
sudo dpkg -i texlive-local.deb
rm texlive-local.deb
```

このパッケージは [debian-control-texlive-in.txt](https://github.com/scottkosty/install-tl-ubuntu/blob/master/debian-control-texlive-in.txt) 上で [`equivs-build`](http://manpages.ubuntu.com/manpages/trusty/man1/equivs-build.1.html) を実行することでビルドされます (Scott Kostyshak による). 基本的に全ての `texlive` パッケージが (実際にはインストールされていないのに) インストールされているように擬態するので, あなたはどの LaTeX パッケージを自分で選ぶ完全な自由 (そして責任) を獲得します.

個人的には LaTeX パッケージを全て `texlive-*` パッケージとして, そしてそれぞれに LaTeX パッケージを梱包して Linux (Debian など) で配布することを好みません. これはあなたが必要なパッケージが1つだけであっても, いくつかの他のパッケージをインストールしなければならないことを意味しています. R ユーザとして (あるいは Python なり他の言語なり) 想像してみてください, 1万を超える R パッケージが, CRAN が 20 **組** のパッケージとしてビルドされていて, あなたが必要としているのが **ggplot2** パッケージだけなのに他の関連するデータ可視化パッケージが全て強制的にインストールされてしまうとき, あなたはどうしますか? こんなことをする明らかな利点は,  (もしかすると) 毎回毎度毎度のように不足しているパッケージをインストールする必要がなくなることですが, 無駄を削ぎ落とした TeX Live ディストリビューションと, インストールするパッケージを自分で選ぶほうが私は好きです (私にとって全く難しいことではありません).

**訳注**: equivs-build と同様のことは TeX Wiki のこのページ https://texwiki.texjp.org/?texlive-dummy でも紹介されています.

## インストール先フォルダを変えることはできますか? {#change-directory}

フォルダのパスはインストールスクリプトにハードコードされているので, TinyTeX インストール時にコマンドラインから変更することはできません. Linux macOS, Windows ではデフォルトで隠しフォルダになるため, 私はこの場所を選びました. TeX Live はほとんどの場合でインストール場所に注意することも (要求することも) ありません. あなたが本当にフォルダを変更したいなら, いくつかの方法があります.

* ダウンロードとインストール用のスクリプトを自分で改造することができます (スクリプトはオープンソースです)

* R ユーザなら, R パッケージの **tinytex** をインストールし,  `tinytex::install_tinytex()` の `dir` 引数にカスタムフォルダを与えることができます.

* あるいは TinyTeX を先にインストールしてから, 好きな場所にフォルダごと移動させます (USB スティックに移動することもできます). TinyTeX が TinyTeX が TeX Live の**ポータブル**版だという理由です. この方法のトリッキーなところは, `PATH` 変数に対処することです. `tlmgr path add` を実行する必要がありますが, デフォルトのインストール場所から移動すると `tlmgr` は `PATH` にはありません. そこで, `tlmgr` をフルパスで実行しなければなりません. これが例です.

```sh
# Linux 上で ~/.TinyTeX から /opt/tinytex に移動する想定
/opt/tinytex/bin/*/tlmgr path add

# macOS 上で ~/Library/TinyTeX から /opt/tinytex に移動する想定
/opt/tinytex/bin/*/tlmgr path add

# Windows 上で %APPDATA%\TinyTeX から C:\Software\TinyTeX に移動する想定
"C:\Software\TinyTeX\bin\win32\tlmgr" path add
```

これが必要なのは1度だけです. FAQ \@ref(sys-install) の方法で TinyTeX をインストールしたなら, `tlmgr path add` は `sudo` で実行する必要があります.

## USB ドライブや他のポータブルデバイスに TinyTeX をインストールするにはどうすればいいですか?

すでに述べたように TinyTeX は TeX Live のポータブル版です. よって簡単にポータブルデバイスにコピーすることができます. あなたがする必要があるのは他のコンピュータにデバイスを挿してから `tlmgr path add` を実行することだけです. ここでもポータブルデバイスの `tlmgr` のフルパスが必要です (FAQ \@ref(change-directory)を見てください). このコマンドを実行してアプリケーションを再起動すれば, フルパスなしで `tlmgr` を実行できるはずです.

あなたが R ユーザなら上記のステップは以下の2つの関数でできます.

    

```{.r .numberLines .lineAnchors}
# インストール済みの TinyTeX をどこにコピーするか
tinytex::copy_tinytex()
# TinyTeX がデバイスのどこにあるかを指定し, tlmgr path add を実行
tinytex::use_tinytex()
```

あるプラットフォームでインストールした TinyYeX は, 同じプラットフォーム上でのみ動作することに注意してください. 例えば Windows 版は Windows でしか動作しません. 例えば TinyTeX の macOS 版は Windows マシンにコピーすることも使うこともできません.

これは情報システム担当者やシステム管理者を介さなくていいすばらしいやり方です. LaTeX パッケージをインストールしたりアップグレードしたりするよう彼らに要求する必要がありません. 全て自分でやることができます.

**訳注**: 昨今では USB ドライブを勝手に挿入することもセキュリティ面で問題視されそうな気がするので注意してください.

## デフォルトのインストールパスに TeX Live の年度が含まれていないのはなぜですか?

TeX Live はデフォルトでは `/usr/local/texlive/2017` のように年度で名付けたフォルダにインストールされます (MacTeX でもそうです). 普通のユーザにとってはこれにあまり意味がないと私は思っています. TeX Live の開発者以外に, 同一のコンピュータに TeX Live の複数のバージョンをインストールしたい人がいるでしょうか? TeX Live の完全版は巨大であり, 毎年のようにディスク容量を食いつぶすようなことは, あなたもきっと望んでないでしょう.

繰り返しますが, TinyTeX のインストール場所を変更したいなら, あなたにはそれが可能です.

ある TinyTeX ユーザが, 自分はこのことに気づかずに毎年 TeX Live (MacTeX) をインストールしていた, と私に教えてくれました. TinyTeX を試しに使ってみなかったら, これらの  `/usr/local/texlive/2015`, `.../2016`, and `.../2017`, といった複数の TinyTeX フォルダが合計で 15GB にもなっていたことに気づかなかったことでしょう. このことは私の推測の裏付けになります. ユーザはしょっちゅう, 過去のバージョンのアンインストールや上書きをせずに TeX Live の新バージョンをインストールしていることを知らず, このモンスターは毎年ごとに大きく成長しています. そう, 今日ではディスク容量は安上がりですが, それは無駄遣いしてもよいということを意味しません.

## 数年前にインストールしたTinyTeXをアップグレードするにはどうすればいいですか? "tlmgr: Remote repository is newer than local (2017 < 2018)" のようなメッセージが出てきました.

インストールスクリプトが常に最新版をインストールしているように, TinyTeX を再度インストールすることができます. しかし現在インストールされている LaTeX パッケージは失われます. これらのパッケージも再インストールしたいなら,  この R 関数を使うとよいでしょう.


```{.r .numberLines .lineAnchors}
tinytex::reinstall_tinytex()
```

R を使用していないなら, パッケージのリストを取得し, あとでインストールできます.

```sh
TL_INSTALLED_PKGS=$(tlmgr info --list --only-installed --data name | tr '\n' ' ')
# この後ホームページに書いた方法で TinyTeX を再インストールします.
# TinyTeX を再インストールしたら, 以下でパッケージも再インストールします
tlmgr install $TL_INSTALLED_PKGS
```

## R パッケージの tikzDevice の使用にはどの LaTeX パッケージが必要ですか? {#faq-tikz}

グラフィックデバイスの `tikzDevice::tikz()` を使用するにはこれらの LaTeX パッケージが必要です.

```sh
tlmgr install pgf preview xcolor
```

## `tlmgr` を実行するたびに "not verified: gpg unavailable" という警告が現れます. この警告はどうやったら表示されなくなりますか?

この文が示すように, あなたのシステムに `gpg` (GnuPG) がありません.

```
tlmgr: package repository http://example.org/.../tlnet (not verified: gpg unavailable)
```

Windows と macOS で TeX Live を使用しているなら, `gpg` をインストールする最も簡単な方法は http://www.preining.info/tlgpg/ です. "one-time installation" で十分です.

:::{.infobox .memo data-latex="{memo}"}

**訳注**:  2021/6/24 時点に確認した限りでは, "one-time installation" は以下を実行するだけで十分らしいです. `sudo` をつけるべきかどうかは, これ以前の項目を参考にしてください (翻訳者は未確認です)

```sh
tlmgr --repository http://www.texlive.info/tlgpg/ install tlgpg
```

また, Windows/macOS ユーザでないならばこれは**不要**です. 通常の方法で GnuPG をインストールするだけで十分です.

:::


## HTTP/HTTPS/FTP プロキシで TinyTeX/LaTeX パッケージをインストールするにはどうすればいいですか?

`~/.wgetrc` ファイル (`~` はあなたのユーザーフォルダ (ホームディレクトリ) を表しています) を作成し, 変数 `http_proxy`, `https_proxy`, あるいは `ftp_proxy` を設定するとよいでしょう. 例えばこのように.
    
```config
http_proxy=http://user:password@proxy.example.com:8001/
```
    
詳細は [tug.org のこのページ](https://www.tug.org/texlive/acquire-netinstall.html)と [`wget` のドキュメント](https://www.gnu.org/software/wget/manual/html_node/Proxies.html)を見てください.

## macOS で Homebrew を使って TinyTeX をインストールすることはできますか?

できます. ただし明確な利点はありませんから, 私としてはこの方法はお勧めしません.

```sh
brew install -v yihui/tinytex/tinytex
```

Formula は GitHub レポジトリ [yihui/homebrew-tinytex](https://github.com/yihui/homebrew-tinytex) にあります. この方法を使わなければならないなら, 以下2点の注意事項に留意すべきです.

* Homebrew は TeX Live のシンボリックリンクを管理し (`brew link tinytex` で), `/usr/local/bin` 以下にリンクを作成します. このコマンドで `tlmgr path add` と同じ場所にシンボリックリンクが作られるので, インストール後に `tlmgr path add` を実行しないほうがよいです. (`tlmgr install` で) **metafont** のようなバイナリを含む TeX Live パッケージ (`mf` を含んでいます) をインストールしたときには, 以下を実行すべきです.

    ```sh
    brew postinstall tinytex && brew unlink tinytex && brew link tinytex
    ```

* Homebrew で **tinytex** を更新した後は (`brew update && brew upgrade` によって), すでに自分でインストールした LaTeX パッケージはもはや使用できず, 再インストールする必要があります. 解決法の1つとして `brew pin tinytex` でこの formula を将来にわたって更新させないようにします (実際, アップグレードの必要はありません). 別の解決法は, formula の HEAD 版を使用することです.
    
    ```sh
    brew cleanup -s
    brew remove tinytex
    brew install -v --HEAD tinytex
    ```

    これらの問題がとても複雑だと感じたら (実際複雑です), ホームページ (\@ref(tinytex)節) に書いてあるような macOS に TinyTeX をインストールする簡単な手順にしたがってください.

## ここで説明されているコマンドをコマンドウィンドウ (ターミナル) を開いて実行するにはどうすればいいですか?

Linux ユーザに対してはこの質問の回答をしません.^[**訳注**: ディストリビューションによって異なりますし, デスクトップ環境ならばおそらく自明でしょう.] macOS ユーザに対しては `Command + Space` で Spotlight 検索を立ち上げ, `Terminal` とタイプしてください. 結果の最初が `Terminal.app` となっているはずです. それだけです. Windows ユーザの場合, タスクバー上の Windows のアイコンを右クリックして `Widnwos PowerShell` を選んでください

あなたが[RStudio](https://www.rstudio.com) ユーザならば, より簡単です. v1.1 以降の RStudio には[ターミナルのビルトインサポート](https://blog.rstudio.com/2017/08/11/rstudio-v1-1-preview-terminal/) があるので, RStudio 内でターミナルを開くことができます.

<!--chapter:end:faq.Rmd-->

# Half of Pain {#pain}

「なぜ TinyTeX か ~LaTeX ディストリビューションの何が苦痛か?~」

オリジナルのページ: https://yihui.org/tinytex/pain/

オリジナルの更新日:  2017/12/15

----

私がしているのが車輪の再発明にすぎないと考えている開発者もいるかもしれません. 私は理由もなく車輪の再発明などしたくありません. ホームページで述べたように, 既存の LaTeX ディストリビューションを使うことによる苦痛は, ファイルとドキュメントが巨大であり, 包括的で有用な一方で, 私にとって最も有用が部分がどこなのか (つまり, 足りないパッケージをインストールする方法) が強調されないことです.

これを嫌うまた別の理由として, LaTeX パッケージの管理のために (UNIX系OSで) `sudo` がしょっちゅう必要であることもあります. TeX Live はコンピュータのどこに配置することもできる自己完結的なフォルダ構成であることを考えると, `sudo` が必要な理由が見当たりません. 数年間も待って `tlmgr` がとうとう Debian/Ubuntu で利用可能になると私は[とても喜びました](https://twitter.com/xieyihui/status/397238590523973632)が, それが使い物にならないとわかると私はすぐに失望しました (`sudo` なしでは何もできません). 今年再確認すると, まだ使い物にならないままでした. きっと私はこれを正しく使えません (なにをやろうとしてもエラーになります) が, `tlmgr` のユーザーモードを使うときだけ許可されますが, その機能は私にとって制約が多すぎました.

私が普段使っているのは macOS で, 公式に TeX Live ディストリビューションとして MacTeX が推奨されています. これには私にとって不要ないくつかの追加パッケージ, たとえば TeX Live Utility (私は `tlmgr` コマンドの使い方を知っています), TeXShop (R Markdown を使っていますし, 可能な限り生の LaTeX を編集することも, 読むことすらしたくないです), LaTeXiT などが含まれています.

実際 Window 上での MiKTeX (現在はこれはクロスプラットフォームになったようです) のよい機能の1つを私は評価しています. 不足している LaTeX パッケージの自動インストールのことです. これはとても便利だと思うので, 私はこの機能を R パッケージの **tinytex** でも借用し, R ユーザは TeX Live または TinyTeX 使用時にこの機能を活用することができます. つまり, 基本的な MiKTeX ですらまだ大きすぎ, そして私を腹立たせたのは MiKTeX の `bbitex.exe` です. これはいつも `.aux` ファイル内の文献データベースファイルに `.bib` 拡張子を付け足します. 例えば `bar.tex` 内に `\bibliography{foo.bib}` があったとして, `bibtex.exe` は `bar.aux` 内に `\bibdata{foo.bib.bib}` を生成してしまいます. なんであれ, Rパッケージの **tinytex** においては MiKTeX を使うウィンドウズユーザに対しては[パッチを当てました](https://github.com/yihui/tinytex/blob/4275a375c6/R/latex.R#L189-L196). TeX Live ユーザはこの問題に苦しむことはありません.

このページでは LaTeX のインストールと管理の話を共有したいと思います.  まずは私の発見した辛い場面を紹介しようと思います.

:::{.infobox .important data-latex="{important}"}

**訳注**: 以降は既存の LaTeX ディストリビューションの不便さを嘆く声を集めた内容となっており, TinyTeX の使用法とは直接関係ないため翻訳を省略します.

:::

- [Why LaTeX is such a bloated system?](https://ubuntuforums.org/showthread.php?t=395863)

- [Is there a lightweight implementation/distribution of TeX for Mac OSX?](https://tex.stackexchange.com/q/43862/9128)

- [Installing (a lightweight version of) latex on an external hard drive](https://tex.stackexchange.com/q/81802/9128)

- texlive-full is [too big](https://github.com/rstudio/rticles/pull/130#issuecomment-313732003), [too big](https://github.com/rocker-org/rocker/issues/266), and [just too big](https://github.com/road2stat/liftr/issues/25) for Docker images

- [Which LaTeX to install on Linux?](https://tex.stackexchange.com/q/18939/9128)

- Missing LaTeX packages confuse users [forever](https://github.com/rstudio/rmarkdown/issues/359), [forever](https://github.com/rstudio/rmarkdown/issues/1076), [forever](https://github.com/rstudio/rmarkdown/issues/1393), and it takes [forever](https://twitter.com/xieyihui/status/763805846807547904) to figure them out and install. [Error messages](https://stackoverflow.com/q/47400936/559676) can also be confusing. Sometimes we just [don't have a clue](https://github.com/rstudio/bookdown/issues/507).

- When all you need is a LaTeX package, **upquote**, of which the size is 5K, [you have to install 500MB of additional software on Ubuntu/Debian](https://github.com/jgm/pandoc/issues/2439#issuecomment-326518438).

- You may suffer from [old bugs](https://github.com/rstudio/rmarkdown/issues/1505) that have been fixed in certain LaTeX packages.

- MiKTeX [might fail](https://github.com/rstudio/bookdown/issues/531) and we don't know what the error message "GUI framework cannot be initialized" means. Sometimes we have [no clue](https://stackoverflow.com/questions/48892499/compile-rmarkdown-document-with-render#comment84801684_48892499) why it doesn't work.

Below are stories and experiences contributed by other users:

> Removed TeX Live from my system (openSUSE): 1.5gb. Installed TinyTeX + the dependencies to compile my thesis: 150mb!!!! This is great!

> --- [Bruno Rodrigues](https://twitter.com/brodriguesco/status/942162790587957248)

<!-- -->

> Really liking the simplicity of tinytex package. Easy to get up and running to knit PDFs. No need for slow LaTeX install.

> --- [Daley Mikalson](https://twitter.com/lingwhatics/status/941766989424537602)

<!-- -->

> A tiny LaTeX distribution easy to install from RStudio or on Travis CI is just what we needed!

> --- [Philippe Grosjean](https://twitter.com/PhilGrosjean/status/941241878309232640)

<!-- -->

> Seriously one of my only holdups teaching LaTeX in Rmarkdown (still taught it anyway) is now solved.

> --- [ Tyson Barrett](https://twitter.com/healthandstats/status/941169151749406720)

<!-- -->

> Tried TinyTeX with rmarkdown and both English and Chinese rendering. The most smooth experience ever using LaTeX!

> --- [Kun Ren](https://twitter.com/renkun_ken/status/941352666730455041)

<!-- -->

> TinyTeX is awesome, if it had existed before I would have saved hours of my life spent dealing with LaTeX packages and failed R Markdown knits...

> --- [Antonio Vazquez Brust](https://github.com/rstudio/bookdown/issues/292#issuecomment-356480809)

<!-- -->

> Many people don't realize that Texlive on some Linux systems (say you need a rstudio server) doesn't come with the TeX package manager. If the package you need is not in their system, you are basically screwed as you can't even install it. TinyTeX solves this problem and makes everything sweet and easy. Also, after using it for more than a month, I found the  messages of tinytex are very helpful, comparing with basically NULL in texlive. 

> --- [Hao Zhu](https://community.rstudio.com/t/texlive-distribution-on-centos-for-rstudio-server-and-connect/2916)

<!-- -->

> I'm a novice with R and I found the entire process of installing rmarkdown, knitr, MiKTeX, and pandoc and then reconciling all their directories very difficult. [...] The 3-4 hour detour into MiKTeX etc and how to produce PDFs was a frustration experience to say the least. (Up and running with TinyTeX five minutes later...)

> --- Justin Shapiro

<!-- -->

> If you work in a locked down windows corporate enviroment and spent countless hours with MiKTeX or portable MiKTeX, you'll want to buy \@xieyihui lunch.

> --- [Thomas Speidel](https://twitter.com/ThomasSpeidel/status/957856056108920835)

<!-- -->

> Too bad, this computer had a good month of TeX-free.

> --- [Romain François](https://twitter.com/romain_francois/status/979022989634174976)

<!-- -->

> I'd been avoiding installing LaTeX on my macOS and Windows machines b/c it's always such a hassle. Just learned about about TinyTeX today and easily got setup on both machines.

> --- [John Blischak](https://twitter.com/jdblischak/status/974066342780461056)

<!-- -->

> Like 2 hours later, I'm not sure I want to know, but there *must* be a faster way of updating all my TeX packages than the ftp in TeX Live Utility...

> --- [Mara Averick](https://twitter.com/dataandme/status/994531189217259521)

<!-- -->

> Installing a complete version of MiKTeX is a freaking nightmare though. Don't know if it's my internet connection or what but I just can't get it all installed in one go. I finally just did the basic install and am selecting batches of pkgs at a time to add.

> [...] I just installed this (TinyTeX) and it was SO EASY and I'm kind of upset that I didn't know about it before. Gonna be shouting this one [from the rooftops](https://twitter.com/swmpkim/status/1014546819085369344).

> --- [Kim Cressman](https://twitter.com/swmpkim/status/1014514680088399874)

<!-- -->

> Siri, how much of my life have I spent [installing TeX distributions](https://twitter.com/djnavarro/status/1112674242523361281)?

> I have discovered that the tinytex package has handy tools for analysing the .log files, which solved my problems for me. That is, after I wasted half an hour trying to install MiKTeX for no good reason

> --- [Danielle Navarro](https://twitter.com/djnavarro/status/1112687238196822016)

<!-- -->

> I used tinytex to upgrade TeX Live and install a package and it ... worked. \@xieyihui may have ended some of my LaTeX torment.

> --- [PirateGrunt](https://twitter.com/FanninQED/status/1146857305285242880)

<!-- -->

> literally took me longer to remove all traces of MacTeX than to install TinyTeX and all the packages needed to build my thesis, and i saved about 10gb of disk space.

> --- [Tim Hosgood](https://twitter.com/tjohnhos/status/1175575859748638720)

<!-- -->

> I've always had a problem knitting to pdf with windows.  I followed instruction for tinytex and it works great now!

> --- [Dilsher Dhillon](https://twitter.com/TexanDhillon/status/1183831526485712897)

<!-- -->

> Learning to write a LaTeX manuscript  in R Markdown and was having problems loading new packages: tlmgr wasn't updating the packages. So, found the "tinytex" package that fixed everything.

> --- [Akash](https://twitter.com/dynamic_choice/status/1203389695419224064)

<!-- -->

> Just had to reinstall texlive on my Mac and once again, \@xieyihui's tinytex package saves the day with tinytex::reinstall_tinytex().

> --- [Zhian N Kamvar](https://twitter.com/ZKamvar/status/1202525888031657984)

<!-- -->

> Having just for fun installed R on my new Raspberry pi 4, I thought to myself now it would be nice to have latex, but really, all of texlive, probably not, so this was exactly what was needed. It installed for me immediately and "just worked."

> --- [Roger Koenker](/tinytex/#comment-4720783918)

<!-- -->

> I've always had problems knitting to pdf on Windows until I came across tinytex package.

> --- [Dilsher Dhillon](https://twitter.com/dhillon_stats/status/1214204113359179779)

<!-- -->

> [...] uninstall MacTeX and install TinyTeX was the ticket! It FINALLY knit to pdf!!!!

> --- [Tiffany Timbers](https://twitter.com/TiffanyTimbers/status/1214344978769403905)

<!-- -->

> [...] Why install 3GB of languages you don't know when all you want are beautiful tables?

> --- [Roman Link](https://twitter.com/RomanMLink/status/1227496566157524992)

<!-- -->

> [...] Have you tried {tinytex}? It installs only what you need from LaTeX so you can knit to pdf! Changed my life!

> --- [Kim Cressman](https://twitter.com/swmpkim/status/1234517924028784640)

<!-- -->

> [...] R Markdown + library(tinytex) basically unlock all of the power of LaTeX without any of the headaches.

> --- [Aaron R. Williams](https://twitter.com/awunderground/status/1234665396654837761)

<!-- -->

> A nice, ultra light, LaTeX distribution, I used TinyTeX on my chromebook for a while.

> --- [\@onebellboy](https://twitter.com/onebellboy/status/1374345267256905729)

<!-- -->

> But also LaTeX took 6 hours to download and now I'm struggling to set the path variable for it so maybe I'll just give up for today and try tomorrow.

> --- [jade eden](https://twitter.com/JadeMasterMath/status/1384672233499643905)

<!--chapter:end:pain.Rmd-->

# TinyTeX 中文文档 (未翻訳)

オリジナルのページ: https://yihui.org/tinytex/cn/

オリジナルの更新日: 2017/12/03

----

:::{.infobox .memo data-latex="{memo}"}

このページは (機械翻訳で確認して) 英語の解説ページとほぼ同じ内容だと判断したので翻訳していません.

:::


<!--chapter:end:cn.Rmd-->


============
Golang環境構築メモ
============

Golang環境を構築し理解する

:作成日: 2019/09/07


はじめに
====

1. 事前準備
-------

MacOS Mojave環境で実施。事前にHomebrewはインスコしておこう。


.. blockdiag::

   blockdiag
   {
      開発環境構築 -> Sphinxインスコ -> Eclipseインスコ -> Eclipse設定 -> Document作成;
   }


2. Go言語とは
---------
- C言語の実行速度に近づけるという目標でデザインされた。
- Go言語はバックエンドシステムのプログラムを単純かつ効率的に書くために開発された。
- 新しい言語だが、リリースされて以降、XaaSのプログラムを書くための言語として人気に。
- 静的付け言語だが動的な型付けが可能
- 関数プログラミングのものとか考えられている機能が多い

3. なぜGo言語で開発するのか
----------------
- ネイティブコードにコンパイルされるのでインタプリタと比べた処理が早い。
- 並列実行を標準でサポートしていて、高速に動作するサーバーサイドのプログラムが書ける
- OSスレッドに対して多数のゴールーチンを生成する※垂直スケーラビリティに優れている。
- Go Web アプリは動作依存性のない静的なバイナリとしてコンパイルされるので、Goが組み込まれていないシステムにも分散が可能なため※水平スケーラビリティにも優れている。
- モジュール性が高いため、小規模Goサービスを複数組み合わせて大規模アプリを作成することも可能
- gofmtによるフォーマットやgodocによる資料生成、gotestによるテストなど、コードベースの保守が容易。

4. Webアプリケーションとは何か？
-------------------
- クライアントのHTTPリクエストに応答し、HTTPレスポンスとしてクライアントにHTMLを送り返すプログラム。
- Webサーバーが行なっていること
- Webアプリケーション＝Webサーバー
- HTMLをただ表示する静的サイトやAdobe FlashはWebアプリではない。

.. blockdiag::

   blockdiag
   {
      クライアントがリクエストを送信 -> WebサーバWebアプリ;
   }

.. blockdiag::

   blockdiag
   {
      WebサーバWebアプリがレスポンスを送信 -> クライアント;
   }

Webアプリケーションがやっていること
^^^^^^^^^^^^^^^^^^^
1. HTTPを介し、HTTPリクエストメッセージの形でクライアントから入力を受け取る
2. HTTPリクエストメッセージを処理し、必要な処理を行う
3. HTMLを生成し、HTTPレスポンスメッセージを入れて返す。

Webアプリケーションまとめ
^^^^^^^^^^^^^^
- ハンドラとテンプレートエンジンから成り立つ。
- ハンドラはクライアントから送られてきたHTTPリクエストを受け付けて処理する。
- HTMLを生成するためにテンプレートエンジンを呼び出し、HTTPレスポンスに組み込んでクライアントへ返送する。

5. HTTP入門
---------
- HTTPはWorld Wide Web (WWW)を支える力。
- 1990年に定義されてから3回しか更新されておらず、現在のバージョンはHTTP/2
- HTTPの要求と応答の１回のやりとりで完結し、他のやりとりと紐づかない
- テキストベースのリクエスト/レスポンス型プロトコル。(2台のパソコンがリクエスト/レスポンスで会話するイメージ。)
- クライアント/サーバ型のコンピュータモデルで使用される

HTTPリクエスト行構成
^^^^^^^^^^^^
1. リクエスト行 - リクエストメソッド＋URI＋HTTPバージョン
2. リクエストヘッダ (0行以上)
3. 空行
4. メッセージ本体

リクエストメソッド
^^^^^^^^^
- HTTP0.9にはメソッドはGET一種類しかなかった
- 1.0でPOSTとHEADが追加
- 1.1でPUT,DELETE,OPTIONS,TRACEが追加
- 最新ではGETとHEAD以外は省略可能
- HTMLの範囲で指定できるリクエストはGETとPOSTのみ

6. Webアプリケーションの歴史
-----------------
- 初期段階・・・CGI (Common Gateway Interface)で送られてくるコンテンツが動的に生成される試み。
- 次の試み・・・SSI (server-side includes)はHTMLに命令を含むことでコンテンツを生成。
- SSIの進化・・・HTMLにさらに複雑なPHP,ASP,JSPといったコードを埋め込めるようにになる。

1. Golangのインストール
================

まずはgoをインスコ

.. code-block:: bash

   brew install go

GOのRoot Pathを$HOME/devに変更し、そのPATHを通す。デフォルトのgo rootは"go env GOROOT"で確認可能。

.. code-block:: bash

   export GOPATH=$HOME/dev
   export PATH=$PATH:$GOPATH/bin

go getでgoreをインストールする。オプション-uで最新をダウンロードする。
また、オプション-uですでにローカルにインストール済みであっても再度インストールが可能。

.. code-block:: bash

   go get -u github.com/motemen/gore/cmd/gore

上記実施後、$GOPATHの中のbin配下を確認するとインストールされたgoreの実行形式ファイルが確認できるはず。
これにてGOのRoot Pathが変更されてインストール先が更新さえていることがわかる。
(デフォルトgo rootは"go env GOROOT"で取得されるPath)

また、goreをインストールするとgithub上の階層と同様にsrc配下に作成される。

.. code-block:: bash

   brew install tree
   tree -L 3 $GOPATH/src
   /Users/minada/dev/src
   ├── github.com
   │   ├── k0kubun
   │   │   └── pp
   │   ├── mattn
   │   │   ├── go-colorable
   │   │   ├── go-isatty
   │   │   └── go-runewidth
   │   ├── mitchellh
   │   │   └── go-homedir
   │   ├── motemen
   │   │   ├── go-quickfix
   │   │   └── gore
   │   ├── nsf
   │   │   └── gocode
   │   └── peterh
   │       └── liner
   └── golang.org
       └── x
           ├── net
           ├── text
           └── tools

2. Hello Worldを表示する
===================

以下の通り、goreを使用してHello Worldを表示する。

.. code-block:: bash

   minada$ gore -autoimport
   gore version 0.4.1  :help for help
   gore> fmt.Println("Hello World")
   Hello World
   12
   nil

コードの補完や出力のハイライト、APIドキュメントの参照を可能にする。

.. code-block:: bash

   go get -u github.com/nsf/gocode
   go get -u github.com/k0kubun/pp
   go get -u golang.org/x/tools/cmd/godoc

3. ghqを使ってリポジトリの取得と管理
=====================

以下のようにghqをインストールし、ソースを持ってくるディレクトリを指定する。

.. code-block:: bash

   brew install ghq
   git config --global ghq.root $GOPATH/src

これにて設定完了。ghq listでディレクトリ常に存在するリポジトリ一覧を取得できる。

.. code-block:: bash

   $ ghq list
   github.com/k0kubun/pp
   github.com/mattn/go-colorable
   github.com/mattn/go-isatty
   github.com/mattn/go-runewidth
   github.com/mitchellh/go-homedir
   github.com/motemen/go-quickfix
   github.com/motemen/gore
   github.com/nsf/gocode
   github.com/peterh/liner
   golang.org/x/net
   golang.org/x/text
   golang.org/x/tools

これでghq get https://github.com/... という風に手元にソースコードを取得し、
リボジトリ管理を簡単にできるようになった。

4. zshを導入し、pecoでリポジトリ間の移動
=========================

まず、リポジトリ移動に便利なpecoの導入です。

.. code-block:: bash

   brew install peco

以下の通り、zshを導入する。インストールした後、使用できるシェルに"/usr/local/bin/zsh"を追加する(/etc/shellsの最終行に追加)。
最後にログインシェルを変更する。

.. code-block:: bash

   brew install zsh
   sudo vi /etc/shells
   chsh -s /usr/local/bin/zsh

上記を終えたら起動しているターミナルアプリケーションを閉じてMacデフォルトのターミナルを起動する。

.. code-block:: bash

   --- Type one of the keys in parentheses --- 

と表示されているかと思うので、returnキーを押下する。そして以下のようにzshが動作していることを確認する。

.. code-block:: bash

   % echo $SHELL
   /usr/local/bin/zsh

次に.zshrcを編集し、カスタマイズを行う。

.. code-block:: bash

   vi ~/.zshrc
   source ~/.zshrc

以下の設定を追加。

.. code-block:: bash

   # Configuration for peco and golang
   bindkey '^]' peco-src
   
   function peco-src()
   {       
           local src=$(ghq list --full-path | peco --query "$LBUFFER")
           if [ -n "src" ]; then
                   BUFFER="cd $src"
                   zle accept-line
           fi
           zle -R -c
   }
   zle -N peco-src
   
上記設定変更と実施の後、ターミナル上で"control+]"を押下することでpecoのUIを表示できる。
試しに"motemen"と入力し、Enterしてみると、選択されたプロジェクトリポジトリに移動することができる。
これでgo getまたはghq getで取得したリポジトリに自由に移動できるようになった。

5. エディタと開発環境
============

エディター
-----

みんなのGO(書籍)曰く、GOはコマンドラインツール作成にも向いているのでターミナル上での開発を勧めているらしい。
なのでvim-goというプライグインを使ったvimをエディタとして用いることにする。手順は基本的に`vim-go チュートリアル <https://github.com/hnakamur/vim-go-tutorial-ja>`_
に従うことにする。vim-goを使うにはNeoBundleを設定する必要があるらしい。

.. code-block:: bash

   brew install vim
   mkdir -p ~/.vim/bundle
   git clone git://github.com/Shougo/neobundle.vim ~/.vim/bundle/neobundle.vim
   curl https://raw.githubusercontent.com/Shougo/neobundle.vim/master/bin/install.sh > install.sh
   sh ./install.sh
   rm -rf install.sh
   go get github.com/fatih/vim-go-tutorial
   vi ~/.vimrc

~/.vimrcは以下のように記述しておく。

.. code-block:: bash

   call plug#begin()
   Plug 'fatih/vim-go', { 'do': ':GoInstallBinaries' }
   call plug#end()

インストール時には含まれていない有用なコマンドラインツール群があるのでまとめてインスコする。

.. code-block:: bash

   go get golang.org/x/tools/cmd/..
   
コードフォーマッター
----------

コードフォーマッターはインデントや改行位置、変数の整列などを自動調整してくれる。
Goは標準でgofmtというコードフォーマッターが準備されている。main.goをフォーマットする場合は・・。
オプションwで対象のファイルを直接書き換えることが可能。

.. code-block:: bash

   gofmt -w main.go
   
gofmtの上位互換としてgoimportsがある。あとで説明する。

静的解析ツール
-------

Goは標準でlingツールがついてくる。lintツールはコードの静的解析を行い、
バグが発生しそうな部分やスタイルが不揃いな店やGoっぽくない書き方などを指摘してくれる。

- go vet ... バグになりそうな箇所を特定 (標準で同梱)
- golint ... Goらしくないコーディングスタイルを警告

.. code-block:: bash

   go get github.com/golang/lint/golint

ドキュメント閲覧ツール
-----------

- go doc ... 標準のドキュメント閲覧コマンド
- godoc ... より強力なドキュメント閲覧コマンド

以下のようにインストールし、fmtのドキュメント閲覧してみる。

.. code-block:: bash

   go get golang.org/x/tools/cmd/godoc
   godoc fmt

$GOPATH以下のパッケージにすばらくアクセスすることも可能。

.. code-block:: bash

   godoc $(ghq list | peco) | less
   
その他のツール
-------

- gorename ... 変数名や関数名をリネーム
- guru ... ソースコードの静的解析
- gocode ... コード補完用のエンジンを提供
- godef ... 定義ジャンプのためのツール
- gotag ... ctags五感のタグを生成してくれるツール

6. Goをはじめる
==========

チュートリアル
-------

A Tour of Goというチュートリアルは初心者に必須らしい。

.. code-block:: Bash

   go get github.com/atotto/go-tour-jp/gotour
   gotour

- https://tour.golang.org (英語版)
- https://go-tour-jp.appspot.com (日本語版)
- https://golang.org/doc (公式ドキュメント)

A Tour of Goで学ぶGoコードの基本
-----------------------

- プログラムはpackageで構成される
- プログラムはmainパッケージから開始される
- importでパッケージを読み込む
- factoredインポートステートメントでimportをグループ化可能
- 変数名の後ろに型名を書く
- x int, y int は x, y int とも書ける
- 関数の戻り値は複数の値を設定できる

.. code-block:: Go

   func swap(x, y string) (string, string) {
      return y, x
   }

- 戻り値に名前をつけることが可能。

.. code-block:: Go

   func split(sum int) (x, y int) {
      x = sum * 4 / 9
      y = sum - x
      return
   }

- var ... 複数の変数の最後に型を書くことで、変数のリストを宣言
- 初期化子が与えられている場合、型を省略可能 (bool c = true, bool python = false, java = "no!"　と以下は同様)

.. code-block:: Go

   var c, python, java = true, false, "no!"

- ":=" の代入文を使うと暗黙的な型宣言が可能。
- byte // uint8 の別名, rune // int32 の別名
- 変数 v 、型 T があった場合、 T(v) は、変数 v を T 型へ変換

.. code-block:: Go

   var i int = 42
   var f float64 = float64(i)
   var u uint = uint(f)

- さらにシンプルにもできる

.. code-block:: Go

   i := 42
   f := float64(i)
   u := uint(f)

- 型推論

.. code-block:: Go

   func main() {
      v := 42 // change me!
      fmt.Printf("v is of type %T\n", v)
   }

.. code-block:: bash

   v is of type int
   
- 定数( constant )は、 const キーワードを使って変数と同じように宣言

.. code-block:: Go

   const World = "世界"


6. Golangプロジェクトを始める
===================

プロジェクトを作るまたは読み込み前の基礎的注意点
------------------------
- mainパッケージを除き、プロジェクトのディレクトリ名はソースコード内に記述されるパッケージ名と同一であることを推奨。

テキトーなプロジェクトを読み込んでみる
-------------------

`テキトーなCRUDアプリのコード <http://studio-andy.hatenablog.com/entry/go-todo-crud>`_

.. code-block:: bash

   ghq get https://github.com/andoshin11/go-todo-example
   
Control+]によるpecoでインスコしたリポジトリに移動し、tree表示してみる。

.. code-block:: bash

   % tree -L 3 $GOPATH/src/github.com/andoshin11/go-todo-example 
   /Users/minada/dev/src/github.com/andoshin11/go-todo-example
   ├── Gopkg.lock
   ├── Gopkg.toml
   ├── README.md
   ├── db
   │   ├── dbconf_clone.yml
   │   ├── migrations
   │   │   ├── 20180415220424_createTask.sql
   │   │   └── 20180416000712_autoIncrementTask.sql
   │   └── xo
   │       └── templates
   ├── main.go
   └── src
       ├── controller
       │   ├── index.go
       │   └── task.go
       └── model
           ├── model.go
           ├── task.xo.go
           └── xo_db.xo.go

依存管理 (vendoringとglide)
----------------------
- 依存管理とは・・サードパーティのパッケージを使う場合のバージョンの固定などの面倒を見てくれる依存管理の仕組み。
- vendoring...標準の個別依存管理の機能。vendor/配下にパッケージを置いておけばGOROOTやGOPATHよりも優先してそっちを参照する機能。
- glide...vendoringを管理するためのサードパーティ

.. code-block:: bash

   brew install glide
   go get github.com/Masterminds/glide

その他,補足
======
- IDEにIntelliJ IDEAとかもオススメらしい (https://qiita.com/syu_chan_1005/items/46f94412f7493d6e60eb)

用いた専門用語たち
=========
- タスクランナー...Webサイト構築に必要な処理をタスクとして自動化してくれるプログラム。
- 垂直スケーラビリティ・・・CPUを増やして処理能力を向上する手法
- 水平スケーラビリティ・・・マシンの台数を増やして処理能力を向上する手法
- URI (Uniform Resource Indentifier)・・・Internet上でのリソース位置を文字列で表現。一般型 - <スキーム名>:<階層部>[?<クエリ>][#<フラグメント>]

引用した資料たち
========
- みんなのGO言語 - 現場で使える実践テクニック (株式会社技術評論社)
- GOプログラミング実践入門 (Sau Sheong Chang著)
- 初心者向け：Zshの導入 (https://qiita.com/iwaseasahi/items/a2b00b65ebd06785b443)
- 初心者向け vimrcの設定方法 (https://qiita.com/iwaseasahi/items/0b2da68269397906c14c)
- vim-goを使うなら使用したいコマンド集と設定 (https://qiita.com/gorilla0513/items/a027885d03af0d6d5863)
- $GOPATH/bin にバイナリファイルができなかった (https://qiita.com/nagata03/items/cec2587140a376e345a9)
- hnakamur/vim-go-tutorial-ja (https://github.com/hnakamur/vim-go-tutorial-ja)
- 【Golang】vim-goをインストールしてみた (https://o21o21.hatenablog.jp/entry/vim-go)

.. image:: _static/pic/GitHub3.png
   :scale: 100%
   :align: center
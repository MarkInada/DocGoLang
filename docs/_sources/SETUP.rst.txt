====================
Sphinx+Eclipse環境構築メモ
====================

Sphinx+Eclipse環境で快適なドキュメントワークを実現する。

:作成日: 2018/07/17


はじめに
====
Windows 7 64bit環境にて作業を行うが、LinuxでもMacでもそんなに変わらないはず。

:以下、手順概要:

.. blockdiag::

   blockdiag
   {
      Pythonインスコ -> Sphinxインスコ -> Eclipseインスコ -> Eclipse設定 -> Document作成;
   }


1. Pythonのインストール
================
Sphinxをインストールする前にPythonのインストールが必要。すでにインストール済みの場合は作業の必要なし。
公式のPythonインストーラを使うより、Pythonディストリビューションの"Anaconda"を使うことをおすすめする。
Anacondaだとデータサイエンス系に必要なライブラリもまとめてインストールすることが可能。
後になってAnacondaにしておけば！となったときに面倒なため最初からAnacondaにする。
とにかく、AnacondaでPython環境を入れよう。https://www.anaconda.com/download/#windows

★注意！：Anacondaをインストールするとき、PATH(環境変数)を自動的に追加するチェックボックスは忘れなくチェックしておく。その他はデフォルトのままで問題ない。

インストールが完了したら、pythonがちゃんとインスコされているかバージョンを確認してみる。

.. code-block:: bash

   $python -V
   Python 3.6.5 :: Anaconda, Inc.


2. Sphinxのインストール
================
Pythonがインストールできたら、次はSphinxをインストールする。Pythonのpipコマンドでインストール。
環境変数にはAnacondaインスコ時に自動的に追加されているはずなので、コマンドプロンプトに直接入力。

.. code-block:: bash

   $pip install sphinx Pillow

ちゃんとインストール完了できたかバージョンを見てみる。

.. code-block:: bash

   $sphinx-quickstart --version
   sphinx-quickstart 1.7.4

では早速ドキュメントのプロジェクトを作ってみる。quickstartの詳細説明(http://sphinx-users.jp/gettingstarted/sphinxquickstart.html)

.. code-block:: bash

   $sphinx-quickstart %USERPROFILE%\SphinxProjects\SampleDoc

.. image:: _static/pic/quickstart1.png
   :scale: 100%
   :align: center

.. image:: _static/pic/quickstart2.png
   :scale: 100%
   :align: center

上記実行すると色々と質問されるが、Project NameとAuthor Name以外はEnterキーでスキップしてOK。これでユーザープロファイル下にSphinxProjectsディレクトリをつくり、その中にプロジェクト"SampleDoc"の作成が完了。

.. image:: _static/pic/Projects.png
   :scale: 100%
   :align: center

3. Eclipseのインストール
=================
この時点で、テキストエディタとコマンドプロンプトでSphinxのドキュメント作成を行うことは可能。
しかし、もう少し我慢して一手間かけて統合開発環境 Eclipseで快適な環境にしよう。
Eclipseのインストールして初期設定までは行おう⇒手順リンク(https://github.com/MarkInada/DocEclipseHowTo)


4. Eclipseの設定
=============
まずは必要なプラグインが2つあるのでインストールする。

4-1. ReST Editor
----------------
Help -> Eclipse Marketplaceから"ReST Editor"を検索し、インストールする。

.. image:: _static/pic/ReSTEditor.png
   :scale: 100%
   :align: center

4-2. PyDev
----------
Help -> Eclipse Marketplaceから"PyDev"を検索し、インストールする。

.. image:: _static/pic/PyDev.png
   :scale: 100%
   :align: center

4-3. Pythonライブラリロード
-------------------
インストールが終わったらPythonのライブラリをEclipseにロードさせる。Window -> Preferences -> PyDev -> Interpreters -> Python Interpreter。

.. image:: _static/pic/PythonLoad.png
   :scale: 100%
   :align: center

5. Buildしてhtml生成
================

5-1. Sphinxプロジェクトの読み込み
----------------------
File -> New -> Other から、以下のようにプロジェクトを読み込む

.. image:: _static/pic/New1.png
   :scale: 100%
   :align: center

.. image:: _static/pic/New2.png
   :scale: 100%
   :align: center

.. image:: _static/pic/New3.png
   :scale: 100%
   :align: center

最後にconf.pyとindex.rstをsourceディレクトリ内に移動し、上書きする。
※注意！！　すでにsourceディレクトリにconf.pyとindex.rstがある状態で読み込みをこれらが初期化されてしまいます！

.. image:: _static/pic/ChangeSouce.png
   :scale: 100%
   :align: center
   
5-2. ビルド設定
----------
Run -> Run Configurationsを開き、"Sphinx (via make file)"から"new configuration"を選択し、下記のように入力する。

.. image:: _static/pic/RunConf.png
   :scale: 100%
   :align: center

環境変数をEclipseにロードさせてビルド設定は終了。コマンドプロンプトで"PATH"をテキストに書き出し、その内容をEnviornmentに追加する。

.. code-block:: bash

   $path > %userprofile%\path.txt

.. image:: _static/pic/RunConfPath.png
   :scale: 100%
   :align: center


5-3. ビルドしてみる
------------

これで作業完了。実際にビルドしてみる。

.. image:: _static/pic/Run.png
   :scale: 100%
   :align: center

Consoleでビルドができたことを確認。

.. image:: _static/pic/BuildFinished.png
   :scale: 100%
   :align: center

buildディレクトリ内のhtmlディレクトリ内に生成されたhtmlファイルが格納される。

.. image:: _static/pic/Complete.png
   :scale: 100%
   :align: center


6. おすすめの設定
==========
6-1. blockdiag
--------------
シーケンス図やフローチャートがかけるようになる。

.. code-block:: bash

   $pip install sphinxcontrib.blockdiag blockdiag

以下のようにconf.pyを編集して拡張子の追加とフォントの指定を行う。

.. image:: _static/pic/Blockdiag.png
   :scale: 100%
   :align: center


6-2. sphinx_rtd_theme
---------------------
お勧めのテーマが"sphinx_rtd_theme"。見やすいので使ってみる。

.. code-block:: bash

   $pip install sphinx_rtd_theme

以下のようにconf.pyを編集してテーマの変更を行う。

.. image:: _static/pic/Theme.png
   :scale: 100%
   :align: center


6-3. PC画面用に横幅をカスタマイズ
--------------------
スマホ等でも見れるようにデフォルトでは横幅が固定されているが、PCでしか見ない資料ならば横幅固定を外したい場合はcss上書きする。
以下の内容のcssファイルをプロジェクトのディレクトリ内（\build\html\_static\css\my_theme.css）に作成する。

.. code-block:: css

   @import url("theme.css");
    
   .wy-nav-content {
       max-width: none;
   }

そして、以下のようにcond.pyを編集する。

.. image:: _static/pic/Confpy.png
   :scale: 100%
   :align: center


7. ドキュメントを書いてみる
===============
まずは、Textファイルをsourceディレクトリ内に作成する。

.. image:: _static/pic/NewFile.png
   :scale: 100%
   :align: center

.. image:: _static/pic/NewFile2.png
   :scale: 100%
   :align: center

作成したら以下のようにindexのTreeにファイルを追加する。

.. image:: _static/pic/Hello2.png
   :scale: 100%
   :align: center

試しに以下のようなTextを書いてみる。

.. image:: _static/pic/Hello1.png
   :scale: 100%
   :align: center

Run(ビルド)してhtmlファイルを開くと・・・・。

.. image:: _static/pic/Done.png
   :scale: 80%
   :align: center

これで作業終了。あとは コード書く⇒ビルド⇒htmlの確認 を繰り返してドキュメントを書いていく。

htmlのソースが表示されてしまう場合は、html上で右クリックし、Open withでWeb Browserから開こう。

もしReSTエディターで一部文字化けが発生する場合、Window->Prederences->General->Apearance->Colors and FontsでFontを変更する。

.. image:: _static/pic/MSGosic.png
   :scale: 100%
   :align: center


8. GitHubで作成したドキュメントを公開
=======================
GitHubの使い方はまたいつか詳しく説明しようと思う。
とりあえず今回は公開の仕方のみ説明。

実際に私が公開しているGitHubのページを使って説明する。リンク（https://github.com/MarkInada/DocSphinxHowTo）

まずbuild/htmlディレクトリをコピーし、ディレクトリ名を"docs"にする。docs内に".nojekyll"という中身のないファイルを作る。

完了したらこのdocsディレクトリをリポジトリのプロジェクトルートにプッシュする。次にGitHubプロジェクトのSettingに行く。

.. image:: _static/pic/GitHub1.png
   :scale: 100%
   :align: center

Sourceで master branch /docs folder を選択し、Saveすると docs/index.html が公開される。

.. image:: _static/pic/GitHub2.png
   :scale: 100%
   :align: center

公開先のアドレスは、https://[ユーザー名].github.io/[リポジトリ名]/

README.md にURLを記載しておくと、以下のように表示され、便利である。

.. image:: _static/pic/GitHub3.png
   :scale: 100%
   :align: center
   

引用した資料たち
========
・Sphinxをはじめよう (http://sphinx-users.jp/gettingstarted/)

・Sphinx最初の一歩 (http://www.sphinx-doc.org/ja/1.7/tutorial.html#install-sphinx)

・reStructured Text in Eclipse (https://www.slideshare.net/tcalmant/rest-editor-eclipse-demo-camp-grenoble-2011)

・Sphinx再入門 (http://muraoka-edo.hatenablog.com/entry/2015/01/18/171522)

・sphinx_rtd_theme をカスタマイズする (http://kuttsun.blogspot.com/2016/11/sphinx-sphinxrtdtheme.html)

・blockdiag (http://blockdiag.com/ja/blockdiag/introduction.html)

・GitHub Pagesで自分の作ったサイトを公開する (https://qiita.com/nagisa88/items/91c4f57c784842f365d7)
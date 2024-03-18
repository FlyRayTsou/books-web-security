# 3

- Web ブ ラウザが備えている仕組み
- Chrome が採用して いる「Site Isolation」
- CORB(Cross-Origin Read Blocking)
- CORP(Cross-Origin Resource Policy)
- COEP(Cross-Origin Embedder Policy)
- COOP(Cross-Origin Opener Policy)
- Fetch Metadata

# 3.1

Web ブラウザにて存在しているコンポーネント
- UIを管理するコンポーネント
- HTMLをパース、レンダリングするエンジン
- ネットワーク通信を行うコンポーネント
- JavaScriptを実行するためのエンジン
- ストレージを管理するコンポーネント
- プラグイン(Flash、Java アプレットなど)
- DOM(Document Object Model)を管理するインターフェイス

## RBGL07
Using Processes to Improve the Reliability of Browser-based Applications
https://gribble.org/papers/UW-CSE-07-12-01.pdf

## 図 3.1 シングルプロセス
- 全てのタブは同じプロセス

## 問題

- 耐障害性(fault tolerance)の不足：あるコンポーネントがクラッシュしただけで Web ブラウザ全体がクラッシュしてしまうこと
- 情報の機密性に対する問題
  - binary exploitation : https://qiita.com/Kuroakira/items/82446f083ee2fd05e925
- 並列性(concurrency)の担保の難しさ：単一のプロセスで動作している Web ブラウザでは、あ る Web ページが重い処理を始めた途端、Web ブラウザ全体がフリーズしかねません。
- メモリ管理(memory management)の難しさ：単一のプロセ スで動作している Web ブラウザでは、そのメモリリークがページを閉じた後でも維 持されてしまいます。

## Konqueror 
- Konqueror （コンカラー、コンケラー、コンキュラー）は、KDEデスクトップ環境の中核として設計されたファイルビューアとしての機能を提供するウェブブラウザおよびファイルマネージャである。
- https://ja.wikipedia.org/wiki/Konqueror
- https://invent.kde.org/network/konqueror

# 3.2 プロセスを分離した場合の問題

## 複数のプロセスからなる Web ブラウザアーキテクチャの実装
- Konqueror
- Tahoma
  - [CHGL06]
- SubOS
  - [IB01]
- OP
  - [GTK08]
- Gazelle
  - [WGM+09]
- IBOS
  - [TMK10]
- OP2
  - [GTK11]

## Web ブラウザのプロセスを分離することによる互換性の問題
- Tahoma
  - Web リソースの隔離には利用できません。
- SubOS、OP、OP2、 Gazelle、IBOS 
  - JavaScript を用いたリソース間通信のための仕組み(document. domain による SOP の緩和など)
- Chromium
  - Chromium（クロミウム）は、フリーかつオープンソースのウェブブラウザ向けのコードベースである。主にGoogleによって開発とメンテナンスが行われている。Googleは、Chromiumのコードに機能追加をすることでGoogle Chromeブラウザを作成している。
    - https://ja.wikipedia.org/wiki/Chromium
  - 既存の Web リソースとの互換性を保ったままプロセス分 離を可能にする抽象的なモデルを定義
  - RG09: https://dl.acm.org/doi/10.1145/1519065.1519090
    - Browsing Instance
    - Site
    - Site Instance

## Browsing Instance
ウィンドウオブジェクトに対する参照が存在するようなウィンドウやフレームの集合を指します。

## Site
「スキーム部分」と、「ホスト部分から取り出した登録可能なドメイン名」
- https://publicsuffix.org/
- https://publicsuffix.org/list/public_suffix_list.dat
- (https:, def. github.io) 
- (https:, example.io)
- TLD(Top-level Domain)
- eTLD（Effective Top-Level Domain）

## Site Instance
「Browsing Instance の部分集合であって、同じ Site を持つウィンドウ同士からなる集合」


## Process-per-Browsing-Instance モデル
「1つのレンダラプロセスは同一 Browsing Instance に属するウィンドウやフレームの処理しかしてはいけない」

## Process-per-Site-Instance モデル

## Process-per-Site モデル

# 日本語

備える（そなえる）
モノリシック（monolithic）

# 略語
- CTF（Capture The Flag）
- PSL(Public Suffix List)
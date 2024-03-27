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
- 「1つのレンダラプロセスは同一 Browsing Instance に属するウィンドウやフレームの処理しかしてはいけない」
- Chromium

## Process-per-Site-Instance モデル
- 「1つのレンダラプロセスは同一 Site Instance に属するウィンドウやフレームの処理しかしてはいけない」
- Chrome

## Process-per-Site モデル
- 「1 つのレンダラプロセスは同一 Schemeful Site を持ったウィンドウやフレームの処理しかしてはいけない」

# 3.3 Process-per-Browsing-Instance モデルに対する攻撃

# 3.3.1
## opener
- if window A opens window B, B.opener returns A.
- https://developer.mozilla.org/en-US/docs/Web/API/Window/opener

# 3.3.2 memory disclosure attacker

- ISA
  - https://sylph.fuis.u-fukui.ac.jp/~moris/lecture/ARC/2020/ISA.pdf

- サイドチャネル攻撃(side-channel attack)
  - サイドチャネル攻撃とは、コンピュータセキュリティの分野において、アルゴリズムの実装自体の弱さ（例：暗号そのものに対する解読やソフトウェアのバグ）ではなく、コンピュータシステムの実装から得られる情報を元にした暗号解読の攻撃のことである。タイミング情報、電力消費、電磁放射線のリーク、ときには音声さえも、追加の情報源となって悪用される可能性がある。
  - https://ja.wikipedia.org/wiki/%E3%82%B5%E3%82%A4%E3%83%89%E3%83%81%E3%83%A3%E3%83%8D%E3%83%AB%E6%94%BB%E6%92%83

- Flush+Reload
  - https://milestone-of-se.nesuke.com/sv-advanced/sv-security/cache-side-channel/
  - https://www.usenix.org/conference/usenixsecurity14/technical-sessions/presentation/yarom

- Transient Execution Attack
  - y = array2[array1[x] * C];
    - x = (z - array1) / S
    - array1[x] * C = *(array1 + x * S) * C
    - *(array1 + (z - array1) / S * S) * C = (*z) * C
  - array2[array1[x] * C] = array2 + K * C *(*z)

# 3.4

# 3.4.1 CORB(Cross-Origin Read Blocking)
- 対象JSON、HTML、XML、「<iframe>、 <object>、<embed> のようなタグ以外から発生するリクエスト」

- nosniff: この属性を付与するとファイルの内容をContent-Type属性から判断してねとお願いできる。
  - https://qiita.com/kohekohe1221/items/f87a9308b606172b5f15
- 206 Partial Content
  - https://developer.mozilla.org/ja/docs/Web/HTTP/Status/206
- MIME Sniffing 
  - https://techblog.gmo-ap.jp/2022/12/09/mime_sniffing/

# 3.4.2 CORP(Cross-Origin Resource Policy)
- CORB により JSON、HTML、XML は保護されますが、それ以外のリソースは守れ
ません。
- Fetch Standard
  - https://fetch.spec.whatwg.org/
- オプトイン式
- same-site, same-origin, cross-origin
  - https://zenn.dev/agektmr/articles/f8dcd345a88c97

# 日本語

備える（そなえる）
モノリシック（monolithic）
すなわち(in other words)

# 略語
- CTF（Capture The Flag）
- PSL(Public Suffix List)
- MIME(Multipurpose Internet Mail Extensions)
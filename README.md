# DJ Mix Analyzer — Beta Test Release

> **ベータ版（テスト公開）**: このリリースは限定的なテスト公開です。公開期間は約1週間を予定しています。
> フィードバックや不具合報告は [Issue](../../issues) からお願いします。

DJ Mix Analyzerは、Serato DJ / rekordbox / Traktor のプレイ履歴を読み込み、トラック使用傾向・トランジション分析・セットリスト構築などを行うデスクトップアプリケーションです。[Tauri](https://tauri.app/)（Rust製のデスクトップアプリ開発フレームワーク）を使用して開発しています。

---

## ダウンロード

[Releases ページ](../../releases) から最新版をダウンロードしてください。

| プラットフォーム | ファイル | 備考 |
|:---:|:---|:---|
| **macOS** (Apple Silicon) | `DJ_Mix_Analyzer_0.2.5_aarch64.dmg` | macOS 12以降 |
| **Windows** (64-bit) | `DJ_Mix_Analyzer_0.2.5_x64-setup.exe` | Windows 10以降（推奨） |
| **Windows** (64-bit) | `DJ_Mix_Analyzer_0.2.5_x64_en-US.msi` | 企業環境向け |

### ファイル検証（SHA-256）

ダウンロード後、ファイルの整合性を確認できます。

```
f69866e4fcf43515bc9f1239bbbdc0810c79444f8af5df3635f628c68e90aa4c  DJ_Mix_Analyzer_0.2.5_aarch64.dmg
c08a6921d86e785674653d8745fbd06ffb58019f66c162fd87defa39a7e0b7b4  DJ_Mix_Analyzer_0.2.5_x64-setup.exe
82bfa552e2fd309e2abb138fc76a624f01958508e40b920db0e692739b1691e6  DJ_Mix_Analyzer_0.2.5_x64_en-US.msi
```

**macOS での確認方法:**
```bash
shasum -a 256 ~/Downloads/DJ_Mix_Analyzer_0.2.5_aarch64.dmg
```

**Windows での確認方法 (PowerShell):**
```powershell
Get-FileHash "$env:USERPROFILE\Downloads\DJ_Mix_Analyzer_0.2.5_x64-setup.exe" -Algorithm SHA256
```

---

## インストール方法

### macOS

1. `.dmg` ファイルを開く
2. 「DJ Mix Analyzer」を「Applications」フォルダにドラッグ
3. 初回起動時に **「開発元が未確認」** の警告が表示されます
   - ダイアログ右上の？ボタンをクリック
   - 「システム設定」→「プライバシーとセキュリティ」→「このまま開く」を選択
   - または `.app` を右クリック →「開く」→「開く」で起動

> **なぜ警告が出るのか**: Apple Developer Programによるコード署名を行っていないためです。アプリの安全性については後述の「プライバシーと安全性」セクションをご確認ください。

### Windows

1. `.exe` ファイル（NSIS installer）を実行
2. **Windows SmartScreen** の警告が表示される場合があります:
   - 「詳細情報」をクリック → 「実行」を選択
3. インストーラーの指示に従い、インストール完了

> **なぜ警告が出るのか**: Microsoft認証のコード署名証明書を取得していないためです。これはベータ版の個人開発ソフトウェアでは一般的です。

> **確認ポイント**:
> - ダウンロード元がこのGitHubリポジトリの Releases ページであること
> - ファイルのSHA-256ハッシュが上記の値と一致すること
> - ファイルサイズが著しく異なっていないこと（.exe は約3.5MB、.msi は約5MB）

### アンインストール

- **macOS**: `Applications` フォルダから「DJ Mix Analyzer」をゴミ箱に移動。データも削除する場合は `~/Library/Application Support/com.kohadachan.dj-mix-analyzer/` を削除。
- **Windows**: 「設定」→「アプリ」→「DJ Mix Analyzer」→「アンインストール」。データも削除する場合は `%APPDATA%\com.kohadachan.dj-mix-analyzer\` を削除。

---

## 使い方

### 初回起動

アプリを起動すると、空のダッシュボードが表示されます。データを取り込むことで分析が開始できます。

### データ取り込み（Import）

1. 画面上部の **「Library」** タブ → **「Import」** サブタブを選択
2. アプリ内の **「How to export from your DJ software」** ガイドで、お使いのDJソフトの手順を確認できます
3. 以下の方法でファイルを取り込みます:
   - ファイルやフォルダをドロップゾーンに **ドラッグ＆ドロップ**
   - または **「Browse Folder...」** ボタンからフォルダを選択
4. 完了後 **「Dashboard」** タブで統計が確認できます

#### Import と Enrich の違い

| | **Import**（セッション取り込み） | **Enrich**（BPM / Key 付与） |
|:---|:---|:---|
| **目的** | プレイ履歴（セッション＋トラック）をDBに登録 | 既存トラックにBPM・Key情報を補完 |
| **データの流れ** | ファイル → 新規セッション＋トラック作成 | ライブラリ → 既存トラックのBPM/Key列を更新 |
| **操作場所** | Library → Import のドロップゾーン | Library → Import 下部の Enrich セクション |

#### DJソフト別 推奨取り込み手順

各DJソフトウェアで**最も効率的にデータを取り込む方法**をまとめています。

<details>
<summary>Serato DJ — 2ステップ（Import → Enrich）</summary>

Seratoではプレイ履歴とトラックメタデータが**別々のファイル**に保存されているため、2ステップで取り込みます。

**ステップ1: Import（プレイ履歴の取り込み）**

Serato DJ はプレイするたびにセッションファイル（`.txt`）を自動保存しています。このフォルダをまるごと取り込むのが最も簡単です。

- **Mac**: `~/Music/_Serato_/History/Sessions/`
- **Windows**: `Music\_Serato_\History\Sessions\`

Sessions フォルダをそのままドラッグ＆ドロップすれば、過去の全セッションを一括インポートできます。
重複チェック機能があるため、同じフォルダを何度取り込んでも二重登録にはなりません。

> **セッションファイルにはBPM/Key情報が含まれません。** Serato のセッションファイルは曲名・アーティスト・再生時刻・デッキ情報のみを記録しており、BPM や Key は保存されない仕様です。次のステップで補完します。

**ステップ2: Enrich（BPM/Key の補完）**

Serato の内部データベース（`database V2`）には、UIの表示設定に関係なく BPM・Key などのメタデータがすべて保持されています。Enrich 機能はこのデータベースから BPM/Key を読み取り、Import 済みのトラックに自動で付与します。

1. **「Library」** → **「Import」** 下部の Enrich セクション
2. Serato のライブラリが自動検出されるので、選択して **「Enrich」** を実行

> **CSV エクスポートについて**: Serato の History タブから CSV をエクスポートすることもできますが、CSV に含まれるカラムは**その時点で画面に表示している項目に依存**します（例: Key カラムを表示していなければ CSV にも含まれません）。セッションファイル（`.txt`）+ Enrich の方が確実です。

</details>

<details>
<summary>rekordbox — 1ステップ（master.db だけでOK）</summary>

rekordbox は `master.db` ファイル1つにプレイ履歴もトラックメタデータもすべて格納しているため、**1回のインポートで完結**します。

1. `master.db` ファイルを探す:
   - **Mac**: `~/Library/Pioneer/rekordbox/master.db`
   - **Windows**: `%APPDATA%\Pioneer\rekordbox\master.db`
2. `master.db` をドラッグ＆ドロップ、または「Browse」で選択

これだけで**プレイ履歴 + BPM + Key + Genre** がすべて取り込まれます。Enrich の実行は不要です。

> **ヒント**: master.db は読み取り専用でアクセスします。rekordbox のデータが変更されることはありません。

> **XMLエクスポートについて**: rekordbox の「コレクションをxml形式でエクスポート」で書き出したXMLファイルも読み込めますが、XML にはプレイ履歴（HISTORY）が含まれません。Enrich ソース（BPM/Key補完用）としてのみ使用できます。

</details>

<details>
<summary>Traktor — 1ステップ（collection.nml だけでOK）</summary>

Traktor も `collection.nml` ファイル1つにプレイ履歴とトラックメタデータが含まれているため、**1回のインポートで完結**します。

- **Mac**: `~/Documents/Native Instruments/Traktor X.X.X/collection.nml`
- **Windows**: `Documents\Native Instruments\Traktor X.X.X\collection.nml`

collection.nml ファイルをドラッグ＆ドロップ、または「Browse」で選択してください。
プレイ履歴 + BPM + Key がすべて取り込まれます。

</details>

#### まとめ: アプリ別の取り込みフロー

| DJソフト | 手順 | Import ソース | Enrich 必要？ |
|:---|:---|:---|:---:|
| **Serato DJ** | 2ステップ | `Sessions/` フォルダ → Enrich | **必要** |
| **rekordbox** | 1ステップ | `master.db` | 不要 |
| **Traktor** | 1ステップ | `collection.nml` | 不要 |

### BPM / Key の付与（Enrich）

Import済みのトラックにBPM・Key情報を補完する機能です。主に **Serato ユーザー**が使用します（rekordbox / Traktor は Import 時に自動取得されます）。

1. **「Library」** タブ → **「Import」** サブタブ下部の **Enrich** セクション
2. DJソフトウェアのライブラリが自動検出されます（検出されない場合は「Add Source...」で手動指定）
3. 検出されたソースを選択して **「Enrich」** を実行

| ソース | Enrich対象データ | 備考 |
|:---|:---|:---|
| **Serato** `_Serato_/` | BPM, Key | database V2 + streaming Metadata |
| **rekordbox** `master.db` | BPM, Key | Import時に取得済みなら不要 |
| **rekordbox** XML | BPM, Key | master.db がない場合のフォールバック |
| **Traktor** `collection.nml` | BPM, Key | Import時に取得済みなら不要 |

### 主な機能

| タブ | 内容 |
|:---|:---|
| **Dashboard** | トラック数、セッション数、Top Tracks/Artists、曜日・時間帯分析 |
| **Tracks** | 全トラック一覧（Key・BPM・Tag フィルタ、インライン詳細展開、表示件数切替） |
| **Transitions** | トランジション統計、Top Pairs、N-Track Chains、Artist Transitions、Deck Analysis |
| **Insights** | Hub Tracks、Entropy Map、Bridge Tracks、Set Diversity、Escape Routes |
| **Flow** | 指定トラックの前後遷移を可視化 |
| **Network** | トランジションネットワークグラフ（ノード内に Key・BPM 表示） |
| **Progression** | セッションごとの BPM・Key 変化分析 |
| **Escapes** | 定番遷移からの脱出候補提案（トラック検索対応） |
| **Rediscover** | 最近使っていないが以前よく使った曲の再発見 |
| **Explorer** | セットリスト手動構築（次トラック候補をリアルタイム提案） |
| **Generator** | 遷移実績に基づくセットリスト自動生成 |
| **Setlists** | 保存済みセットリストの管理・CSV出力 |
| **Settings** | Key表記（Camelot/Musical）、フォントサイズ、テーマ、エクスポート先 |

---

## プライバシーと安全性

以下はソースコードの実装に基づく事実です。

### 外部通信

**このアプリはインターネット通信を一切行いません。**

- HTTPクライアントライブラリ（reqwest, hyper等）を含んでいません
- フロントエンドからの外部URL接続はCSP（Content Security Policy）で制限されています
- Tauri の権限設定（capabilities）でネットワークAPIは許可されていません

### テレメトリ・分析

**使用状況の収集、クラッシュレポート、分析用トラッキングは一切行いません。**

### ファイルアクセス

- **読み取り**: ユーザーがファイルダイアログで明示的に選択したDJソフトウェアのファイルのみ
- **書き込み**: アプリデータフォルダ内のSQLiteデータベース（`dj_history.db`）と、ユーザーが指定したエクスポート先へのCSVファイルのみ
- **既存ファイルの改変**: DJソフトウェアの元ファイルを変更・削除することはありません（読み取り専用）

### BPM/Key Enrich 時の自動検出について

Enrichソース検出時に、以下のディレクトリの存在を確認します（ファイル内容の読み取りは行いません）:

- **macOS**: `/Volumes/` 配下の外部ドライブ
- **Windows**: D: 〜 Z: ドライブ

これはDJソフトウェアのライブラリフォルダを検出する目的のみで使用されます。ユーザーが「Enrich」を実行するまで、検出されたファイルの読み取りは行われません。

### データ保存場所

全データはローカルに保存されます:

| OS | パス |
|:---|:---|
| macOS | `~/Library/Application Support/com.kohadachan.dj-mix-analyzer/` |
| Windows | `%APPDATA%\com.kohadachan.dj-mix-analyzer\` |

詳細は [PRIVACY.md](PRIVACY.md) をご覧ください。

---

## 既知の制限事項

- macOS Intel版のビルドは提供していません（Apple Silicon のみ）
- コード署名されていないため、OS の警告が表示されます
- Serato セッションファイル（.txt）にはBPM/Key情報が含まれないため、Enrich機能でSeratoのDBから補完する必要があります
- Serato 4.0.x の CSV エクスポートからは BPM・Key を直接取得できます（日本語ロケールにも対応）
- ベータ版のため、予期しない動作がある可能性があります

## フィードバック

バグ報告や機能リクエストは [Issue](../../issues) からお願いします。
バグ報告の際は [テンプレート](../../issues/new?template=bug_report.yml) をご利用ください。

---

## 免責事項

本ソフトウェアは「現状のまま（AS IS）」で提供されます。作者は、本ソフトウェアの使用または使用不能に起因するいかなる損害（データの損失、機器の不具合、その他一切の直接的・間接的損害を含む）についても責任を負いません。ベータ版のため、予期しない動作が発生する可能性があります。使用にあたっては、重要なデータのバックアップを事前に行ってください。

本ソフトウェアをインストールまたは使用された場合、この免責事項に同意されたものとみなします。

## ライセンス

All rights reserved. このソフトウェアのソースコードは非公開です。
本リポジトリで配布されるインストーラーは、個人利用目的でのみ使用できます。




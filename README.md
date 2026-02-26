# DJ Mix Analyzer — Beta Test Release

> **ベータ版（テスト公開）**: このリリースは限定的なテスト公開です。公開期間は約1週間を予定しています。
> フィードバックや不具合報告は [Issue](../../issues) からお願いします。

DJ Mix Analyzerは、Serato DJ / rekordbox / Traktor のプレイ履歴を読み込み、トラック使用傾向・トランジション分析・セットリスト構築などを行うデスクトップアプリケーションです。

---

## ダウンロード

[Releases ページ](../../releases) から最新版をダウンロードしてください。

| プラットフォーム | ファイル | 備考 |
|:---:|:---|:---|
| **macOS** (Apple Silicon) | `DJ_Mix_Analyzer_0.1.0_aarch64.dmg` | macOS 12以降 |
| **Windows** (64-bit) | `DJ_Mix_Analyzer_0.1.0_x64-setup.exe` | Windows 10以降（推奨） |
| **Windows** (64-bit) | `DJ_Mix_Analyzer_0.1.0_x64_en-US.msi` | 企業環境向け |

### ファイル検証（SHA-256）

ダウンロード後、ファイルの整合性を確認できます。

```
f9a906c52e50f3dfce9cd584416c1bfa8013b52744e2f309f37de356a4d3ad9c  DJ_Mix_Analyzer_0.1.0_aarch64.dmg
9c9e04af87e514e0f7a2403948680540f06c70e4993202453652460e7b740ea3  DJ_Mix_Analyzer_0.1.0_x64-setup.exe
177d1360da763bafb34ddaae6d421f1eb34b6d8caf03c2f9a8ea87ccf3e7c0ee  DJ_Mix_Analyzer_0.1.0_x64_en-US.msi
```

**macOS での確認方法:**
```bash
shasum -a 256 ~/Downloads/DJ_Mix_Analyzer_0.1.0_aarch64.dmg
```

**Windows での確認方法 (PowerShell):**
```powershell
Get-FileHash "$env:USERPROFILE\Downloads\DJ_Mix_Analyzer_0.1.0_x64-setup.exe" -Algorithm SHA256
```

---

## インストール方法

### macOS

1. `.dmg` ファイルを開く
2. 「DJ Mix Analyzer」を「Applications」フォルダにドラッグ
3. 初回起動時に **「開発元が未確認」** の警告が表示されます
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
2. **「Select Folder」** をクリック
3. DJソフトウェアのプレイ履歴フォルダを選択:
   - **Serato DJ**: `_Serato_/History/Sessions/` フォルダ内の `.txt` / `.csv` ファイル
   - **rekordbox**: エクスポートした `.xml` / `.tsv` ファイル
   - **Traktor**: `collection.nml` ファイル
4. ファイルが検出され、インポートが実行されます
5. 完了後 **「Dashboard」** タブで統計が確認できます

### BPM / Key の付与（Enrich）

1. **「Library」** タブ → **「Import」** サブタブ下部の **Enrich** セクション
2. DJソフトウェアのライブラリが自動検出されます（検出されない場合は「Add Source...」で手動指定）
3. 検出されたソースを選択して **「Enrich」** を実行
4. rekordbox / Traktor のライブラリからBPM・Key情報が既存トラックに付与されます

### 主な機能

| タブ | 内容 |
|:---|:---|
| **Dashboard** | トラック数、セッション数、Top Tracks/Artists、曜日・時間帯分析 |
| **Tracks** | 全トラック一覧、再生回数・BPM・Key での並べ替え |
| **Transitions** | トラック間の遷移パターン分析、チェイン検出 |
| **Explorer** | セットリスト手動構築（次トラック候補をリアルタイム提案） |
| **Generator** | エネルギーカーブに基づくセットリスト自動生成 |
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
- Serato セッションにはBPM/Key情報が含まれないため、Enrich機能でrekordbox/Traktorから補完する必要があります
- ベータ版のため、予期しない動作がある可能性があります

## フィードバック

バグ報告や機能リクエストは [Issue](../../issues) からお願いします。
バグ報告の際は [テンプレート](../../issues/new?template=bug_report.yml) をご利用ください。

---

## ライセンス

All rights reserved. このソフトウェアのソースコードは非公開です。
本リポジトリで配布されるインストーラーは、個人利用目的でのみ使用できます。

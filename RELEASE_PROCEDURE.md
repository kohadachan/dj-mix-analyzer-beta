# リリース手順書（内部用）

この文書は DJ Mix Analyzer のベータリリースを `dj-mix-analyzer-beta` リポジトリへ公開する手順です。

---

## 前提条件

- 開発リポジトリ（`dj-mix-analyzer`、非公開）で開発・テスト済みであること
- macOS ビルド環境（Apple Silicon Mac + Rust + Node.js）
- GitHub CLI (`gh`) がインストール済みであること

## 手順

### 1. バージョン番号の決定

`app/src-tauri/tauri.conf.json` の `version` フィールドを更新:
```json
"version": "0.1.1"
```

### 2. macOS ビルド

```bash
cd app
PATH="/Users/kohada/.cargo/bin:$PATH" npx tauri build --bundles app
```

ビルド後、`.app` から DMG を作成:
```bash
hdiutil create -volname "DJ Mix Analyzer" \
  -srcfolder "src-tauri/target/release/bundle/macos/DJ Mix Analyzer.app" \
  -ov -format UDZO \
  "DJ_Mix_Analyzer_<VERSION>_aarch64.dmg"
```

### 3. Windows ビルド

開発リポにpush後、GitHub Actions で自動ビルド:
```bash
git push origin main
gh workflow run build-windows.yml
```

ビルド完了後、成果物をダウンロード:
```bash
gh run list --workflow=build-windows.yml --limit=1
gh run download <RUN_ID> -n windows-msi -D /tmp/win-artifacts/msi
gh run download <RUN_ID> -n windows-nsis -D /tmp/win-artifacts/nsis
```

### 4. ハッシュ生成

```bash
shasum -a 256 DJ_Mix_Analyzer_*.dmg /tmp/win-artifacts/msi/*.msi /tmp/win-artifacts/nsis/*.exe
```

### 5. README.md 更新

`dj-mix-analyzer-beta` リポジトリの `README.md` を更新:
- バージョン番号
- ファイル名
- SHA-256 ハッシュ値

### 6. GitHub Release 作成

```bash
cd /path/to/dj-mix-analyzer-beta

gh release create v<VERSION> \
  --title "v<VERSION> Beta" \
  --notes-file release_notes.md \
  "/path/to/DJ_Mix_Analyzer_<VERSION>_aarch64.dmg" \
  "/tmp/win-artifacts/nsis/DJ Mix Analyzer_<VERSION>_x64-setup.exe" \
  "/tmp/win-artifacts/msi/DJ Mix Analyzer_<VERSION>_x64_en-US.msi"
```

### 7. リリースノートの書き方

```markdown
## v0.1.x Beta

### 変更点
- （修正内容を箇条書き）

### 既知の問題
- （既知のバグや制限事項）

### ファイル検証（SHA-256）
| ファイル | ハッシュ |
|:---|:---|
| `DJ_Mix_Analyzer_<VER>_aarch64.dmg` | `<hash>` |
| `DJ_Mix_Analyzer_<VER>_x64-setup.exe` | `<hash>` |
| `DJ_Mix_Analyzer_<VER>_x64_en-US.msi` | `<hash>` |
```

### 8. 公開後チェック

- [ ] Release ページからファイルがダウンロード可能
- [ ] ダウンロードしたファイルのハッシュが一致
- [ ] README のリンクが正しく機能
- [ ] Issue テンプレートが表示される

---

## テスト公開の終了

公開期間終了時:
1. GitHub Release を Draft に戻す（または削除）
2. リポジトリの Visibility を Private に変更（Settings → Danger Zone）
3. または リポジトリごと削除

```bash
# リポジトリを非公開にする
gh repo edit kohadachan/dj-mix-analyzer-beta --visibility private

# または削除
gh repo delete kohadachan/dj-mix-analyzer-beta --yes
```

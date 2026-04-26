# CLAUDE.md

このファイルは、リポジトリ内のコードを操作する際に Claude Code（claude.ai/code）へ提供するガイダンスです。

## プロジェクト概要

日本語ユーザー向けの、スタンドアロンな単一ファイル Web ユーティリティアプリ 4 本を静的 HTML として提供するスイートです。

- `index.html` — ランディングページ
- `clock.html` — 和暦形式対応のデジタル時計
- `timer.html` — フルスクリーンストップウォッチ
- `kioxia.html` — キオクシア（6600.JP）株価のリアルタイム表示

## ローカルでの実行

ビルド手順は不要です。任意の静的 HTTP サーバーでファイルを配信してください。

```bash
python3 -m http.server 8000
```

その後 `http://localhost:8000` にアクセスします。

## アーキテクチャ

各ファイルは完全に自己完結しています（HTML＋インライン CSS＋インライン `<script>`）。共有コード、バンドラー、外部ライブラリは一切なく、すべてバニラ ES6＋とブラウザネイティブ API で実装されています。

### 規約

- 言語属性：全ページに `lang="ja"`
- デフォルトはダークテーマ：背景 `#111`、文字色ホワイト
- レイアウト：レスポンシブなフルスクリーン対応のため `flexbox` とビューポート単位（`vw`、`dvh`）を使用
- 各 `<style>` の先頭に CSS リセット：`* { margin: 0; padding: 0; box-sizing: border-box; }`
- DOM 要素 ID：キャメルケース（`timeEl`、`priceEl`、`changeEl`）
- 関数名：キャメルケース（`fetchStooq`、`fetchDirect`）
- リアルタイム更新は `setInterval` を使用
- 非同期データ取得は `fetch` と `async/await`、`try/catch` を使用

### 株価データの取得（kioxia.html）

CORS 制限を回避するため、3 つのソースによるフォールバックチェーンを使用しています。

1. **Stooq API**（`stooq.com/q/l/?s=6600.jp`）— 第一優先、CORS 問題なし
2. **allorigins.win プロキシ** — CORS 回避のため Yahoo Finance をラップ
3. **Yahoo Finance 直接接続**（`query1.finance.yahoo.com`）— 最終手段

`#change` 要素に `.up` / `.down` カラークラスをトグルして株価の変動をスタイリングします。

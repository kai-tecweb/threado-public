# Threads運用センター（Threado）

Threadsの投稿・返信・リサーチ・分析をAIが自動で管理するローカル**デスクトップアプリ**です。Windows サービスとして常駐し、ダブルクリックでアプリウィンドウが開きます。

## 動作環境
- Windows 10 / 11（推奨）
- Python 3.10以上
- Claude Code または Codex CLI

> ⚠ macOSは現在未対応です。次期バージョンで対応予定。

## 技術スタック
- バックエンド：Python（http.server、ポート8765）
- DB：SQLite 3（WALモード）
- フロントエンド：Vanilla JS / HTML / CSS（SPA）
- デスクトップウィンドウ：pywebview（Windows は Edge WebView2 バックエンド）
- 常駐運用：pywin32 で Windows サービス化（ThreadoService）
- AI CLI：Claude Code / Codex CLI（アカウントごとに切替可）
- 外部連携：Google Apps Script ブリッジ（OAuth callback / Webhook）

## 主な機能
- 複数 Threads アカウントの投稿・予約投稿・スレッド投稿
- リサーチ → ナレッジ抽出 → AI 投稿生成 → 投稿 → メトリクス計測 → PDCA 改善ループ
- 自動返信ルール（キーワードマッチ + AI 生成 + 遅延送信）
- **シナリオ分岐型自動リプライ**（キーワードトリガーで多段ステップ時間差配信）
- **ナレッジアップロード**（.txt / .md / .pdf / .docx をアップロードして AI に投稿の型を自動生成）
- アカウントごとの「優先 AI CLI」設定（Claude Code / Codex CLI）

## 使い方

### 準備（1回だけ）

1. [Python](https://www.python.org/downloads/) をインストールする（**「Add Python to PATH」のチェックを忘れずに**）
2. [Claude Code](https://claude.ai/code) をインストールしてログインする
3. このページの緑の「Code」ボタン→「Download ZIP」でダウンロードする
4. ダウンロードしたZIPを右クリック→「すべて展開」する
5. 展開されたフォルダの中の「setup.bat」を右クリック→「管理者として実行」する

setup.bat は依存パッケージのインストール、Windows サービス（ThreadoService）の登録・自動起動設定、デスクトップに「Threado」アイコンの自動作成までを一発で行います。

### 毎回の起動

デスクトップの「Threado」アイコンをダブルクリックする（pywebview ウィンドウが開きます）

> ⚠️ フォルダを移動・削除するとサービスが起動しなくなります。展開したフォルダはそのままの場所に置いてください。

> ⚠️ サービスを完全に削除する場合は `uninstall_service.bat` を管理者として実行してください。

> ⚠️ macOSは現在未対応です。次期バージョンで対応予定。

## 変更履歴
[変更履歴はこちら](docs/CHANGELOG.md)

# Threads運用センター（Threado）

Threadsの投稿・返信・リサーチ・分析をAIが自動で管理するアプリです。

> 🆕 **v3.0 Linux 対応開始（2026-05-19）**
>
> Threado が **Linux サーバ上でも動作する** ようになりました（Phase 1 実機検証完了）。
> OCI Always Free Tier の範囲内（**月額 0 円**）または さくらVPS（月額 約 1,760 円）で 24 時間稼働できます。
>
> - 動作環境：Ubuntu 22.04 / 24.04 LTS（ARM64 / x86_64）
> - HTTPS 終端：Caddy + Let's Encrypt 自動発行
> - 常駐運用：systemd
>
> セットアップ手順は [Linux/さくらVPS セットアップガイド](docs/customer-setup-guide.md) を参照。
> Windows 版（v2.x）も引き続きサポートしますが、新規機能は v3.0 系を優先します。

---

- **Windows 版（v2.x）**：ローカルデスクトップアプリ。Windows サービスとして常駐し、ダブルクリックでアプリウィンドウが開きます。
- **Linux 版（v3.0、Phase 1 実機検証完了）**：Linux サーバ上で systemd 常駐、Caddy + Let's Encrypt で HTTPS 公開、ブラウザでアクセス。詳細は **[docs/customer-setup-guide.md](docs/customer-setup-guide.md)** を参照。

## 動作環境
- Windows 10 / 11（v2.x、推奨）
- **Linux**（Ubuntu 22.04 / 24.04 LTS、ARM64 / x86_64、v3.0）
- Python 3.10 以上
- Claude Code または Codex CLI

> ⚠ macOS はブラウザクライアントとして Linux 版にアクセスする形でサポート。Mac ネイティブのデスクトップアプリ実装は予定なし。

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

### Windows v2.x の場合（凍結中・継続サポート）

#### 準備（1回だけ）

1. [Python](https://www.python.org/downloads/) をインストールする（**「Add Python to PATH」のチェックを忘れずに**）
2. [Claude Code](https://claude.ai/code) をインストールしてログインする
3. このページの緑の「Code」ボタン→「Download ZIP」でダウンロードする
4. ダウンロードしたZIPを右クリック→「すべて展開」する
5. 展開されたフォルダの中の「setup.bat」を右クリック→「管理者として実行」する

setup.bat は依存パッケージのインストール、Windows サービス（ThreadoService）の登録・自動起動設定、デスクトップに「Threado」アイコンの自動作成までを一発で行います。

#### 毎回の起動

デスクトップの「Threado」アイコンをダブルクリックする（pywebview ウィンドウが開きます）

> ⚠️ フォルダを移動・削除するとサービスが起動しなくなります。展開したフォルダはそのままの場所に置いてください。

> ⚠️ サービスを完全に削除する場合は `uninstall_service.bat` を管理者として実行してください。

---

### Linux v3.0 の場合（推奨・新規機能はこちらに優先実装）

詳細手順は **[docs/customer-setup-guide.md](docs/customer-setup-guide.md)** に集約しています。Option A（OCI Always Free Tier、月額 0 円）／Option B（さくら VPS、月額 約 1,760 円）の両対応。

大まかな流れ：

1. Linux VM 立ち上げ（OCI / さくら VPS どちらか）
2. SSH 接続 → `git clone` でリポジトリ取得
3. `bash setup.sh` で apt / venv / pip / Playwright / Caddy / systemd 一括セットアップ
4. ドメイン取得 + DNS A レコード設定（Cloudflare 経由でも OK）
5. Caddy が Let's Encrypt から HTTPS 証明書を自動取得
6. ブラウザで `https://<取得ドメイン>/` にアクセス

Linux 版でも Windows v2.x の全機能が動作します（複数アカウント管理 / 投稿 / シナリオ分岐返信 / リサーチ / ナレッジ / 自律投稿 / PDCA / メトリクス）。

## 変更履歴
[変更履歴はこちら](docs/CHANGELOG.md)

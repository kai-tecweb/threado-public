# Threads運用センター（Threado）

Threadsの投稿・返信・リサーチ・分析をAIが自動で管理するアプリです。

> 🚀 **v3.0.0-beta1 β リリース開始（2026-05-19）**
>
> Threado v3.0（Linux 対応版）の **β リリース版**を限定 2 名で開始しました。Linux サーバ上で 24 時間稼働 + 画像/動画投稿 + ライセンスキー認証 + Cloudflare Proxy 経由 HTTPS で動作します。
>
> **このリリースでできること**
>
> - 既存 Threads アカウントの統合運用（複数アカウント管理、投稿、リサーチ、自動返信、PDCA 改善ループ）
> - **画像 / 動画つき Threads 投稿**（JPEG / PNG / WebP / MP4、サイズ上限 50 MB）— 新機能
> - **ライセンスキー認証**（β 期間 90 日）— 新機能
> - **Linux サーバ常駐運用**（systemd + Caddy + Let's Encrypt 自動 HTTPS）
> - **Cloudflare Proxy 経由配信**（DDoS 防御 + HTTPS 終端）
> - 動作環境：Ubuntu 22.04 / 24.04 LTS（ARM64 / x86_64）
>
> **β 期間中の運用方針**
>
> - β 期間中（90 日）はセットアップに関するご質問・不具合報告は **お受けできません**
> - 顧客がご自身でセットアップを完結できるよう、ガイドを「自己完結レベル」に整備しました
> - 困った時は [顧客向けセットアップガイド](docs/customer-setup-guide.md) の **「自己解決ガイド」** をご活用ください
> - 開発側で発見した重要修正は手動配信（`git pull` で取得）
>
> **インストール手順**：[顧客向けセットアップガイド](docs/customer-setup-guide.md)（9 Step 構成、所要 1.5-3 時間）
>
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

### Linux v3.0.0-beta1 の場合（推奨・新規機能はこちらに優先実装）

詳細手順は **[docs/customer-setup-guide.md](docs/customer-setup-guide.md)** に集約しています。Option A（OCI Always Free Tier、月額 0 円）／Option B（さくら VPS、月額 約 1,760 円）の両対応。

大まかな流れ（9 Step 構成）：

1. **Step 1**：Linux VM 立ち上げ（OCI / さくら VPS どちらか）
2. **Step 2**：ドメイン取得 + DNS 設定（独自ドメイン / 暫定サブドメインの 2 パターンから選択）
3. **Step 3**：Cloudflare Configuration Rule（独自ドメイン選択時のみ）
4. **Step 4**：deploy key 経由 `git clone` + `bash setup.sh`（apt / venv / pip / Playwright / Caddy / systemd 一括セットアップ）
5. **Step 5**：ライセンスキー入力（β 期間 90 日）
6. **Step 6**：画像/動画投稿の使い方
7. **Step 7**：⭐ Meta Threads OAuth App をご自身で作成（3 本のコールバック URL 登録 + Threads スマホアプリで承認 + Threads の App Secret 使用、所要 30-60 分）
8. **Step 8**：初期設定ウィザード
9. **Step 9**：動作確認チェックリスト

Linux 版でも Windows v2.x の全機能 + 画像/動画投稿が動作します（複数アカウント管理 / 投稿 / シナリオ分岐返信 / リサーチ / ナレッジ / 自律投稿 / PDCA / メトリクス）。

#### β 期間中の特別な注意

- **不具合報告・サポート問い合わせは受け付けていません**（β 期間 90 日間）
- セットアップでつまずいた場合は [docs/customer-setup-guide.md](docs/customer-setup-guide.md) の **「自己解決ガイド」セクション**（10 個の頻出トラブル別手順）を最初にご確認ください
- Meta 開発者アカウント取得 / ドメイン取得 / DNS 設定 / VPS 申込 等は全てご自身での対応となります
- 開発側で発見した重要修正は手動配信（メール / LINE）で通知されますので、案内に従って `git pull` してください

## 変更履歴
[変更履歴はこちら](docs/CHANGELOG.md)

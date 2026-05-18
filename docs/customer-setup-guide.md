# Threado v3.0 顧客向けセットアップガイド

最終更新: 2026-05-18（Phase 1 実機検証中）
対象読者: Threado を購入された顧客（オズさん含む β テスター）

> ⚠️ **この文書は下書き** です。Phase 1（基盤構築）の実機検証で具体的なコマンド・
> 落とし穴を確認した後、内容を更新します。

---

## このガイドで達成すること

1. **インフラを選ぶ**（OCI Always Free 0 円 または さくら VPS 約 1,760 円/月）
2. Linux VM を立ち上げる
3. 独自ドメインを取得し DNS を VM に向ける
4. Threado をインストール・起動する
5. ライセンスキーを入力して使い始める

**所要時間目安**：合計 1.5〜2 時間（OCI 登録に時間がかかる場合あり、さくら VPS なら 30〜60 分）

**月額費用**：選択するインフラによって変動（次の「インフラ選択肢」を参照）
※ ドメイン代は別途（年 1,000〜2,000 円程度）

---

## インフラ選択肢

Threado v3.0 は Linux サーバ上で動作します。**サーバの調達方法は 2 パターン**から選べます。

### Option A：OCI Always Free（月額 0 円）

| 項目 | 値 |
|---|---|
| 月額費用 | **0 円**（Always Free 枠の範囲内、永続無料）|
| スペック | Ampere A1 ARM（最大 4 OCPU / 24 GB RAM）|
| アーキ | ARM64（aarch64）|
| 取得難度 | やや高い（在庫切れの可能性あり、PAYG 化推奨）|
| 即日利用 | △（在庫待ちが発生する場合あり）|

**こんな人にお勧め**：月額コストを 0 円に抑えたい、時間に余裕がある、ARM64 で問題ない。

**注意点**：東京/大阪リージョンの Ampere ARM は人気で**在庫切れになることがあります**。PAYG（従量課金）にアップグレードすると枠が取りやすくなりますが、Always Free 部分は引き続き 0 円のままです。

### Option B：さくら VPS（月額 約 1,760 円）

| 項目 | 値 |
|---|---|
| 月額費用 | **約 1,760 円**（2GB プラン、年払いで 12% 割引あり）|
| スペック | 2 GB RAM / 50 GB SSD / 仮想 3 コア |
| アーキ | x86_64 |
| 取得難度 | 低い（メール登録 → 数分で利用可能）|
| 即日利用 | ◎（クレジットカード登録後すぐ起動）|

**こんな人にお勧め**：今すぐ動かしたい、登録の手間を減らしたい、x86_64 環境に慣れている。

**注意点**：Always Free と違い月額がかかります。メモリ 2GB はギリギリ動く水準なので、Phase 3（メディアアップロード）以降は **4GB プラン（約 3,520 円/月）への切り替えを推奨**。

### どちらを選んでも、Step 2 以降は完全に共通

VM が立ち上がってドメインを取得した後の手順（Caddy / Threado インストール / ライセンス入力）は**インフラを問わず同じ**です。インフラの差は「Step 1：VM を立ち上げる」の中だけ。迷ったら、すぐ動かしたい方は Option B、コストを抑えたい方は Option A。

---

## 用意するもの

- クレジットカード（OCI 登録時の本人確認用、課金は発生しません）
- 電話番号（OCI 登録時の SMS 認証用）
- メールアドレス
- パソコン（Windows / Mac / Linux どれでも OK、ブラウザがあれば良い）
- Threado のライセンスキー（購入時に送付）

---

## Step 1：VM を立ち上げる

選んだインフラに応じて、対応する小節へ進んでください。

- **Option A を選んだ場合** → 「Step 1-A：OCI 編」へ
- **Option B を選んだ場合** → 「Step 1-B：さくら VPS 編」へ

---

## Step 1-A：OCI 編

### 1-A-1. OCI 無料アカウントを作る

1. https://www.oracle.com/cloud/free/ を開く
2. 「Start for free」をクリック
3. 国（Japan）・メールアドレス・名前を入力
4. 認証メールが届くのでリンクをクリック
5. パスワード設定、組織情報入力（個人なら自分の名前で OK）
6. クレジットカードを登録（**請求は発生しません**、本人確認のみ）
7. SMS で電話番号認証
8. 登録完了 → OCI コンソールにログイン

> 💡 **ポイント**：「Always Free」と「30 日間無料トライアル」が同時に始まります。
> 30 日経過後もトライアル部分が切れるだけで、Always Free 枠は永続無料です。
> 課金が発生しないように、後述の「課金アラート設定」を必ず行ってください。

### 1-A-2. PAYG（従量課金）へのアップグレード（推奨）

無料トライアル中は OCI の安定性が落ちる場合があります。PAYG にアップグレードすると
Always Free 枠は引き続き無料で、安定性が向上します。

1. OCI コンソール右上のメールアドレス → 「Manage account」
2. 「Upgrade to Paid」をクリック
3. 画面の指示に従う
4. **重要**：アップグレードしても Always Free 枠は引き続き 0 円で使えます

### 1-A-3. 課金アラートを必ず設定

「うっかり課金」を防ぐため：

1. OCI コンソール → ハンバーガーメニュー → 「Billing & Cost Management」
2. 「Budgets」→ 「Create Budget」
3. 「Monthly Budget Amount」を **$1 USD** に設定
4. 「Budget Alert」を「50%」「100%」で 2 段階通知
5. 通知メールアドレスを登録
6. 保存

→ もし誤って課金されるリソースを作っても、$1 を超えた時点でメールが届きます。

---

## Step 2-A：OCI で Linux VM 立ち上げ（Option A 専用）

> Option B（さくら VPS）の方は本セクションを飛ばして「Step 2-B」へ進んでください。

### 2-A-1. Compute Instance を作成

1. OCI コンソール → ハンバーガーメニュー → 「Compute」→ 「Instances」
2. 右上の「Create Instance」をクリック
3. 以下の設定を選ぶ：

| 項目 | 値 |
|---|---|
| Name | `threado-server`（任意） |
| Image | **Ubuntu 22.04** を選択 |
| Shape | **VM.Standard.A1.Flex**（ARM、Always Free） |
| OCPU 数 | 1（最小、後で拡張可能） |
| Memory | 6 GB |
| Boot Volume | 50 GB（Always Free 範囲内） |

4. **SSH キー**：「Generate a key pair for me」を選び、**両方ダウンロード**
   （`.key`（秘密鍵）と `.pub`（公開鍵）の 2 つ。`.key` は絶対に他人に渡さないこと）
5. 「Create」をクリック
6. VM が起動するまで 1〜3 分待つ

> ⚠️ **「Out of capacity」エラーが出たら**：東京/大阪リージョンの Ampere ARM は
> 人気があり時々在庫切れになります。リージョンを「US East (Ashburn)」等に切り替えて
> 試すか、PAYG にアップグレード（1-2 を参照）してから再試行してください。

### 2-A-2. パブリック IP を確認

1. Instance の詳細ページに「Public IP Address」が表示される
2. これをメモしておく（例：`152.69.123.45`）

### 2-A-3. ファイアウォール（VCN）の設定

Threado に外部からアクセスするため、80 / 443 ポートを開放します。

1. Instance の詳細ページの「Primary VNIC」セクションで「Subnet」のリンクをクリック
2. 「Default Security List」をクリック
3. 「Add Ingress Rules」をクリックし以下を 2 つ追加：

| Source CIDR | IP Protocol | Destination Port Range |
|---|---|---|
| 0.0.0.0/0 | TCP | 80 |
| 0.0.0.0/0 | TCP | 443 |

4. 「Add Ingress Rules」をクリックして保存

### 2-A-4. VM に SSH 接続

ダウンロードした秘密鍵を使って VM に接続します。

#### Mac / Linux の場合

```bash
chmod 600 ssh-key-XXXX.key
ssh -i ssh-key-XXXX.key ubuntu@152.69.123.45
```

#### Windows の場合

- PowerShell で同じ ssh コマンドを実行（Windows 10/11 は ssh 標準搭載）
- もしくは Tera Term / PuTTY などの SSH クライアントを使用

初回接続時に「Are you sure you want to continue connecting?」と聞かれたら `yes` で OK。

→ ここから先（Step 3 以降）は **Option A / B 共通**です。

---

## Step 2-B：さくら VPS で Linux VM 立ち上げ（Option B 専用）

> Option A（OCI）の方は本セクションを飛ばして既に立ち上げ済みの VM をご利用ください。

### 2-B-1. さくらの会員登録

1. https://vps.sakura.ad.jp/ を開く
2. 右上の「お申し込み」をクリック
3. さくらインターネットの会員登録（メール認証）→ 既存アカウントがあればログイン
4. 支払い方法を登録（クレジットカード推奨、銀行振込も可）

### 2-B-2. VPS プランを選択

「VPS for ホビー」ではなく「**さくらの VPS**」（標準プラン）を選択。

| 項目 | 推奨値 |
|---|---|
| プラン | **2GB プラン**（約 1,760 円/月）|
| リージョン | 東京 第 1 / 東京 第 2 / 大阪 / 石狩 のいずれか |
| OS | **Ubuntu 24.04 amd64**（標準 OS から選択）|
| 仮想化方式 | 標準（KVM）|
| ストレージ | プラン標準（2GB プランなら 50GB SSD）|
| 支払い周期 | 月額（年額にすると 12% 割引）|
| 初期費用 | 0 円 |

> 💡 **Phase 3（メディアアップロード対応）以降を見据えるなら**、最初から **4GB プラン**（約 3,520 円/月）を選んでおくと安全です。メモリ 2GB は Playwright Chromium 起動でややキツくなります。

### 2-B-3. VPS 起動 + 初期情報の受領

1. 申し込み完了 → 数分で VPS 起動
2. 登録メールアドレスに以下が届く：
   - **IPv4 アドレス**（例: `153.126.131.215`）
   - **初期ユーザ名**: `ubuntu`
   - **初期パスワード**: メール記載
3. さくらの「コントロールパネル」にもログインできることを確認

### 2-B-4. ファイアウォール（標準）

さくら VPS は標準で 22 / 80 / 443 ポートが開放されているので、特別な設定は不要です。

> ⚠️ ただし VPS 内部で `ufw` を有効化する場合は、`sudo ufw allow 22,80,443/tcp` で対応ポートを許可してください。

### 2-B-5. VM に SSH 接続

#### Mac / Linux / WSL の場合

```bash
ssh ubuntu@153.126.131.215
```

初回接続時に「Are you sure you want to continue connecting?」と聞かれたら `yes`。
続いてメールに記載のパスワードを入力（カーソルは動かないが正常）。

#### Windows の場合

- PowerShell で同じ ssh コマンドを実行（Windows 10/11 は ssh 標準搭載）
- もしくは Tera Term / PuTTY などの SSH クライアントを使用

### 2-B-6. SSH 鍵での認証に切り替え（推奨）

パスワード認証は安全性が低いため、初回ログイン後に SSH 鍵を登録します。

**ローカル PC で実行**：

```bash
# 鍵がなければ作成
ssh-keygen -t ed25519 -N "" -f ~/.ssh/id_ed25519

# VPS に公開鍵をコピー
ssh-copy-id ubuntu@153.126.131.215
```

成功後はパスワード入力不要で SSH 接続できます。
さらにセキュリティを高めるなら、VPS 内で `/etc/ssh/sshd_config` を編集し `PasswordAuthentication no` に設定（オプション）。

### 2-B-7. passwordless sudo + スワップ追加（推奨）

メモリ 2GB は Playwright が動くとカツカツになるので、スワップを足します。setup.sh も sudo を多用するのでパスワードレス化します。

VPS にログインした状態で以下を 1 回実行（sudo パスワードを 1 回だけ要求されます）：

```bash
sudo bash -c '
echo "ubuntu ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/90-ubuntu-nopasswd
chmod 440 /etc/sudoers.d/90-ubuntu-nopasswd
fallocate -l 2G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
grep -q /swapfile /etc/fstab || echo "/swapfile none swap sw 0 0" >> /etc/fstab
echo OK
'
```

→ ここから先（Step 3 以降）は **Option A / B 共通**です。

---

## Step 3：ドメインを取得・DNS 設定

### 3-1. ドメインを取得

Threado は HTTPS でアクセスするため独自ドメインが必要です。安価で日本語サポートのある業者：

- お名前.com — https://www.onamae.com/
- ムームードメイン — https://muumuu-domain.com/
- Cloudflare Registrar — https://www.cloudflare.com/products/registrar/

`.com` / `.net` で年 1,500〜2,000 円程度。

### 3-2. DNS A レコードを設定

ドメイン取得業者の管理画面で：

1. DNS 設定 / ネームサーバ設定の画面を開く
2. **A レコード** を 1 つ追加
   - 名前: `threado`（または `@` でドメイン直）
   - 値: VM のパブリック IP（例：`152.69.123.45`）
   - TTL: 3600（デフォルトのまま）
3. 保存

例：ドメイン `example.com` に `threado` という A レコードを足すと、
`threado.example.com` で VM にアクセスできるようになる。

### 3-3. DNS 反映を確認

```bash
# Mac / Linux
nslookup threado.example.com

# Windows
nslookup threado.example.com
```

VM のパブリック IP が返ってくれば OK。**反映に最大 30 分かかります**。

---

## Step 4：Threado をインストール

VM に SSH 接続した状態で、以下を実行します。

### 4-1. Threado を取得

```bash
# ホームディレクトリに移動
cd ~

# Threado v3.0 をダウンロード（β テスター向け配布リポジトリから）
# ※ 配布方法は購入時の案内メールを参照
git clone https://github.com/kai-tecweb/threado-public.git threado
cd threado
```

> 📝 **配布方法は要確認**：β 時点では threado-public を使うが、
> 一般リリース時には別 URL or ライセンスキー連動ダウンロードに切替予定。

### 4-2. セットアップスクリプトを実行

```bash
bash setup.sh
```

このスクリプトが自動で以下を行います：

1. システムパッケージのインストール（Python venv / Caddy 等）
2. Python 仮想環境（venv）作成
3. 依存パッケージインストール
4. Playwright Chromium インストール（リサーチ機能で使用）
5. Caddy（HTTPS 終端用）インストール
6. systemd サービス登録 + 自動起動

実行時間：約 5〜10 分

> ⚠️ パスワード入力（sudo）を求められたら、SSH ログイン時のユーザのパスワード
> （OCI Ubuntu は通常 `ubuntu` ユーザでパスワードレス sudo）を入力。

### 4-3. Caddyfile を編集（ドメインを設定）

```bash
# Caddyfile.example をコピー
sudo cp Caddyfile.example /etc/caddy/Caddyfile

# 編集
sudo nano /etc/caddy/Caddyfile
```

`{$DOMAIN}` の部分を実ドメインに置き換える：

```
threado.example.com {
    log {
        output stdout
        format console
        level INFO
    }
    reverse_proxy localhost:8765 {
        ...（以下既存のまま）
    }
}
```

保存（`Ctrl+O` → Enter → `Ctrl+X`）して終了。

### 4-4. Caddy を起動

```bash
sudo systemctl restart caddy
sudo systemctl enable caddy
```

初回起動時に Let's Encrypt から HTTPS 証明書が自動発行されます（30 秒〜2 分）。

### 4-5. 動作確認

ブラウザで `https://threado.example.com` を開く。

- 🔒 鍵マークが付き HTTPS 接続できれば成功
- 鍵マークが出ない・接続エラーの場合：
  - DNS 反映が完了しているか（Step 3-3 で再確認）
  - VCN セキュリティリスト（Step 2-3）の 80 / 443 開放を確認
  - `sudo systemctl status caddy` で Caddy の状態を確認

---

## Step 5：ライセンスキーを入力

### 5-1. 初回起動画面

ブラウザで Threado にアクセスすると、**ライセンスキー入力画面** が表示されます。

> 📝 Phase 2（ライセンスキー認証）完了後の挙動。Phase 1 段階ではこの画面は無く、
> いきなりメイン画面が開きます。

### 5-2. キーを入力

購入時に送付されたメールに記載のライセンスキー（`XXXX-XXXX-XXXX-XXXX` 形式）を貼り付け。

「認証する」ボタンをクリック。

認証成功すると、メイン画面（ダッシュボード）に遷移します。

### 5-3. 認証情報の保持

一度認証すると、サーバ上に保存され（`data/license_state.json`）、再起動しても再入力不要です。
ライセンスサーバへの heartbeat は 1 日 1 回バックグラウンドで実行されます。

ネット不通の場合も **7 日間は猶予期間** として動作継続します。

---

## Step 6：Threads アカウントを連携

### 6-1. Meta 開発者ポータルでアプリを作成

1. https://developers.facebook.com/ にログイン
2. 「マイアプリ」→ 「アプリを作成」
3. 「ビジネス」を選択
4. アプリ名・連絡先メールを入力
5. **Threads API** を製品に追加
6. App ID / App Secret をメモ
7. **Redirect URI** に Threado の URL を登録：
   `https://threado.example.com/oauth/callback`

### 6-2. OAuth スコープ承認

「アクセス許可とフィーチャー」で以下を承認申請：

| スコープ | 用途 |
|---|---|
| `threads_basic` | 基本情報・トークン更新 |
| `threads_content_publish` | 投稿・予約投稿・スレッド投稿 |
| `threads_keyword_search` | リサーチ機能 |
| `threads_manage_insights` | メトリクス取得 |
| `threads_manage_replies` | 自動返信 |
| `threads_read_replies` | 受信リプライ読み取り |

### 6-3. Threado でアカウント追加

1. Threado の設定画面 → 「アカウント」タブ
2. 「新規アカウント追加」ボタン
3. App ID / App Secret / Redirect URI を入力
4. 「OAuth 認証を開始」をクリック
5. Threads にログインし、アクセスを許可
6. 自動でリダイレクトされ、アカウントが登録される

---

## トラブルシューティング

### Q. setup.sh の途中で止まる

```bash
sudo journalctl -xe
```

でエラー詳細を確認。よくある原因：

- apt update がネットワーク問題で失敗 → リトライ
- ARM64 で対応していないパッケージがある → Issue 報告

### Q. Caddy が HTTPS 証明書を取得しない

```bash
sudo journalctl -u caddy -f
```

でログ確認。よくある原因：

- DNS が反映されていない → 30 分待ってから再起動
- VCN で 80 ポートが閉じている → Let's Encrypt は HTTP-01 challenge で 80 を使う
- レート制限（同一ドメインで何度も発行を試した）→ 1 時間待つ

### Q. Threado が起動しない

```bash
sudo systemctl status threado-main
sudo journalctl -u threado-main -f
```

でログ確認。

### Q. ライセンスが「無効」と表示される

- ライセンスサーバとの通信ができていない → ファイアウォール確認
- キー入力ミス → 購入メールから再コピー
- 期限切れ → 更新料を支払って `update_expires_at` 更新

---

## 運用上の推奨

### バックアップ

`data/` ディレクトリに DB（threads_control_center.sqlite3）・メディア・ライセンス
状態が全て入っています。

OCI Object Storage に毎日バックアップする cron 設定を推奨：

```bash
# /etc/cron.daily/threado-backup
#!/bin/bash
# (具体的なバックアップスクリプトは Phase 5 で別途提供予定)
```

### アップデート

```bash
cd ~/threado
git pull
bash setup.sh   # 依存変更があれば反映される
sudo systemctl restart threado-main
```

> ⚠️ アップデート対象期間（購入から 1 年）を過ぎている場合、
> 動作はしますがサポート対象外となります。

### ログ確認

```bash
# Threado メインサーバのログ
sudo journalctl -u threado-main -f

# Caddy（HTTPS）のログ
sudo journalctl -u caddy -f

# 全部まとめて見る
sudo journalctl -f
```

---

## サポート

- 不具合・質問: support@example.com（β テスター用、後で正式メール）
- ドキュメント: threado-public Git リポジトリ
- ライセンス更新・買い直し: 購入時の案内メールを参照

---

## 改訂履歴

| 日付 | 内容 |
|---|---|
| 2026-05-17 | 初版下書き（Phase 1 実機検証前）|

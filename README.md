<div align="center">

# LiveQ&A (日本語カスタム版)

[![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)](https://developer.mozilla.org/ja/docs/Web/HTML)
[![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white)](https://developer.mozilla.org/ja/docs/Web/CSS)
[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)](https://developer.mozilla.org/ja/docs/Web/JavaScript)
[![Firebase](https://img.shields.io/badge/Firebase-FFCA28?style=for-the-badge&logo=firebase&logoColor=black)](https://firebase.google.com/?hl=ja)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)](LICENSE)

**ワークショップ、カンファレンス、授業、タウンホールミーティング向けのリアルタイム質疑応答（Q&A）＆投票プラットフォーム**

[ライブデモ](https://<YOUR_USERNAME>.github.io/live-qna/) · [バグ報告](https://github.com/<YOUR_USERNAME>/live-qna/issues) · [機能リクエスト](https://github.com/<YOUR_USERNAME>/live-qna/issues)

*※本リポジトリは [alfredang/live-qna](https://github.com/alfredang/live-qna) をフォークし、日本語環境向けにローカライズおよびカスタマイズしたものです。*

</div>

---

## スクリーンショット

![Screenshot](screenshot.png)

## 概要 (About)

LiveQ&Aは、Pigeonhole Liveにインスパイアされたリアルタイムのオーディエンス参加型プラットフォームです。
主催者（ホスト）がQ&Aセッションを作成し、QRコードまたはセッションコードを参加者に共有します。参加者はスマホ等から質問を投稿したり、気になる質問に「いいね（投票）」したりでき、人気の質問がリアルタイムで上位に表示されます。

### 🇯🇵 オリジナル版からの変更点 (Customizations)

- **UIの完全日本語化**: ランディングページ、ホスト用ダッシュボード、参加者ビューの表示テキストやトースト通知をすべて日本語に翻訳
- **日本語フォント・レイアウト調整**: 日本語表示時に自然に見えるようCSSのフォントファミリーや改行ルールを調整
- **CSVエクスポートの改善**: Excel等で開いた際に日本語が文字化けしないよう調整（※必要に応じて記載）
- **類似質問判定の調整**: 日本語の質問文に対応するための調整（※必要に応じて記載）

### 主な機能 (Key Features)

| 機能 | 説明 |
|---------|-------------|
| **QRコード参加** | 参加者はQRコードをスキャンするだけで即座にセッションに参加可能 |
| **リアルタイム同期** | 質問、投票、回答状況がFirebase経由でリアルタイムに更新 |
| **投票（Upvote）機能** | 参加者の投票により、関心度の高い質問が自動的に上位へ浮上 |
| **ホスト用ダッシュボード** | 質問への回答、ピン留め、スポットライト表示、モデレーションが可能 |
| **匿名認証** | アカウント登録不要 — Firebase Anonymous Authで参加者を識別 |
| **ダークモード対応** | OSのシステム設定に連動するダーク/ライトテーマ切り替え |
| **CSVエクスポート** | イベント終了後の振り返り用に、全質問と回答をCSV出力 |
| **重複質問の検知** | 単語の類似度マッチングにより、似たような質問の重複投稿を防止 |
| **モバイルファースト** | 参加者画面はスマートフォンの画面サイズに最適化 |

## 技術スタック (Tech Stack)

| カテゴリ | 使用技術 |
|----------|-----------|
| フロントエンド | HTML5, CSS3, Vanilla JavaScript |
| バックエンド | Firebase Cloud Firestore (リアルタイム NoSQL) |
| 認証 | Firebase Anonymous Authentication (匿名認証) |
| QRコード生成 | qrcode-generator (CDN) |
| ホスティング | GitHub Pages (GitHub Actions 経由) |
| CI/CD | GitHub Actions (デプロイ時のSecret注入) |

## アーキテクチャ (Architecture)

```text
┌─────────────────────────────────────────────────┐
│                   GitHub Pages                  │
│               (Static Hosting + CDN)            │
└──────────────────────┬──────────────────────────┘
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
   index.html      host.html    participant.html
   (トップ画面)   (ホスト管理画面)  (モバイル参加者画面)
        │              │              │
        └──────────────┼──────────────┘
                       ▼
              ┌────────────────┐
              │  firebase-     │
              │  config.js     │
              │  (SDK 初期化)  │
              └───────┬────────┘
                      ▼
        ┌─────────────────────────┐
        │    Firebase Services    │
        ├─────────────────────────┤
        │  Cloud Firestore        │
        │  ├── sessions/          │
        │  │   ├── questions/     │
        │  │   │   └── votes/     │
        │  Anonymous Auth         │
        └─────────────────────────┘
```

## ディレクトリ構成 (Project Structure)

```text
live-qna/
├── index.html              # ランディングページ — セッションの作成・参加
├── host.html               # ホスト用ダッシュボード（管理・モデレーションツール）
├── participant.html        # モバイル最適化された参加者用ビュー
├── css/
│   └── style.css           # スタイルシート（ダークモード対応）
├── js/
│   ├── firebase-config.js  # Firebase初期化（デプロイ時にSecretを注入）
│   ├── utils.js            # 共通ヘルパー（QR生成、トースト通知、時間表示、CSV出力、類似度判定）
│   ├── app.js              # ランディングページのロジック（セッション作成・参加）
│   ├── host.js             # ホスト画面のロジック（モデレーション、回答、エクスポート）
│   └── participant.js      # 参加者画面のロジック（質問投稿、投票、リアルタイム更新）
├── .github/
│   └── workflows/
│       └── deploy.yml      # GitHub Actions — ビルド＆GitHub Pagesへのデプロイ
├── firebase.json           # Firebase Hosting 設定
├── firestore.rules         # Firestore セキュリティルール
├── firestore.indexes.json  # Firestore インデックス設定
└── .gitignore
```

## セットアップ手順 (Getting Started)

### 前提条件 (Prerequisites)

- [Firebase](https://console.firebase.google.com/) プロジェクトを作成し、以下を有効化してください：
  - **Authentication（認証）** で「匿名（Anonymous）」を有効化
  - **Cloud Firestore** データベースを作成
- [GitHub CLI](https://cli.github.com/) （任意：コマンドラインからSecretsを設定する場合）

### ローカル開発環境での実行 (Local Development)

1. **リポジトリのクローン**
   ```bash
   git clone [https://github.com/](https://github.com/)<YOUR_USERNAME>/live-qna.git
   cd live-qna
   ```

2. **Firebase設定の追加**

   `js/firebase-config.js` を編集し、`__FIREBASE_*__` のプレースホルダーをご自身のFirebaseプロジェクトの設定値に書き換えます（※ローカルテスト時のみ。本番用リポジトリにAPIキー等を直接コミットしないよう注意してください）。

3. **ブラウザで開く**
   ```bash
   open index.html
   ```
   または、任意のローカルサーバー（例: `npx serve .` や VS Code の Live Server など）で起動します。

### 環境変数・シークレットの設定 (GitHub Pages デプロイ用)

GitHubリポジトリの **Settings → Secrets and variables → Actions** から、以下の Repository secrets を登録してください：

| シークレット名 | 設定する値 |
|--------|-------|
| `FIREBASE_API_KEY` | Firebase API キー |
| `FIREBASE_AUTH_DOMAIN` | `your-project.firebaseapp.com` |
| `FIREBASE_PROJECT_ID` | Firebase プロジェクト ID |
| `FIREBASE_STORAGE_BUCKET` | `your-project.firebasestorage.app` |
| `FIREBASE_MESSAGING_SENDER_ID` | 送信者 ID (Sender ID) |
| `FIREBASE_APP_ID` | アプリ ID (App ID) |
| `FIREBASE_MEASUREMENT_ID` | 測定 ID (Measurement ID) |

GitHub Actionsのワークフローによってデプロイ時にこれらの値が注入されるため、ソースコード上にシークレットが直接露出することはありません。

## デプロイ (Deployment)

### GitHub Pages (自動デプロイ)

`main` ブランチにプッシュすると、GitHub Actions ワークフローが自動的に以下を実行します：
1. コードのチェックアウト
2. GitHub Environment / Secrets から Firebase の設定値を注入
3. GitHub Pages へデプロイ

### Firebase Hosting (代替手段)

```bash
npm install -g firebase-tools
firebase login
firebase init hosting
firebase deploy
```

## Firestore データモデル (Firestore Data Model)

```text
sessions/{sessionId}
  ├── title              (string: セッション名)
  ├── code               (string: 6文字のユニークコード)
  ├── hostId             (string: ホストのFirebase UID)
  ├── isActive           (boolean: セッション有効状態)
  ├── acceptingQuestions (boolean: 質問受付中フラグ)
  ├── createdAt          (timestamp: 作成日時)
  ├── participantCount   (number: 参加者数)
  │
  └── questions/{questionId}
        ├── text                 (string: 質問本文)
        ├── participantName      (string: 投稿者名)
        ├── participantId        (string: 投稿者のUID)
        ├── createdAt            (timestamp: 投稿日時)
        ├── voteCount            (number: 得票数)
        ├── status               (string: open | answered)
        ├── isPinned             (boolean: ピン留めフラグ)
        ├── isCurrentlyAnswering (boolean: 現在回答中フラグ)
        ├── answer               (string: 回答テキスト)
        │
        └── votes/{participantId}
              ├── voted          (boolean: 投票済みフラグ)
              └── timestamp      (timestamp: 投票日時)
```

## コントリビューション (Contributing)

プルリクエストや改善提案を歓迎します！

1. このリポジトリをフォーク
2. フィーチャーブランチを作成 (`git checkout -b feature/amazing-feature`)
3. 変更をコミット (`git commit -m 'Add amazing feature'`)
4. ブランチにプッシュ (`git push origin feature/amazing-feature`)
5. Pull Request を作成

## 作者・クレジット (Credits)

- **Original Creator**: **Dr. Alfred Ang** ([alfredang/live-qna](https://github.com/alfredang/live-qna))
- **Japanese Localization & Customization**: [<YOUR_USERNAME>](https://github.com/<YOUR_USERNAME>)

## 謝辞 (Acknowledgements)

- [Firebase](https://firebase.google.com/) — リアルタイムバックエンド
- [qrcode-generator](https://github.com/kazuhikoarase/qrcode-generator) — QRコード生成ライブラリ
- [Pigeonhole Live](https://pigeonholelive.com/) — インスピレーション

---

<div align="center">

このプロジェクトが役に立ったら、ぜひオリジナルおよび本リポジトリに ⭐ をお願いします！

</div>

# Hearing Manager (GROW UP)

複数の案件のクライアント向けヒアリングシートを 1 つのサイトで管理する SPA。

- **トップページ** (`index.html`) : プロジェクト一覧 / 新規作成
- **クライアント用URL** (`?p=<slug>`) : クライアントが入力する画面
- **管理用URL** (`?p=<slug>&admin=<passphrase>`) : GROW UP 側が閲覧
- **リセットURL** (`?p=<slug>&reset`) : プロジェクトデータを全削除

スケジュール管理アプリ (`~/Documents/claude/アプリ/スケジュール`) と同じく、
1 つの Firebase プロジェクトに複数プロジェクトのデータを保存する構成。

## Firestore データ構造

```
hearing_projects/{slug}   →  { name, project, purpose, adminPassphrase, createdAt, updatedAt, createdYm }
hearings/{slug}           →  { data: {...フォーム値}, updatedAt }
```

## セットアップ

現在の `index.html` は `voyage-hearing-growup` の Firebase 設定を再利用しています。
そのままだと Firestore のセキュリティルールが厳しく `permission-denied` になります。

**A. 既存の voyage-hearing-growup を使う場合**

Firebase Console → Firestore Database → ルール で `firestore.rules` の内容に差し替え。

```
firebase deploy --only firestore:rules
```

でもデプロイ可能（`.firebaserc` に `voyage-hearing-growup` を指定して）。

**B. 新規 Firebase プロジェクトを作る場合**

1. Firebase Console で新規プロジェクト作成（例: `growup-hearing-manager`）
2. Firestore Database を有効化
3. `index.html` の `window.FIREBASE_CONFIG = {...}` を書き換え
4. `firestore.rules` をデプロイ

## ローカル動作確認

```bash
python3 -m http.server 8765
open http://localhost:8765
```

Firebase 未接続でも localStorage で動作します（プロジェクト一覧・入力データ両方）。

## デプロイ

GitHub Pages を想定：

```bash
git init -b main
git add -A
git commit -m "Initial: hearing manager"
gh repo create growup-do/hearing-manager --public --source=. --push
gh api -X POST repos/growup-do/hearing-manager/pages \
  --raw-field 'source[branch]=main' --raw-field 'source[path]=/'
```

公開URL: `https://growup-do.github.io/hearing-manager/`

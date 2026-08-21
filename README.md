# Hearing Manager (GROW UP)

複数案件のクライアント向けヒアリングシートを 1 つの管理画面で一覧・閲覧・削除する SPA。

公開URL: https://growup-do.github.io/hearing-manager/

## 運用フロー（2026.08改訂）

1. **ヒアリングシートの作成は Claude の `/hearing-sheet` スキルで行う**
   （テンプレは愛沢えみり様／VOYAGE 仕様がベース。管理画面に作成機能は無い）
2. スキルがシートを `sheets/{slug}/` に生成 → GitHub Pages に公開 → Firestore に自動登録
3. 管理画面のカードから2つの入り口に飛べる
   - **📝 クライアント入力** … クライアントに送るURL
   - **🔒 GROW UP 閲覧** … `?admin=<passphrase>` 付きの読み取り専用ビュー（別タブで開く）
4. **削除はカード右上の「×」**（Firestore の meta + 入力データを削除。静的ファイルの削除は別途 push）

## URL 設計

| URL | 画面 |
|---|---|
| `/hearing-manager/` | 管理画面（プロジェクト一覧） |
| `/hearing-manager/sheets/{slug}/` | 各案件のヒアリングシート（新方式） |
| `/hearing-manager/sheets/{slug}/?admin={pass}` | GROW UP 閲覧モード |
| `/hearing-manager/sheets/{slug}/?reset` | データリセット |
| `/hearing-manager/?p={slug}` | （旧方式）SPA内蔵フォーム。voyage の後方互換用 |

外部リポジトリで公開済みのシート（例: `voyage-design-hearing`）は Firestore の
`externalUrl` に登録すればカードの入り口がそちらを向く。

## Firestore（voyage-hearing-growup / 全案件共通）

```
hearing_projects/{slug}  → { name, project, purpose, adminPassphrase, externalUrl, templateId, createdAt, updatedAt, createdYm }
hearings/{slug}          → { data: {...フォーム値}, updatedAt }
```

ルール（deploy 済み）: 両コレクションとも read/write 全開放（URL秘匿運用）。

## ローカル動作確認

```bash
python3 -m http.server 8765
open http://localhost:8765
```

## デプロイ

```bash
git add -A && git commit -m "..." && git push
# GitHub Pages が main ブランチから自動ビルド（30〜60秒）
```

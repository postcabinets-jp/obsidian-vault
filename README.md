# Obsidian Vault - Limitless AI 統合

**Limitless AI Pendant → Obsidian → Claude Code → OpenClaw**

このフォルダは、Limitless AI から自動キャプチャされた情報を整理・分類・実行するための Obsidian Vault です。

## フォルダ構成

```
obsidian-vault/
├── captures/           ← Limitless AI が毎日自動キャプチャ（GitHub Actions）
│   └── YYYY-MM-DD/
│       └── session.md  (filtered: false のステータス)
│
├── filtered/           ← Claude Code が自動分類（limitless-ai-processor）
│   ├── decisions/      ← Layer 1: 決定事項・Goal
│   ├── insights/       ← Layer 2: 参考情報・Background
│   └── reference/
│       └── transcripts/← Layer 3: 記録・その他
│
├── _templates/         ← Obsidian テンプレート
│   ├── _template_goal.md
│   ├── _template_task.md
│   └── _template_project.md
│
└── .obsidian/          ← Obsidian アプリ設定
```

## フロー図

```
┌──────────────────────────────┐
│ Limitless AI Pendant         │
│ （毎日自動キャプチャ）         │
└───────────┬──────────────────┘
            ↓
┌──────────────────────────────┐
│ GitHub Actions               │
│ 09:30 JST 毎日実行           │
│ limitless-ai-to-obsidian-sync│
└───────────┬──────────────────┘
            ↓
   captures/YYYY-MM-DD/
   session.md
   (filtered: false)
            ↓
┌──────────────────────────────┐
│ Claude Code スキル           │
│ limitless-ai-processor       │
│ 自動分類（Layer 1,2,3）       │
└───────────┬──────────────────┘
            ↓
   filtered/
   ├── decisions/
   ├── insights/
   └── reference/transcripts/
            ↓
┌──────────────────────────────┐
│ GitHub 自動 push             │
│ Obsidian に同期              │
└───────────┬──────────────────┘
            ↓
┌──────────────────────────────┐
│ OpenClaw                     │
│ GitHub API で goals/*.md 読む
│ CEO エージェント起動          │
└──────────────────────────────┘
```

## セットアップ

### 必須環境

1. **Limitless AI Pendant**
   - 装着済み
   - API Key 取得済

2. **Obsidian**
   - インストール済
   - このフォルダを Open Vault

3. **GitHub**
   - このリポをクローン可能
   - Personal Access Token 設定済

### 初期化

```bash
# このリポをクローン
git clone https://github.com/postcabinets/obsidian-vault.git
cd obsidian-vault

# Obsidian で Open Vault
# macOS: Obsidian → Open vault folder → このフォルダ選択

# GitHub Actions の設定確認
# https://github.com/postcabinets/limitless-ai-to-obsidian-sync/settings/secrets
# LIMITLESS_API_KEY が設定されているか確認
```

## 自動化フロー

### 毎日 09:30 JST（自動）

1. **GitHub Actions 実行**
   - `limitless-ai-to-obsidian-sync` ワークフロー開始
   - Limitless API から前日のデータ取得

2. **captures/ に保存**
   - `captures/YYYY-MM-DD/session.md` に自動生成
   - frontmatter に `filtered: false` を設定

3. **手動操作不要**
   - Obsidian プラグインのボタンを押す必要なし
   - 自動的に Obsidian に同期

4. **分類実行**（別途）
   ```bash
   cd ~/.openclaw/skills/limitless-ai-processor
   npm start
   ```
   - captures/ → filtered/ に自動分類
   - filtered/ → GitHub に自動 push

## ファイル例

### captures/2026-04-07/session.md

```markdown
---
type: limitless-capture
date: 2026-04-07
session: pendant-sync-2026-04-07
duration: 120
source: limitless-ai-pendant
filtered: false
participants: []
---

# Limitless AI キャプチャ（2026-04-07）

## 記録内容

### Pendant 記録

#### 1. 朝礼
**時間:** 09:00:00 - 09:45:00

ノブ: 「今日の目標は...」

---

## Ask AI チャット

### チャット 1: 議論の要点
**作成:** 2026-04-07 10:30:00

**Q:** 昨日の会議で何が決まった？
**A:** Q2 予算が 150万に決定...
```

### filtered/decisions/2026-04-07-*.md

```markdown
---
type: decision
status: active
priority: 5
owner: ノブ
tags:
  - "#決定事項"
  - "#予算"
deadline: 2026-04-20
created_from_session: pendant-sync-2026-04-07
---

# Q2営業予算は150万に決定

決定内容...
```

## トラブルシューティング

### captures/ に自動でファイルが出現しない

**原因:** GitHub Actions が実行されていない

**確認方法:**
1. https://github.com/postcabinets/limitless-ai-to-obsidian-sync/actions
2. "Daily Limitless AI → Obsidian Sync" の最新実行結果を確認

**解決方法:**
- Secret `LIMITLESS_API_KEY` が設定されているか確認
- API Key が有効か確認（Limitless.ai で確認）
- 手動トリガー: Actions → "Run workflow"

### Obsidian に captures/ が表示されない

**原因:** Git pull が自動実行されていない

**解決方法:**
```bash
# 手動で pull
cd /path/to/obsidian-vault
git pull origin main
```

### filtered/ に自動分類されない

**原因:** limitless-ai-processor が実行されていない

**解決方法:**
```bash
cd ~/.openclaw/skills/limitless-ai-processor
npm start
```

## 重要なメモ

- **正本DB**: GitHub リポ（obsidian-vault + filtered files）
- **スケジュール**: 毎日 09:30 JST に自動実行
- **手動操作**: 通常、不要（完全自動化）
- **エラー監視**: GitHub Actions の実行ログを確認

---

**Last Updated:** 2026-04-06
**Status:** ✅ Automated Sync Running

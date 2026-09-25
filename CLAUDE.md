# CLAUDE.md — ORBIT SWITCH

タップだけで遊ぶワンタップ・アクション。Web（静的サイト・PWA）と iOS（Swift + SpriteKit）の2つの版がある。
ユーザーとは日本語で話す。遊び方と構成は `README.md`、iOS 版は `ios/README.md`、これまでのレビューは `REVIEW_LOG.md`。

## コマンド
```sh
npm ci
npm run lint      # ESLint
npm test          # ユニットテスト＋公平性テスト（node:test）
npm run e2e       # Playwright でスマホ/PC/横向き/オートプレイ/オフラインを検証
npm run gen:ios   # js/i18n.js から iOS の文言ファイルを生成（--check で差分検査）
npm start         # http://localhost:8080
```
iOS のビルドとテストは Mac がなくても、push 時に GitHub Actions（`.github/workflows/ios.yml`）が行う。
push の前に `npm run lint && npm test` を通す。

## コードの約束
1. **Web と iOS は同じゲーム。** ゲームの規則（`js/core.js` ↔ `ios/OrbitSwitch/Core/GameCore.swift`）と経済（`js/meta.js` ↔ `Core/Meta.swift`）を変えたら、両方を直す。乱数と生成規則が一致していないと、デイリーチャレンジのステージが Web と iOS でずれる（XCTest で一致を検証している）
2. **文言は `js/i18n.js` だけに書く。** iOS の `Strings.generated.swift` は `npm run gen:ios` で生成し、手で直さない。日本語と英語の両方を足す
3. **公平性のテストを弱めない。** レベル生成を変えたら `tests/bot.js` のボット（完璧ボット・人間ボット）で確かめる。テストを外したり、条件を緩めたりして通さない
4. **`js/core.js` は DOM に依存させない。** シード付きの乱数で決定的に動き、Node でもテストできる状態を保つ
5. **端末の外にデータを出さない。** 今は通信・計測・通知・課金・広告がない。これを変える変更は、App Store のプライバシー表示と開示に関わるので、先にユーザーに確認する

## 企画・設計・公開前の点検
AI 機能の企画や公開前の点検は、ナレッジベース [kouki0727mickey/addictive-game](https://github.com/kouki0727mickey/addictive-game) に基づいて行う。手元にないので、読み込み用に取得する:

```sh
git clone --depth 1 https://github.com/kouki0727mickey/addictive-game /tmp/ai-ent-kb
```

- 読むのは `/tmp/ai-ent-kb/knowledge/`。入口は `knowledge/00_landscape.md`
- スキルはナレッジベース側にある。このリポジトリでスラッシュコマンドとしては使えないので、該当する `SKILL.md` を読んで、その手順に従う

| やりたいこと | 読む手順書 |
|---|---|
| AI 機能の企画書を作る | `/tmp/ai-ent-kb/.claude/skills/game-concept/SKILL.md` |
| 会話NPC・AIキャラクターを設計する | `/tmp/ai-ent-kb/.claude/skills/game-ai-character/SKILL.md` |
| 公開前に健全性・AI開示・法務・ストア規約を点検する | `/tmp/ai-ent-kb/.claude/skills/game-release-check/SKILL.md` |

- ナレッジベースの作業ルールにも従う（`/tmp/ai-ent-kb/CLAUDE.md` の「作業ルール」）。特に次の4つ:
  - 提案には根拠の記事やパターン番号を添える。事実には出典を付け、仮説には 💡 を付ける
  - `engagement-design.md` のレッドラインを越える設計は提案しない
  - 法務は一般的な情報整理であり、法的助言ではないと明記する
  - Claude API を呼ぶときは、`--dry-run` で見積もり、`claude-haiku-4-5` から、`--max-usd` を付けて流す。API キーは表示・記録しない
- **企画書・設計書・点検結果は `docs/ai-design/` に置く。** 今ある企画書: `docs/ai-design/concept.md`（原本はナレッジベースの `studio/orbit-switch/concept.md`）
- 公開前チェックの記録は、ナレッジベースの `studio/orbit-switch/release-check-2026-09-25.md` にある

## 分かったことをナレッジに戻す
制作で分かったこと（うまくいった設計、失敗、プレイヤーの反応、実測したコスト）は、ナレッジベースに PR で戻す。書き方と戻し先は、ナレッジベースの `CLAUDE.md` の「成果をナレッジに戻す」に従う。

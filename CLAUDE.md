# CLAUDE.md

このファイルは、Claude Code (claude.ai/code) がこのリポジトリで作業する際のガイダンスを提供します。

## プロジェクト概要

**Zettelkasten情報管理システム** - NotionとObsidianを融合させたような知識管理アプリケーション。個人利用からチーム利用まで対応可能な、Markdown対応の情報管理システムです。

思考の断片（走り書き）から体系的な知識（永久保存メモ）への変換を支援し、ノート間のリンクを可視化して知識のつながりを発見します。

## 核心概念

Zettelkasten方式に従った3種類のメモを管理します：

1. **走り書きメモ (Fleeting Notes)**: 一時的なアイデア・思いつきを素早く記録。「メモ済み」にすると自動的に永久保存メモが生成される
2. **文献メモ (Literature Notes)**: 外部ソース（本、記事、Web）からの引用・要約。URL（オプション）を含む
3. **永久保存メモ (Permanent Notes)**: 整理された知識として体系的に保存。原子的（1つの概念＝1メモ）に構成され、他のメモとリンク可能

走り書きメモまたは文献メモを「メモ済み」にすると、永久保存メモが自動的に作成され、元メモはアーカイブされます（削除はしない）。

## 技術スタック

- **Next.js 15** (App Router)
- **React 19**
- **TypeScript 5**
- **Tailwind CSS 4** (新PostCSSアーキテクチャ)
- **Prisma 6** (ORM)
- **Supabase** (PostgreSQL)
- **Auth.js v5** (NextAuth.js v5)
- **Zod 3** (バリデーション)
- **react-markdown 9** + remark-gfm 4 + rehype-highlight 7
- **D3.js 7** (グラフビュー)

## 開発コマンド

```bash
# 開発サーバー起動
npm run dev

# 本番ビルド
npm run build

# 本番サーバー起動
npm run start

# リンター実行
npm run lint

# Prismaマイグレーション生成
npx prisma migrate dev

# Prisma Studio起動（DB管理GUI）
npx prisma studio

# データベースをPrismaスキーマと同期
npx prisma db push
```

## プロジェクトアーキテクチャ

### データベース設計（実装予定）

データモデルの階層構造：

```
User (1:N)
├─ FleetingNote (isDoneフラグ → PermanentNoteを自動生成)
├─ LiteratureNote (isDoneフラグ → PermanentNoteを自動生成)
└─ PermanentNote
   ├─ N:M Category（フラット構造、階層なし）
   ├─ N:M LiteratureNote（引用関係）
   └─ N:M 自己参照（永久保存メモ同士の双方向リンク）
```

実装すべき主要テーブル：
- **認証**: `users`, `accounts`, `sessions`, `verification_tokens`
- **メモ**: `fleeting_notes`, `literature_notes`, `permanent_notes`, `permanent_note_links`
- **カテゴリー**: `categories`, `categories_on_permanent_notes`, `categories_on_literature_notes`
- **リレーション**: `permanent_notes_on_literature_notes`

### 重要なフィールド

- **PermanentNote**: `sourceType`と`sourceId`で元メモを追跡（fleeting/literature/manual）
- **PermanentNote**: `showInGraph`でグラフビューへの表示を制御
- **FleetingNote/LiteratureNote**: `isDone`フラグで永久保存メモの自動作成をトリガー

### Markdownリンク記法

`[[メモタイトル]]`記法（Obsidian/Roam風）を使用して内部リンクを作成。パーサーでこれを検出し、データベースのリレーションシップに変換してバックリンク追跡を実現します。

### グラフビュー実装

D3.jsを使用して永久保存メモとその接続を可視化。ノードはメモ、エッジは双方向リンクを表現。ノードクリックでモーダル表示。カテゴリーやメモタイプでフィルタリング可能。

## UI/UXアーキテクチャ

### レイアウト構造
- **サイドバー** (LINKセクション): カテゴリー、`isDone=true`の文献メモ、永久保存メモ一覧を表示
- **メインエリア**: 3カラムレイアウト（モバイルではタブ切替）で走り書き/文献/永久保存メモを表示
- **モーダルシステム**: 全メモタイプの閲覧・編集用の統一モーダル。プレビューモードと編集モードを切替
- **グラフビュー**: フィルタリングコントロール付きのフルスクリーンD3.js可視化

### モーダルの挙動
- **開き方**: メモカード、グラフのノード、カテゴリーページからクリック
- **2つのモード**: プレビュー（読み取り専用）と編集（リアルタイムMarkdownプレビュー）
- **常に表示**: タイトル、カテゴリー、Markdown本文、タイムスタンプ、下部のバックリンクセクション

## 実装フェーズ

### Phase 1（現在のMVP目標）
- 認証システム（メール/パスワード + Google OAuth）
- 3種類のメモのCRUD操作
- 「メモ済み」チェックボックス → 永久保存メモの自動生成
- 多対多のカテゴリーシステム
- プレビュー/編集モード付きモーダルシステム
- 基本UIレイアウト（サイドバー、メインエリア）
- 静的な検索UI（バックエンド未実装）

### Phase 2
- `[[リンク]]`記法のパースとバックリンク検出
- 基本的なインタラクション機能を持つD3.jsグラフビュー
- 全文検索の実装（Supabase FTSまたはFuse.js）

### Phase 3
- グラフビューのフィルター（カテゴリー別、メモタイプ別）
- チーム機能（ワークスペース、招待）
- エクスポート機能、キーボードショートカット、ダークモード

## パスエイリアス

`tsconfig.json`で設定されている`@/*`を使用して`src`ディレクトリからインポートします：

```typescript
import Component from '@/components/Component'
import { helper } from '@/lib/utils'
```

## ディレクトリ構成

実装時は以下の構造に従ってください：

```
src/
├── app/
│   ├── (auth)/              # 認証ルート
│   │   ├── login/
│   │   └── register/
│   ├── (dashboard)/         # メインアプリ
│   │   ├── page.tsx         # トップページ
│   │   ├── fleeting/        # 走り書きメモ
│   │   ├── literature/      # 文献メモ
│   │   ├── permanent/       # 永久保存メモ
│   │   ├── categories/      # カテゴリー
│   │   └── graph/           # グラフビュー
│   ├── api/
│   │   ├── auth/[...nextauth]/
│   │   ├── fleeting/
│   │   ├── literature/
│   │   ├── permanent/
│   │   ├── categories/
│   │   ├── graph/
│   │   └── search/
│   └── layout.tsx
├── components/
│   ├── layout/
│   │   ├── Sidebar.tsx
│   │   └── Header.tsx
│   ├── notes/
│   │   ├── NoteCard.tsx
│   │   ├── NoteModal.tsx
│   │   ├── NoteEditor.tsx
│   │   ├── NotePreview.tsx
│   │   └── BacklinkSection.tsx
│   ├── graph/
│   │   ├── GraphView.tsx
│   │   ├── GraphFilter.tsx
│   │   └── GraphNodeModal.tsx
│   ├── category/
│   │   ├── CategoryList.tsx
│   │   └── CategoryCard.tsx
│   ├── search/
│   │   └── SearchBar.tsx
│   └── ui/                  # 汎用UI
├── lib/
│   ├── prisma.ts
│   ├── auth.ts
│   ├── markdown.ts
│   ├── link-parser.ts
│   └── graph-data.ts
├── hooks/
│   ├── useNotes.ts
│   ├── useCategories.ts
│   └── useModal.ts
├── types/
│   ├── note.ts
│   ├── category.ts
│   └── graph.ts
└── utils/
    └── validators.ts        # Zodスキーマ
```

## コーディング規約

- UIテキストとコメントは日本語で記述
- Next.js 15 App Router規約に従った厳格なTypeScriptを使用
- ESLintはNext.js core-web-vitalsとTypeScriptプリセットを使用
- Tailwind CSS v4の新しいPostCSSアーキテクチャを使用（`@tailwindcss/postcss`依存）
- Prismaスキーマは`prisma/schema.prisma`に定義

## 重要な設計上の注意点

### メモ済みチェックの実装

走り書きメモまたは文献メモで`isDone`フラグをtrueにした際：
1. 自動的に新しい永久保存メモを作成
2. `sourceType`と`sourceId`に元メモの情報を記録
3. 元メモはデータベースに保持（削除しない）
4. トップページの一覧からは非表示（`isDone = false`のみ表示）

### Markdownエディタの実装

```tsx
<ReactMarkdown
  remarkPlugins={[remarkGfm]}
  rehypePlugins={[rehypeHighlight]}
  skipHtml={false}
  unwrapDisallowed={true}
>
  {content}
</ReactMarkdown>
```

### リンク記法の解析

- `[[メモタイトル]]`を正規表現で検出
- データベースでタイトルを検索
- 該当メモへのリンクに変換
- `permanent_note_links`テーブルに自動登録してバックリンクを追跡

### グラフデータ構造

```typescript
{
  nodes: Array<{
    id: string
    name: string
    connections: number
    group: number  // カテゴリーやメモタイプで色分け
  }>
  links: Array<{
    source: string
    target: string
  }>
}
```

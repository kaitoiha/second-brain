# Zettelkasten 情報管理システム - 要件定義書

## 📋 プロジェクト概要

Notion・Obsidianを融合させたような、Zettelkasten方式のMarkdown対応情報管理システム。
個人利用からチーム利用まで対応可能な、知識のネットワーク化を促進するアプリケーション。

---

## 🎯 システムの目的

- 思考の断片（走り書き）から体系的な知識（永久保存メモ）への変換を支援
- ノート間のリンクを可視化し、知識のつながりを発見
- Markdownベースで軽量かつ柔軟なメモ管理

---

## 🛠️ 技術スタック

### フロントエンド
- **Next.js 14+** (App Router)
- **React 18+**
- **TypeScript**
- **Tailwind CSS**
- **D3.js** (グラフビュー)

### バックエンド
- **Next.js API Routes**
- **Prisma** (ORM)
- **Supabase** (PostgreSQL)

### 認証
- **Auth.js (NextAuth.js v5)**
  - メールアドレス + パスワード
  - Google OAuth

### バリデーション
- **Zod**

### Markdownエディタ
- **react-markdown** (v9.0.3)
- **remark-gfm** (v4.0.0) - GitHub Flavored Markdown
- **rehype-highlight** (v7.0.1) - シンタックスハイライト
- **highlight.js**
- **@tailwindcss/typography** (v0.5.16)

### 検索機能（Phase 2以降）
- 検討中（Supabase FTS / Fuse.js / Algolia など）
- Phase 1では静的実装

---

## 📊 主要機能

### 1. メモの種類（3タイプ）

#### 1.1 走り書きメモ (Fleeting Notes)
- **目的**: 一時的なアイデア・思いつきを素早く記録
- **フィールド**:
  - タイトル
  - 本文（Markdown、null許容）
  - メモ済みフラグ
  - 作成日時、更新日時

#### 1.2 文献メモ (Literature Notes)
- **目的**: 外部ソース（本、記事、Web）からの引用・要約
- **フィールド**:
  - タイトル
  - URL（null許容）
  - 本文（Markdown、null許容）
  - メモ済みフラグ
  - 作成日時、更新日時
- **特徴**: 永久保存メモとのリレーション可能

#### 1.3 永久保存メモ (Permanent Notes)
- **目的**: 整理された知識として体系的に保存
- **フィールド**:
  - タイトル
  - 本文（Markdown）
  - グラフビュー表示フラグ
  - 元メモの種類・ID（sourceType, sourceId）
  - 作成日時、更新日時
- **生成方法**:
  - 走り書きメモの「メモ済み」チェック
  - 文献メモの「メモ済み」チェック
  - 手動作成
- **リレーション**:
  - 他の永久保存メモとの双方向リンク
  - 文献メモとの紐付け
  - カテゴリーとの紐付け

---

### 2. メモ済みチェックの挙動

#### フロー
1. 走り書き/文献メモで「メモ済み」チェックON
2. **自動的に永久保存メモが新規作成**
3. 元メモはアーカイブ（削除せず保持）
4. トップページの一覧から非表示
5. 永久保存メモがトップページに表示

#### 元メモの扱い
- **走り書きメモ**: アーカイブとして保持
- **文献メモ**: 永久保存メモとリレーション可能なため保持

---

### 3. カテゴリー機能（索引メモ）

- **構造**: フラット（階層なし）
- **紐付け**:
  - 永久保存メモに複数カテゴリー紐付け可能
  - 文献メモに複数カテゴリー紐付け可能
- **表示**:
  - カテゴリー一覧ページ（参考UI: Image 2）
  - カテゴリークリック → 紐づいているメモ一覧を表示
- **概念**: タグとカテゴリーは同じ

---

### 4. ノート間リンク機能

#### リンク記法
- **形式**: Markdownライブラリに準拠
- **候補**: `[[メモタイトル]]` (Obsidian/Roam風)
- **解析**: react-markdownのプラグインで実装

#### バックリンク（被リンク）
- **表示場所**: モーダル下部セクション
- **表示内容**:
  - 「このメモにリンクしているメモ: 3件」
  - リンク元メモのリスト（タイトル、プレビュー）

---

### 5. グラフビュー

#### 基本仕様
- **ベース**: 添付のD3.jsコード
- **表示対象**:
  - 永久保存メモ（`showInGraph = true`のもの）
  - ノード間のリンク（双方向リンク）

#### フィルター機能
- **UI**: 画面上部にドロップダウン
- **オプション**:
  - 全て表示
  - 永久保存メモのみ
  - カテゴリー別（例: 「カテゴリー: キーワード1」）
  - 永久保存メモ ↔ 文献メモの関係
- **Phase 1**: 単一フィルターのみ
- **Phase 2以降**: 複数フィルター組み合わせ

#### インタラクション
- **ノードクリック**: モーダルでプレビュー表示
- **編集ボタン**: モーダル内で編集モードに切替
- **ドラッグ**: ノード位置の移動
- **ズーム**: スクロールでズーム
- **パン**: 背景ドラッグで視点移動

#### モバイル対応
- グラフビューは簡易版で実装

---

### 6. モーダル表示

#### 基本機能
- **開き方**:
  - メモカードクリック
  - グラフビューのノードクリック
  - カテゴリーページからメモクリック
- **モード切替**:
  - **プレビューモード**（初期表示、読み取り専用）
  - **編集モード**（「編集」ボタンで切替）

#### 表示内容
- タイトル
- カテゴリータグ
- Markdown本文（プレビュー/編集）
- 作成日時、更新日時
- **バックリンクセクション**（下部）
  - 件数表示
  - リンク元メモのリスト

#### 編集モード
- リアルタイムプレビュー（左右分割 or タブ切替）
- Markdownツールバー（Phase 2以降で検討）

#### UI参考
- 参考画像3のデザイン

---

### 7. 検索機能

#### Phase 1（静的実装）
- 検索UIのみ配置
- バックエンドは後で実装

#### Phase 2以降
- **検索対象**: タイトル + 本文
- **表示形式**: リスト形式
- **追加情報**: メモの種類（走り書き/文献/永久保存）
- **検索ライブラリ候補**:
  - Supabase Full Text Search
  - Fuse.js
  - Algolia（無料枠）

---

### 8. サイドバー（LINKセクション）

#### 構成
- **メモ済み文献一覧**
  - 文献メモの`isDone = true`のみ表示
  - クリック → 一覧ページ（参考UI: Image 1）
  - 編集可能
- **永久保存メモ一覧**
  - 永久保存メモの一覧表示
- **索引メモ（カテゴリー）**
  - カテゴリー一覧
  - クリック → カテゴリー詳細ページ（参考UI: Image 2）
- **DB**
  - 管理機能（Phase 2以降）

---

### 9. トップページ

#### 表示内容
- **走り書きメモ**（`isDone = false`のみ）
- **文献メモ**（`isDone = false`のみ）
- **永久保存メモ**（全件）

#### レイアウト
- 3カラム or タブ切替（デバイスに応じて）
- カード形式で表示
- カードクリック → モーダル表示

---

## 🗂️ データベース設計

### ER図（概念）

```
User (ユーザー)
  ↓ 1:N
├─ FleetingNote (走り書きメモ)
├─ LiteratureNote (文献メモ)
└─ PermanentNote (永久保存メモ)
     ↓ N:M
   Category (カテゴリー)

PermanentNote ←→ LiteratureNote (N:M)
PermanentNote ←→ PermanentNote (N:M 自己参照)
```

### テーブル一覧

#### 認証関連
- `users` - ユーザー
- `accounts` - OAuth連携
- `sessions` - セッション
- `verification_tokens` - メール認証トークン

#### メモ関連
- `fleeting_notes` - 走り書きメモ
- `literature_notes` - 文献メモ
- `permanent_notes` - 永久保存メモ
- `permanent_note_links` - 永久保存メモ同士のリンク

#### カテゴリー関連
- `categories` - カテゴリー
- `categories_on_permanent_notes` - カテゴリー ↔ 永久保存メモ
- `categories_on_literature_notes` - カテゴリー ↔ 文献メモ

#### リレーション
- `permanent_notes_on_literature_notes` - 永久保存メモ ↔ 文献メモ

### 主要フィールド

#### FleetingNote
```typescript
{
  id: string
  title: string
  content?: string
  isDone: boolean
  userId: string
  createdAt: DateTime
  updatedAt: DateTime
}
```

#### LiteratureNote
```typescript
{
  id: string
  title: string
  url?: string
  content?: string
  isDone: boolean
  userId: string
  createdAt: DateTime
  updatedAt: DateTime
}
```

#### PermanentNote
```typescript
{
  id: string
  title: string
  content: string
  showInGraph: boolean
  sourceType?: "fleeting" | "literature" | "manual"
  sourceId?: string
  userId: string
  createdAt: DateTime
  updatedAt: DateTime
}
```

#### Category
```typescript
{
  id: string
  name: string
  userId: string
  createdAt: DateTime
  updatedAt: DateTime
}
```

---

## 📁 ディレクトリ構成

```
zettelkasten-app/
├── prisma/
│   ├── schema.prisma
│   └── migrations/
├── src/
│   ├── app/
│   │   ├── (auth)/              # 認証ルート
│   │   │   ├── login/
│   │   │   └── register/
│   │   ├── (dashboard)/         # メインアプリ
│   │   │   ├── page.tsx         # トップページ
│   │   │   ├── fleeting/        # 走り書きメモ
│   │   │   ├── literature/      # 文献メモ
│   │   │   ├── permanent/       # 永久保存メモ
│   │   │   ├── categories/      # カテゴリー
│   │   │   └── graph/           # グラフビュー
│   │   ├── api/
│   │   │   ├── auth/[...nextauth]/
│   │   │   ├── fleeting/
│   │   │   ├── literature/
│   │   │   ├── permanent/
│   │   │   ├── categories/
│   │   │   ├── graph/
│   │   │   └── search/
│   │   └── layout.tsx
│   ├── components/
│   │   ├── layout/
│   │   │   ├── Sidebar.tsx
│   │   │   └── Header.tsx
│   │   ├── notes/
│   │   │   ├── NoteCard.tsx
│   │   │   ├── NoteModal.tsx
│   │   │   ├── NoteEditor.tsx
│   │   │   ├── NotePreview.tsx
│   │   │   └── BacklinkSection.tsx
│   │   ├── graph/
│   │   │   ├── GraphView.tsx
│   │   │   ├── GraphFilter.tsx
│   │   │   └── GraphNodeModal.tsx
│   │   ├── category/
│   │   │   ├── CategoryList.tsx
│   │   │   └── CategoryCard.tsx
│   │   ├── search/
│   │   │   └── SearchBar.tsx
│   │   └── ui/                  # 汎用UI
│   ├── lib/
│   │   ├── prisma.ts
│   │   ├── auth.ts
│   │   ├── markdown.ts
│   │   ├── link-parser.ts
│   │   └── graph-data.ts
│   ├── hooks/
│   │   ├── useNotes.ts
│   │   ├── useCategories.ts
│   │   └── useModal.ts
│   ├── types/
│   │   ├── note.ts
│   │   ├── category.ts
│   │   └── graph.ts
│   └── utils/
│       └── validators.ts        # Zodスキーマ
├── public/
├── .env
├── next.config.js
├── tailwind.config.js
└── package.json
```

---

## 🚀 実装フェーズ

### Phase 1: MVP（最優先）

#### 1. セットアップ
- [ ] Next.js プロジェクト作成
- [ ] Supabase プロジェクト作成・接続
- [ ] Prisma セットアップ
- [ ] マイグレーション実行

#### 2. 認証機能
- [ ] Auth.js 設定
- [ ] ログイン画面
- [ ] 登録画面
- [ ] Google OAuth

#### 3. メモのCRUD
- [ ] 走り書きメモのCRUD
- [ ] 文献メモのCRUD
- [ ] 永久保存メモのCRUD
- [ ] メモ済みチェック → 永久保存メモ自動作成

#### 4. UI実装
- [ ] レイアウト（サイドバー、ヘッダー）
- [ ] トップページ（メモ一覧）
- [ ] メモカード
- [ ] モーダル（プレビューモード）
- [ ] Markdownエディタ＆プレビュー

#### 5. カテゴリー機能
- [ ] カテゴリーCRUD
- [ ] メモとカテゴリーの紐付け
- [ ] カテゴリー一覧ページ
- [ ] カテゴリー詳細ページ

#### 6. 検索UI（静的）
- [ ] 検索バー配置
- [ ] 検索結果表示枠

---

### Phase 2: 高度な機能

#### 1. ノート間リンク
- [ ] `[[リンク]]` 記法パーサー
- [ ] リンク自動検出
- [ ] バックリンク表示
- [ ] リンク先プレビュー

#### 2. グラフビュー（基本版）
- [ ] D3.js統合
- [ ] 永久保存メモの可視化
- [ ] ノードクリック → モーダル
- [ ] ドラッグ＆ズーム

#### 3. 全文検索
- [ ] 検索ライブラリ選定・実装
- [ ] 検索API実装
- [ ] 検索結果表示

---

### Phase 3: 拡張機能

#### 1. グラフビュー拡張
- [ ] フィルター機能
- [ ] カテゴリー別表示
- [ ] 永久保存 ↔ 文献メモの関係表示
- [ ] グラフからの編集

#### 2. チーム機能
- [ ] ワークスペース
- [ ] 招待機能
- [ ] 共有メモ

#### 3. その他
- [ ] エクスポート機能（Markdown/JSON）
- [ ] ショートカットキー
- [ ] ダークモード

---

## 📱 レスポンシブ対応

- **デスクトップ**: フル機能
- **タブレット**: 2カラムレイアウト
- **スマートフォン**:
  - 1カラムレイアウト
  - ハンバーガーメニュー
  - グラフビュー簡易版

---

## 🎨 UI/UX 参考

### 参考画像
- **Image 1**: 文献メモ一覧ページ
- **Image 2**: カテゴリー（索引メモ）一覧ページ
- **Image 3**: メモ詳細モーダル

### デザイン方針
- シンプル・ミニマル
- Notion風の洗練されたUI
- ダークモード対応（Phase 3）

---

## 🔧 技術的な注意点

### Markdownエディタ
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
- `[[メモタイトル]]` を検出
- データベースでタイトル検索
- 該当メモへのリンクに変換
- バックリンクテーブルに自動登録

### グラフデータ構造
```typescript
{
  nodes: Array<{
    id: string
    name: string
    connections: number
    group: number
  }>
  links: Array<{
    source: string
    target: string
  }>
}
```

---

## 🔐 環境変数

```env
# Database
DATABASE_URL="postgresql://..."

# Auth.js
NEXTAUTH_URL="http://localhost:3000"
NEXTAUTH_SECRET="..."

# Google OAuth
GOOGLE_CLIENT_ID="..."
GOOGLE_CLIENT_SECRET="..."

# Supabase（参考）
NEXT_PUBLIC_SUPABASE_URL="..."
NEXT_PUBLIC_SUPABASE_ANON_KEY="..."
```

---

## 📦 主要パッケージ

```json
{
  "dependencies": {
    "next": "^14.0.0",
    "react": "^18.0.0",
    "react-dom": "^18.0.0",
    "@prisma/client": "^5.0.0",
    "next-auth": "^5.0.0-beta",
    "zod": "^3.22.0",
    "react-markdown": "^9.0.3",
    "remark-gfm": "^4.0.0",
    "rehype-highlight": "^7.0.1",
    "highlight.js": "^11.0.0",
    "@tailwindcss/typography": "^0.5.16",
    "d3": "^7.0.0",
    "@types/d3": "^7.0.0"
  },
  "devDependencies": {
    "prisma": "^5.0.0",
    "typescript": "^5.0.0",
    "@types/node": "^20.0.0",
    "@types/react": "^18.0.0",
    "tailwindcss": "^3.0.0",
    "autoprefixer": "^10.0.0",
    "postcss": "^8.0.0"
  }
}
```

---

## 🎯 成功の指標（KPI）

### Phase 1
- ユーザー登録・ログイン可能
- 3種類のメモが作成・編集・削除可能
- メモ済み → 永久保存メモの変換が動作
- カテゴリー機能が動作
- モーダル表示が動作

### Phase 2
- ノート間リンクが機能
- バックリンク自動検出
- グラフビュー表示
- 全文検索が動作

### Phase 3
- グラフビューフィルター
- チーム機能（複数ユーザー対応）

---

## 📚 参考資料

### Zettelkasten Method
- 永久保存メモは原子的（1つの概念＝1メモ）
- リンクによる知識のネットワーク化
- 継続的な知識の蓄積と発見

### 類似サービス
- **Obsidian**: ローカルファイルベース、プラグイン豊富
- **Notion**: データベース機能、チーム協業
- **Roam Research**: 双方向リンク、デイリーノート

---

## ✅ 開発開始チェックリスト

- [ ] Supabaseアカウント作成
- [ ] GoogleクラウドでOAuth設定
- [ ] 開発環境構築（Node.js, npm/yarn）
- [ ] このドキュメントの理解
- [ ] Prismaスキーマの確認
- [ ] ディレクトリ構成の理解

---

## 📞 質問・確認事項

開発中に不明点があれば、このドキュメントを参照してください。
追加仕様や変更があれば、このファイルを更新してください。

**作成日**: 2025-10-21
**最終更新**: 2025-10-21
**バージョン**: 1.0.0
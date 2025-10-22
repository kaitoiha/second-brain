# CLAUDE.md

このファイルは、Claude Code (claude.ai/code) がこのリポジトリで作業する際のガイダンスを提供します。

## 📋 実装チケットの進捗管理

このプロジェクトでは`/docs`ディレクトリ内のチケットファイルで実装タスクを管理しています。

### チケット管理のルール
1. **各チケットファイルの構成**:
   - ステータス: `[ ]` 未着手 / `[x]` 完了
   - タスク: 実装すべき詳細タスクのチェックリスト
   - 完了条件: チケットを完了とみなす条件

2. **タスク実装時の手順**:
   - チケット内のタスクを1つずつ実装
   - タスク完了時、必ず `- [ ]` を `- [x]` に更新
   - **すべてのタスクチェックボックスを必ず確認・更新すること**
   - チケット内のすべてのタスクが完了したら、上部のステータスも `[x]` に変更

3. **進捗確認**:
   - `docs/README.md` で全体の進捗を確認
   - 各チケットファイルで詳細な進捗を確認

4. **重要**:
   - タスク完了時、該当するチェックボックスを **必ず更新** すること
   - チェックボックスの更新を忘れると進捗が正しく追跡できない

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

## Next.js App Router ベストプラクティス

### サーバーコンポーネントとクライアントコンポーネント

#### デフォルトはServer Components
- **すべてのコンポーネントはデフォルトでServer Components**として実装
- データフェッチやバックエンドリソースへのアクセスはServer Componentsで行う
- `'use client'`は必要な場合のみ使用

#### Client Componentsが必要なケース
以下の機能を使用する場合のみ`'use client'`を追加：
- `useState`, `useEffect`, `useReducer`などのReact Hooks
- イベントハンドラー（`onClick`, `onChange`など）
- ブラウザAPI（`localStorage`, `window`など）
- カスタムフック
- React Context

#### コンポーネント設計の推奨パターン
```typescript
// ✅ 良い例: Server ComponentでデータフェッチしてClient Componentに渡す
// app/notes/page.tsx (Server Component)
export default async function NotesPage() {
  const notes = await fetchNotes(); // サーバーで実行
  return <NoteList notes={notes} />; // Client Componentにpropsで渡す
}

// components/notes/NoteList.tsx (Client Component)
'use client';
export function NoteList({ notes }) {
  const [selected, setSelected] = useState(null);
  // インタラクティブな処理
}

// ❌ 悪い例: Client ComponentでデータフェッチするのはNG
'use client';
export function NoteList() {
  const [notes, setNotes] = useState([]);
  useEffect(() => {
    fetch('/api/notes').then(/* ... */); // クライアント側で不必要なフェッチ
  }, []);
}
```

### データフェッチング

#### Server Componentsでの直接フェッチ
```typescript
// ✅ 推奨: Server ComponentでPrisma直接使用
import { prisma } from '@/lib/prisma';

export default async function NotesPage() {
  const notes = await prisma.permanentNote.findMany({
    where: { userId: session.user.id },
    include: { categories: true }
  });

  return <NoteList notes={notes} />;
}
```

#### API Routesの使用判断
以下の場合にAPI Routesを使用：
- クライアント側からのミューテーション（POST/PUT/DELETE）
- Webhookやサードパーティからのリクエスト受信
- 複雑なビジネスロジックの分離

#### Server Actionsの活用
フォーム送信やミューテーションには**Server Actions**を優先的に使用：

```typescript
// app/notes/actions.ts
'use server';

import { revalidatePath } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function createNote(formData: FormData) {
  const title = formData.get('title') as string;
  const content = formData.get('content') as string;

  await prisma.fleetingNote.create({
    data: { title, content, userId: session.user.id }
  });

  revalidatePath('/notes');
}

// components/notes/NoteForm.tsx
'use client';

import { createNote } from '@/app/notes/actions';

export function NoteForm() {
  return (
    <form action={createNote}>
      <input name="title" />
      <textarea name="content" />
      <button type="submit">作成</button>
    </form>
  );
}
```

### ルーティングとレイアウト

#### ルートグループの活用
認証状態や異なるレイアウトで`()`を使用：
```
app/
├── (auth)/
│   ├── login/page.tsx
│   └── register/page.tsx
└── (dashboard)/
    ├── layout.tsx         # 認証後のレイアウト
    └── page.tsx
```

#### レイアウトでの共通処理
- 認証チェックはレイアウトで実施
- 共通のサイドバーやヘッダーはレイアウトに配置
- `loading.tsx`でローディング状態を管理
- `error.tsx`でエラーハンドリング

```typescript
// app/(dashboard)/layout.tsx
export default async function DashboardLayout({ children }) {
  const session = await auth();
  if (!session) redirect('/login');

  return (
    <div className="flex">
      <Sidebar />
      <main>{children}</main>
    </div>
  );
}
```

### メタデータとSEO

#### 静的メタデータ
```typescript
// app/(dashboard)/notes/page.tsx
export const metadata = {
  title: 'メモ一覧',
  description: 'あなたの全てのメモを管理',
};
```

#### 動的メタデータ
```typescript
// app/notes/[id]/page.tsx
export async function generateMetadata({ params }) {
  const note = await prisma.permanentNote.findUnique({
    where: { id: params.id }
  });

  return {
    title: note.title,
    description: note.content.slice(0, 100),
  };
}
```

### パフォーマンス最適化

#### 画像の最適化
```tsx
import Image from 'next/image';

// ✅ 推奨
<Image
  src="/images/logo.png"
  alt="ロゴ"
  width={200}
  height={50}
  priority={true} // LCPに重要な画像に使用
/>
```

#### 動的インポート
重いコンポーネントは遅延ロード：
```typescript
import dynamic from 'next/dynamic';

// グラフビューは重いのでクライアントサイドで遅延ロード
const GraphView = dynamic(() => import('@/components/graph/GraphView'), {
  loading: () => <p>グラフを読み込み中...</p>,
  ssr: false // SSRをスキップ
});
```

#### キャッシングとリバリデーション
```typescript
// 60秒ごとに再検証
export const revalidate = 60;

// または個別にfetchで指定
const data = await fetch('https://api.example.com', {
  next: { revalidate: 3600 } // 1時間
});

// Server Actionsでの再検証
import { revalidatePath, revalidateTag } from 'next/cache';

revalidatePath('/notes'); // 特定パスを再検証
revalidateTag('notes'); // タグ付きキャッシュを再検証
```

### ファイル規約

#### 特殊ファイル
- `page.tsx` - ルートのUIを定義
- `layout.tsx` - 共通レイアウト
- `loading.tsx` - ローディングUI（Suspenseの自動ラップ）
- `error.tsx` - エラーUI（Error Boundaryの自動ラップ）
- `not-found.tsx` - 404ページ
- `route.ts` - API Route Handler

#### プライベートフォルダ
`_folder`で始まるフォルダはルーティングから除外：
```
app/
├── _lib/          # ルーティング対象外
├── _components/   # ルーティング対象外
└── notes/
    └── page.tsx   # /notes としてアクセス可能
```

### 環境変数

#### 命名規則
- **クライアント側**: `NEXT_PUBLIC_`プレフィックスが必須
- **サーバー側のみ**: プレフィックスなし

```env
# サーバー側のみ
DATABASE_URL="postgresql://..."
NEXTAUTH_SECRET="..."

# クライアント・サーバー両方
NEXT_PUBLIC_SUPABASE_URL="..."
```

#### 使用方法
```typescript
// Server Component または API Route
const dbUrl = process.env.DATABASE_URL; // OK

// Client Component
const apiUrl = process.env.NEXT_PUBLIC_API_URL; // OK
const dbUrl = process.env.DATABASE_URL; // undefined（アクセス不可）
```

### 型安全性

#### Prisma型の活用
```typescript
import { Prisma } from '@prisma/client';

// Prisma生成型を使用
type NoteWithCategories = Prisma.PermanentNoteGetPayload<{
  include: { categories: true }
}>;

// コンポーネントのpropsに使用
interface NoteCardProps {
  note: NoteWithCategories;
}
```

#### Zodでのバリデーション
```typescript
// utils/validators.ts
import { z } from 'zod';

export const createNoteSchema = z.object({
  title: z.string().min(1, 'タイトルは必須です').max(200),
  content: z.string().optional(),
  categoryIds: z.array(z.string()).optional(),
});

export type CreateNoteInput = z.infer<typeof createNoteSchema>;
```

### セキュリティ

#### 認証チェック
```typescript
// Server ComponentまたはAPI Routeで必ず認証確認
import { auth } from '@/lib/auth';

export default async function ProtectedPage() {
  const session = await auth();
  if (!session) redirect('/login');

  // 認証済みユーザーのみアクセス可能な処理
}
```

#### CSRFトークン
Server Actionsは自動的にCSRF保護されるため追加実装不要。

#### XSS対策
Reactが自動でエスケープするが、`dangerouslySetInnerHTML`は避ける：
```tsx
// ✅ 推奨: react-markdownを使用
<ReactMarkdown>{content}</ReactMarkdown>

// ❌ 避ける
<div dangerouslySetInnerHTML={{ __html: content }} />
```

## Supabase ベストプラクティス

### データベース接続

#### Prismaとの統合
PrismaをORMとして使用し、SupabaseのPostgreSQLに接続：

```typescript
// prisma/schema.prisma
datasource db {
  provider  = "postgresql"
  url       = env("DATABASE_URL")
  directUrl = env("DIRECT_URL") // Connection Poolingを回避する場合
}

generator client {
  provider = "prisma-client-js"
}
```

```env
# .env
DATABASE_URL="postgresql://postgres:[PASSWORD]@db.[PROJECT_REF].supabase.co:5432/postgres?pgbouncer=true"
DIRECT_URL="postgresql://postgres:[PASSWORD]@db.[PROJECT_REF].supabase.co:5432/postgres"
```

#### Prismaクライアントのシングルトン化
開発時の複数インスタンス生成を防ぐ：

```typescript
// lib/prisma.ts
import { PrismaClient } from '@prisma/client';

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined;
};

export const prisma =
  globalForPrisma.prisma ??
  new PrismaClient({
    log: process.env.NODE_ENV === 'development' ? ['query', 'error', 'warn'] : ['error'],
  });

if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma;
```

### Row Level Security (RLS)

#### RLSポリシーの設定
Supabaseでは必ずRLSを有効化してセキュリティを確保：

```sql
-- fleeting_notesテーブルの例
ALTER TABLE fleeting_notes ENABLE ROW LEVEL SECURITY;

-- ユーザーは自分のメモのみ参照可能
CREATE POLICY "Users can view their own fleeting notes"
  ON fleeting_notes
  FOR SELECT
  USING (auth.uid() = user_id);

-- ユーザーは自分のメモのみ作成可能
CREATE POLICY "Users can create their own fleeting notes"
  ON fleeting_notes
  FOR INSERT
  WITH CHECK (auth.uid() = user_id);

-- ユーザーは自分のメモのみ更新可能
CREATE POLICY "Users can update their own fleeting notes"
  ON fleeting_notes
  FOR UPDATE
  USING (auth.uid() = user_id)
  WITH CHECK (auth.uid() = user_id);

-- ユーザーは自分のメモのみ削除可能
CREATE POLICY "Users can delete their own fleeting notes"
  ON fleeting_notes
  FOR DELETE
  USING (auth.uid() = user_id);
```

#### Service Roleの使用
RLSをバイパスする必要がある場合（管理機能など）：

```typescript
// lib/supabase-admin.ts
import { createClient } from '@supabase/supabase-js';

// Service Role Keyは絶対にクライアント側に公開しない
export const supabaseAdmin = createClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL!,
  process.env.SUPABASE_SERVICE_ROLE_KEY!, // サーバー側のみ
  {
    auth: {
      autoRefreshToken: false,
      persistSession: false
    }
  }
);
```

### マイグレーション管理

#### Prisma Migrateの使用
```bash
# マイグレーション作成
npx prisma migrate dev --name add_categories

# 本番環境へのデプロイ
npx prisma migrate deploy
```

#### Supabase CLIとの併用
Supabase特有の機能（Storage、Edge Functionsなど）：

```bash
# Supabaseプロジェクトと連携
npx supabase init
npx supabase link --project-ref [PROJECT_REF]

# ローカルでSupabaseを起動
npx supabase start

# マイグレーションの作成
npx supabase migration new add_rls_policies
```

### リアルタイム機能

このアプリでは当面不要だが、将来的な拡張の場合：

```typescript
// リアルタイム購読（チーム機能で使用可能）
'use client';

import { useEffect } from 'react';
import { createClient } from '@supabase/supabase-js';

export function useRealtimeNotes() {
  useEffect(() => {
    const supabase = createClient(
      process.env.NEXT_PUBLIC_SUPABASE_URL!,
      process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
    );

    const channel = supabase
      .channel('notes-changes')
      .on(
        'postgres_changes',
        {
          event: '*',
          schema: 'public',
          table: 'permanent_notes'
        },
        (payload) => {
          console.log('Change received!', payload);
          // 状態を更新
        }
      )
      .subscribe();

    return () => {
      supabase.removeChannel(channel);
    };
  }, []);
}
```

### バックアップとリストア

```bash
# データベースバックアップ（Supabase Dashboard推奨）
# または pg_dump を使用
pg_dump "postgresql://postgres:[PASSWORD]@db.[PROJECT_REF].supabase.co:5432/postgres" > backup.sql

# リストア
psql "postgresql://postgres:[PASSWORD]@db.[PROJECT_REF].supabase.co:5432/postgres" < backup.sql
```

## Auth.js (NextAuth.js v5) ベストプラクティス

### 基本セットアップ

#### Auth.js設定ファイル
```typescript
// lib/auth.ts
import NextAuth from "next-auth";
import Google from "next-auth/providers/google";
import Credentials from "next-auth/providers/credentials";
import { PrismaAdapter } from "@auth/prisma-adapter";
import { prisma } from "./prisma";
import bcrypt from "bcryptjs";

export const { handlers, auth, signIn, signOut } = NextAuth({
  adapter: PrismaAdapter(prisma),
  session: {
    strategy: "jwt", // JWTを使用（Supabaseとの相性が良い）
  },
  pages: {
    signIn: "/login",
    error: "/login",
  },
  providers: [
    Google({
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
      allowDangerousEmailAccountLinking: true, // メールアドレスでのアカウント統合
    }),
    Credentials({
      credentials: {
        email: { label: "Email", type: "email" },
        password: { label: "Password", type: "password" },
      },
      async authorize(credentials) {
        if (!credentials?.email || !credentials?.password) {
          return null;
        }

        const user = await prisma.user.findUnique({
          where: { email: credentials.email as string },
        });

        if (!user || !user.hashedPassword) {
          return null;
        }

        const isValid = await bcrypt.compare(
          credentials.password as string,
          user.hashedPassword
        );

        if (!isValid) {
          return null;
        }

        return {
          id: user.id,
          email: user.email,
          name: user.name,
        };
      },
    }),
  ],
  callbacks: {
    async jwt({ token, user }) {
      // JWTトークンにユーザーIDを追加
      if (user) {
        token.userId = user.id;
      }
      return token;
    },
    async session({ session, token }) {
      // セッションにユーザーIDを追加
      if (session.user) {
        session.user.id = token.userId as string;
      }
      return session;
    },
  },
});
```

#### API Route Handler
```typescript
// app/api/auth/[...nextauth]/route.ts
import { handlers } from "@/lib/auth";

export const { GET, POST } = handlers;
```

#### 型定義の拡張
```typescript
// types/next-auth.d.ts
import NextAuth from "next-auth";

declare module "next-auth" {
  interface Session {
    user: {
      id: string;
      email: string;
      name?: string;
      image?: string;
    };
  }
}
```

### 認証の実装パターン

#### Server Componentsでの認証チェック
```typescript
// app/(dashboard)/page.tsx
import { auth } from "@/lib/auth";
import { redirect } from "next/navigation";

export default async function DashboardPage() {
  const session = await auth();

  if (!session) {
    redirect("/login");
  }

  // 認証済みユーザーのみアクセス可能
  return <div>ようこそ、{session.user.name}さん</div>;
}
```

#### Middlewareでの認証保護
```typescript
// middleware.ts
import { auth } from "@/lib/auth";
import { NextResponse } from "next/server";

export default auth((req) => {
  const isLoggedIn = !!req.auth;
  const isAuthPage = req.nextUrl.pathname.startsWith("/login") ||
                     req.nextUrl.pathname.startsWith("/register");

  if (isAuthPage) {
    if (isLoggedIn) {
      // ログイン済みユーザーは認証ページにアクセスできない
      return NextResponse.redirect(new URL("/", req.url));
    }
    return NextResponse.next();
  }

  if (!isLoggedIn) {
    // 未認証ユーザーは保護されたページにアクセスできない
    return NextResponse.redirect(new URL("/login", req.url));
  }

  return NextResponse.next();
});

export const config = {
  matcher: ["/((?!api|_next/static|_next/image|favicon.ico).*)"],
};
```

#### Client Componentsでのセッション取得
```typescript
// app/providers.tsx
'use client';

import { SessionProvider } from "next-auth/react";

export function Providers({ children }: { children: React.ReactNode }) {
  return <SessionProvider>{children}</SessionProvider>;
}

// app/layout.tsx
import { Providers } from "./providers";

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <Providers>{children}</Providers>
      </body>
    </html>
  );
}

// components/Header.tsx
'use client';

import { useSession, signOut } from "next-auth/react";

export function Header() {
  const { data: session } = useSession();

  if (!session) return null;

  return (
    <header>
      <p>{session.user.email}</p>
      <button onClick={() => signOut()}>ログアウト</button>
    </header>
  );
}
```

### ログイン・登録の実装

#### ログインフォーム（Server Actions使用）
```typescript
// app/(auth)/login/page.tsx
import { signIn } from "@/lib/auth";
import { redirect } from "next/navigation";

export default function LoginPage() {
  async function handleCredentialsLogin(formData: FormData) {
    "use server";

    const email = formData.get("email") as string;
    const password = formData.get("password") as string;

    const result = await signIn("credentials", {
      email,
      password,
      redirect: false,
    });

    if (result?.error) {
      // エラーハンドリング
      return { error: "ログインに失敗しました" };
    }

    redirect("/");
  }

  async function handleGoogleLogin() {
    "use server";
    await signIn("google", { redirectTo: "/" });
  }

  return (
    <div>
      <h1>ログイン</h1>

      {/* 認証情報でログイン */}
      <form action={handleCredentialsLogin}>
        <input name="email" type="email" placeholder="メールアドレス" required />
        <input name="password" type="password" placeholder="パスワード" required />
        <button type="submit">ログイン</button>
      </form>

      {/* Googleでログイン */}
      <form action={handleGoogleLogin}>
        <button type="submit">Googleでログイン</button>
      </form>
    </div>
  );
}
```

#### ユーザー登録
```typescript
// app/(auth)/register/actions.ts
"use server";

import { prisma } from "@/lib/prisma";
import bcrypt from "bcryptjs";
import { signIn } from "@/lib/auth";
import { z } from "zod";

const registerSchema = z.object({
  name: z.string().min(1, "名前は必須です"),
  email: z.string().email("有効なメールアドレスを入力してください"),
  password: z.string().min(8, "パスワードは8文字以上である必要があります"),
});

export async function registerUser(formData: FormData) {
  const validatedFields = registerSchema.safeParse({
    name: formData.get("name"),
    email: formData.get("email"),
    password: formData.get("password"),
  });

  if (!validatedFields.success) {
    return { error: "入力内容が正しくありません" };
  }

  const { name, email, password } = validatedFields.data;

  // 既存ユーザーチェック
  const existingUser = await prisma.user.findUnique({
    where: { email },
  });

  if (existingUser) {
    return { error: "このメールアドレスは既に登録されています" };
  }

  // パスワードのハッシュ化
  const hashedPassword = await bcrypt.hash(password, 10);

  // ユーザー作成
  await prisma.user.create({
    data: {
      name,
      email,
      hashedPassword,
    },
  });

  // 自動ログイン
  await signIn("credentials", {
    email,
    password,
    redirect: false,
  });

  return { success: true };
}
```

### セキュリティのベストプラクティス

#### 環境変数の設定
```env
# .env
NEXTAUTH_URL="http://localhost:3000"
NEXTAUTH_SECRET="your-secret-key-here" # openssl rand -base64 32 で生成

# Google OAuth
GOOGLE_CLIENT_ID="your-google-client-id"
GOOGLE_CLIENT_SECRET="your-google-client-secret"

# Database
DATABASE_URL="postgresql://..."
```

#### パスワードのハッシュ化
```typescript
import bcrypt from "bcryptjs";

// 登録時
const hashedPassword = await bcrypt.hash(password, 10);

// ログイン時の検証
const isValid = await bcrypt.compare(password, user.hashedPassword);
```

#### CSRFトークン保護
Auth.js v5は自動的にCSRF保護を提供。Server Actionsも自動保護。

#### セッション管理
```typescript
// JWTストラテジーを使用（推奨）
session: {
  strategy: "jwt",
  maxAge: 30 * 24 * 60 * 60, // 30日
}

// データベースセッションを使用する場合
session: {
  strategy: "database",
  maxAge: 30 * 24 * 60 * 60,
  updateAge: 24 * 60 * 60, // 24時間ごとに更新
}
```

### Server Actionsでの認証利用

```typescript
// app/notes/actions.ts
"use server";

import { auth } from "@/lib/auth";
import { prisma } from "@/lib/prisma";
import { revalidatePath } from "next/cache";

export async function createNote(formData: FormData) {
  // 認証チェック
  const session = await auth();
  if (!session?.user?.id) {
    throw new Error("認証が必要です");
  }

  const title = formData.get("title") as string;
  const content = formData.get("content") as string;

  // ユーザーIDを自動的に設定
  await prisma.fleetingNote.create({
    data: {
      title,
      content,
      userId: session.user.id, // セッションから取得
    },
  });

  revalidatePath("/");
}
```

### API Routesでの認証

```typescript
// app/api/notes/route.ts
import { auth } from "@/lib/auth";
import { prisma } from "@/lib/prisma";
import { NextResponse } from "next/server";

export async function GET(request: Request) {
  const session = await auth();

  if (!session?.user?.id) {
    return NextResponse.json({ error: "Unauthorized" }, { status: 401 });
  }

  const notes = await prisma.permanentNote.findMany({
    where: { userId: session.user.id },
  });

  return NextResponse.json(notes);
}
```

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

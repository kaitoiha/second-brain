# #001 プロジェクトセットアップ

## ステータス
[x] 完了

## 概要
Next.js、Prisma、Supabaseの基本セットアップを行い、開発環境を構築する。

## タスク

### 環境変数の設定
- [x] `.env.example`ファイルを作成
- [x] `.env`ファイルに必要な環境変数を設定
  - [x] `DATABASE_URL`
  - [x] `DIRECT_URL`
  - [x] `NEXTAUTH_URL`
  - [x] `NEXTAUTH_SECRET` (生成: `openssl rand -base64 32`)

### Supabaseプロジェクトの作成
- [x] Supabaseアカウントを作成
- [x] 新規プロジェクトを作成
- [x] データベース接続情報を取得
- [x] `.env`にSupabase接続情報を設定

### 必要なパッケージのインストール
- [x] Prisma関連
  ```bash
  npm install @prisma/client
  npm install -D prisma
  ```
- [x] Auth.js関連
  ```bash
  npm install next-auth@beta @auth/prisma-adapter
  npm install bcryptjs
  npm install -D @types/bcryptjs
  ```
- [x] バリデーション
  ```bash
  npm install zod
  ```
- [x] Markdown関連
  ```bash
  npm install react-markdown remark-gfm rehype-highlight highlight.js
  ```
- [x] D3.js関連
  ```bash
  npm install d3
  npm install -D @types/d3
  ```

### Prismaの初期化
- [x] Prismaの初期化
  ```bash
  npx prisma init
  ```
- [x] `prisma/schema.prisma`の`datasource`設定を確認

### lib/prisma.tsの作成
- [x] Prismaクライアントのシングルトンファイルを作成
- [x] 開発環境でのHot Reload対応

## 完了条件
- [x] すべての依存パッケージがインストールされている
- [x] Supabaseプロジェクトが作成され、接続情報が設定されている
- [x] 環境変数ファイルが正しく設定されている
- [x] Prismaクライアントが正常に動作する

## 参考
- CLAUDE.md: プロジェクト概要、技術スタック
- CLAUDE.md: Supabaseベストプラクティス

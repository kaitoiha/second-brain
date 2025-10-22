# #001 プロジェクトセットアップ

## ステータス
[ ] 未着手

## 概要
Next.js、Prisma、Supabaseの基本セットアップを行い、開発環境を構築する。

## タスク

### 環境変数の設定
- [ ] `.env.example`ファイルを作成
- [ ] `.env`ファイルに必要な環境変数を設定
  - [ ] `DATABASE_URL`
  - [ ] `DIRECT_URL`
  - [ ] `NEXTAUTH_URL`
  - [ ] `NEXTAUTH_SECRET` (生成: `openssl rand -base64 32`)

### Supabaseプロジェクトの作成
- [ ] Supabaseアカウントを作成
- [ ] 新規プロジェクトを作成
- [ ] データベース接続情報を取得
- [ ] `.env`にSupabase接続情報を設定

### 必要なパッケージのインストール
- [ ] Prisma関連
  ```bash
  npm install @prisma/client
  npm install -D prisma
  ```
- [ ] Auth.js関連
  ```bash
  npm install next-auth@beta @auth/prisma-adapter
  npm install bcryptjs
  npm install -D @types/bcryptjs
  ```
- [ ] バリデーション
  ```bash
  npm install zod
  ```
- [ ] Markdown関連
  ```bash
  npm install react-markdown remark-gfm rehype-highlight highlight.js
  ```
- [ ] D3.js関連
  ```bash
  npm install d3
  npm install -D @types/d3
  ```

### Prismaの初期化
- [ ] Prismaの初期化
  ```bash
  npx prisma init
  ```
- [ ] `prisma/schema.prisma`の`datasource`設定を確認

### lib/prisma.tsの作成
- [ ] Prismaクライアントのシングルトンファイルを作成
- [ ] 開発環境でのHot Reload対応

## 完了条件
- [ ] すべての依存パッケージがインストールされている
- [ ] Supabaseプロジェクトが作成され、接続情報が設定されている
- [ ] 環境変数ファイルが正しく設定されている
- [ ] Prismaクライアントが正常に動作する

## 参考
- CLAUDE.md: プロジェクト概要、技術スタック
- CLAUDE.md: Supabaseベストプラクティス

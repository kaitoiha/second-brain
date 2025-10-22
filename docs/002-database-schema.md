# #002 データベーススキーマ設計

## ステータス
[ ] 未着手

## 概要
Prismaスキーマを定義し、認証とメモ管理に必要なテーブルを作成する。

## タスク

### Prismaスキーマの作成
- [ ] `prisma/schema.prisma`にモデルを定義

#### 認証関連モデル
- [ ] `User`モデル
  - id, name, email, emailVerified, image, hashedPassword, createdAt, updatedAt
- [ ] `Account`モデル (OAuth連携用)
- [ ] `Session`モデル
- [ ] `VerificationToken`モデル

#### メモ関連モデル
- [ ] `FleetingNote`モデル（走り書きメモ）
  - id, title, content, isDone, userId, createdAt, updatedAt
- [ ] `LiteratureNote`モデル（文献メモ）
  - id, title, url, content, isDone, userId, createdAt, updatedAt
- [ ] `PermanentNote`モデル（永久保存メモ）
  - id, title, content, showInGraph, sourceType, sourceId, userId, createdAt, updatedAt

#### カテゴリー関連モデル
- [ ] `Category`モデル
  - id, name, userId, createdAt, updatedAt
- [ ] `CategoriesOnPermanentNotes`モデル (中間テーブル)
- [ ] `CategoriesOnLiteratureNotes`モデル (中間テーブル)

#### リレーション
- [ ] `PermanentNoteLink`モデル（永久保存メモ同士のリンク）
  - id, fromId, toId, createdAt
- [ ] `PermanentNotesOnLiteratureNotes`モデル（永久保存メモと文献メモの関連）
  - permanentNoteId, literatureNoteId, assignedAt

### マイグレーションの実行
- [ ] 初回マイグレーションを生成
  ```bash
  npx prisma migrate dev --name init
  ```
- [ ] Prismaクライアントを生成
  ```bash
  npx prisma generate
  ```

### RLSポリシーの設定（Supabase）
- [ ] `fleeting_notes`テーブルのRLS有効化
- [ ] `literature_notes`テーブルのRLS有効化
- [ ] `permanent_notes`テーブルのRLS有効化
- [ ] `categories`テーブルのRLS有効化
- [ ] 各テーブルにSELECT/INSERT/UPDATE/DELETEポリシーを設定

### 動作確認
- [ ] Prisma Studioでスキーマを確認
  ```bash
  npx prisma studio
  ```
- [ ] テストデータを手動で挿入して動作確認

## 完了条件
- [ ] すべてのテーブルが作成されている
- [ ] リレーションが正しく設定されている
- [ ] マイグレーションが成功している
- [ ] RLSポリシーが設定されている

## 参考
- README.md: データベース設計
- CLAUDE.md: データベース設計（実装予定）
- CLAUDE.md: Supabase RLS

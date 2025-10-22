# #009 カテゴリー機能の実装

## ステータス
[ ] 未着手

## 概要
カテゴリー（索引メモ）の作成・管理・メモへの紐付け機能を実装する。

## タスク

### Server Actions
- [ ] `app/(dashboard)/categories/actions.ts`を作成
- [ ] `createCategory` - 新規作成
- [ ] `updateCategory` - 更新（名前変更）
- [ ] `deleteCategory` - 削除
- [ ] `assignCategoryToPermanentNote` - 永久保存メモへの紐付け
- [ ] `removeCategoryFromPermanentNote` - 紐付け解除
- [ ] `assignCategoryToLiteratureNote` - 文献メモへの紐付け
- [ ] `removeCategoryFromLiteratureNote` - 紐付け解除
- [ ] すべてのアクションで認証チェック

### バリデーションスキーマ
- [ ] `utils/validators.ts`に追加
- [ ] `createCategorySchema`
- [ ] `updateCategorySchema`

### カテゴリー一覧ページ
- [ ] `app/(dashboard)/categories/page.tsx`を作成
- [ ] すべてのカテゴリーを一覧表示
- [ ] カテゴリーごとの紐付きメモ数を表示
- [ ] カテゴリークリックで詳細ページへ遷移
- [ ] 新規カテゴリー作成フォーム
- [ ] サイドバーからアクセス可能

### カテゴリー詳細ページ
- [ ] `app/(dashboard)/categories/[id]/page.tsx`を作成
- [ ] カテゴリー名を表示
- [ ] カテゴリーに紐づく永久保存メモ一覧
- [ ] カテゴリーに紐づく文献メモ一覧
- [ ] カテゴリー編集・削除ボタン

### カテゴリーカードコンポーネント
- [ ] `components/category/CategoryCard.tsx`を作成
- [ ] カテゴリー名
- [ ] 紐付きメモ数
- [ ] クリックで詳細ページへ

### カテゴリータグコンポーネント
- [ ] `components/category/CategoryTag.tsx`を作成
- [ ] 小さなタグUI
- [ ] メモカードやモーダルで使用
- [ ] クリックでカテゴリー詳細へ

### カテゴリーセレクター
- [ ] `components/category/CategorySelector.tsx`を作成
- [ ] マルチセレクト機能
- [ ] 既存カテゴリーから選択
- [ ] 新規カテゴリー作成も可能
- [ ] メモ作成・編集フォームで使用

### メモへのカテゴリー紐付け
- [ ] 永久保存メモ作成・編集時にカテゴリー選択
- [ ] 文献メモ作成・編集時にカテゴリー選択
- [ ] 紐付け情報をPrismaで保存

### サイドバーへの表示
- [ ] サイドバーにカテゴリー一覧を表示
- [ ] クリックでカテゴリー詳細ページへ

## 完了条件
- [ ] カテゴリーの作成・編集・削除ができる
- [ ] カテゴリー一覧ページが表示される
- [ ] カテゴリー詳細ページが表示される
- [ ] カテゴリーをメモに紐付けできる
- [ ] カテゴリーごとにメモをフィルタリングできる
- [ ] サイドバーにカテゴリーが表示される

## 参考
- CLAUDE.md: データベース設計 - カテゴリー関連モデル
- README.md: カテゴリー機能（索引メモ）
- README.md: サイドバー - 索引メモ（カテゴリー）

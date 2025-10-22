# #008 永久保存メモのCRUD実装

## ステータス
[ ] 未着手

## 概要
永久保存メモ（Permanent Notes）の作成・読取・更新・削除機能を実装する。

## タスク

### Server Actions
- [ ] `app/(dashboard)/permanent/actions.ts`を作成
- [ ] `createPermanentNote` - 新規作成（手動）
- [ ] `updatePermanentNote` - 更新
- [ ] `deletePermanentNote` - 削除
- [ ] `toggleShowInGraph` - グラフビュー表示フラグ切替
- [ ] すべてのアクションで認証チェック
- [ ] `revalidatePath`で画面更新

### バリデーションスキーマ
- [ ] `utils/validators.ts`に追加
- [ ] `createPermanentNoteSchema`
- [ ] `updatePermanentNoteSchema`

### メモカードコンポーネント
- [ ] `components/notes/PermanentNoteCard.tsx`を作成
- [ ] タイトル表示
- [ ] プレビュー（本文の一部）
- [ ] カテゴリータグ表示
- [ ] グラフビュー表示フラグアイコン
- [ ] 元メモ情報（sourceType）の表示
- [ ] 作成日時・更新日時
- [ ] 編集・削除ボタン
- [ ] クリックでモーダル表示

### メモ一覧表示
- [ ] トップページに永久保存メモ一覧を表示
- [ ] すべての永久保存メモを表示（`isDone`フィルターなし）
- [ ] カード形式でグリッド表示
- [ ] カテゴリー別にフィルタリング可能
- [ ] 空状態のメッセージ

### 新規作成フォーム
- [ ] `components/notes/PermanentNoteForm.tsx`を作成
- [ ] タイトル入力
- [ ] 本文入力（Markdownエディタ）
- [ ] グラフビュー表示チェックボックス
- [ ] カテゴリー選択（マルチセレクト）
- [ ] Server Actionで送信
- [ ] フォームリセット

### 永久保存メモ一覧ページ
- [ ] `app/(dashboard)/permanent/page.tsx`を作成
- [ ] すべての永久保存メモを一覧表示
- [ ] サイドバーからアクセス可能
- [ ] 検索・フィルタリング機能（将来的）

### 元メモへの参照
- [ ] `sourceType`と`sourceId`から元メモを取得
- [ ] モーダルで元メモへのリンクを表示

## 完了条件
- [ ] 永久保存メモの手動作成ができる
- [ ] 永久保存メモの一覧が表示される
- [ ] 永久保存メモの編集ができる
- [ ] 永久保存メモの削除ができる
- [ ] グラフビュー表示フラグが切替できる
- [ ] カテゴリーが表示される
- [ ] 元メモ情報が表示される

## 参考
- CLAUDE.md: 核心概念 - 永久保存メモ
- README.md: メモの種類 - 永久保存メモ
- README.md: サイドバー - 永久保存メモ一覧

# #006 走り書きメモのCRUD実装

## ステータス
[ ] 未着手

## 概要
走り書きメモ（Fleeting Notes）の作成・読取・更新・削除機能を実装する。

## タスク

### Server Actions
- [ ] `app/(dashboard)/fleeting/actions.ts`を作成
- [ ] `createFleetingNote` - 新規作成
- [ ] `updateFleetingNote` - 更新
- [ ] `deleteFleetingNote` - 削除
- [ ] `toggleFleetingNoteDone` - メモ済みフラグ切替（永久保存メモ自動生成）
- [ ] すべてのアクションで認証チェック
- [ ] `revalidatePath`で画面更新

### バリデーションスキーマ
- [ ] `utils/validators.ts`に追加
- [ ] `createFleetingNoteSchema`
- [ ] `updateFleetingNoteSchema`

### メモカードコンポーネント
- [ ] `components/notes/FleetingNoteCard.tsx`を作成
- [ ] タイトル表示
- [ ] プレビュー（本文の一部）
- [ ] 作成日時・更新日時
- [ ] メモ済みチェックボックス
- [ ] 編集・削除ボタン
- [ ] クリックでモーダル表示

### メモ一覧表示
- [ ] トップページに走り書きメモ一覧を表示
- [ ] `isDone = false`のメモのみ表示
- [ ] カード形式でグリッド表示
- [ ] 空状態のメッセージ

### 新規作成フォーム
- [ ] `components/notes/FleetingNoteForm.tsx`を作成
- [ ] タイトル入力
- [ ] 本文入力（textarea）
- [ ] Server Actionで送信
- [ ] フォームリセット

### メモ済みチェック時の処理
- [ ] `isDone`をtrueに更新
- [ ] 永久保存メモを自動生成
  - [ ] `sourceType = "fleeting"`
  - [ ] `sourceId`に元メモのIDを設定
- [ ] 元の走り書きメモはアーカイブ（削除しない）

## 完了条件
- [ ] 走り書きメモの作成ができる
- [ ] 走り書きメモの一覧が表示される
- [ ] 走り書きメモの編集ができる
- [ ] 走り書きメモの削除ができる
- [ ] メモ済みチェックで永久保存メモが生成される
- [ ] メモ済み後、一覧から非表示になる

## 参考
- CLAUDE.md: 核心概念 - 走り書きメモ
- CLAUDE.md: 重要な設計上の注意点 - メモ済みチェックの実装
- README.md: メモの種類 - 走り書きメモ
- README.md: メモ済みチェックの挙動

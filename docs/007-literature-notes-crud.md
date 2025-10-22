# #007 文献メモのCRUD実装

## ステータス
[ ] 未着手

## 概要
文献メモ（Literature Notes）の作成・読取・更新・削除機能を実装する。

## タスク

### Server Actions
- [ ] `app/(dashboard)/literature/actions.ts`を作成
- [ ] `createLiteratureNote` - 新規作成
- [ ] `updateLiteratureNote` - 更新
- [ ] `deleteLiteratureNote` - 削除
- [ ] `toggleLiteratureNoteDone` - メモ済みフラグ切替
- [ ] すべてのアクションで認証チェック
- [ ] `revalidatePath`で画面更新

### バリデーションスキーマ
- [ ] `utils/validators.ts`に追加
- [ ] `createLiteratureNoteSchema`（URL、タイトル、本文）
- [ ] `updateLiteratureNoteSchema`

### メモカードコンポーネント
- [ ] `components/notes/LiteratureNoteCard.tsx`を作成
- [ ] タイトル表示
- [ ] URL表示（リンク）
- [ ] プレビュー（本文の一部）
- [ ] 作成日時・更新日時
- [ ] メモ済みチェックボックス
- [ ] 編集・削除ボタン
- [ ] クリックでモーダル表示

### メモ一覧表示
- [ ] トップページに文献メモ一覧を表示
- [ ] `isDone = false`のメモのみ表示
- [ ] カード形式でグリッド表示
- [ ] 空状態のメッセージ

### 新規作成フォーム
- [ ] `components/notes/LiteratureNoteForm.tsx`を作成
- [ ] タイトル入力
- [ ] URL入力（オプション）
- [ ] 本文入力（textarea）
- [ ] Server Actionで送信
- [ ] フォームリセット

### メモ済み文献一覧ページ
- [ ] `app/(dashboard)/literature/done/page.tsx`を作成
- [ ] `isDone = true`の文献メモを一覧表示
- [ ] サイドバーからアクセス可能
- [ ] カテゴリーフィルター（将来的）

### メモ済みチェック時の処理
- [ ] `isDone`をtrueに更新
- [ ] 永久保存メモを自動生成
  - [ ] `sourceType = "literature"`
  - [ ] `sourceId`に元メモのIDを設定
- [ ] 元の文献メモは保持（削除しない）
- [ ] 永久保存メモとリレーション可能な状態を維持

## 完了条件
- [ ] 文献メモの作成ができる
- [ ] 文献メモの一覧が表示される
- [ ] 文献メモの編集ができる
- [ ] 文献メモの削除ができる
- [ ] URLが正しく表示・リンクされる
- [ ] メモ済みチェックで永久保存メモが生成される
- [ ] メモ済み後、一覧から非表示になる
- [ ] メモ済み文献一覧ページが機能する

## 参考
- CLAUDE.md: 核心概念 - 文献メモ
- CLAUDE.md: 重要な設計上の注意点 - メモ済みチェックの実装
- README.md: メモの種類 - 文献メモ
- README.md: サイドバー - メモ済み文献一覧

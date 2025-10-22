# #003 認証システムのセットアップ

## ステータス
[ ] 未着手

## 概要
Auth.js (NextAuth.js v5) を使用した認証システムを構築する。

## タスク

### Auth.js設定ファイルの作成
- [ ] `lib/auth.ts`を作成
  - [ ] NextAuth設定を実装
  - [ ] PrismaAdapterの設定
  - [ ] JWTストラテジーの設定
  - [ ] Credentialsプロバイダーの実装
  - [ ] Googleプロバイダーの設定
  - [ ] Callbacks（jwt, session）の実装

### Google OAuth設定
- [ ] Google Cloud Consoleでプロジェクト作成
- [ ] OAuth 2.0クライアントIDの作成
- [ ] リダイレクトURIの設定
  - 開発: `http://localhost:3000/api/auth/callback/google`
  - 本番: `https://your-domain.com/api/auth/callback/google`
- [ ] `.env`にクライアントID・シークレットを設定

### API Routeの作成
- [ ] `app/api/auth/[...nextauth]/route.ts`を作成
- [ ] GET/POSTハンドラーをエクスポート

### TypeScript型定義の拡張
- [ ] `types/next-auth.d.ts`を作成
- [ ] Session型にユーザーIDを追加

### Middlewareの作成
- [ ] `middleware.ts`を作成
- [ ] 認証保護ルートの定義
- [ ] 未認証時のリダイレクト処理
- [ ] 認証済みユーザーの認証ページアクセス制限

### SessionProviderのセットアップ
- [ ] `app/providers.tsx`を作成
- [ ] SessionProviderでラップ
- [ ] `app/layout.tsx`にProvidersを追加

## 完了条件
- [ ] Auth.jsの設定が完了している
- [ ] Google OAuthが動作する
- [ ] Middlewareで保護されたルートが機能する
- [ ] セッション情報が取得できる

## 参考
- CLAUDE.md: Auth.js ベストプラクティス
- CLAUDE.md: セキュリティのベストプラクティス

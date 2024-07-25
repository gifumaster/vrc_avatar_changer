# VRC_Avatar_Changer

## 注意事項

自分用に作成しており、動作確認は最小限になっています。  
また VRChatAPI は公開されたサービスではなく、予告なく使えなくなる可能性があります。

## 環境

Vue + Electron  
`npm install` した後に `npm run electron:serve`
で開発環境が動作します。  
`npm run electron:build` で win パッケージの作成が可能です。

## アプリ

ログイン情報は API を利用するために使用します。
API を利用できるトークンは保存しますが、ID やパスワードは一切保存していません。
2FA ログインに対応していますが、メール版は動きません。

ログイン後、Refresh ボタンを押すことでアバターのリストを取得します。
現在は最大で 600 アバターまでロードが可能です。

検索は avatar の name / description から部分一致でフィルタリングできます。
リストはキャッシュされるため、アバターの name / description / image を更新した場合は新しいデータを取得するために REFRESH ボタンを押す必要があります。

## タグを変えたいんだけど

resource フォルダ内の tags.json を書き換えてください。

メニュー表示名: 検索対象フィルタ

です。

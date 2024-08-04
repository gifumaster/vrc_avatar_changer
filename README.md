　# VRC_Avatar_Changer

## なにこれ

アプリからアバターの変更が可能になるツールです。
HMDで利用時にデスクトップ画面から操作することを想定しています。
タグフィルタによる絞り込みによってアバター名や説明文に検索をかけ、目当てのアバターを探せるようにしています。
300以上のアバターをアップロードしていてアバターが埋もれて探すのが難しいと思ってる人におすすめです。

動画(Youtube)  
[![](https://img.youtube.com/vi/LPBFArKa-dY/0.jpg)](https://www.youtube.com/watch?v=LPBFArKa-dY)

## インストール

[Release](https://github.com/gifumaster/vrc_avatar_changer/releases)から最新のバージョンのインストーラー(avatar-changer.Setup.*.*.**.exe
)をダウンロードしてください。

## 注意事項

- 自分用に作成しており、動作確認は最小限になっています。  
- VRChatAPI は公開されたサービスではないため予告なく使えなくなる可能性があります。
- 時々、アバターを変更してもゲーム内で反映されない現象を確認しています。その場合は別のアバターにするかゲーム内から変更する必要があります。
- インストール時にWindows Defender等によってインストールがブロックされます。うまく回避してください。
 
## ログイン情報について

ログイン情報は API を利用するためのトークンを取得する為に使用します。
ID やパスワードは一切保存しません。
二段階認証に対応していますが、メール版は確認していません、たぶん動きません（APIが違うので）

## 画面

ログイン後、「アバターリストを取得」を押すと自分のアバターリストが表示されるようになります。

- 数が多い場合は時間がかかります。
- アバターが1000を超えているとそれ以上は取得しないようにしています。
- 古いデータが表示されている場合は再度「アバターリストを取得」を押してください。
- タグはavatar の name / description から部分一致したものを絞り込み表示します。
- タグを編集することでフィルタリングするワードを設定・変更できます。
- 初期設定ではSpring/Summer/Fall/Winterが表示されていますがこれらも消すことができます。


![image](https://github.com/user-attachments/assets/b14a1150-674e-413a-9799-35be043dcc3e)


## 開発したい人

Vue + Electron で動作してます。 
`npm install` した後に `npm run electron:serve`
で開発環境が動作します。  
`npm run electron:build` で win パッケージの作成が可能です。

## アンインストール

アンインストールはWindowsのアプリから可能です。


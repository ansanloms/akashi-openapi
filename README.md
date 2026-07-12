# akashi-openapi

AKASHI (マネーフォワード クラウド勤怠 Plus) 公開 API の OpenAPI 定義。

[公開 API 仕様](https://akashi.zendesk.com/hc/ja/articles/115000475854) を元に作成している。

## 基本的な使い方

エンドポイントにアクセストークンを付加のうえリクエストを送信することで、各 API に応じた機能を利用できる。
エンドポイントのドメインはログイン後 URL のドメインを確認して指定する。

## 認証

- GET 系の API はクエリパラメータ `token` にアクセストークンを指定する。
- POST/PATCH/DELETE 系の API はリクエストボディの `token` プロパティにアクセストークンを指定する。

アクセストークンはマイページから発行する。有効期限は 1 ヶ月と 1 日である。
再発行はマイページ、またはアクセストークン再発行 API を利用する。

## 共通仕様

- 各 API はアクセストークンに紐づいた従業員の権限によって実行が許可、または却下される。
- 処理成否のレスポンスは JSON とし、成功時は `success` が true となり `response` に処理結果が格納される。
- 失敗時は `success` が false となり `errors` にエラーメッセージの配列が格納される。
- 値が無い場合のレスポンスは String/Integer/Object が null、Array が空配列となる。Boolean は必ず true/false が設定される。

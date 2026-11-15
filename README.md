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

## 開発

- `.node-version` に書かれたバージョンの Node が必要。
- `npm ci`: 依存を入れる。
- `npm run lint`: 型検査、oxlint と oxfmt、textlint、redocly lint を順に実行する。
- `npm test`: `bin/` 配下のスクリプトのテストを実行する。
- `npm run fix`: sort と textlint、oxlint、oxfmt の各 fixer を順に実行して最後に lint する (途中で失敗しても続行)。
- `npm run build`: `dist/` に静的サイト (`index.html`、`assets/` 配下の JS・CSS、`api.yaml`、同梱した依存のライセンス一覧 `licenses.md`) を出力する。
- `npm run dev`: spec を bundle せずに Vite の dev サーバを `http://localhost:4000` で起動する。spec のファイルを編集するとブラウザが再読み込みされる。
- `npm run search`: bundle 済みの spec を読み、全エンドポイントを JSON で出力する。既定では `dist/api.yaml` を読むので、先に `npm run build:bundle` で作っておく。`-- --target <path>` で bundle 済みの別の spec を指定できる。各要素は `method`、`path`、`operationId`、`summary` と、そのエンドポイントが直接または間接に参照する schema の一覧 `schemas` を持つ。jq に渡す場合は npm のヘッダ行を除くため `-s` を付ける。次の例は schema `EnumAlertType` を利用しているエンドポイントを出力する。

  ```sh
  npm run -s search | jq -r '.[] | select(.schemas | index("EnumAlertType")) | "\(.method) \(.path)"'
  ```

依存は `.npmrc` により正確な版で固定する。自前の 2 パッケージ (textlint-plugin-yaml-keys、redocly-plugins) は GitHub のタグを git 依存で参照しており、Dependabot が更新しないため `package.json` のタグを手で書き換えて `npm install` する。

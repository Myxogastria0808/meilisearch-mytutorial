# meilisearch mytutorial

## セットアップ

1. docker-compose.yaml を作成

```yaml
version: "3.8"

services:
  meilisearch:
    container_name: meilisearch
    image: "getmeili/meilisearch:prototype-japanese-184"
    volumes:
      - ./meili_data:/meili_data
    environment:
      #meili cloudを使わない場合、マスターキーは自分で設定する
      - MEILI_MASTER_KEY=master_key
      - MEILI_ENV=development
    ports:
      #データ部分をマウント
      - "7700:7700"
    tty: true
```

2. `docker-compose up -d`でコンテナを起動

3. `http://localhost:7700`にアクセスして MeiliSearch の管理画面にアクセス

## API キーの取得

参考サイト 3. より引用

```sh
sudo apt install jq
curl   -X GET 'http://localhost:7700/keys'   -H 'Authorization: Bearer KSjeon19dn3Ls93nFNl349FNS93nkljasIk39fnsa' | jq
```

実行結果例

以下、参考サイト 3. より引用

> - Default Search API Key: 検索リクエスト用
> - Default Admin API Key: 検索だけでなく、インデックスの作成や削除など管理者用

```sh
{
  "results": [
    {
      "name": "Default Search API Key",
      "description": "Use it to search from the frontend",
      "key": "ff27d4e90304c11e01cde59ebf47546cdb58de264c3226a757baf07a5f1c03",
      "uid": "876301c6-022a-4b2e-ac7d-ab4585ac96a3",
      "actions": [
        "search"
      ],
      "indexes": [
        "*"
      ],
      "expiresAt": null,
      "createdAt": "2024-08-25T13:26:32.809356262Z",
      "updatedAt": "2024-08-25T13:26:32.809356262Z"
    },
    {
      "name": "Default Admin API Key",
      "description": "Use it for anything that is not a search operation. Caution! Do not expose it on a public frontend",
      "key": "1b85758befd0e2d3fd70f898966856af681c36848194addab18b21b6b180d34c",
      "uid": "50ba8d1c-ceb3-46a7-8dec-f0b296d6cc80",
      "actions": [
        "*"
      ],
      "indexes": [
        "*"
      ],
      "expiresAt": null,
      "createdAt": "2024-08-25T13:26:32.803196234Z",
      "updatedAt": "2024-08-25T13:26:32.803196234Z"
    }
  ],
  "offset": 0,
  "limit": 20,
  "total": 2
}
```

## meilisearch にデータを入れる

参考サイト 1. より引用

```sh
curl \
  -X POST 'http://localhost:7700/indexes/{インデックス名}/documents?primaryKey={任意のキー}' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer meili-master-key' \
  --data-binary @sample.json
```

例

```sh
  -X POST 'http://localhost:7700/indexes/sample/documents?primaryKey=id' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer master_key' \
  --data-binary @sample.json
```

対象の json ファイル

```json
[
  {
    "id": 1,
    "name": "John",
    "age": 30,
    "image": "https://yukiosada.work/amazon1.webp",
    "cars": {
      "car1": "Ford",
      "car2": "BMW",
      "car3": "Fiat"
    }
  },
  ...
]

```

## データを検索

参考サイト 1. より引用

```sh
curl \
  -X POST 'http://localhost:7700/indexes/{インデックス名}/search' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer meili-master-key' \
  --data-binary '{ "q": "{検索ワード}" }'
```

例

```sh
curl \
  -X POST 'http://localhost:7700/indexes/sample/search' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer master_key' \
  --data-binary '{ "q": "{alice}" }'
```

実行結果例

```json
{
  "hits": [
    {
      "id": 2,
      "name": "Alice",
      "age": 10,
      "cars": { "car1": "Toyota", "car2": "Suzuki", "car3": "Mazuda" },
      "image": "https://yukiosada.work/amazon2.webp"
    }
  ],
  "query": "{alice}",
  "processingTimeMs": 0,
  "limit": 20,
  "offset": 0,
  "estimatedTotalHits": 1
}
```

## 参考サイト

1. https://zenn.dev/ndjndj/articles/c8b6359d2b42b0

2. https://qiita.com/mosuka/items/fbda479b25a7ccd7c350

3. https://virment.com/setup-masterkey-and-apikey-for-meilisearch/

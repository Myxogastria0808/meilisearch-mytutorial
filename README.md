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

# meilisearch にデータを入れる

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

結果

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

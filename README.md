# PubSubの構造

- 1つのトピックに複数のサブスクリプションを作れる
- 1つのサブスクリプションに対して複数のサブスクライバーを作れる
    - が、サブスクリプションがトピックから拾い上げた1つのメッセージはサブスクライバーのいずれか1つに配信される
    - 全てのサブスクライバーに全てのメッセージが配信される訳ではない
- 基本的にはトピックに対して実行したい責務別にサブスクリプションを分ける
    - サブスクリプションA：トピックを加工してBigQueryに書き込む
    - サブスクリプションB：トピックの内容を検証して必要に応じてアラートを出す
    - 同じサブスクリプションを購読するサブスクライバーは同じ責務を負い、サブスクライバー間で実現したいことは変わらないようにしておく

![alt text](images/image.png)

# Dataflow templateでPubSub to BigQueryする

- Dataflowテンプレートから簡単にジョブを作ることもできる
- `Pub/Sub to BigQuery` と`Pub/Sub Subscription to BigQuery` がある

![alt text](images/image-1.png)

# Dataflowテンプレートの違いを確認

![alt text](images/image-2.png)

# PubSub Subscription to BigQueryの詳細

- テンプレートを選択すると予め構築されたDataflowの図が右側に表示された
- 一方でPubSubサブスクリプションから直接BigQueryに書き込む機能があるらしい

![alt text](images/image-3.png)

# PubSubサブスクリプションを直接BigQueryへ

- 配信タイプの`pull/push`に加えて`BigQueryへの書き込み`が選択できるらしいのでこっちを試す

![alt text](images/image-4.png)

![alt text](images/image-5.png)

![alt text](images/image-6.png)

## テーブルのスキーマはそれらしいものを生成してもらう

![alt text](images/image-7.png)

## 空のテーブルができた

![alt text](images/image-8.png)

![alt text](images/image-9.png)

![alt text](images/image-10.png)

![alt text](images/image-11.png)

# PubSubトピックにメッセージをpublishしてみる

![alt text](images/image-12.png)

- メッセージのpublishはPubSubコンソールからも可能

![alt text](images/image-13.png)

# BigQueryへの書き込みを確認

- PubSubからの書き込みはストリーミング挿入で行われる
    - リアルタイム性が重視される場合に有用

![alt text](images/image-14.png)

# CloudShellでPubSubトピックにpublishする

```sql
gcloud pubsub topics publish user-activity-logs --message='{
  "user_id": "789012",
  "timestamp": "2024-09-01T15:30:00Z",
  "activity_type": "click",
  "page_url": "https://example.com/product/12345",
  "referrer": "https://example.com/home",
  "user_agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.114 Safari/537.36"
}'
```

![alt text](images/image-15.png)

- こちらもBigQueryへの書き込みが確認できた

![alt text](images/image-16.png)
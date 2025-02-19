---
title: ログインデックス
type: apicontent
order: 23
external_redirect: '/api/#logs-indexes'
---
## ログインデックス

<div class="alert alert-warning">
このエンドポイントは公開ベータ版です。Datadog のクライアントライブラリでサポートされていません。フィードバックがありましたら、<a href="/help">Datadog のサポートチームまでお寄せください</a>。
</div>

`Index` オブジェクトはログインデックスの構成を記述します。次の属性を持ちます。

* **`name`** (読み取り専用):
    インデックスの名前。
* **`filter.query`** :
    このインデックスでは、フィルター条件に一致するログだけが考慮されます。
    検索クエリは[ログ検索構文][1]に従います。
* **`num_retention_days`** (読み取り専用):
    このインデックスからログが削除されるまでの日数。
* **`daily_limit`** (読み取り専用):
    レート制限されるまでに、このインデックスで送信できるログイベントの数。
* **`is_rate_limited`** (読み取り専用):
    インデックスがレート制限されているかどうかを示すブール値。レート制限は、1 日の上限を超えるログが送信されたことを意味します。レート制限は毎日 UTC 午後 2 時にリセットされます。
* **`exclusion_filters`**
    `ExclusionFilter` オブジェクトの配列 (以下を参照)。ログは、配列の順序に従い、各 `ExclusionFilter` のクエリに照らしてテストされます。最初に一致したアクティブな `ExclusionFilter` だけが適用され、他のフィルターは一致しても無視されます。

  * **`name`** :
    除外フィルターの名前。
  * **`is_enabled`** :
    除外がアクティブ (最終的にログを除外する) かどうかを示すブール値。
  * **`filter.query`** :
    フィルター条件と親インデックスのクエリの両方に一致するログだけがこの除外フィルターで考慮されます。
    検索クエリは[ログ検索構文][1]に従います。
  * **`filter.sample_rate`** :
    この除外フィルターがアクティブな場合に除外されるログの割合。サンプリングは均一です。


**注**: このエンドポイントを使用するには、API キーおよびアプリケーションキーと共に管理者権限が必要です。

[1]: https://docs.datadoghq.com/ja/logs/explorer/search/#search-syntax
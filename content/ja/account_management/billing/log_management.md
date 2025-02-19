---
title: ログ管理の課金
kind: faq
---
## 料金

月末に、インデックス化されたログイベントの総数が計算されます。

- 総数が契約量以下の場合、請求額は変わりません。
- 契約量を超過した場合は、契約量を差し引いた**オンデマンドの使用量**に対して 50% 増の料金が課金されます。

### オンデマンド

Datadog ログ管理を使用するお客様は、インデックス化されたログイベントについて 1 か月の契約量を定めます。ただし、トラブルが発生している間は、ログ数が急増し、契約量を上回る可能性があります。インフラストラクチャーの健全性を常に可視化しておくことが重要なため、1 か月の契約量の制限は受けません。

契約量は月単位なので、1 日だけログイベントが過剰に生成されても、月平均の 1 日のログ消費量が契約の予想量と変わらない場合、使用量の超過は発生しません。

## ログイベントの追跡

Datadog に送信されたログイベントの数は、複数箇所で確認できます。

1. [使用量ページ][1]には、1 時間にインデックス化されたログイベントの数が表示される列があります。

    {{< img src="account_management/billing/log-events01.png" alt="Log Events" responsive="true">}}

2. [構成ページ][2]では、インデックスをダブルクリックすると、この 2 日間にインデックス化されたログイベントの数が表示されます。

    {{< img src="account_management/billing/log-events02.png" alt="Log Events" responsive="true">}}

3. [ログエクスプローラー][3]では、タイムフレームを変更すると、リストの先頭でログイベントの数をチェックできます。

    {{< img src="account_management/billing/log-events03.png" alt="Log Events" responsive="true">}}

ファセットを使用して、ログイベントによって定義された属性別またはタグ別にログ数を確認することもできます。これは、多数のデータを生成しているホスト、サービス、エンドポイントなどを特定するのに役立ちます。

## トラブルシューティング
技術的な質問については、[Datadog のサポートチーム][4]にお問い合わせください。

課金に関するご質問は、[カスタマーサクセス][5]マネージャーにお問い合わせください。

[1]: https://app.datadoghq.com/account/usage/hourly
[2]: https://app.datadoghq.com/logs/pipelines
[3]: https://app.datadoghq.com/logs
[4]: /ja/help
[5]: mailto:success@datadoghq.com
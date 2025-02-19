---
title: メトリクス
type: apicontent
order: 25
external_redirect: /api/#metrics
---
## メトリクス
メトリクスエンドポイントを使用して、以下を行うことができます。

* Datadog のダッシュボードでグラフ化できるメトリクスデータをポストする
* 任意の期間のメトリクスをクエリする

Datadog の UI に表示されるグラフには、一定数のポイントしか含まれず、表示されているメトリクスのタイムフレームが長くなると、その一定数以下にポイントを抑えるために、ポイント間の集約が行われます。

したがって、クエリするデータのタイムフレームが長くなるほど、返されるポイントの集約度は高くなります。Datadog での最高粒度は 1 ポイント/秒です。この間隔 (1 秒) でポイントを送信し、ごく短い時間 (この場合はおよそ 100 秒未満) をクエリ API にリクエストすれば、送信したポイントがすべてそのまま返されます。そうでない場合、Datadog のアルゴリズムは、指定されたタイムウィンドウごとに約 150 個のポイントを返そうとするため、リクエストされた時間が長くなるほど、粒度の粗いデータが表示されることになります。この時間集計は平均化によって行われます。

メトリクスポイントは 1 秒の解像度で保存されますが、ポイントの送信は 15 秒間隔で行うことをお勧めします。
 1 秒未満のタイムスタンプを持つメトリクスは、最も近い秒単位に丸められます。また、同じタイムスタンプを持つポイントが複数ある場合は、最新のポイントによって以前のポイントが上書きされます。

Datadog では、1 ホストあたり 100 個の時系列というソフト制限を設けています。
メトリクス名とタグの一意の組み合わせが 1 つの時系列になります。

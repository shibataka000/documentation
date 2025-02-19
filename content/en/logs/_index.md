---
title: Log Management
kind: Documentation
description: "Configure your Datadog Agent to gather logs from your host, containers & services."
disable_toc: true
aliases:
    - /guides/logs/
    - /logs/logging_without_limits
further_reading:
- link: "/logs/guide"
  tag: "Guide"
  text: Additional helpful articles about log collection and processing.
- link: "tracing/advanced/connect_logs_and_traces/?tab=java"
  tag: "Documentation"
  text: "Connect Logs and Traces"
- link: "https://learn.datadoghq.com"
  tag: "Learning Center"
  text: "Introduction to Logs in Datadog"
---

{{< vimeo 293195142 >}}

Sometimes, your infrastructure may generate a volume of log events that is too large or has significant fluctuations. In this situation, you may need to choose which logs to send to a log management solution, and which logs to archive. Filtering your logs before sending them, however, may lead to gaps in coverage or the accidental removal of valuable data.

Datadog's log management removes these limitations by decoupling log ingestion from indexing. This enables you to cost-effectively collect, process, archive, explore, and monitor all your logs. This is called Logging without Limits*.

{{< whatsnext desc="With Logging without Limits*, you can:">}}
  {{< nextlink href="/logs/log_collection">}}<u>Log Collection & Integrations</u>: Ingest all your logs from your hosts, containers, and cloud providers.{{< /nextlink >}}
  {{< nextlink href="/logs/processing">}}<u>Processing</u>: Process and enrich all of your logs with pipelines and processors. {{< /nextlink >}}
  {{< nextlink href="/logs/live_tail">}}<u>Live Tail</u>: See your ingested logs in real time across all your environments.{{< /nextlink >}}
  {{< nextlink href="/logs/logs_to_metrics">}}<u>Generate Metrics</u>: Generate Metrics from Ingested Logs.{{< /nextlink >}}
  {{< nextlink href="/logs/archives">}}<u>Archives</u>: Archive all enriched logs into S3 buckets.{{< /nextlink >}}
  {{< nextlink href="/logs/indexes">}}<u>Index</u>: Dynamically decide what to include or exclude from your indexes to control your costs.{{< /nextlink >}}
{{< /whatsnext >}}
{{< whatsnext desc="Once your logs are indexed, you can explore them in the Log Explorer:">}}
  {{< nextlink href="/logs/explorer/">}}<u>Log Explorer</u>: Discover the Log Explorer view, how to add Facets and Measures.{{< /nextlink >}}
  {{< nextlink href="/logs/explorer">}}<u>Search</u>: Search through all of your indexed logs.{{< /nextlink >}}
  {{< nextlink href="/logs/explorer/analytics">}}<u>Analytics</u>: Perform Log Analytics over your indexed logs.{{< /nextlink >}}
  {{< nextlink href="/logs/explorer/patterns">}}<u>Patterns</u>: Spot Log Patterns by clustering your indexed logs together.{{< /nextlink >}}
  {{< nextlink href="/logs/explorer/saved_views/">}}<u>Saved Views</u>: Use Saved Views to automatically configure your Log Explorer.{{< /nextlink >}}
{{< /whatsnext >}}

## Further Reading

{{< partial name="whats-next/whats-next.html" >}}
<br>
*Logging without Limits is a trademark of Datadog, Inc.

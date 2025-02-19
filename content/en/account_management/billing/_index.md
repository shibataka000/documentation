---
title: Billing
kind: documentation
---

## Overview

The billing cycle begins the first of the month regardless of when you sign up. Your first month is prorated based on your actual sign-up date.

Datadog meters the count of hosts, containers, and custom metrics hourly. The billable count of hosts and containers is calculated at the end of the month using the maximum count (high-water mark) of the lower 99 percent of usage for those hours. We exclude the top 1% to reduce the impact of spikes in usage on your bill. The billable count of custom metrics is based on the average number of custom metric hours for the month. See your [Usage][1] in Datadog.

### Hosts

Hosts are defined as any instances with the Datadog Agent installed plus any AWS EC2s, GCP, Azure, or vSphere VMs monitored with our integrations. Any EC2s or VMs with the Agent installed count as a single instance (no double-billing).

Non-reporting hosts (status `???` in your [Infrastructure list][2]) do not count towards billing. It could take up to 24 hours for these hosts to drop out of the [Infrastructure List][2]. Datadog retains the historical data for these hosts (paid accounts). Metrics can be graphed on a dashboard by knowing the specific host name or tags.

### Containers

It is recommended that containers are monitored with a single containerized Agent per host. This Agent collects both container and host metrics. If you choose to install the Agent directly in each container, each container is counted as a host from a billing perspective.  More details can be found in the [Agent installation][3] documentation.

## Invoicing

If you pay by credit card, receipts are available to [Administrators][4] for previous months under [Billing History][5].

If you pay by check or wire, invoices are emailed to the billing email addresses when due. If you need an additional copy, email [Datadog billing][6].

### Billing emails

You can set specific email addresses to receive invoices on the [Plan][7] page under **Manage Billing Emails**:

{{< img src="account_management/billing/billing01.png" alt="Manage Billing Emails" responsive="true">}}

**Note**: The email address does not need to be a team member within Datadog. For example, you could use `invoices@yourcompany.com`.

## Further Reading
  
{{< whatsnext desc="Specific billing topics:">}}
    {{< nextlink href="account_management/billing/pricing/" >}}Pricing{{< /nextlink >}}
    {{< nextlink href="account_management/billing/usage_details/" >}}Usage details{{< /nextlink >}}
    {{< nextlink href="account_management/billing/credit_card/" >}}Credit card{{< /nextlink >}}
    {{< nextlink href="account_management/billing/custom_metrics/" >}}Custom metrics{{< /nextlink >}}
    {{< nextlink href="account_management/billing/containers/" >}}Containers{{< /nextlink >}}
    {{< nextlink href="account_management/billing/log_management/" >}}Log management{{< /nextlink >}}
    {{< nextlink href="account_management/billing/apm_distributed_tracing/" >}}APM & Distributed Tracing{{< /nextlink >}}
    {{< nextlink href="account_management/billing/aws/" >}}AWS integration{{< /nextlink >}}
    {{< nextlink href="account_management/billing/azure/" >}}Azure integration{{< /nextlink >}}
    {{< nextlink href="account_management/billing/google_cloud/" >}}Google Cloud integration{{< /nextlink >}}
    {{< nextlink href="account_management/billing/vsphere/" >}}vSphere integration{{< /nextlink >}}
{{< /whatsnext >}}

[1]: https://app.datadoghq.com/account/usage/hourly
[2]: /graphing/infrastructure
[3]: /agent
[4]: /account_management/team/#datadog-user-roles
[5]: https://app.datadoghq.com/account/billing_history
[6]: mailto:billing@datadoghq.com
[7]: https://app.datadoghq.com/account/billing

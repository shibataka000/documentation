---
aliases:
  - /fr/integrations/dnscheck
categories:
  - network
  - web
creates_events: false
ddtype: check
dependencies:
  - 'https://github.com/DataDog/integrations-core/blob/master/dns_check/README.md'
display_name: DNS
git_integration_title: dns_check
guid: 31e4c84c-fc4b-4cd4-97ed-0331bf4e2023
integration_id: dns
integration_title: Check DNS
is_public: true
kind: integration
maintainer: help@datadoghq.com
manifest_version: 1.0.0
metric_prefix: dns.
metric_to_check: dns.response_time
name: dns_check
public_title: "Intégration Datadog/Check\_DNS"
short_description: Surveillez les délais de résolution et de correspondance des enregistrements DNS.
support: core
supported_os:
  - linux
  - mac_os
  - windows
---
## Présentation

Surveillez les délais de résolution et de correspondance des enregistrements DNS à l'aide des serveurs de noms de votre choix.

## Implémentation

### Installation

Le check DNS est inclus avec le paquet de l'[Agent Datadog][1] : vous n'avez donc rien d'autre à installer sur le serveur à partir duquel vous allez sonder vos serveurs DNS.

Bien qu'il soit recommandé d'exécuter bon nombre des checks axés sur des métriques sur le(s) mêmes host(s) que le service surveillé, ce check axé sur des statuts peut être lancé sur des hosts qui n'exécutent pas les services DNS surveillés.

### Configuration

1. Modifiez le fichier `dns_check.d/conf.yaml` dans le dossier `conf.d/` à la racine du [répertoire de configuration de votre Agent][2] pour commencer à recueillir vos données DNS.
    Consultez le [fichier d'exemple dns_check.d/conf.yaml][3] pour découvrir toutes les options de configuration disponibles :

    ```yaml
      init_config:

      instances:
        - name: Example (com)
          # nameserver: 8.8.8.8   # The nameserver to query, this must be an IP address
          hostname: example.com # the record to fetch
          # record_type: AAAA   # default is A
        - name: Example (org)
          hostname: example.org
    ```

    Si vous ne définissez pas l'option `nameserver`, le check utilise le serveur de noms configuré dans les paramètres de réseau local.

2. [Redémarrez l'Agent][4] pour commencer à envoyer vos checks de service et délais de réponse DNS à Datadog.

### Validation

[Lancez la sous-commande `status` de l'Agent][5] et cherchez `dns_check` dans la section Checks.

## Données collectées

### Métriques
{{< get-metrics-from-git "dns_check" >}}


### Événements
Le check DNS n'inclut aucun événement.

### Checks de service
Ce check d'Agent applique les tags suivants à l'ensemble des checks de service recueillis ;

  * `nameserver:<serveurdenoms_en_yaml>`
  * `resolved_hostname:<hostname_en_yaml>`

`dns.can_resolve` :

Renvoie CRITICAL si l'Agent ne parvient pas à résoudre la requête. Si ce n'est pas le cas, renvoie UP.

Tags appliqués : `hostname` et `record_type`.

## Dépannage
Besoin d'aide ? Contactez [l'assistance Datadog][7].

[1]: https://app.datadoghq.com/account/settings#agent
[2]: https://docs.datadoghq.com/fr/agent/guide/agent-configuration-files/?tab=agentv6#agent-configuration-directory
[3]: https://github.com/DataDog/integrations-core/blob/master/dns_check/datadog_checks/dns_check/data/conf.yaml.example
[4]: https://docs.datadoghq.com/fr/agent/guide/agent-commands/?tab=agentv6#start-stop-and-restart-the-agent
[5]: https://docs.datadoghq.com/fr/agent/guide/agent-commands/?tab=agentv6#agent-status-and-information
[6]: https://github.com/DataDog/integrations-core/blob/master/dns_check/metadata.csv
[7]: https://docs.datadoghq.com/fr/help


{{< get-dependencies >}}
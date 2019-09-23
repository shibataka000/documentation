---
title: Obtenir l'utilisation horaire pour Fargate
type: apicontent
order: 33.7
external_redirect: '/api/#get-hourly-usage-for-fargate'
---
## Obtenir l'utilisation horaire pour Fargate

Obtenez l'utilisation horaire pour [Fargate][1].

##### Arguments
* **`start_hr`** [*obligatoire*] :
    datetime au format ISO-8601, UTC, à l'heure près : [AAAA-MM-JJThh] pour une utilisation commençant à cette heure
* **`end_hr`** [*facultatif*, *défaut*=**1d+start_hr**] :
    datetime au format ISO-8601, UTC, à l'heure près : [AAAA-MM-JJThh] pour une utilisation se terminant AVANT cette heure

[1]: /fr/integrations/ecs_fargate
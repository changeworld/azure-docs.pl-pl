---
title: Zbieranie niestandardowe dane JSON w usłudze Log Analytics | Dokumentacja firmy Microsoft
description: Może być zbierana niestandardowego źródła danych JSON w usłudze Log Analytics przy użyciu agenta usługi Log Analytics dla systemu Linux.  Te niestandardowymi źródłami danych mogą być proste skrypty zwracanie JSON, takich jak narzędzie curl lub jeden z jego FluentD ponad 300 wtyczek. Ten artykuł zawiera opis konfiguracji wymaganej do zbierania danych.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: c8972df6f42920af6a9bd5f04a27f14dc647da44
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336941"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-log-analytics"></a>Zbieranie niestandardowego źródła danych JSON za pomocą agenta usługi Log Analytics dla systemu Linux w usłudze Log Analytics
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
Może być zbierana niestandardowego źródła danych JSON w usłudze Log Analytics przy użyciu agenta usługi Log Analytics dla systemu Linux.  Te niestandardowymi źródłami danych mogą być proste skrypty, takie jak zwracanie JSON [curl](https://curl.haxx.se/) lub jednego z [firmy FluentD ponad 300 wtyczek](http://www.fluentd.org/plugins/all). Ten artykuł zawiera opis konfiguracji wymaganej do zbierania danych.

> [!NOTE]
> Agent analizy dziennika dla systemu Linux v1.1.0-217 + jest wymagany dla niestandardowe dane JSON

## <a name="configuration"></a>Konfigurowanie

### <a name="configure-input-plugin"></a>Konfigurowanie danych wejściowych wtyczki

Aby zebrać dane JSON w usłudze Log Analytics, należy dodać `oms.api.` do ekranu startowego tagu FluentD we wtyczce danych wejściowych.

Na przykład Oto osobnego pliku konfiguracji `exec-json.conf` w `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Ta metoda korzysta z wtyczki FluentD `exec` do uruchomienia polecenia curl co 30 sekund.  Dane wyjściowe tego polecenia są zbierane przez wtyczkę dane wyjściowe JSON.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
Dodawany plik konfiguracyjny `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` będzie wymagać, aby mieć prawa własności do swoją zmieniono za pomocą następującego polecenia.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Konfigurowanie wtyczki danych wyjściowych 
Dodaj następującą konfigurację wtyczki dane wyjściowe do głównej konfiguracji w `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` lub osobnego pliku konfiguracji wprowadzanych w `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Uruchom ponownie agenta usługi Log Analytics dla systemu Linux
Uruchom ponownie agenta usługi Log Analytics dla usługi systemu Linux za pomocą następującego polecenia.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Dane wyjściowe
Dane będą zbierane w usłudze Log Analytics z typem rekordu `<FLUENTD_TAG>_CL`.

Na przykład niestandardowy tag `tag oms.api.tomcat` w usłudze Log Analytics z typem rekordu `tomcat_CL`.  Można pobrać wszystkie rekordy tego typu z następujące wyszukiwanie w dzienniku.

    Type=tomcat_CL

Zniżki w stosunku do pola nadrzędnego na podstawie zagnieżdżone dane JSON źródła są obsługiwane, ale są indeksowane. Na przykład następujące dane JSON jest zwracana z wyszukiwania usługi Log Analytics, jako `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [dziennikach](../../log-analytics/log-analytics-queries.md) analizować dane zbierane z innych źródeł danych i rozwiązań. 
 

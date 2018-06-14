---
title: Zbieranie danych niestandardowych JSON w OMS analizy dzienników | Dokumentacja firmy Microsoft
description: Mogą być zbierane niestandardowych źródeł danych JSON do analizy dzienników dla systemu Linux przy użyciu agenta pakietu OMS.  Te źródła danych niestandardowych można proste skrypty zwracanie JSON, takie jak curl lub jednego z jego FluentD 300 + wtyczek. W tym artykule opisano konfiguracja wymagana dla tej kolekcji danych.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 800ee1269556e7c2d56fbbf2b497c10509b5c78c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855222"
---
# <a name="collecting-custom-json-data-sources-with-the-oms-agent-for-linux-in-log-analytics"></a>Zbieranie niestandardowych źródeł danych JSON z agentem pakietu OMS dla systemu Linux w analizy dzienników
Mogą być zbierane niestandardowych źródeł danych JSON do analizy dzienników dla systemu Linux przy użyciu agenta pakietu OMS.  Te źródła danych niestandardowych mogą być proste skrypty, takie jak zwracanie JSON [curl](https://curl.haxx.se/) lub jednego z [w FluentD 300 + wtyczek](http://www.fluentd.org/plugins/all). W tym artykule opisano konfiguracja wymagana dla tej kolekcji danych.

> [!NOTE]
> Agent pakietu OMS dla systemu Linux v1.1.0-217 + jest wymagana dla danych JSON niestandardowe

## <a name="configuration"></a>Konfiguracja

### <a name="configure-input-plugin"></a>Skonfiguruj wtyczkę wejściowych

Aby zbierać dane JSON w analizy dzienników, Dodaj `oms.api.` na początku tag FluentD w wejściowych wtyczki.

Na przykład poniżej przedstawiono plik konfiguracji oddzielnych `exec-json.conf` w `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Używa wtyczki FluentD `exec` do uruchamiania polecenia curl co 30 sekund.  Dane wyjściowe tego polecenia są zbierane przez wtyczkę dane wyjściowe JSON.

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
Plik konfiguracji dodany w obszarze `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` trzeba mieć własność zmienić przy użyciu następującego polecenia.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Skonfiguruj wtyczkę danych wyjściowych 
Dodaj następującą konfigurację wtyczki dane wyjściowe do konfiguracji głównego w `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` lub plik konfiguracji oddzielne wprowadzanych w`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

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

### <a name="restart-oms-agent-for-linux"></a>Uruchom ponownie agenta pakietu OMS dla systemu Linux
Uruchom ponownie agenta pakietu OMS usługi systemu Linux przy użyciu następującego polecenia.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Dane wyjściowe
Dane zostaną zebrane w analizy dzienników z typem rekordu `<FLUENTD_TAG>_CL`.

Na przykład niestandardowe tag `tag oms.api.tomcat` w analizy dzienników z typem rekordu `tomcat_CL`.  Można pobrać wszystkie rekordy tego typu z następujących wyszukiwania dziennika.

    Type=tomcat_CL

Zagnieżdżone dane JSON źródeł, są obsługiwane, ale są indeksowane na podstawie pola nadrzędnego. Na przykład następujące dane JSON jest zwracana z analizy dzienników wyszukiwanie jako `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [dziennika wyszukiwania](log-analytics-log-searches.md) analizować dane zebrane ze źródeł danych i rozwiązania. 
 
---
title: Zbieranie niestandardowych danych JSON w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Niestandardowe źródła danych JSON mogą być zbierane w usłudze Azure Monitor przy użyciu agenta analizy dzienników dla systemu Linux.  Te niestandardowe źródła danych mogą być proste skrypty zwracając JSON, takie jak curl lub jeden z 300+ wtyczek FluentD.These custom data sources can be simple scripts returning JSON such as curl or one of FluentD's 300+ plugins. W tym artykule opisano konfigurację wymaganą dla tego zbierania danych.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 49eb3fa22bc9afffb9e93f3152cdc00323b76d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662165"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Zbieranie niestandardowych źródeł danych JSON za pomocą agenta usługi Log Analytics dla systemu Linux w usłudze Azure Monitor
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Niestandardowe źródła danych JSON mogą być zbierane w [usłudze Azure Monitor](data-platform.md) przy użyciu agenta usługi Log Analytics dla systemu Linux.  Te niestandardowe źródła danych mogą być proste skrypty zwracając JSON, takie jak [curl](https://curl.haxx.se/) lub jeden z [300 + wtyczek FluentD's](https://www.fluentd.org/plugins/all). W tym artykule opisano konfigurację wymaganą dla tego zbierania danych.


> [!NOTE]
> Agent analizy dzienników dla systemu Linux w wersji 1.1.0-217+ jest wymagany dla niestandardowych danych JSON

## <a name="configuration"></a>Konfigurowanie

### <a name="configure-input-plugin"></a>Konfigurowanie wtyczki wejściowej

Aby zbierać dane JSON `oms.api.` w usłudze Azure Monitor, dodaj do początku tagu FluentD w wtyczce wejściowej.

Na przykład poniżej znajduje `exec-json.conf` się `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`oddzielny plik konfiguracyjny w programie .  W ten sposób używa `exec` wtyczki FluentD do uruchamiania polecenia curl co 30 sekund.  Dane wyjściowe z tego polecenia są zbierane przez wtyczkę wyjściową JSON.

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
Plik konfiguracji dodany `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` w obszarze będzie wymagać, aby jego własność została zmieniona za pomocą następującego polecenia.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Konfigurowanie wtyczki wyjściowej 
Dodaj następującą konfigurację wtyczki `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` wyjściowej do konfiguracji głównej w lub jako oddzielny plik konfiguracyjny umieszczony w`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

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
Uruchom ponownie agenta usługi Log Analytics dla systemu Linux za pomocą następującego polecenia.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Dane wyjściowe
Dane będą zbierane w usłudze Azure `<FLUENTD_TAG>_CL`Monitor z typem rekordu .

Na przykład tag `tag oms.api.tomcat` niestandardowy w usłudze `tomcat_CL`Azure Monitor z typem rekordu .  Można pobrać wszystkie rekordy tego typu z następującą kwerendą dziennika.

    Type=tomcat_CL

Zagnieżdżone źródła danych JSON są obsługiwane, ale są indeksowane na podstawie pola nadrzędnego. Na przykład następujące dane JSON są zwracane `tag_s : "[{ "a":"1", "b":"2" }]`z kwerendy dziennika jako .

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md) do analizowania danych zebranych ze źródeł danych i rozwiązań. 

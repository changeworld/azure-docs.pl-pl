---
title: Zbieranie danych z zebrane w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Zebrane jest demonów systemu Linux typu open source, który okresowo zbiera dane z aplikacji i informacje o poziomie systemu.  Ten artykuł zawiera informacje dotyczące zbierania danych z zebrane w usłudze Azure Monitor.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: magoedte
ms.openlocfilehash: 2118f137f2c0d32f891a170c3509bceee7ba13ed
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426141"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Zbieranie danych z zebrane na agentach systemu Linux w usłudze Azure Monitor
[Zebrane](https://collectd.org/) jest demonów systemu Linux typu open source, który okresowo zbiera metryki wydajności z aplikacji i informacje o poziomie systemu. Przykładowe aplikacje obejmują maszyny wirtualnej Java (JVM), serwer MySQL i Nginx. Ten artykuł zawiera informacje dotyczące zbierania danych wydajności z zebrane w usłudze Azure Monitor.

Pełną listę dostępnych wtyczek znajduje się w temacie [tabeli wtyczek](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Zebrane — omówienie](media/data-sources-collectd/overview.png)

Następująca konfiguracja zebrane znajduje się w agenta usługi Log Analytics dla systemu Linux na przesyłanie danych zebrane do agenta usługi Log Analytics dla systemu Linux.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Ponadto jeśli używane wersje zebrane przed 5.5 zamiast tego użyj poniższej konfiguracji.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

Domyślnie korzysta z konfiguracji zebrane`write_http` dodatek plug-in, aby Wysyłaj dane metryk wydajności za pośrednictwem portu 26000 do agenta usługi Log Analytics dla systemu Linux. 

> [!NOTE]
> Jeśli to konieczne, można skonfigurować tego portu na port niestandardowy.

Agenta usługi Log Analytics dla systemu Linux również nasłuchuje na porcie 26000 metryki zebrane i konwertuje je do usługi Azure Monitor schematu metryk. Poniżej przedstawiono agenta usługi Log Analytics dla systemu Linux konfiguracji `collectd.conf`.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>Obsługiwane wersje
- Usługa Azure Monitor obsługuje obecnie zebrane wersji 4.8 i nowsze wersje.
- Log Analytics agenta dla systemu Linux v1.1.0-217 lub nowszego jest wymagany na potrzeby kolekcji metryki zebrane.


## <a name="configuration"></a>Konfigurowanie
Poniżej przedstawiono podstawowe kroki, aby skonfigurować zbieranie zebrane dane w usłudze Azure Monitor.

1. Skonfiguruj zebrane w celu wysyłania danych do agenta usługi Log Analytics dla systemu Linux przy użyciu wtyczki write_http.  
2. Konfigurowanie agenta usługi Log Analytics dla systemu Linux do nasłuchiwania pod kątem zebrane dane w odpowiedni port.
3. Uruchom ponownie agenta zebrane i Log Analytics dla systemu Linux.

### <a name="configure-collectd-to-forward-data"></a>Konfigurowanie zebrane do przekazywania danych 

1. Aby dane zebrane trasy do agenta usługi Log Analytics dla systemu Linux `oms.conf` musi zostać dodane do katalogu konfiguracji zebrane firmy. Miejsce docelowe tego pliku zależy od dystrybucja systemu Linux maszyny.

    Jeśli katalog konfiguracji zebrane znajduje się w /etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Jeśli katalog konfiguracji zebrane znajduje się w /etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >W przypadku wersji zebrane przed 5.5 trzeba będzie zmodyfikować tagów w `oms.conf` jak pokazano powyżej.
    >

2. Skopiuj collectd.conf katalog konfiguracji omsagent żądanego obszaru roboczego.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Uruchom ponownie agenta zebrane i Log Analytics dla systemu Linux przy użyciu poniższych poleceń.

    ponowne uruchomienie "sudo" usługi zebrane ponowne uruchomienie "sudo" /opt/microsoft/omsagent/bin/service_control

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Metryki zebrane do konwersji schematu usługi Azure Monitor
Aby zachować znanego modelu między metryk infrastruktury już zebrane przez agenta usługi Log Analytics dla systemu Linux i nowe metryki zebrane przez zebrane następujące mapowanie schematu jest używany:

| Metryki zebrane pola | Pole monitorowania platformy Azure |
|:--|:--|
| `host` | Computer (Komputer) |
| `plugin` | Brak |
| `plugin_instance` | Nazwa wystąpienia<br>Jeśli **plugin_instance** jest *null* następnie InstanceName = "*_Total*" |
| `type` | Nazwa obiektu |
| `type_instance` | CounterName<br>Jeśli **type_instance** jest *null* następnie CounterName =**puste** |
| `dsnames[]` | CounterName |
| `dstypes` | Brak |
| `values[]` | CounterValue |

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md) analizować dane zbierane z innych źródeł danych i rozwiązań. 
* Użyj [pól niestandardowych](custom-fields.md) do analizowania danych z rekordy syslog na poszczególne pola.

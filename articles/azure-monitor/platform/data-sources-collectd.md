---
title: Zbieranie danych z zebranych w Azure Monitor | Microsoft Docs
description: Zebrany to demon Open Source systemu Linux, który okresowo zbiera dane z aplikacji i informacji o poziomie systemu.  Ten artykuł zawiera informacje dotyczące zbierania danych z zebranych w Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/27/2018
ms.openlocfilehash: 277e6c9736266b64fd717b719dc740525047ae88
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75395870"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Zbieraj dane z zebranych w agencie systemu Linux w Azure Monitor
[Zebrany](https://collectd.org/) to demon Open Source systemu Linux, który okresowo zbiera metryki wydajności aplikacji i informacji o poziomie systemu. Przykładowe aplikacje obejmują wirtualna maszyna Java (JVM), MySQL Server i Nginx. Ten artykuł zawiera informacje dotyczące zbierania danych o wydajności z zebranych w Azure Monitor.

Pełną listę dostępnych wtyczek można znaleźć w [tabeli wtyczek](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Przegląd zebranych](media/data-sources-collectd/overview.png)

Następująca zebrana konfiguracja jest uwzględniona w agencie Log Analytics dla systemu Linux w celu rozesłania zebranych danych do agenta Log Analytics dla systemu Linux.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Ponadto w przypadku korzystania z wersji zebranych przed 5,5 Użyj poniższej konfiguracji.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

Zebrana konfiguracja używa wtyczki domyślnej`write_http` do wysyłania danych metryk wydajności przez port 26000 do Log Analytics agenta dla systemu Linux. 

> [!NOTE]
> Ten port można skonfigurować do niestandardowego portu zdefiniowanego w razie potrzeby.

Agent Log Analytics dla systemu Linux nasłuchuje również na porcie 26000 dla zebranych metryk, a następnie konwertuje je na metryki schematu Azure Monitor. Poniżej przedstawiono `collectd.conf`konfiguracji agenta Log Analytics dla systemu Linux.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>

> [!NOTE]
> Zbierane domyślnie są ustawione na odczyt wartości w [przedziale](https://collectd.org/wiki/index.php/Interval)10 sekund. Ponieważ ma to bezpośredni wpływ na ilość danych wysyłanych do dzienników Azure Monitor, może być konieczne dostrojenie tego interwału w ramach zebranej konfiguracji w celu uzyskania odpowiedniej równowagi między wymaganiami dotyczącymi monitorowania a związanymi z nimi kosztami i użyciem dzienników Azure Monitor.

## <a name="versions-supported"></a>Obsługiwane wersje
- Azure Monitor obecnie obsługuje zebraną wersję 4,8 lub nowszą.
- Agent Log Analytics dla systemu Linux v 1.1.0-217 lub nowszego jest wymagany dla kolekcji metryk zebranych.


## <a name="configuration"></a>Konfigurowanie
Poniżej przedstawiono podstawowe kroki konfigurowania kolekcji zbieranych danych w Azure Monitor.

1. Skonfiguruj zebrane dane w celu wysłania danych do agenta Log Analytics dla systemu Linux przy użyciu wtyczki write_http.  
2. Skonfiguruj Log Analytics agenta dla systemu Linux, aby nasłuchiwać zebranych danych na odpowiednim porcie.
3. Uruchom ponownie zebrane i Log Analytics agenta dla systemu Linux.

### <a name="configure-collectd-to-forward-data"></a>Konfigurowanie zebranych danych do przesyłania dalej 

1. Aby skierować dane zebrane do agenta Log Analytics dla systemu Linux, `oms.conf` należy dodać do katalogu konfiguracji zebranych. Miejsce docelowe tego pliku zależy od dystrybucji komputera z systemem Linux.

    Jeśli zebrany katalog konfiguracji znajduje się w/etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Jeśli zebrany katalog konfiguracji znajduje się w/etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >W przypadku wersji zebranych przed 5,5 należy zmodyfikować Tagi w `oms.conf` jak pokazano powyżej.
    >

2. Kopiuj zebrane. conf do katalogu konfiguracji omsagent wybranego obszaru roboczego.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Uruchom ponownie zebrane i Log Analytics agenta dla systemu Linux przy użyciu następujących poleceń.

    Usługa sudo została ponownie uruchomiona sudo/opt/Microsoft/omsagent/bin/service_control ponownego uruchomienia

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Zebrane metryki do Azure Monitor konwersji schematu
Aby zachować znany model między metrykami infrastruktury już zebranymi przez agenta Log Analytics dla systemu Linux i są używane nowe metryki zbierane przez zebrane następujące mapowanie schematu:

| Pole metryki zebranej | Pole Azure Monitor |
|:--|:--|
| `host` | Computer (Komputer) |
| `plugin` | Brak |
| `plugin_instance` | Nazwa wystąpienia<br>Jeśli **plugin_instance** ma *wartość null* , następnie InstanceName = " *_Total*" |
| `type` | ObjectName |
| `type_instance` | CounterName<br>Jeśli **type_instance** ma *wartość null* , CounterName =**puste** |
| `dsnames[]` | CounterName |
| `dstypes` | Brak |
| `values[]` | CounterValue |

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md) analizować dane zbierane z innych źródeł danych i rozwiązań. 
* Użyj [pól niestandardowych](custom-fields.md) do analizowania danych z rekordy syslog na poszczególne pola.

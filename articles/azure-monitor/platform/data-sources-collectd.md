---
title: Zbieranie danych z kolekcji CollectD w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: CollectD to demon linuksowy typu open source, który okresowo zbiera dane z aplikacji i informacji na poziomie systemu.  Ten artykuł zawiera informacje dotyczące zbierania danych z kolekcjonowania w usłudze Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/27/2018
ms.openlocfilehash: b8c09d4ac5d0856eb0d448a1cabd9adc567850c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670614"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Zbieranie danych z collectD na temat agentów systemu Linux w usłudze Azure Monitor
[CollectD](https://collectd.org/) to demon linuksowy typu open source, który okresowo zbiera metryki wydajności z aplikacji i informacji na poziomie systemu. Przykładowe aplikacje obejmują maszynę wirtualną Java (JVM), serwer MySQL i Nginx. Ten artykuł zawiera informacje dotyczące zbierania danych o wydajności z kolekcjonowania w usłudze Azure Monitor.

Pełną listę dostępnych wtyczek można znaleźć w [Tabeli wtyczek](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Przegląd kolekcji CollectD](media/data-sources-collectd/overview.png)

Następująca konfiguracja CollectD jest zawarta w agencie usługi Log Analytics dla systemu Linux, aby rozsyłać dane CollectD do agenta usługi Log Analytics dla systemu Linux.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Ponadto jeśli przy użyciu wersji collectD przed 5.5 użyj następującej konfiguracji zamiast tego.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

Konfiguracja CollectD używa`write_http` domyślnej wtyczki do wysyłania danych metryk wydajności za pomocą portu 26000 do agenta usługi Log Analytics dla systemu Linux. 

> [!NOTE]
> W razie potrzeby można skonfigurować ten port do portu zdefiniowanego na zamówienie.

Agent usługi Log Analytics dla systemu Linux nasłuchuje również na metrykach portu 26000 dla metryk CollectD, a następnie konwertuje je na metryki schematu usługi Azure Monitor. Poniżej znajduje się agent usługi `collectd.conf`Log Analytics dla konfiguracji systemu Linux .

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>

> [!NOTE]
> CollectD domyślnie jest ustawiony na odczyt wartości w [odstępie](https://collectd.org/wiki/index.php/Interval)10 sekund . Ponieważ ma to bezpośredni wpływ na ilość danych wysyłanych do dzienników usługi Azure Monitor, może być konieczne dostrojenie tego interwału w konfiguracji CollectD, aby uzyskać właściwą równowagę między wymaganiami monitorowania i skojarzonymi kosztami i użyciem dzienników usługi Azure Monitor.

## <a name="versions-supported"></a>Obsługiwane wersje
- Usługa Azure Monitor obsługuje obecnie usługę CollectD w wersji 4.8 lub wyższej.
- Agent analizy dziennika dla systemu Linux w wersji 1.1.0-217 lub wyższej jest wymagany do zbierania danych metryk CollectD.


## <a name="configuration"></a>Konfigurowanie
Poniżej przedstawiono podstawowe kroki konfigurowania zbierania danych gromadzonych w usłudze Azure Monitor.

1. Skonfiguruj CollectD do wysyłania danych do agenta usługi Log Analytics dla systemu Linux przy użyciu wtyczki write_http.  
2. Skonfiguruj agenta usługi Log Analytics dla systemu Linux, aby nasłuchiwać danych CollectD na odpowiednim porcie.
3. Uruchom ponownie agent CollectD i Log Analytics dla systemu Linux.

### <a name="configure-collectd-to-forward-data"></a>Konfigurowanie kolekcjonuje do przesyłania dalej danych 

1. Aby rozsyłać dane CollectD `oms.conf` do agenta usługi Log Analytics dla systemu Linux, należy dodać do katalogu konfiguracyjnego CollectD. Miejsce docelowe tego pliku zależy od dystrybucji Linuksa komputera.

    Jeśli katalog konfiguracjowy CollectD znajduje się w /etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Jeśli katalog konfiguracjowy CollectD znajduje się w /etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >W przypadku wersji CollectD przed 5.5 będziesz `oms.conf` musiał zmodyfikować tagi, jak pokazano powyżej.
    >

2. Skopiuj plik collectd.conf do katalogu konfiguracji omsagent żądanego obszaru roboczego.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Uruchom ponownie agenta CollectD i Log Analytics dla systemu Linux za pomocą następujących poleceń.

    usługa sudo zbiera ponowne uruchomienie sudo /opt/microsoft/omsagent/bin/service_control restart

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Metryki CollectD do konwersji schematu usługi Azure Monitor
Aby zachować znany model między metrykami infrastruktury już zebranymi przez agenta usługi Log Analytics dla systemu Linux, a nowymi metrykami zebranymi przez CollectD jest używane następujące mapowanie schematu:

| Pole Metryka Zbieranie danych | Pole Monitor platformy Azure |
|:--|:--|
| `host` | Computer (Komputer) |
| `plugin` | Brak |
| `plugin_instance` | Nazwa wystąpienia<br>Jeśli **plugin_instance** jest *null,* to InstanceName="*_Total*" |
| `type` | ObjectName |
| `type_instance` | CounterName<br>Jeśli **type_instance** jest *null,* to Nazwa przeciwmroczyzna=**puste** |
| `dsnames[]` | CounterName |
| `dstypes` | Brak |
| `values[]` | Wartość licznika |

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md) do analizowania danych zebranych ze źródeł danych i rozwiązań. 
* [Użyj pól niestandardowych,](custom-fields.md) aby przeanalizować dane z rekordów syslogu w poszczególnych polach.

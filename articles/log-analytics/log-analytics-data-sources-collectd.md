---
title: Zbieranie danych z CollectD w OMS Log Analytics | Dokumentacja firmy Microsoft
description: CollectD jest demonów systemu Linux typu open source, który okresowo zbiera dane z aplikacji oraz informacje o poziomie systemu.  Ten artykuł zawiera informacje dotyczące zbierania danych z CollectD w analizy dzienników.
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
ms.date: 05/02/2017
ms.author: magoedte
ms.openlocfilehash: a63b15ca5126b45451f0694c9ee75d7b67b1ceaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855208"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-log-analytics"></a>Zbieranie danych z CollectD na agentach systemu Linux w analizy dzienników
[CollectD](https://collectd.org/) jest demonów systemu Linux typu open source, która okresowo gromadzi metryki wydajności z aplikacji i informacji o poziomie systemu. Przykładowe aplikacje obejmują maszyny wirtualnej Java (JVM), serwer MySQL i Nginx. Ten artykuł zawiera informacje dotyczące zbierania danych wydajności z CollectD w analizy dzienników.

Pełną listę dostępnych wtyczek można znaleźć w folderze [tabeli wtyczek](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Omówienie CollectD](media/log-analytics-data-sources-collectd/overview.png)

Następująca konfiguracja CollectD wchodzi w Agent pakietu OMS dla systemu Linux na przesyłanie danych CollectD do Agent pakietu OMS dla systemu Linux.

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Ponadto jeśli używane wersje collectD przed 5.5 zamiast tego użyj następującej konfiguracji.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

Konfiguracja CollectD używa domyślnej`write_http` dodatek plug-in, aby wysłać dane wydajności za pośrednictwem portu 26000 do agenta pakietu OMS dla systemu Linux. 

> [!NOTE]
> W razie potrzeby można skonfigurować tego portu do portu niestandardowy.

Agent pakietu OMS dla systemu Linux również nasłuchuje na porcie 26000 dla metryki CollectD, a następnie konwertuje je na OMS schematu metryki. Poniżej znajduje się Agent pakietu OMS dla systemu Linux konfiguracji `collectd.conf`.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>Obsługiwane wersje
- Analiza dzienników obsługuje obecnie CollectD wersji 4.8 i powyżej.
- Dla kolekcji metryki CollectD wymagany jest Agent pakietu OMS dla systemu Linux v1.1.0-217 lub nowszym.


## <a name="configuration"></a>Konfiguracja
Poniżej przedstawiono podstawowe czynności umożliwiające konfigurowanie zbierania danych CollectD w analizy dzienników.

1. Skonfiguruj CollectD do wysyłania danych do Agent pakietu OMS dla systemu Linux przy użyciu wtyczki write_http.  
2. Skonfiguruj agenta pakietu OMS dla systemu Linux odbierać dane CollectD na odpowiedni port.
3. Uruchom ponownie CollectD i Agent pakietu OMS dla systemu Linux.

### <a name="configure-collectd-to-forward-data"></a>Skonfiguruj CollectD przekazywanie danych 

1. Na przesyłanie danych CollectD do Agent pakietu OMS dla systemu Linux `oms.conf` musi zostać dodany do katalogu konfiguracji CollectD firmy. Miejsce docelowe pliku zależy od distro systemu Linux na komputerze.

    Jeśli w katalogu config CollectD znajduje się w /etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Jeśli w katalogu config CollectD znajduje się w /etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >Dla wersji CollectD przed 5.5 należy zmodyfikować tagów w `oms.conf` zgodnie z powyższym.
    >

2. Skopiuj collectd.conf do katalogu konfiguracji omsagent żądanego obszaru roboczego.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Uruchom ponownie CollectD i Agent pakietu OMS dla systemu Linux za pomocą następujących poleceń.

    ponowne uruchomienie sudo usługi collectd ponownego uruchomienia operacji sudo /opt/microsoft/omsagent/bin/service_control

## <a name="collectd-metrics-to-log-analytics-schema-conversion"></a>Metryki CollectD do konwersji schematu analizy dzienników
Aby zachować znanego modelu między metryki infrastruktury już zebrane przez agenta pakietu OMS dla systemu Linux i nowe metryki zbierane przez CollectD następującego mapowania schematu jest używany:

| Metryka CollectD pola | Pole analizy dzienników |
|:--|:--|
| Host | Computer (Komputer) |
| Wtyczki | Brak |
| plugin_instance | Nazwa wystąpienia<br>Jeśli **plugin_instance** jest *null* następnie InstanceName = "*_Total*" |
| type | Nazwa obiektu |
| type_instance | CounterName<br>Jeśli **type_instance** jest *null* następnie CounterName =**puste** |
| [dsnames] | CounterName |
| dstypes | Brak |
| wartości] | Równowartości |

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [dziennika wyszukiwania](log-analytics-log-searches.md) analizować dane zebrane ze źródeł danych i rozwiązania. 
* Użyj [pola niestandardowe](log-analytics-custom-fields.md) do analizowania danych z rekordów dziennika systemowego do poszczególnych pól.


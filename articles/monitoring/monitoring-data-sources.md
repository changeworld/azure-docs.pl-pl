---
title: Źródła danych w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: W tym artykule opisano dostępne do monitorowania kondycji i wydajności zasobów na platformie Azure i aplikacji działających na nich danych.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2018
ms.author: bwren
ms.openlocfilehash: 5e9dc207d84a9a66d83f01f49c3aefe2d77a64fa
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281442"
---
# <a name="sources-of-data-in-azure-monitor"></a>Źródła danych w usłudze Azure Monitor
W tym artykule opisano źródeł danych zbieranych przez usługi Azure Monitor do monitorowania kondycji i wydajności zasobów oraz aplikacje uruchomione na nich. Te zasoby może być na platformie Azure, w innej chmurze lub lokalnie.  Zobacz [dane zebrane przez usługi Azure Monitor](monitoring-data-collection.md) szczegółowe informacje na temat sposobu przechowywania tych danych i jak można je wyświetlić.

Monitorowanie danych na platformie Azure pochodzi z różnych źródeł, które mogą być organizowane w warstwach, najwyższej warstwy aplikacji i systemy operacyjne i niższych warstwach, są składniki platformy Azure. Jest to zilustrowane na poniższym diagramie z każdej warstwy, które opisano szczegółowo w poniższych sekcjach.

![Warstwy danych monitorowania](media/monitoring-data-sources/monitoring-tiers.png)

## <a name="azure-tenant"></a>Dzierżawa usługi Azure
Dane telemetryczne związane z dzierżawą platformy Azure są zbierane z całej dzierżawie usług, takich jak Azure Active Directory.

![Kolekcja dzierżawy platformy Azure](media/monitoring-data-sources/tenant-collection.png)

### <a name="azure-active-directory-audit-logs"></a>Dzienniki inspekcji usługi Azure Active Directory
[Raporty usługi Azure Active Directory](../active-directory/reports-monitoring/overview-reports.md) zawiera historię logowania działań i inspekcji dziennik zmian wprowadzonych w określonej dzierżawie. Te dzienniki inspekcji mogą być zapisywane do usługi Log Analytics do analizowania je z innymi danymi dziennika.


## <a name="azure-platform"></a>Platforma Azure
Kondycja i działania związane z telemetrią systemu Azure sam obejmuje dane dotyczące operacji i zarządzania subskrypcją platformy Azure. Obejmuje to usługę kondycji dane przechowywane w dzienniku aktywności platformy Azure i dzienników inspekcji w usłudze Azure Active Directory.

![Kolekcja subskrypcji platformy Azure](media/monitoring-data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure Service Health
[Usługa Azure Service Health](../monitoring-and-diagnostics/monitoring-service-notifications.md) zawiera informacje o kondycji usług platformy Azure w ramach subskrypcji, korzystające z aplikacji i zasobów. Można tworzyć alerty, aby otrzymywać powiadomienia o bieżący i oczekiwany istotnych kwestiach, które mogą wpływać na aplikację. Rekordy usługi kondycji są przechowywane w [dziennik aktywności platformy Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), dzięki czemu można je wyświetlić w Eksploratorze dziennika aktywności i skopiuj je do usługi Log Analytics.

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure
[Dziennika aktywności platformy Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) zawiera rekordy kondycji usługi wraz z rekordów na zmiany konfiguracji wprowadzone do Twoich zasobów platformy Azure. Dziennik aktywności jest dostępna dla wszystkich zasobów platformy Azure i zapewnia ich _zewnętrznych_ widoku. Konkretne typy rekordów w dzienniku aktywności są opisane w [schemat zdarzeń dziennika aktywności platformy Azure](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

Możesz wyświetlić dziennik aktywności dla określonego zasobu, na jej stronie Azure portal lub widoku dzienników z wielu zasobów w [Explorer dziennika aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Jest to szczególnie przydatne skopiować wpisów dziennika do usługi Log Analytics można łączyć z innymi danymi monitorowania. Można również wysyłać je do innych lokalizacji za pomocą [usługi Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md).



## <a name="azure-services"></a>Usługi systemu Azure
Metryki i zasobów poziom dzienniki diagnostyczne zawierają informacje o _wewnętrzny_ operacji zasobów platformy Azure. Są one dostępne dla większości usług platformy Azure i rozwiązań do zarządzania zapewniania dodatkowego wglądu w określonej usługi.

![Kolekcja zasobów platformy Azure](media/monitoring-data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Metryki
Większość usług platformy Azure spowoduje wygenerowanie [metryk platformy](monitoring-data-collection.md#metrics) odzwierciedlające ich wydajność i działanie. Konkretne [metryki będą się różnić dla każdego typu zasobu](../monitoring-and-diagnostics/monitoring-supported-metrics.md).  Są dostępne w programie Metrics explorer i mogą być kopiowane do usługi Log Analytics dla analizy trendu i innych.


### <a name="resource-diagnostic-logs"></a>Dzienniki diagnostyczne zasobu
Gdy dziennik aktywności zawiera informacje dotyczące operacji wykonywanych na zasoby platformy Azure, poziom zasobów [dzienniki diagnostyczne](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) zapewniają wgląd w działanie samego zasobu.   Wymagania dotyczące konfiguracji i zawartości te dzienniki [zależy od typu zasobu](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

Dzienniki diagnostyczne bezpośrednio nie można wyświetlić w witrynie Azure portal, ale możesz [wysyłać je do usługi Azure storage w celu archiwizowania](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) i wyeksportować je do [Centrum zdarzeń](../event-hubs/event-hubs-about.md) przekierowania do innych usług lub [w Dzienniku Analiza](../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) do analizy. Niektóre zasoby można napisać bezpośrednio do usługi Log Analytics, podczas gdy inne zapisu do konta magazynu przed [importowane do usługi Log Analytics](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="monitoring-solutions"></a>Rozwiązania do monitorowania
 [Monitorowanie rozwiązań](monitoring-solutions.md) zbierania danych, aby dostarczyć dodatkowy wgląd w operacje dotyczące określonej usługi lub aplikacji. One zbierać dane do usługi Log Analytics, których mogą być analizowane za pomocą [języka zapytań](../log-analytics/log-analytics-queries.md) lub [widoków](../log-analytics/log-analytics-view-designer.md) najczęściej uwzględnianych w rozwiązaniu.

## <a name="guest-operating-system"></a>System operacyjny gościa
Zasoby obliczeniowe na platformie Azure, w innych chmur i rozwiązań lokalnych mają system operacyjny gościa do monitorowania. W instalacji jednego lub więcej agentów można zbierać dane telemetryczne z gościa do tych samych narzędzi do monitorowania jako samych usług platformy Azure.

![Kolekcja zasobów obliczeniowych platformy Azure](media/monitoring-data-sources/compute-resource-collection.png)

### <a name="diagnostic-extension"></a>Rozszerzenie diagnostyki
Za pomocą [rozszerzenie Diagnostyka Azure](../monitoring-and-diagnostics/azure-diagnostics.md), można zebrać dzienniki i zasoby obliczeniowe, dane wydajności z systemu operacyjnego klienta platformy Azure. Metryk i dzienników zbieranych z klientów są przechowywane na koncie magazynu platformy Azure, które są dostępne [skonfiguruj usługę Log Analytics, aby zaimportować z](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).  Eksplorator metryk rozumie sposób odczytywania z konta magazynu i będzie zawierać metryk klienta z innymi metrykami zebrane.


### <a name="log-analytics-agent"></a>Log Analytics Agent
Agenta usługi Log Analytics można zainstalować na dowolnym [Windows](../log-analytics/log-analytics-agent-windows.md) lub [Linux]() maszyny wirtualnej lub komputera fizycznego. Maszyna wirtualna może działać na platformie Azure, innej chmurze lub lokalnie.  Agent nawiązuje połączenie z usługą Log Analytics albo bezpośrednio lub za pomocą [podłączonej grupy zarządzania programu System Center Operations Manager](../log-analytics/log-analytics-om-agents.md) i umożliwia zbieranie danych z [źródeł danych](../log-analytics/log-analytics-data-sources.md) konfigurowanej lub [rozwiązań do zarządzania](monitoring-solutions.md) , zapewniania dodatkowego wglądu w aplikacje działające na maszynie wirtualnej.

### <a name="service-map"></a>Mapa usługi
[Usługa Service Map](../monitoring/monitoring-service-map.md) wymaga agenta zależności na maszynach wirtualnych Windows i Linux. Działa to z usługą Log Analytics agent zbiera dane dotyczące procesów uruchomionych na maszynie wirtualnej i zależności zewnętrznych procesów. W nim te dane są przechowywane w usłudze Log Analytics oraz konsoli, który wizualnie wyświetla dane, które są zbierane, oprócz innych — dane przechowywane w usłudze Log Analytics.

## <a name="applications"></a>Aplikacje
Oprócz telemetrii, że aplikacja może zapisać do systemu operacyjnego gościa, monitorowanie aplikacji szczegółowy odbywa się za pomocą [usługi Application Insights](https://docs.microsoft.com/azure/application-insights/). Usługa Application Insights może zbierać dane z aplikacji działających na wielu różnych platformach. Aplikacja może być uruchomiony na platformie Azure, innej chmurze lub lokalnie.

![Zbieranie danych aplikacji](media/monitoring-data-sources/application-collection.png)


### <a name="application-data"></a>Dane aplikacji
Po włączeniu usługi Application Insights dla aplikacji, instalując pakiet Instrumentacja zbiera metryki i dzienniki dotyczące wydajności i działania aplikacji. Obejmuje to szczegółowe informacje dotyczące wyświetleń stron, aplikacji żądań i wyjątków. Usługa Application Insights przechowuje dane, które są zbierane w metryk usługi Azure i usługi Log Analytics. Zawiera on rozbudowane narzędzia do analizowania tych danych, ale można również analizować je przy użyciu danych z innych źródeł przy użyciu narzędzi takich jak Eksplorator metryk i przeszukiwania dzienników.

Można również użyć usługi Application Insights do [tworzenia metryk niestandardowych](../application-insights/app-insights-api-custom-events-metrics.md).  Dzięki temu można zdefiniować własną logiką do obliczania wartości numerycznej, a następnie przechowywanie tę wartość z innymi metrykami, które mogą być dostępne z poziomu Eksploratora metryk i używane do [skalowania automatycznego](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) i alertów dotyczących metryk.

### <a name="dependencies"></a>Zależności
Aby monitorować różne operacje logiczne aplikacji, musisz [zbierać dane telemetryczne dotyczące wielu składników](../application-insights/app-insights-transaction-diagnostics.md). Usługa Application Insights obsługuje [rozproszonych korelacja telemetrii](../application-insights/application-insights-correlation.md) identyfikujący zależności między składnikami, co pozwala analizować je ze sobą.

### <a name="availability-tests"></a>Testy dostępności
[Testy dostępności](../application-insights/app-insights-monitor-web-app-availability.md) w usłudze Application Insights umożliwiają testowanie dostępności i czasu odpowiedzi aplikacji w różnych lokalizacjach w publicznej sieci Internet. Można wykonać test proste polecenie ping, aby sprawdzić, czy aplikacja jest aktywny lub tworzenie testu sieci web, która symuluje scenariusz użytkownika przy użyciu programu Visual Studio.  Testy dostępności nie wymagają żadnych Instrumentacji w aplikacji.

## <a name="custom-sources"></a>Niestandardowe źródła
Oprócz standardowych warstw aplikacji może być konieczne monitorowanie innych zasobów, które mają dane telemetryczne, które nie mogą być zbierane z innymi źródłami danych. Dla tych zasobów należy zapisać te dane przy użyciu interfejsu API usługi Azure Monitor.

![Zbieranie danych niestandardowych](media/monitoring-data-sources/custom-collection.png)

### <a name="data-collector-api"></a>Interfejs API modułu zbierającego dane
Usługa Azure Monitor może zbierać dane dzienników z dowolnego klienta REST przy użyciu [interfejsu API modułu zbierającego dane](../log-analytics/log-analytics-data-collector-api.md). Dzięki temu można tworzyć niestandardowe scenariusze monitorowania i rozszerzania monitorowania do zasobów, które nie udostępniają danych telemetrycznych za pośrednictwem innych źródeł.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [typów danych monitorowania zbieranych przez usługi Azure Monitor](monitoring-data-collection.md) oraz jak wyświetlać i analizować te dane.

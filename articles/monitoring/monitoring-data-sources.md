---
title: Źródła danych na platformie Azure monitorowania | Dokumentacja firmy Microsoft
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
ms.date: 07/05/2018
ms.author: bwren
ms.openlocfilehash: 48cbfac78b41b47419799584837e094d45757628
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627461"
---
# <a name="sources-of-monitoring-data-in-azure"></a>Źródła danych na platformie Azure monitorowania
W tym artykule opisano dostępne do monitorowania kondycji i wydajności zasobów na platformie Azure i aplikacji działających na nich danych.  Zbieranie i analizowanie danych za pomocą narzędzi opisanych w [zbieranie danych na platformie Azure monitorowania](monitoring-data-collection.md)

Monitorowanie danych na platformie Azure pochodzi z różnych źródeł, które mogą być organizowane warstw, najwyższej warstwy są aplikacji i najniższej warstwy, które są na platformie Azure. Jest to zilustrowane na poniższym diagramie z każdej warstwy, które opisano szczegółowo w poniższych sekcjach.

![Warstwy danych monitorowania](media/monitoring-data-sources/monitoring-tiers.png)


## <a name="azure-platform"></a>Platforma Azure
Kondycja i działania związane z telemetrią systemu Azure sam obejmuje dane dotyczące operacji i zarządzania subskrypcją platformy Azure lub dzierżawy. Obejmuje to usługę kondycji dane przechowywane w dzienniku aktywności platformy Azure i dzienników inspekcji w usłudze Azure Active Directory.

![Kolekcja platformy Azure](media/monitoring-data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure Service Health
[Usługa Azure Service Health](../monitoring-and-diagnostics/monitoring-service-notifications.md) zawiera informacje o kondycji usług platformy Azure w ramach subskrypcji, korzystające z aplikacji i zasobów. Można tworzyć alerty, aby otrzymywać powiadomienia o bieżący i oczekiwany istotnych kwestiach, które mogą wpływać na aplikację. Rekordy usługi kondycji są przechowywane w [dziennik aktywności platformy Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), dzięki czemu można je wyświetlić w Eksploratorze dziennika aktywności i skopiuj je do usługi Log Analytics.

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure
[Dziennika aktywności platformy Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) zawiera rekordy kondycji usługi wraz z rekordów na zmiany konfiguracji wprowadzone do Twoich zasobów platformy Azure. Dziennik aktywności jest dostępna dla wszystkich zasobów platformy Azure i zapewnia ich _zewnętrznych_ widoku. Konkretne typy rekordów w dzienniku aktywności są opisane w [schemat zdarzeń dziennika aktywności platformy Azure](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

Możesz wyświetlić dziennik aktywności dla określonego zasobu, na jej stronie Azure portal lub widoku dzienników z wielu zasobów w [Explorer dziennika aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Jest to szczególnie przydatne skopiować wpisów dziennika do usługi Log Analytics można łączyć z innymi danymi monitorowania. Można również wysyłać je do innych lokalizacji za pomocą [usługi Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md).


### <a name="azure-active-directory-audit-logs"></a>Dzienniki inspekcji usługi Azure Active Directory
[Raporty usługi Azure Active Directory](../active-directory/reports-monitoring/overview-reports.md) zawiera historię logowania działań i inspekcji dziennik zmian wprowadzonych w określonej dzierżawie. Obecnie nie można połączyć dane inspekcji usługi Azure Active Directory z innymi danymi monitorowania, ponieważ nie jest tylko dostępny za pośrednictwem usługi Azure Active Directory i [interfejsu API raportowania usługi Azure Active Directory](../active-directory/reports-monitoring/concept-reporting-api.md).


## <a name="azure-services"></a>Usługi systemu Azure
Metryki i zasobów poziom dzienniki diagnostyczne zawierają informacje o _wewnętrzny_ operacji zasobów platformy Azure. Są one dostępne dla większości usług platformy Azure i rozwiązań do zarządzania zapewniania dodatkowego wglądu w określonej usługi.

![Kolekcja zasobów platformy Azure](media/monitoring-data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Metryki
Większość usług platformy Azure, spowoduje wygenerowanie metryki, które odzwierciedlają ich wydajność i działanie. Konkretne [metryki będą się różnić dla każdego typu zasobu](../monitoring-and-diagnostics/monitoring-supported-metrics.md).  Są dostępne w programie Metrics Explorer i mogą być kopiowane do usługi Log Analytics dla analizy trendu i innych.


### <a name="resource-diagnostic-logs"></a>Dzienniki diagnostyczne zasobu
Gdy dziennik aktywności zawiera informacje dotyczące operacji wykonywanych na zasoby platformy Azure, poziom zasobów [dzienniki diagnostyczne](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) zapewniają wgląd w działanie samego zasobu.   Wymagania dotyczące konfiguracji i zawartości te dzienniki [zależy od typu zasobu](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

Dzienniki diagnostyczne bezpośrednio nie można wyświetlić w witrynie Azure portal, ale możesz [wysyłać je do usługi Azure storage w celu archiwizowania](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) i wyeksportować je do [Centrum zdarzeń](../event-hubs/event-hubs-what-is-event-hubs.md) przekierowania do innych usług lub [w Dzienniku Analiza](../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) do analizy. Niektóre zasoby można napisać bezpośrednio do usługi Log Analytics, podczas gdy inne zapisu do konta magazynu przed [importowane do usługi Log Analytics](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="management-solutions"></a>Rozwiązania do zarządzania
 [Rozwiązania do zarządzania](../monitoring/monitoring-solutions.md) zbierania danych, aby dostarczyć dodatkowy wgląd w operacje dotyczące określonej usługi. One zbierać dane do usługi Log Analytics, których mogą być analizowane za pomocą [języka zapytań](../log-analytics/log-analytics-log-search.md) lub widoki, które najczęściej przygotowywanych do uwzględnienia w rozwiązaniu.

## <a name="guest-operating-system"></a>System operacyjny gościa
Oprócz telemetrii wygenerowanej przez wszystkich usług platformy Azure zasoby obliczeniowe mają system operacyjny gościa do monitorowania. W instalacji jednego lub więcej agentów można zbierać dane telemetryczne z gościa do tych samych narzędzi do monitorowania jako samych usług platformy Azure.

![Kolekcja zasobów obliczeniowych platformy Azure](media/monitoring-data-sources/compute-resource-collection.png)

### <a name="diagnostic-extension"></a>Rozszerzenie diagnostyki
Za pomocą [rozszerzenie Diagnostyka Azure](../monitoring-and-diagnostics/azure-diagnostics.md), można zebrać dzienniki i zasoby obliczeniowe, dane wydajności z systemu operacyjnego klienta platformy Azure. Metryk i dzienników zbieranych z klientów są przechowywane na koncie magazynu platformy Azure, które są dostępne [skonfiguruj usługę Log Analytics, aby zaimportować z](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).  Eksplorator metryk rozumie sposób odczytywania z konta magazynu i będzie zawierać metryk klienta z innymi metrykami zebrane.


### <a name="log-analytics-agent"></a>Log Analytics Agent
Agenta usługi Log Analytics można zainstalować na dowolnej maszynie wirtualnej Windows lub Linux lub komputera fizycznego. Maszyna wirtualna może działać na platformie Azure, innej chmurze lub lokalnie.  Agent nawiązuje połączenie z usługą Log Analytics albo bezpośrednio lub za pomocą [podłączonej grupy zarządzania programu System Center Operations Manager](../log-analytics/log-analytics-om-agents.md) i umożliwia zbieranie danych z [źródeł danych](../log-analytics/log-analytics-data-sources.md) konfigurowanej lub [rozwiązań do zarządzania](../monitoring/monitoring-solutions.md) , zapewniania dodatkowego wglądu w aplikacje działające na maszynie wirtualnej.

### <a name="service-map"></a>Mapa usługi
[Usługa Service Map](../operations-management-suite/operations-management-suite-service-map.md) wymaga agenta zależności na maszynach wirtualnych Windows i Linux. Działa to z usługą Log Analytics agent zbiera dane dotyczące procesów uruchomionych na maszynie wirtualnej i zależności zewnętrznych procesów. W nim te dane są przechowywane w usłudze Log Analytics oraz konsoli, który wizualnie wyświetla dane, które są zbierane, oprócz innych — dane przechowywane w usłudze Log Analytics.

## <a name="applications"></a>Aplikacje
Oprócz telemetrii, że aplikacja może zapisać do systemu operacyjnego gościa, monitorowanie aplikacji szczegółowy odbywa się za pomocą [usługi Application Insights](https://docs.microsoft.com/azure/application-insights/). Usługa Application Insights może zbierać dane z aplikacji działających na wielu różnych platformach. Aplikacja może być uruchomiony na platformie Azure, innej chmurze lub lokalnie.

![Zbieranie danych aplikacji](media/monitoring-data-sources/application-collection.png)


#### <a name="application-data"></a>Dane aplikacji
Po włączeniu usługi Application Insights dla aplikacji, instalując pakiet Instrumentacja zbiera metryki i dzienniki dotyczące wydajności i działania aplikacji. Obejmuje to szczegółowe informacje dotyczące wyświetleń stron, aplikacji żądań i wyjątków. Usługa Application Insights przechowuje dane, które są zbierane w metryk usługi Azure i usługi Log Analytics. Zawiera on rozbudowane narzędzia do analizowania tych danych, ale można również analizować je przy użyciu danych z innych źródeł przy użyciu narzędzi takich jak Eksplorator metryk i przeszukiwania dzienników.

Można również użyć usługi Application Insights do [tworzenia metryk niestandardowych](../application-insights/app-insights-api-custom-events-metrics.md).  Dzięki temu można zdefiniować własną logiką do obliczania wartości numerycznej, a następnie przechowywanie tę wartość z innymi metrykami, które mogą być dostępne z poziomu Eksploratora metryk i używane do [skalowania automatycznego](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) i alertów dotyczących metryk.

#### <a name="dependencies"></a>Zależności
Aby monitorować różne operacje logiczne aplikacji, musisz [zbierać dane telemetryczne dotyczące wielu składników](../application-insights/app-insights-transaction-diagnostics.md). Usługa Application Insights obsługuje [rozproszonych korelacja telemetrii](../application-insights/application-insights-correlation.md) identyfikujący zależności między składnikami, co pozwala analizować je ze sobą.

#### <a name="availability-tests"></a>Testy dostępności
[Testy dostępności](../application-insights/app-insights-monitor-web-app-availability.md) w usłudze Application Insights umożliwiają testowanie dostępności i czasu odpowiedzi aplikacji w różnych lokalizacjach w publicznej sieci Internet. Można wykonać test proste polecenie ping, aby sprawdzić, czy aplikacja jest aktywny lub tworzenie testu sieci web, która symuluje scenariusz użytkownika przy użyciu programu Visual Studio.  Testy dostępności nie wymagają żadnych Instrumentacji w aplikacji.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [typów danych monitorowania i narzędzia platformy Azure](monitoring-data-collection.md) umożliwia zbieranie i analizowanie ich.

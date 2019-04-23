---
title: Źródła danych w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: W tym artykule opisano dostępne do monitorowania kondycji i wydajności zasobów na platformie Azure i aplikacji działających na nich danych.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: bwren
ms.openlocfilehash: 6d03c219025c8cd39214bd8ab6807125709f9742
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790880"
---
# <a name="sources-of-data-in-azure-monitor"></a>Źródła danych w usłudze Azure Monitor
W tym artykule opisano źródeł danych zbieranych przez usługi Azure Monitor do monitorowania kondycji i wydajności zasobów oraz aplikacje uruchomione na nich. Te zasoby może być na platformie Azure, w innej chmurze lub lokalnie.  Zobacz [dane zebrane przez usługi Azure Monitor](data-platform.md) szczegółowe informacje na temat sposobu przechowywania tych danych i jak można je wyświetlić.

Monitorowanie danych na platformie Azure pochodzi z różnych źródeł, które mogą być organizowane w warstwach, najwyższej warstwy aplikacji i systemy operacyjne i niższych warstwach, są składniki platformy Azure. Jest to zilustrowane na poniższym diagramie z każdej warstwy, które opisano szczegółowo w poniższych sekcjach.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

![Warstwy danych monitorowania](media/data-sources/monitoring-tiers.png)

## <a name="azure-tenant"></a>Dzierżawa usługi Azure
Dane telemetryczne związane z dzierżawą platformy Azure są zbierane z całej dzierżawie usług, takich jak Azure Active Directory.

![Kolekcja dzierżawy platformy Azure](media/data-sources/tenant-collection.png)

### <a name="azure-active-directory-audit-logs"></a>Dzienniki inspekcji usługi Azure Active Directory
[Raporty usługi Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) zawiera historię logowania działań i inspekcji dziennik zmian wprowadzonych w określonej dzierżawie. Te dzienniki inspekcji mogą być zapisywane dzienniki usługi Azure Monitor, aby analizować je przy użyciu innych danych dziennika.


## <a name="azure-platform"></a>Platforma Azure
Kondycja i działania związane z telemetrią systemu Azure sam obejmuje dane dotyczące operacji i zarządzania subskrypcją platformy Azure. Obejmuje to usługę kondycji dane przechowywane w dzienniku aktywności platformy Azure i dzienników inspekcji w usłudze Azure Active Directory.

![Kolekcja subskrypcji platformy Azure](media/data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure Service Health
[Usługa Azure Service Health](service-notifications.md) zawiera informacje o kondycji usług platformy Azure w ramach subskrypcji, korzystające z aplikacji i zasobów. Można tworzyć alerty, aby otrzymywać powiadomienia o bieżący i oczekiwany istotnych kwestiach, które mogą wpływać na aplikację. Rekordy usługi kondycji są przechowywane w [dziennik aktywności platformy Azure](activity-logs-overview.md), dzięki czemu można je wyświetlić w Eksploratorze dziennika aktywności i skopiuj je do dzienników usługi Azure Monitor.

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure
[Dziennika aktywności platformy Azure](activity-logs-overview.md) zawiera rekordy kondycji usługi wraz z rekordów na zmiany konfiguracji wprowadzone do Twoich zasobów platformy Azure. Dziennik aktywności jest dostępna dla wszystkich zasobów platformy Azure i zapewnia ich _zewnętrznych_ widoku. Konkretne typy rekordów w dzienniku aktywności są opisane w [schemat zdarzeń dziennika aktywności platformy Azure](activity-log-schema.md).

Możesz wyświetlić dziennik aktywności dla określonego zasobu, na jej stronie Azure portal lub widoku dzienników z wielu zasobów w [Explorer dziennika aktywności](activity-logs-overview.md). Jest to szczególnie przydatne skopiować wpisów dziennika do usługi Azure Monitor, aby połączyć je z innymi danymi monitorowania. Można również wysyłać je do innych lokalizacji za pomocą [usługi Event Hubs](activity-logs-stream-event-hubs.md).



## <a name="azure-services"></a>Usługi platformy Azure
Metryki i zasobów poziom dzienniki diagnostyczne zawierają informacje o _wewnętrzny_ operacji zasobów platformy Azure. Są one dostępne dla większości usług platformy Azure i rozwiązań do zarządzania zapewniania dodatkowego wglądu w określonej usługi.

![Kolekcja zasobów platformy Azure](media/data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Metryki
Większość usług platformy Azure spowoduje wygenerowanie [metryk platformy](data-platform-metrics.md) odzwierciedlające ich wydajność i działanie. Konkretne [metryki będą się różnić dla każdego typu zasobu](metrics-supported.md).  Są dostępne z analizy metryki i mogą być kopiowane do dzienników dla analizy trendu i innych za pomocą usługi Log Analytics.


### <a name="resource-diagnostic-logs"></a>Dzienniki diagnostyczne zasobu
Gdy dziennik aktywności zawiera informacje dotyczące operacji wykonywanych na zasoby platformy Azure, poziom zasobów [dzienniki diagnostyczne](diagnostic-logs-overview.md) zapewniają wgląd w działanie samego zasobu.   Wymagania dotyczące konfiguracji i zawartości te dzienniki [zależy od typu zasobu](diagnostic-logs-schema.md).

Dzienniki diagnostyczne bezpośrednio nie można wyświetlić w witrynie Azure portal, ale możesz [wysyłać je do usługi Azure storage w celu archiwizowania](archive-diagnostic-logs.md) i wyeksportować je do [Centrum zdarzeń](../../event-hubs/event-hubs-about.md) przekierowania do innych usług lub [do Usługa Azure Monitor](diagnostic-logs-stream-log-store.md) do analizy. Niektóre zasoby można napisać bezpośrednio do usługi Azure Monitor, podczas gdy inne zapisu do konta magazynu przed [importowane do usługi Log Analytics](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="monitoring-solutions"></a>Rozwiązania do monitorowania
 [Monitorowanie rozwiązań](../../azure-monitor/insights/solutions.md) zbierania danych, aby dostarczyć dodatkowy wgląd w operacje dotyczące określonej usługi lub aplikacji. One zbierać dane do usługi Azure Monitor dzienniki których mogą być analizowane za pomocą [języka zapytań](../../azure-monitor/log-query/log-query-overview.md) lub [widoków](view-designer.md) najczęściej uwzględnianych w rozwiązaniu.


## <a name="guest-operating-system"></a>System operacyjny gościa
Zasoby obliczeniowe na platformie Azure, w innych chmur i rozwiązań lokalnych mają system operacyjny gościa do monitorowania. W instalacji jednego lub więcej agentów można zbierać dane telemetryczne z gościa do tych samych narzędzi do monitorowania jako samych usług platformy Azure.

![Kolekcja zasobów obliczeniowych platformy Azure](media/data-sources/compute-resource-collection.png)

### <a name="azure-diagnostic-extension"></a>Rozszerzenie diagnostyki platformy Azure
Za pomocą rozszerzenia usługi Azure Diagnostics oferty klient otrzymuje podstawowy poziom monitorowania, zbieranie dzienników i zasoby obliczeniowe, dane wydajności z systemu operacyjnego klienta platformy Azure.   

### <a name="log-analytics-agent"></a>Log Analytics agent
Kompleksowe monitorowanie i zarządzanie maszyny wirtualne Windows lub Linux lub komputera fizycznego jest dostarczany za pomocą agenta usługi Log Analytics. Maszyna wirtualna może być uruchomiony na platformie Azure, innej chmurze lub lokalnie i agent nawiązuje połączenie Azure monitorowanie, bezpośrednio lub za pomocą programu System Center Operations Manager i umożliwia zbieranie danych z [źródeł danych](agent-data-sources.md) , Konfigurowanie lub [rozwiązania do monitorowania](../../azure-monitor/insights/solutions.md) , zapewniania dodatkowego wglądu w aplikacje działające na maszynie wirtualnej.


### <a name="dependency-agent"></a>Agent zależności
[Usługa Service Map](../insights/service-map.md) i [usługi Azure Monitor dla maszyn wirtualnych](../../azure-monitor/insights/vminsights-overview.md) wymaga agenta zależności na maszynach wirtualnych Windows i Linux. To jest zintegrowany z agenta usługi Log Analytics zbiera odnalezionych danych dotyczących procesów uruchomionych na maszynę wirtualną i zależności w procesie zewnętrznym. Ona te dane są przechowywane w usłudze Azure Monitor i wizualizuje odnalezionych powiązanych elementów.  

Aby jeszcze lepiej zrozumieć różnice między agentami a której mają zostać użyte w zależności od wymagań dotyczących monitorowania, zobacz [omówienie agentów monitorowania](agents-overview.md).

## <a name="applications"></a>Aplikacje
Oprócz telemetrii, że aplikacja może zapisać do systemu operacyjnego gościa, monitorowanie aplikacji szczegółowy odbywa się za pomocą [usługi Application Insights](https://docs.microsoft.com/azure/application-insights/). Usługa Application Insights może zbierać dane z aplikacji działających na wielu różnych platformach. Aplikacja może być uruchomiony na platformie Azure, innej chmurze lub lokalnie.

![Zbieranie danych aplikacji](media/data-sources/application-collection.png)


### <a name="application-data"></a>Dane aplikacji
Po włączeniu usługi Application Insights dla aplikacji, instalując pakiet Instrumentacja zbiera metryki i dzienniki dotyczące wydajności i działania aplikacji. Obejmuje to szczegółowe informacje dotyczące wyświetleń stron, aplikacji żądań i wyjątków. Usługa Application Insights są przechowywane dane, które są zbierane w usłudze Azure Monitor. Zawiera on rozbudowane narzędzia do analizowania tych danych, ale można również analizować je przy użyciu danych z innych źródeł przy użyciu narzędzi, takich jak metryki analytics i log analytics.

Można również użyć usługi Application Insights do [tworzenia metryk niestandardowych](../../application-insights/app-insights-api-custom-events-metrics.md).  Dzięki temu można zdefiniować własną logiką do obliczania wartości numerycznej, a następnie przechowywanie tę wartość z innymi metrykami, które mogą być dostępne z analizy metryki i używane do [skalowania automatycznego](autoscale-custom-metric.md) i alertów dotyczących metryk.


### <a name="dependencies"></a>Zależności
Aby monitorować różne operacje logiczne aplikacji, musisz [zbierać dane telemetryczne dotyczące wielu składników](../../application-insights/app-insights-transaction-diagnostics.md). Usługa Application Insights obsługuje [rozproszonych korelacja telemetrii](../../application-insights/application-insights-correlation.md) identyfikujący zależności między składnikami, co pozwala analizować je ze sobą.

### <a name="availability-tests"></a>Testy dostępności
[Testy dostępności](../../application-insights/app-insights-monitor-web-app-availability.md) w usłudze Application Insights umożliwiają testowanie dostępności i czasu odpowiedzi aplikacji w różnych lokalizacjach w publicznej sieci Internet. Można wykonać test proste polecenie ping, aby sprawdzić, czy aplikacja jest aktywny lub tworzenie testu sieci web, która symuluje scenariusz użytkownika przy użyciu programu Visual Studio.  Testy dostępności nie wymagają żadnych Instrumentacji w aplikacji.

## <a name="custom-sources"></a>Niestandardowe źródła
Oprócz standardowych warstw aplikacji może być konieczne monitorowanie innych zasobów, które mają dane telemetryczne, które nie mogą być zbierane z innymi źródłami danych. Dla tych zasobów należy zapisać te dane przy użyciu interfejsu API usługi Azure Monitor.

![Zbieranie danych niestandardowych](media/data-sources/custom-collection.png)

### <a name="data-collector-api"></a>Interfejs API modułu zbierającego dane
Usługa Azure Monitor może zbierać dane dzienników z dowolnego klienta REST przy użyciu [interfejsu API modułu zbierającego dane](../../azure-monitor/platform/data-collector-api.md). Dzięki temu można tworzyć niestandardowe scenariusze monitorowania i rozszerzania monitorowania do zasobów, które nie udostępniają danych telemetrycznych za pośrednictwem innych źródeł.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [typów danych monitorowania zbieranych przez usługi Azure Monitor](data-platform.md) oraz jak wyświetlać i analizować te dane.

---
title: Analizuj dane dzienników w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Wymagane jest zapytanie dziennika, aby pobrać dane dzienników z usługi Azure Monitor.  W tym artykule opisano sposób nowy dziennik, zapytania są używane w usłudze Azure Monitor i zapewnia pojęcia, które należy zrozumieć przed utworzeniem jeden.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: bwren
ms.openlocfilehash: dcac701f3c1b6d64a7017c31679c019b91103ba2
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904733"
---
# <a name="analyze-log-data-in-azure-monitor"></a>Analizuj dane dzienników w usłudze Azure Monitor

Dane dzienników zbieranych przez usługi Azure Monitor są przechowywane w obszarze roboczym usługi Log Analytics, która jest oparta na [Eksploratora danych usługi Azure](/azure/data-explorer). Gromadzi dane telemetryczne z różnych źródeł i używa [język zapytania Kusto](/azure/kusto/query) wykorzystywane przez Eksploratora danych do pobierania i analizowania danych.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="log-queries"></a>Rejestrowanie zapytań

Wymagane jest zapytanie dziennika, aby pobrać wszystkie dane dzienników z usługi Azure Monitor.  Czy jesteś [analizowanie danych w portalu](portals.md), [konfigurowania reguły alertu](../platform/alerts-metric.md) Aby otrzymywać powiadomienia o określony warunek, lub podczas pobierania danych przy użyciu [interfejsu API usługi Azure Monitor dzienniki](https://dev.loganalytics.io/) , użyjesz zapytania, aby określić dane, które mają.  Ten artykuł zawiera opis używania kwerend dzienników w usłudze Azure Monitor i oferuje pojęcia, które należy zrozumieć przed utworzeniem jeden.

## <a name="where-log-queries-are-used"></a>Gdzie są używane dziennika zapytań

Różne sposoby, że używany jest program zapytań w usłudze Azure Monitor są następujące:

- **Portal.** Można wykonać analizy interakcyjnej danych dziennika w [witryny Azure portal](portals.md).  Dzięki temu można edytować zapytania i analizować wyniki w różnych formatach i wizualizacji.  
- **Reguły alertów.** [Reguły alertów](../platform/alerts-overview.md) aktywnego identyfikowania problemów z danych w obszarze roboczym.  Każda reguła alertu opiera się na wyszukiwanie w dzienniku, który jest automatycznie uruchamiane w regularnych odstępach czasu.  Wyniki są kontrolowane, aby określić, jeśli utworzony alert.
- **Pulpity nawigacyjne.** Możesz przypiąć wyniki dowolnego zapytania do [pulpitu nawigacyjnego platformy Azure](../learn/tutorial-logs-dashboards.md) co pozwala użytkownikowi na wizualizować dane dzienników i metryk ze sobą i opcjonalnie udostępniać innym użytkownikom usługi Azure. 
- **Widoki.**  Możesz utworzyć wizualizacje danych, które mają zostać uwzględnione w pulpitami nawigacyjnymi użytkownika za pomocą [Projektant widoków](../platform/view-designer.md).  Dziennik zapytań zawierają dane używane przez [Kafelki](../platform/view-designer-tiles.md) i [części wizualizacji](../platform/view-designer-parts.md) w każdym widoku.  

- **Eksportowanie.**  Podczas importowania danych dziennika z usługi Azure Monitor do programu Excel lub [usługi Power BI](../platform/powerbi.md), Utwórz zapytanie dziennika do definiowania danych do wyeksportowania.
- **PowerShell.** Skrypt programu PowerShell można uruchomić z wiersza polecenia lub element runbook usługi Automation, która używa [Get AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresults) można pobrać danych dziennika z usługi Azure Monitor.  To polecenie cmdlet wymaga zapytanie w celu określenia danych do pobrania.
- **Interfejs API dzienniki usługi Azure Monitor.**  [Interfejsu API usługi Azure Monitor dzienniki](../platform/alerts-overview.md) umożliwia dowolnego klienta interfejsu API REST do pobierania danych dziennika z obszaru roboczego.  Żądanie interfejsu API zawiera zapytanie, które jest uruchamiane w usłudze Azure Monitor do ustalenia danych, które można pobrać.

![Wyszukiwanie w Dzienniku](media/log-query-overview/queries-overview.png)

## <a name="write-a-query"></a>Napisz zapytanie
Usługa Azure Monitor korzysta z [wersji języka zapytania Kusto](get-started-queries.md) do pobierania i analizowania danych dziennika w na różne sposoby.  Będzie zazwyczaj rozpocząć podstawowe zapytania, a następnie postęp, aby użyć bardziej zaawansowane funkcje, jakie wymagania są coraz bardziej złożone.

Podstawowa struktura zapytania jest tabela źródłowa następuje szereg operatory oddzielony znakiem kreski pionowej `|`.  Można połączyć w łańcuch ze sobą wiele operatorów, aby dostosować dane i wykonywać zaawansowane funkcje.

Na przykład załóżmy, że chcesz odnaleźć najważniejsze dziesięć komputerów za pomocą większość zdarzeń błędów w ciągu ostatniego dnia.

```Kusto
Event
| where (EventLevelName == "Error")
| where (TimeGenerated > ago(1days))
| summarize ErrorCount = count() by Computer
| top 10 by ErrorCount desc
```

Lub być może chcesz znaleźć komputery, które jeszcze nie było pulsu w ciągu ostatniego dnia.

```Kusto
Heartbeat
| where TimeGenerated > ago(7d)
| summarize max(TimeGenerated) by Computer
| where max_TimeGenerated < ago(1d)  
```

Co myślisz o wykres liniowy z wykorzystaniem procesora dla każdego komputera w ostatnim tygodniu?

```Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Processor Time"
| where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
| render timechart    
```

Z tych przykładów szybkiego widać, że niezależnie od rodzaju danych, że pracujesz z strukturę kwerendy jest podobny.  Możesz ją rozbić na etapy, gdzie wynikowe dane z jednego polecenia są wysyłane za pośrednictwem potoku do następnego polecenia.

Można także badać dane w obszarach roboczych usługi Log Analytics w ramach Twojej subskrypcji.

```Kusto
union Update, workspace("contoso-workspace").Update
| where TimeGenerated >= ago(1h)
| summarize dcount(Computer) by Classification 
```

## <a name="how-azure-monitor-log-data-is-organized"></a>Sposób organizowania danych dziennika usługi Azure Monitor
Podczas tworzenia zapytania należy rozpocząć od określenia tabel, które znajdują się dane, którego szukasz. Różne rodzaje danych podzielono na dedykowane tabel w każdym [obszaru roboczego usługi Log Analytics](../learn/quick-create-workspace.md).  Dokumentacja dla różnych źródeł danych zawiera nazwę typu danych, które tworzy i opis każdego z jego właściwości.  Wiele zapytań będzie wymagać tylko dane z pojedynczej tabeli, ale inne mogą używać różnorodne opcje, aby dołączyć dane z wielu tabel.

Gdy [usługi Application Insights](../app/app-insights-overview.md) przechowuje dane aplikacji, takie jak żądania, wyjątki, ślady i użycia w dziennikach w usłudze Azure Monitor, te dane są przechowywane w innej partycji niż dane dziennika. Umożliwia dostęp do tych danych na ten sam język zapytań, ale muszą używać [konsoli Application Insights](../app/analytics.md) lub [interfejsu API REST usługi Application Insights](https://dev.applicationinsights.io/) do niego dostęp. Możesz użyć [zasobów dla wielu kwerendach](../log-query/cross-workspace-query.md) połączenie danych usługi Application Insights z innymi danymi dzienników w usłudze Azure Monitor.


![Tabele](media/log-query-overview/queries-tables.png)




## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o korzystaniu z [przeszukiwania dzienników usługi Log Analytics można tworzyć i edytować](../log-query/portals.md).
- Zapoznaj się z [samouczek na temat pisania zapytań](../log-query/get-started-queries.md) przy użyciu nowego języka zapytań.

---
title: Analizowanie danych usługi Log Analytics w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Wymagane jest przeszukiwania dzienników można pobrać żadnych danych z usługi Log Analytics.  W tym artykule opisano sposób nowy dziennik wyszukiwania są używane w usłudze Log Analytics i zapewnia pojęcia, które należy zrozumieć przed utworzeniem jeden.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 904502e5c73127cca7920da2173edd013e55762f
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242249"
---
# <a name="analyze-log-analytics-data-in-azure-monitor"></a>Analizowanie danych usługi Log Analytics w usłudze Azure Monitor

Dane dzienników zbieranych przez usługi Azure Monitor są przechowywane w obszarze roboczym usługi Log Analytics, która jest oparta na [Eksploratora danych usługi Azure](/data-explorer). Gromadzi dane telemetryczne z różnych źródeł i używa [języka w Eksploratorze danych zapytań](/kusto) do pobierania i analizowania danych.

> [!NOTE]
> Usługa log Analytics wcześniej zostało potraktowane jako własnej usługi na platformie Azure. On teraz jest traktowane jako część usługi Azure Monitor i koncentruje się na magazynu i analizy danych dziennika przy użyciu języka zapytań. Funkcje, które były traktowane jako część usługi Log Analytics, takie jak Windows i Linux agentów do zbierania danych, widoków, aby wizualizować istniejące dane i alerty do aktywnego powiadamiania użytkownika o problemach, nie uległy zmianie, ale teraz są traktowane jako część usługi Azure Monitor.



## <a name="log-queries"></a>Dziennik zapytań

Wymagane jest zapytanie dziennika, aby pobrać wszystkie dane z usługi Log Analytics.  Czy jesteś [analizowanie danych w portalu](log-analytics-log-search-portals.md), [konfigurowania reguły alertu](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) Aby otrzymywać powiadomienia o określony warunek, lub podczas pobierania danych przy użyciu [interfejsu API usługi Log Analytics](https://dev.loganalytics.io/), możesz użyje kwerendy do określenia danych, które chcesz.  Ten artykuł zawiera opis używania kwerend dzienników w usłudze Log Analytics i zapewnia pojęcia, które należy zrozumieć przed utworzeniem jeden.



## <a name="where-log-queries-are-used"></a>Gdzie są używane dziennika zapytań

Różne sposoby, że używany jest program zapytań w usłudze Log Analytics są następujące:

- **Portals.** Można wykonać analizy interakcyjnej danych dziennika w [witryny Azure portal](log-analytics-log-search-portals.md).  Dzięki temu można edytować zapytania i analizować wyniki w różnych formatach i wizualizacji.  
- **Reguły alertów.** [Reguły alertów](log-analytics-alerts.md) aktywnego identyfikowania problemów z danych w obszarze roboczym.  Każda reguła alertu opiera się na wyszukiwanie w dzienniku, który jest automatycznie uruchamiane w regularnych odstępach czasu.  Wyniki są kontrolowane, aby określić, jeśli utworzony alert.
- **Pulpity nawigacyjne.** Możesz przypiąć wyniki dowolnego zapytania do [pulpitu nawigacyjnego platformy Azure]() co pozwala użytkownikowi na wizualizować dane dzienników i metryk ze sobą i opcjonalnie udostępniać innym użytkownikom usługi Azure. 
- **Widoki.**  Możesz utworzyć wizualizacje danych, które mają zostać uwzględnione w pulpitami nawigacyjnymi użytkownika za pomocą [Projektant widoków](log-analytics-view-designer.md).  Dziennik zapytań zawierają dane używane przez [Kafelki](log-analytics-view-designer-tiles.md) i [części wizualizacji](log-analytics-view-designer-parts.md) w każdym widoku.  
- **Eksportowanie.**  Podczas importowania danych z obszaru roboczego usługi Log Analytics do programu Excel lub [usługi Power BI](log-analytics-powerbi.md), Utwórz zapytanie dziennika do definiowania danych do wyeksportowania.
- **PowerShell.** Skrypt programu PowerShell można uruchomić z wiersza polecenia lub element runbook usługi Automation, która używa [Get AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) do pobierania danych z usługi Log Analytics.  To polecenie cmdlet wymaga zapytanie w celu określenia danych do pobrania.
- **Interfejsu API usługi log Analytics.**  [Zaloguj się interfejs API wyszukiwania usługi Log Analytics](log-analytics-log-search-api.md) umożliwia dowolnego klienta interfejsu API REST do pobierania danych dziennika z obszaru roboczego.  Żądanie interfejsu API zawiera zapytanie, które jest uruchamiane Log Analytics w celu określenia danych do pobrania.

![Wyszukiwanie w Dzienniku](media/log-analytics-queries/queries-overview.png)

## <a name="write-a-query"></a>Napisz zapytanie
Zaloguj się Analytics używa [wersję języka zapytań Eksploratora danych](query-language/get-started-queries.md) do pobierania i analizowania danych dziennika w na różne sposoby.  Będzie zazwyczaj rozpocząć podstawowe zapytania, a następnie postęp, aby użyć bardziej zaawansowane funkcje, jakie wymagania są coraz bardziej złożone.

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

## <a name="how-log-analytics-data-is-organized"></a>Sposób organizowania danych usługi Log Analytics
Podczas tworzenia zapytania należy rozpocząć od określenia tabel, które znajdują się dane, którego szukasz. Różne rodzaje danych podzielono na dedykowane tabel w każdym [obszaru roboczego usługi Log Analytics](log-analytics-quick-create-workspace.md).  Dokumentacja dla różnych źródeł danych zawiera nazwę typu danych, które tworzy i opis każdego z jego właściwości.  Wiele zapytań będzie wymagać tylko dane z pojedynczej tabeli, ale inne mogą używać różnorodne opcje, aby dołączyć dane z wielu tabel.

Gdy [usługi Application Insights](../application-insights/app-insights-overview.md) przechowuje dane aplikacji, takie jak żądania, wyjątki, ślady i użycia w usłudze Log Analytics, te dane są przechowywane w innej partycji niż dane dziennika. Ten sam język zapytań umożliwia dostęp do tych danych, ale musi [konsoli Application Insights](../application-insights/app-insights-analytics.md) lub [interfejsu API REST usługi Application Insights](https://dev.applicationinsights.io/) do niego dostęp. Możesz użyć [zasobów dla wielu kwerendach](log-analytics-cross-workspace-search.md) połączenie danych usługi Application Insights z innymi danymi w usłudze Log Analytics.


![Tabele](media/log-analytics-queries/queries-tables.png)







## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [portale, które umożliwiają tworzenie i edytowanie dziennikach](log-analytics-log-search-portals.md).
- Zapoznaj się z [samouczek na temat pisania zapytań](log-analytics-tutorial-viewdata.md) przy użyciu nowego języka zapytań.

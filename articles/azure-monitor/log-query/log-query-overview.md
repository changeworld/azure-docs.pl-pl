---
title: Przegląd dziennika zapytań w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Odpowiedzi na typowe pytania związane z rejestrowania zapytań i ułatwia rozpoczęcie pracy w ich używania.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: 7605bf36c41c5b1276d29076173efd52409afaa9
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310337"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Omówienie zapytań dzienników w usłudze Azure Monitor
Dziennik zapytań pomagają w pełni wykorzystać wartość danych zebranych w [dzienników monitora platformy Azure](../platform/data-platform-logs.md). Zaawansowany język zapytań pozwala dołączyć dane z wielu tabel, agregować dużych zestawów danych oraz przeprowadzanie złożonych operacji za pomocą minimalnej ilości kodu. Otrzymasz odpowiedzi na niemal każde pytanie, a następnie przeprowadzić analizę, tak długo, jak zostały zebrane dane pomocnicze i zrozumieć sposób tworzenia odpowiednie zapytanie.

Niektóre funkcje w usłudze Azure Monitor, takich jak [insights](../insights/insights-overview.md) i [rozwiązania](../insights/solutions-inventory.md) przetwarzanie danych dziennika bez narażania należy do podstawowych zapytań. W pełni korzystać z innych funkcji usługi Azure Monitor, należy zrozumieć sposób tworzenia zapytań i jak ich używać do interaktywnego analizowania danych w dziennikach monitora platformy Azure.

Użyj tego artykułu jako punktu wyjścia do nauki o zapytaniach dzienników w usłudze Azure Monitor. On odpowiedzi na często zadawane pytania i zawiera łącza do dokumentacji, która zawiera dalsze szczegółowe informacje i wnioski.

## <a name="how-can-i-learn-how-to-write-queries"></a>Jak mogę dowiedzieć się, jak pisać zapytania?
Jeśli chcesz przejść bezpośrednio do czynności, można uruchomić z następujących samouczków:

- [Rozpoczynanie pracy z usługą Log Analytics w usłudze Azure Monitor](get-started-portal.md).
- [Wprowadzenie do zapytań dzienników w usłudze Azure Monitor](get-started-queries.md).

Po użytkownik znasz już podstawy, przeprowadzenie wielu lekcje przy użyciu własnych danych lub dane z naszego środowiska pokazu, zaczynając od: 

- [Praca z ciągami zapytań dzienników usługi Azure Monitor](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Jakiego języka rejestrować użycie zapytania?
Dzienniki platformy Azure Monitor jest oparty na [Eksploratora danych usługi Azure](/azure/data-explorer), i zapytania dziennika są zapisywane z użyciem tego samego języka zapytania Kusto (KQL). Jest to rozbudowane języka, można łatwo odczytać i autora, a powinien móc rozpocząć korzystanie z niej za pomocą minimalnej liczby wskazówek.

Zobacz [dokumentacji usługi Azure Data Explorer KQL](/azure/kusto/query) pełna dokumentacja KQL i odwołania w różnych funkcjach dostępnych.<br>
Zobacz [wprowadzenie do zapytań dzienników w usłudze Azure Monitor](get-started-queries.md) szybki przewodnik języka przy użyciu danych z dzienników monitora platformy Azure.
Zobacz [różnice języka zapytań log usługi Azure Monitor](data-explorer-difference.md) dla niewielkich różnic w wersji KQL używane przez usługi Azure Monitor.

## <a name="what-data-is-available-to-log-queries"></a>Jakie dane są dostępne dla rejestrowania zapytań?
Wszystkie dane zbierane w dziennikach monitora platformy Azure jest dostępna do pobrania i analizowania dziennika zapytań. Różnych źródeł danych będzie zapisywać swoje dane do innych tabel, ale może zawierać wiele tabel w ramach jednego zapytania, aby analizować dane w wielu źródłach. Podczas tworzenia zapytania, możesz uruchomić, ustalając, tabel, które mają danych, którego szukasz, więc powinien mieć co najmniej podstawową wiedzę na temat struktury danych w dziennikach monitora platformy Azure.

Zobacz [źródła usługi Azure Monitor dzienniki](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)dla listy różnych danych źródła wypełnić dzienników monitora platformy Azure.<br>
Zobacz [struktury usługi Azure Monitor dzienniki](logs-structure.md) objaśnienia dotyczące struktury danych.

## <a name="what-does-a-log-query-look-like"></a>Jak wygląda zapytanie dziennika?
Zapytania mogą być bardzo proste, jak nazwa pojedynczej tabeli, do pobierania wszystkich rekordów z tej tabeli:

```Kusto
Syslog
```

Lub można filtrować dla określonych rekordów, podsumować je i wizualizować wyniki na wykresie:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Dla bardziej złożonej analizie może pobierać dane z wielu tabel przy użyciu sprzężenia, aby analizować wyniki ze sobą.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Nawet jeśli nie znasz KQL, należy co najmniej zorientować się podstawową logikę używane przez te zapytania. One rozpoczynać nazwę tabeli, a następnie dodania wielu poleceń do filtrowania i przetworzyć te dane. Zapytania można użyć dowolnej liczby poleceń i możesz napisać bardziej złożone zapytania jako możesz zapoznać się z innego polecenia KQL dostępne.

Zobacz [wprowadzenie do zapytań dzienników w usłudze Azure Monitor](get-started-queries.md) samouczek dotyczący zapytań dziennika, który zawiera wprowadzenie do języka i typowych funkcji.<br>


## <a name="what-is-log-analytics"></a>Co to jest usługa Log Analytics?
Log Analytics jest podstawowym narzędziem w witrynie Azure portal do Pisanie zapytań log i interaktywnego analizowania ich wyniki. Nawet jeśli zapytanie dziennika jest używany w innym miejscu w usłudze Azure Monitor, będzie zazwyczaj zapisu i przetestować zapytanie najpierw przy użyciu usługi Log Analytics.

Można uruchomić usługi Log Analytics z kilku różnych miejsc w witrynie Azure portal. Zakres danych dostępnych do usługi Log Analytics jest określony, jak zacząć. Zobacz [zakres kwerendy](scope.md) Aby uzyskać więcej informacji.

- Wybierz **dzienniki** z **usługi Azure Monitor** menu lub **obszarów roboczych usługi Log Analytics** menu.
- Wybierz **Analytics** z **Przegląd** strony aplikacji usługi Application Insights.
- Wybierz **dzienniki** z menu zasobów platformy Azure.

![Log Analytics](media/log-query-overview/log-analytics.png)

Zobacz [Rozpoczynanie pracy z usługą Log Analytics w usłudze Azure Monitor](get-started-portal.md) do samouczka usługi Log Analytics wprowadzono kilka funkcji.

## <a name="where-else-are-log-queries-used"></a>Gdzie indziej dziennika zapytań są używane?
Oprócz interaktywnie pracujesz w języku zapytań log i ich wyniki w usłudze Log Analytics, obszary, w którym będziesz używać zapytań w usłudze Azure Monitor są następujące:

- **Reguły alertów.** [Reguły alertów](../platform/alerts-overview.md) aktywnego identyfikowania problemów z danych w obszarze roboczym.  Każda reguła alertu opiera się na wyszukiwanie w dzienniku, który jest automatycznie uruchamiane w regularnych odstępach czasu.  Wyniki są kontrolowane, aby określić, jeśli utworzony alert.
- **Pulpity nawigacyjne.** Możesz przypiąć wyniki dowolnego zapytania do [pulpitu nawigacyjnego platformy Azure](../learn/tutorial-logs-dashboards.md) co pozwala użytkownikowi na wizualizować dane dzienników i metryk ze sobą i opcjonalnie udostępniać innym użytkownikom usługi Azure.
- **Widoki.**  Możesz utworzyć wizualizacje danych, które mają zostać uwzględnione w pulpitami nawigacyjnymi użytkownika za pomocą [Projektant widoków](../platform/view-designer.md).  Dziennik zapytań zawierają dane używane przez [Kafelki](../platform/view-designer-tiles.md) i [części wizualizacji](../platform/view-designer-parts.md) w każdym widoku.  
- **Eksportowanie.**  Podczas importowania danych dziennika z usługi Azure Monitor do programu Excel lub [usługi Power BI](../platform/powerbi.md), Utwórz zapytanie dziennika do definiowania danych do wyeksportowania.
- **PowerShell.** Skrypt programu PowerShell można uruchomić z wiersza polecenia lub element runbook usługi Automation, która używa [Get AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) można pobrać danych dziennika z usługi Azure Monitor.  To polecenie cmdlet wymaga zapytanie w celu określenia danych do pobrania.
- **Interfejs API dzienniki usługi Azure Monitor.**  [Interfejsu API usługi Azure Monitor dzienniki](../platform/alerts-overview.md) umożliwia dowolnego klienta interfejsu API REST do pobierania danych dziennika z obszaru roboczego.  Żądanie interfejsu API zawiera zapytanie, które jest uruchamiane w usłudze Azure Monitor do ustalenia danych, które można pobrać.


## <a name="next-steps"></a>Kolejne kroki
- Przeprowadzenie [samouczek na temat korzystania z usługi Log Analytics w witrynie Azure portal](get-started-portal.md).
- Przeprowadzenie [samouczek na temat pisania zapytań](get-started-queries.md).

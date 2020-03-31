---
title: Omówienie zapytań dziennika w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Odpowiedzi na często zadawane pytania związane z zapytaniami dziennika i rozpoczyna się ich używanie.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 54a6f875bc33d24d412d2424c634d1019b4af399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670121"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Omówienie zapytań dziennika w usłudze Azure Monitor
Zapytania dziennika pomagają w pełni wykorzystać wartość danych zebranych w [dziennikach usługi Azure Monitor.](../platform/data-platform-logs.md) Zaawansowany język zapytań umożliwia dołączanie danych z wielu tabel, agregowanie dużych zestawów danych i wykonywanie złożonych operacji przy minimalnym kodzie. Praktycznie na każde pytanie można odpowiedzieć i przeprowadzić analizę tak długo, jak dane pomocnicze zostały zebrane i rozumiesz, jak skonstruować odpowiednie zapytanie.

Niektóre funkcje w usłudze Azure Monitor, takie jak [szczegółowe informacje](../insights/insights-overview.md) i [rozwiązania](../insights/solutions-inventory.md) przetwarzają dane dziennika bez narażania cię na podstawowe zapytania. Aby w pełni wykorzystać inne funkcje usługi Azure Monitor, należy zrozumieć, jak kwerendy są konstruowane i jak można ich używać do interaktywnej analizy danych w dziennikach usługi Azure Monitor.

Ten artykuł służy do poznawanie zapytań dziennika w usłudze Azure Monitor. Odpowiada na typowe pytania i zawiera łącza do innej dokumentacji, która zawiera dalsze szczegóły i lekcje.

## <a name="how-can-i-learn-how-to-write-queries"></a>Jak mogę dowiedzieć się, jak pisać zapytania?
Jeśli chcesz przejść od razu do rzeczy, możesz zacząć od następujących samouczków:

- [Wprowadzenie do usługi Log Analytics w usłudze Azure Monitor](get-started-portal.md).
- [Wprowadzenie do zapytań dziennika w usłudze Azure Monitor](get-started-queries.md).

Gdy już będziesz mieć podstawy, przejdź przez wiele lekcji, korzystając z własnych danych lub danych z naszego środowiska demonstracyjnego, zaczynając od: 

- [Praca z ciągami w kwerendach dziennika usługi Azure Monitor](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Jakiego języka używają kwerendy dziennika?
Dzienniki usługi Azure Monitor są oparte na [usłudze Azure Data Explorer,](/azure/data-explorer)a zapytania dziennika są zapisywane przy użyciu tego samego języka zapytań Kusto (KQL). Jest to bogaty język zaprojektowany tak, aby był łatwy do odczytania i autora, i powinieneś być w stanie zacząć go używać z minimalnymi wskazówkami.

Zobacz [dokumentację KQL eksploratora danych platformy Azure, aby](/azure/kusto/query) uzyskać pełną dokumentację dotyczącą KQL i informacje na temat różnych dostępnych funkcji.<br>
Zobacz [Wprowadzenie do zapytań dziennika w usłudze Azure Monitor,](get-started-queries.md) aby uzyskać szybki przewodnik po języku przy użyciu danych z dzienników usługi Azure Monitor.
Zobacz [różnice językowe zapytań dziennika usługi Azure Monitor](data-explorer-difference.md) dla niewielkich różnic w wersji KQL używanej przez usługę Azure Monitor.

## <a name="what-data-is-available-to-log-queries"></a>Jakie dane są dostępne do rejestrowania zapytań?
Wszystkie dane zebrane w dziennikach usługi Azure Monitor są dostępne do pobierania i analizowania w zapytaniach dziennika. Różne źródła danych będą zapisywać swoje dane w różnych tabelach, ale można dołączyć wiele tabel w jednej kwerendzie do analizowania danych w wielu źródłach. Podczas tworzenia kwerendy, należy rozpocząć od określenia, które tabele mają dane, których szukasz, więc powinien mieć co najmniej podstawową wiedzę na temat sposobu, w jaki dane w dziennikach usługi Azure Monitor jest skonstruowany.

Zobacz [Źródła dzienników monitora platformy Azure](../platform/data-platform-logs.md#sources-of-azure-monitor-logs), aby uzyskać listę różnych źródeł danych, które wypełniają dzienniki usługi Azure Monitor.<br>
Zobacz [strukturę dzienników usługi Azure Monitor, aby](logs-structure.md) uzyskać wyjaśnienie, jak dane są zorganizowane.

## <a name="what-does-a-log-query-look-like"></a>Jak wygląda kwerenda dziennika?
Kwerenda może być tak prosta, jak nazwa pojedynczej tabeli do pobierania wszystkich rekordów z tej tabeli:

```Kusto
Syslog
```

Można też filtrować dla poszczególnych rekordów, podsumowywania ich i wizualizować wyniki na wykresie:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

W przypadku bardziej złożonej analizy można pobrać dane z wielu tabel przy użyciu sprzężenia do analizowania wyników razem.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Nawet jeśli nie jesteś zaznajomiony z KQL, powinieneś być w stanie przynajmniej dowiedzieć się podstawowej logiki używanej przez te zapytania. Zaczynają się od nazwy tabeli, a następnie dodają wiele poleceń do filtrowania i przetwarzania tych danych. Kwerenda może używać dowolnej liczby poleceń i można pisać bardziej złożone zapytania, jak zapoznać się z różnych poleceń KQL dostępne.

Zobacz [Wprowadzenie do zapytań dziennika w usłudze Azure Monitor,](get-started-queries.md) aby zapoznać się z samouczkiem na temat zapytań dziennika, który wprowadza język i typowe funkcje.<br>


## <a name="what-is-log-analytics"></a>Co to jest usługa Log Analytics?
Usługa Log Analytics jest podstawowym narzędziem w portalu Azure do pisania zapytań dziennika i interaktywnej analizy ich wyników. Nawet jeśli zapytanie dziennika jest używane w innym miejscu usługi Azure Monitor, zwykle jest ono pisane i testowane przy użyciu usługi Log Analytics.

Usługa Log Analytics można uruchomić z kilku miejsc w witrynie Azure portal. Zakres danych dostępnych dla usługi Log Analytics zależy od sposobu ich uruchomienia. Aby uzyskać więcej informacji, zobacz [Zakres kwerendy.](scope.md)

- Wybierz **dzienniki** z menu **Azure Monitor** lub menu **obszarów roboczych usługi Log Analytics.**
- Wybierz **Analytics** ze strony **Przegląd** aplikacji usługi Application Insights.
- Wybierz **dzienniki** z menu zasobu platformy Azure.

![Log Analytics](media/log-query-overview/log-analytics.png)

Zobacz [Wprowadzenie do usługi Log Analytics w usłudze Azure Monitor,](get-started-portal.md) aby zapoznać się z samouczkiem dotyczący usługi Log Analytics, która wprowadza kilka jego funkcji.

## <a name="where-else-are-log-queries-used"></a>Gdzie indziej są używane zapytania dziennika?
Oprócz interaktywnej pracy z zapytaniami dziennika i ich wyników w usłudze Log Analytics, obszary w usłudze Azure Monitor, w których będą używane zapytania, obejmują następujące elementy:

- **Reguły alertów.** [Reguły alertów](../platform/alerts-overview.md) proaktywnie identyfikują problemy z danymi w obszarze roboczym.  Każda reguła alertu jest oparta na wyszukiwaniu dziennika, które jest automatycznie uruchamiane w regularnych odstępach czasu.  Wyniki są sprawdzane, aby ustalić, czy należy utworzyć alert.
- **Pulpity nawigacyjne.** Wyniki dowolnej kwerendy można przypiąć do [pulpitu nawigacyjnego platformy Azure,](../learn/tutorial-logs-dashboards.md) które umożliwiają wizualizację danych dziennika i metryki razem i opcjonalnie udostępnić je innym użytkownikom platformy Azure.
- **Widoki.**  W programie [View Designer](../platform/view-designer.md)można tworzyć wizualizacje danych, które mają być uwzględniane w pulpitach nawigacyjnych użytkowników.  Kwerendy dziennika zawierają dane używane przez [kafelki](../platform/view-designer-tiles.md) i [części wizualizacji](../platform/view-designer-parts.md) w każdym widoku.  
- **Eksportu.**  Podczas importowania danych dziennika z usługi Azure Monitor do programu Excel lub [usługi Power BI](../platform/powerbi.md)należy utworzyć kwerendę dziennika definiującą dane do wyeksportowania.
- **Powershell.** Skrypt programu PowerShell można uruchomić z wiersza polecenia lub systemu runbook usługi Azure Automation, który używa [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) do pobierania danych dziennika z usługi Azure Monitor.  To polecenie cmdlet wymaga kwerendy w celu określenia danych do pobrania.
- **Interfejs API dzienników monitora platformy Azure.**  Interfejs [API dzienników usługi Azure Monitor](https://dev.loganalytics.io) umożliwia każdemu klientowi interfejsu API REST pobieranie danych dziennika z obszaru roboczego.  Żądanie interfejsu API zawiera kwerendę, która jest uruchamiana w usłudze Azure Monitor w celu określenia danych do pobrania.


## <a name="next-steps"></a>Następne kroki
- Przejmij [samouczek dotyczący korzystania z usługi Log Analytics w witrynie Azure portal](get-started-portal.md).
- Przejdź przez [samouczek na temat pisania zapytań](get-started-queries.md).

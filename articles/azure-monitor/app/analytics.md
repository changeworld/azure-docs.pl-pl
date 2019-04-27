---
title: Analiza — zaawansowane wyszukiwanie i narzędzia zapytań usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: 'Omówienie usługi Analytics, Narzędzia zaawansowane wyszukiwanie diagnostyczne usługi Application Insights. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: mbullwin
ms.openlocfilehash: 4c3ecdd01106cc8d305764206bc75535fa4dac3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691954"
---
# <a name="analytics-in-application-insights"></a>Analiza w usłudze Application Insights
Analytics to zaawansowane narzędzie wyszukiwania i zapytanie o [usługi Application Insights](app-insights-overview.md). Analytics to narzędzia sieci web, aby konfiguracja nie jest wymagana.
Jeśli już skonfigurowano usługę Application Insights dla jednej z aplikacji, a następnie można analizować dane swojej aplikacji, otwierając analizy z Twojej aplikacji [bloku przeglądu](app-insights-dashboards.md).

![Otwórz portal.azure.com otwórz zasób usługi Application Insights, a następnie kliknij przycisk Analiza.](./media/analytics/001.png)

Można również użyć [Plac zabaw dla analizy](https://go.microsoft.com/fwlink/?linkid=859557) czyli środowisku pokazowym bezpłatne z dużą ilością danych przykładowych.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="relation-to-azure-monitor-logs"></a>Relacja do dzienników usługi Azure Monitor
Analiza usługi Application Insights opiera się na [Eksploratora danych usługi Azure](/azure/data-explorer) takich jak dzienniki usługi Azure Monitor, a także wykorzystuje [język zapytania Kusto](/azure/kusto/query). Używa ona taka sama [portal analizy dzienników](../log-query/get-started-portal.md) jako dzienniki usługi Azure Monitor, chociaż jego dane są przechowywane w oddzielnej partycji.

Nie można bezpośrednio dostęp do danych w obszarze roboczym usługi Log Analytics, z usługi Application Insights analytics ani uzyskać bezpośrednio dostęp do danych aplikacji z usługą log analytics. Aby można było zbadać oba zestawy danych ze sobą, należy napisać [zapytania w usłudze log analytics](../log-query/log-query-overview.md) i wykorzystywania [wyrażenie metodzie app()](../log-query/app-expression.md) do dostępu do danych aplikacji.


## <a name="query-data-in-analytics"></a>Wykonywanie zapytań dotyczących danych w usłudze Analytics
Typowe zapytania zaczyna się od nazwy tabeli, następuje szereg *operatory* rozdzielone `|`.
Na przykład znajdźmy się, ile żądań aplikacji w języku odebranych z różnych krajów, w ciągu ostatnich 3 godzin:
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

Rozpoczniemy pracę z nazwą tabeli *żądań* i dodać elementy gazociągami, zgodnie z potrzebami.  Najpierw definiujemy filtr czasu, aby zapoznać się z tylko rekordy z ostatnich 3 godzin.
Firma Microsoft następnie liczby rekordów dla każdego kraju (znaleziono danych w kolumnie *client_CountryOrRegion*). Na koniec możemy Renderuj wyniki na wykresie kołowym.
<br>

![Wyniki zapytania](./media/analytics/030.png)

Język ma wiele atrakcyjnych funkcji:

* [Filtr](/azure/kusto/query/whereoperator) telemetrii aplikacji pierwotnych według dowolnego pola, w tym swoje właściwości niestandardowych i metryk.
* [Dołącz do](/azure/kusto/query/joinoperator) wielu tabel — korelowanie żądania wyświetleń stron, wywołań zależności, wyjątki i ślady dzienników.
* Zaawansowane statystyczne [agregacji](/azure/kusto/query/summarizeoperator).
* Natychmiastowe i zaawansowane wizualizacje.
* [Interfejs API REST](https://dev.applicationinsights.io/) służące do uruchamiania zapytań programowo, na przykład za pomocą programu PowerShell.

[Pełna dokumentacja języka](https://go.microsoft.com/fwlink/?linkid=856079) szczegóły każdego polecenia obsługiwane i regularnie aktualizuje.

## <a name="next-steps"></a>Kolejne kroki
* Rozpoczynanie pracy z usługą [portalu analizy](https://go.microsoft.com/fwlink/?linkid=856587)
* Rozpoczynanie pracy [Pisanie zapytań](https://go.microsoft.com/fwlink/?linkid=856078)
* Przegląd [SQL użytkowników ściągawka](https://aka.ms/sql-analytics) poszukiwaniu tłumaczeń idiomy najczęściej.
* Testowanie usługi Analytics na naszych [placu zabaw](https://analytics.applicationinsights.io/demo) aplikacji nie jest wysyłania danych do usługi Application Insights jeszcze.
* Obejrzyj [klip wideo z wprowadzeniem](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).

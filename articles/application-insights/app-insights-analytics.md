---
title: Analityka — narzędzie zapytania z usługi Azure Application Insights i zaawansowane wyszukiwanie | Dokumentacja firmy Microsoft
description: 'Przegląd analizowanie narzędziu zaawansowane wyszukiwanie diagnostycznych z usługi Application Insights. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: 170cd76c72e8aeb5de48c711ae4637a0244742fb
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294204"
---
# <a name="analytics-in-application-insights"></a>Analityka w usłudze Application Insights
Analiza jest zaawansowanym narzędziem wyszukiwania i zapytania, z [usługi Application Insights](app-insights-overview.md). Analytics to narzędzie sieci web, konfiguracja nie jest wymagana. Jeśli już skonfigurowano usługi Application Insights dla jednej z aplikacji, można analizować dane aplikacji, otwierając Analytics z aplikacji [bloku omówienie](app-insights-dashboards.md).

![Otwórz portal.azure.com otworzyć zasobu usługi Application Insights, a następnie kliknij przycisk Analytics.](./media/app-insights-analytics/001.png)

Można również użyć [Plac zabaw dla analityka](https://go.microsoft.com/fwlink/?linkid=859557) czyli środowisku wolnego pokaz wiele przykładowych danych.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="query-data-in-analytics"></a>Dane zapytania w module analiz
Typowe zapytania rozpoczyna się od nazwy tabeli następuje szereg *operatory* rozdzielone `|`.
Na przykład załóżmy sprawdzić liczbę żądań aplikacji odebranych z różnych krajów, w trakcie ostatnich 3 godziny:
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

Możemy zaczynać nazwy tabeli *żądań* i w razie potrzeby dodaj gazociągami elementów.  Najpierw zdefiniować filtr czasu, aby przejrzeć tylko rekordy z ostatnich 3 godziny.
Następnie możemy liczba Liczba rekordów na kraju (odnaleziono danych w kolumnie *client_CountryOrRegion*). Na koniec mamy renderowania wyniki wykresu kołowego.
<br>

![Wyniki zapytania](./media/app-insights-analytics/030.png)

Język ma wiele atrakcyjne funkcje:

* [Filtr](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) telemetrii raw aplikacji przez wszystkie pola, w tym właściwości niestandardowych i metryki.
* [Dołącz](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/join-operator) wielu tabel — korelując żądania z wyświetleń strony, wywołania zależności, wyjątków i ślady dziennika.
* Zaawansowane statystyczne [agregacji](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions).
* Natychmiastowe i zaawansowane wizualizacje.
* [Interfejs API REST](https://dev.applicationinsights.io/) czy służy do wykonywania kwerend programowo, na przykład z programu PowerShell.

[Pełna dokumentacja języka](https://go.microsoft.com/fwlink/?linkid=856079) szczegóły każdego polecenia obsługiwane i regularnie aktualizowana.

## <a name="next-steps"></a>Kolejne kroki
* Rozpoczynanie pracy z [portal analityka](https://go.microsoft.com/fwlink/?linkid=856587)
* Rozpoczynanie pracy [Pisanie zapytań](https://go.microsoft.com/fwlink/?linkid=856078)
* Przegląd [SQL użytkowników ściągawka](https://aka.ms/sql-analytics) do tłumaczenia idioms najczęściej.
* Przetestuj Analytics na naszych [Plac zabaw dla](https://analytics.applicationinsights.io/demo) aplikacji nie jest wysyłania danych do usługi Application Insights jeszcze.
* Obejrzyj [wprowadzenie wideo](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).
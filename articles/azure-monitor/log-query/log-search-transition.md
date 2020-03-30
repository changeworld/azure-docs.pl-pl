---
title: Wyświetlanie i analizowanie danych w usłudze Azure Log Analytics | Dokumenty firmy Microsoft
description: Pomoc dla użytkowników wyszukiwania dzienników usługi Log Analytics w usłudze Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: 6cc7fc94162ed101d06fbbbc4722867be46987b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670138"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Przejście z wyszukiwania dzienników usługi Log Analytics do dzienników usługi Azure Monitor
Wyszukiwanie dzienników w usłudze Log Analytics zostało niedawno zastąpione nowym doświadczeniem analizowania dzienników usługi Azure Monitor. Strona wyszukiwania dzienników jest obecnie nadal dostępna za pośrednictwem elementu menu **Dzienniki (klasyczne)** na stronie **Obszarów roboczych usługi Log Analytics** w witrynie Azure Portal, ale zostanie usunięta 15 lutego 2019 r. W tym artykule opisano różnice między tymi dwoma środowiskami, aby ułatwić przejście z wyszukiwania dziennika. 

## <a name="filter-results-of-a-query"></a>Filtrowanie wyników kwerendy
W wyszukiwaniu dzienników lista filtrów jest wyświetlana w miarę wyświetlania wyników wyszukiwania. Zaznacz filtr i kliknij przycisk **Zastosuj,** aby uruchomić kwerendę z wybranym filtrem.

![Filtr wyszukiwania dzienników](media/log-search-transition/filter-log-search.png)

W dziennikach usługi Azure Monitor wybierz *filtr (podgląd),* aby wyświetlić filtry. Kliknij ikonę filtru, aby wyświetlić dodatkowe filtry. Zaznacz filtr i kliknij przycisk **Zastosuj & Uruchom,** aby uruchomić kwerendę z wybranym filtrem.

![Filtr Dzienniki](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Wyodrębnianie pól niestandardowych 
W polu Wyszukiwanie [dzienników](../platform/custom-fields.md) wyodrębniane są pola niestandardowe z widoku Lista, w którym menu pola zawiera _akcje Wyodrębnij pola z tabeli_.

![Pola wyodrębniania wyszukiwania dziennika](media/log-search-transition/extract-fields-log-search.png)

W dziennikach usługi Azure Monitor wyodrębniasz pola niestandardowe z widoku tabeli. Rozwiń rekord, klikając strzałkę po lewej stronie, a następnie kliknij wielokropek, aby uzyskać dostęp do akcji _Wyodrębnij pola._

![Dzienniki wyodrębniania pól](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Funkcje i grupy komputerów
Aby zapisać wyszukiwanie w wyszukiwaniu dziennika, wybierz pozycję **Zapisane wyszukiwania** i **Dodaj,** aby podać nazwę, kategorię i tekst zapytania. Utwórz [grupę komputerów,](../platform/computer-groups.md) dodając alias funkcji.

![Zapisz wyszukiwanie dzienników](media/log-search-transition/save-search-log-search.png)

Aby zapisać bieżącą kwerendę w dziennikach usługi Azure Monitor, wybierz pozycję **Zapisz**. Zmień **zapisz jako** _funkcję_ i podaj **alias funkcji,** aby utworzyć [funkcję](functions.md). Wybierz _pozycję Zapisz tę kwerendę jako grupę komputerów,_ aby użyć aliasu funkcji dla grupy [komputerów](../platform/computer-groups.md).

![Zapisz kwerendę dziennika](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Zapisane kwerendy
W wyszukiwaniu dziennika zapisane zapytania są dostępne za pośrednictwem elementu paska akcji **Zapisane wyszukiwania**. W dziennikach usługi Azure Monitor dostęp do zapisanych zapytań z [Eksploratora zapytań](../log-query/get-started-portal.md#save-queries).

![Eksplorator zapytań](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Przechodzenie do szczegółów w podsumowanych wierszach
W polu Wyszukiwanie dzienników możesz kliknąć wiersz w podsumowaniu kwerendy, aby uruchomić inną kwerendę zawierającą szczegółowe rekordy w tym wierszu.

![Przechodzenie do szczegółów wyszukiwania dziennika](media/log-search-transition/drilldown-search.png)

W dziennikach usługi Azure Monitor należy zmodyfikować kwerendę, aby zwrócić te rekordy. Rozwiń jeden z wierszy w **+** wynikach i kliknij obok wartości, aby dodać go do kwerendy. Następnie skomentuj polecenie **podsumuj** i uruchom kwerendę ponownie.

![Dzienniki usługi Azure Monitor — przechodzenie do szczegółów](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Wykonywanie akcji
W polu Wyszukiwanie dzienników można [uruchomić księgę runbook](take-action.md) od wyniku wyszukiwania, wybierając pozycję **Podejmij akcję**.

![Wykonywanie akcji](media/log-search-transition/take-action-log-search.png)

W dziennikach usługi Azure Monitor [utwórz alert z kwerendy dziennika](../platform/alerts-log.md). Skonfiguruj grupę akcji z jedną lub kilkoma akcjami, które będą uruchamiane w odpowiedzi na alert.

![Grupa akcji](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o nowym [doświadczeniu dzienników usługi Azure Monitor.](get-started-portal.md)

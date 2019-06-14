---
title: Wyświetlanie i analizowanie danych w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Pomoc dla użytkowników przeszukiwania dzienników usługi Log Analytics do usługi Azure Monitor dziennika zapytań środowiska.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: bwren
ms.openlocfilehash: dc3a754bd1a9f689f4b24b9ee2c1e4cbc0611684
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60519905"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Przejście ze przeszukiwania dzienników usługi Log Analytics do dzienników usługi Azure Monitor
Nowe środowisko w celu analizowania dzienników usługi Azure Monitor ostatnio zamieniono przeszukiwania dzienników w usłudze Log Analytics. Strona wyszukiwania dziennika jest obecnie dostępna za pośrednictwem **dzienniki (wersja klasyczna)** elementu menu w **obszarów roboczych usługi Log Analytics** strony w platformie Azure, ale portalu zostaną usunięte 15 lutego 2019 r. W tym artykule opisano różnice między obydwoma środowiskami, aby ułatwić przejście z wyszukiwania w dziennikach. 

## <a name="filter-results-of-a-query"></a>Filtrowanie wyników zapytania
Podczas wyszukiwania dziennika listy filtrów są wyświetlane jako wyniki wyszukiwania są dostarczane. Wybierz filtr, a następnie kliknij przycisk **Zastosuj** Aby uruchomić zapytanie za pomocą wybranego filtru.

![Filtr wyszukiwania dziennika](media/log-search-transition/filter-log-search.png)

Dzienniki usługi Azure Monitor, wybierz *filtru (wersja zapoznawcza)* Aby wyświetlić filtry. Kliknij ikonę filtru, aby wyświetlić dodatkowe filtry. Wybierz filtr, a następnie kliknij przycisk **Zastosuj i uruchom** Aby uruchomić zapytanie za pomocą wybranego filtru.

![Filtruj dzienniki](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Wyodrębnij pola niestandardowe 
Podczas wyszukiwania dziennika wyodrębnić [pól niestandardowych](../platform/custom-fields.md) z widoku listy, obejmującym pole menu akcji _Wyodrębnij pola z tabeli_.

![Zaloguj się Search wyodrębnianie pól](media/log-search-transition/extract-fields-log-search.png)

W dziennikach w usłudze Azure Monitor pól niestandardowych prowadzenie widoku tabeli. Rozwiń rekord, klikając strzałkę po lewej stronie, a następnie kliknij przycisk wielokropka, aby uzyskać dostęp do _wyodrębniania pól_ akcji.

![Dzienniki wyodrębniania pól](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Funkcje i grupami komputerów
Aby zapisać wyszukiwanie w przeszukiwania dzienników, wybierz pozycję **zapisane wyszukiwania** i **Dodaj** nazwy, kategorii i tekst zapytania. Tworzenie [grupa](../platform/computer-groups.md) , dodając alias funkcji.

![Zapisz wyszukiwanie w Dzienniku](media/log-search-transition/save-search-log-search.png)

Aby zapisać bieżącego zapytania w dziennikach w usłudze Azure Monitor, zaznacz **Zapisz**. Zmiana **Zapisz jako** do _funkcja_ i podaj **Alias funkcji** do utworzenia [funkcji](functions.md). Wybierz _Zapisz to zapytanie jako grupę komputerów_ do użycia funkcja alias [grupa](../platform/computer-groups.md).

![Zapisz zapytanie dziennika](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Zapisane zapytania
Podczas wyszukiwania dziennika zapisane kwerendy są dostępne za pośrednictwem elementu pasek akcji **zapisane wyszukiwania**. W dziennikach w usłudze Azure Monitor, dostęp do zapisanych zapytań z [Eksplorator zapytań](../log-query/get-started-portal.md#save-queries).

![Eksplorator zapytań](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Przejść do szczegółów w wierszach podsumowania
Wyszukiwanie w dzienniku można kliknąć wiersz w podsumowane zapytanie w celu uruchomienia innej zapytanie, które wyświetla szczegółowe rekordy, w tym wierszu.

![Dziennik wyszukiwania Przechodzenie do szczegółów](media/log-search-transition/drilldown-search.png)

W dziennikach w usłudze Azure Monitor należy zmodyfikować zapytanie, aby zwrócić tych rekordów. Rozwiń jeden z wierszy w wynikach, a następnie kliknij przycisk **+** obok wartości, aby dodać go do kwerendy. Następnie przekształcić w komentarz **Podsumuj** polecenia, a następnie ponownie uruchom zapytanie.

![Usługa Azure Monitor rejestruje Przechodzenie do szczegółów](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Wykonywanie akcji
Podczas wyszukiwania dziennika możesz [uruchamiania elementu runbook](take-action.md) z wyników wyszukiwania, wybierając **reakcję**.

![Wykonywanie akcji](media/log-search-transition/take-action-log-search.png)

W dziennikach usługi Azure Monitor [Tworzenie alertu z zapytania dotyczącego dziennika](../platform/alerts-log.md). Skonfigurować grupy akcji z jedną lub więcej akcji, które będą uruchamiane w odpowiedzi na alert.

![grupy akcji](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o nowym [usługi Azure Monitor dzienników środowisko](get-started-portal.md).
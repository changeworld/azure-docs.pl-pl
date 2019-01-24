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
ms.openlocfilehash: 89811e95ec24eb354020dd6384f6fdab6cee8c8f
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54392294"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Przejście ze przeszukiwania dzienników usługi Log Analytics do dzienników usługi Azure Monitor
Nowe środowisko w celu analizowania dzienników usługi Azure Monitor ostatnio zamieniono przeszukiwania dzienników w usłudze Log Analytics. Strona wyszukiwania dziennika jest obecnie dostępna za pośrednictwem **dzienniki (wersja klasyczna)** elementu menu w **obszarów roboczych usługi Log Analytics** strony w platformie Azure, ale portalu zostaną usunięte 15 lutego 2019 r. W tym artykule opisano różnice między obydwoma środowiskami, aby ułatwić przejście z wyszukiwania w dziennikach. 

## <a name="extract-custom-fields"></a>Wyodrębnij pola niestandardowe 
Podczas wyszukiwania dziennika wyodrębnić [pól niestandardowych](../platform/custom-fields.md) z widoku listy, obejmującym pole menu akcji _Wyodrębnij pola z tabeli_.

![Zaloguj się Search wyodrębnianie pól](media/log-search-transition/extract-fields-log-search.png)

W dziennikach w usłudze Azure Monitor pól niestandardowych prowadzenie widoku tabeli. Rozwiń rekord, klikając strzałkę po lewej stronie, a następnie kliknij przycisk wielokropka, aby uzyskać dostęp do _wyodrębniania pól_ akcji.

![Dzienniki wyodrębniania pól](media/log-search-transition/extract-fields-logs.png)

## <a name="filter-results-of-a-query"></a>Filtrowanie wyników zapytania
Podczas wyszukiwania dziennika listy filtrów są wyświetlane jako wyniki wyszukiwania są dostarczane. Wybierz filtr, a następnie kliknij przycisk **Zastosuj** Aby uruchomić zapytanie za pomocą wybranego filtru.

![Filtr wyszukiwania dziennika](media/log-search-transition/filter-log-search.png)

Dzienniki usługi Azure Monitor, wybierz *filtru (wersja zapoznawcza)* Aby wyświetlić filtry. Kliknij ikonę filtru, aby wyświetlić dodatkowe filtry. Wybierz filtr, a następnie kliknij przycisk **Zastosuj i uruchom** Aby uruchomić zapytanie za pomocą wybranego filtru.

![Filtruj dzienniki](media/log-search-transition/filter-logs.png)

## <a name="functions-and-computer-groups"></a>Funkcje i grupami komputerów
Aby zapisać wyszukiwanie w przeszukiwania dzienników, wybierz pozycję **zapisane wyszukiwania** i **Dodaj** nazwy, kategorii i tekst zapytania. Tworzenie [grupa](../platform/computer-groups.md) , dodając alias funkcji.

![Zapisz wyszukiwanie w Dzienniku](media/log-search-transition/save-search-log-search.png)

Aby zapisać bieżącego zapytania w dziennikach w usłudze Azure Monitor, zaznacz **Zapisz**. Zmiana **Zapisz jako** do _funkcja_ i podaj **Alias funkcji** do utworzenia [funkcji](functions.md).

![Zapisz zapytanie dziennika](media/log-search-transition/save-query-logs.png)

## <a name="saved-searches"></a>Zapisane wyszukiwania
Podczas wyszukiwania dziennika zapisane wyszukiwania są dostępne za pośrednictwem elementu pasek akcji **zapisane wyszukiwania**. W dziennikach w usłudze Azure Monitor, dostęp do zapisanych zapytań z **Eksplorator zapytań**.

![Eksplorator zapytań](media/log-search-transition/query-explorer.png)

## <a name="take-action"></a>Wykonywanie akcji
Podczas wyszukiwania dziennika możesz [uruchamiania elementu runbook](take-action.md) w wynikach wyszukiwania, selectionselecting **reakcję**.

![Wykonywanie akcji](media/log-search-transition/take-action-log-search.png)

W dziennikach usługi Azure Monitor [Tworzenie alertu z zapytania dotyczącego dziennika](../platform/alerts-log.md). Skonfigurować grupy akcji z jedną lub więcej akcji, które będą uruchamiane w odpowiedzi na alert.

![Grupa akcji](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o nowym [usługi Azure Monitor dzienników środowisko](get-started-portal.md).
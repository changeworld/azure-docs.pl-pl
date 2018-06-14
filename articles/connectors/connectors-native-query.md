---
title: Dodaj akcję zapytania w aplikacjach logiki | Dokumentacja firmy Microsoft
description: Omówienie działania zapytania do wykonywania akcji, takich jak tablicy filtrów.
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 05dd4ae3c4ee439d66401a3f5595f9104051f8ee
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27962649"
---
# <a name="get-started-with-the-query-action"></a>Rozpoczynanie pracy z akcją zapytań
Za pomocą akcji zapytania, możesz pracować z partii i tablic w celu przepływy pracy, aby:

* Utwórz zadania dla wszystkich rekordów o wysokim priorytecie z bazy danych.
* Zapisz wszystkie załączniki plików PDF do wiadomości e-mail do obiektów blob platformy Azure.

Aby rozpocząć, korzystając z akcji zapytania w aplikacji logiki, zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>Za pomocą akcji zapytania
Akcja jest operacja odbywa się przez przepływ pracy, który jest zdefiniowany w aplikacji logiki. [Dowiedz się więcej o akcjach](connectors-overview.md).  

Akcja zapytania ma obecnie jedną operację o nazwie tablicy filtrów, która jest widoczna w projektancie. Umożliwia to zapytanie tablicy i zwraca zestawu wyników filtrowane.

Oto, jak można dodać go w aplikacji logiki:

1. Wybierz **nowy krok** przycisku.
2. Wybierz **Dodaj akcję**.
3. W polu wyszukiwania akcji wpisz **filtru** do listy **tablicy filtrów** akcji.
   
    ![Wybierz akcję zapytania](./media/connectors-native-query/using-action-1.png)
4. Wybierz tablicę do filtrowania. (Poniższy zrzut ekranu przedstawia tablicy wyniki wyszukiwania Twitter).
5. Warunku można oszacować na każdego elementu. (Poniższy zrzut ekranu filtry tweetów od użytkowników, którzy mają więcej niż 100 adherentami).
   
    ![Zakończenie akcji zapytania](./media/connectors-native-query/using-action-2.png)
   
    Akcja dane wyjściowe obejmują nowe tablica, która zawiera tylko wyniki, które zostały spełnione wymagania dotyczące filtru.
6. Kliknij w lewym górnym rogu paska narzędzi, aby zapisać i aplikacji logiki będzie Zapisz i opublikuj (Aktywuj).

\*Jeśli jest wywołaniem punktu końcowego HTTP i odbierania odpowiedzi JSON, użyj _przeanalizować JSON_ akcji można przeanalizować odpowiedzi JSON. Bez konieczności przełączania ten krok _tablicy filtrów_ Zobacz tylko treści, a nie zrozumieć strukturę ładunek JSON.

## <a name="query-action"></a>Akcja kwerendy
Poniżej przedstawiono szczegóły akcję, która obsługuje ten łącznik. Łącznik ma jedną akcję możliwe.

| Akcja | Opis |
| --- | --- |
| Filtruj tablicę |Sprawdza warunek dla każdego elementu w tablicy i zwraca wyniki |

## <a name="action-details"></a>Szczegóły akcji
Akcja kwerendy jest dostarczany z jedną akcję możliwe. Poniższe tabele zawierają opis wymaganych i opcjonalnych pól wejściowych dla akcji i odpowiednie szczegóły danych wyjściowych, które są skojarzone z przy użyciu akcji.

### <a name="filter-array"></a>Filtruj tablicę
Poniżej przedstawiono pól wejściowych dla akcji, dzięki czemu wychodzące żądania HTTP.
A * oznacza, że jest polem wymaganym.

| Nazwa wyświetlana | Nazwa właściwości | Opis |
| --- | --- | --- |
| Z * |z |Tablica do filtrowania |
| Warunek * |gdzie |Warunek, który ma zostać obliczone dla każdego elementu |

<br>

### <a name="output-details"></a>Szczegóły danych wyjściowych
Poniżej przedstawiono szczegóły danych wyjściowych dla odpowiedzi HTTP.

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Filtrowane tablicy |tablica |Tablica, która zawiera obiekt dla każdego wyniku filtrowane |

## <a name="next-steps"></a>Kolejne kroki
Teraz, wypróbuj platformy i [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Dostępne łączniki w aplikacjach logiki można eksplorować analizując naszych [listy interfejsów API](apis-list.md).


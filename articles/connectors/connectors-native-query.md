---
title: Dodawanie akcji zapytania w usłudze logic apps | Dokumentacja firmy Microsoft
description: Przegląd akcji zapytania umożliwiające wykonywanie akcji, takich jak Filtruj tablicę.
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
ms.openlocfilehash: 2a82afe396039857e5b9ad6b8a6d0e710573037f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794883"
---
# <a name="get-started-with-the-query-action"></a>Rozpoczynanie pracy za pomocą akcji zapytania
Za pomocą akcji zapytania, można pracować z partii i tablic w celu wykonania przepływów pracy w celu:

* Tworzenie zadania dla wszystkich rekordów o wysokim priorytecie na podstawie bazy danych.
* Zapisz wszystkie załączniki plików PDF do wiadomości e-mail do obiektu blob platformy Azure.

Aby rozpocząć pracę, za pomocą akcji zapytania w aplikacji logiki, zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>Za pomocą akcji zapytania
Akcja jest operacja, która jest przeprowadzane przez przepływ pracy, który jest zdefiniowany w aplikacji logiki. 
[Dowiedz się więcej o akcjach](../connectors/apis-list.md).  

Akcja zapytania ma obecnie jedna operacja, wywoływana Filtruj tablicę, która jest widoczna w projektancie. Dzięki temu można do wykonywania zapytań względem tablicy i zwraca zestaw filtrowanych wyników.

Poniżej przedstawiono, jak je dodać w aplikacji logiki:

1. Wybierz **nowy krok** przycisku.
2. Wybierz **Dodaj akcję**.
3. Wpisz w polu wyszukiwania akcji **filtru** do listy **Filtruj tablicę** akcji.
   
    ![Wybieranie akcji zapytania](./media/connectors-native-query/using-action-1.png)
4. Wybierz tablicę do filtrowania. (Poniższy zrzut ekranu przedstawia tablicę wyników wyszukiwania usługi Twitter).
5. Utwórz warunek do oceny dla każdego elementu. (Poniższy zrzut ekranu filtry tweety użytkownicy posiadający ponad 100 obserwujących).
   
    ![Wykonaj akcję zapytania](./media/connectors-native-query/using-action-2.png)
   
    Akcja zwróci nową tablicę, która zawiera tylko tych wyników, które spełniają wymagania dotyczące filtru.
6. Kliknij w lewym górnym rogu narzędzi, aby zapisać, a Twoja aplikacja logiki będzie zapisywanie i publikowanie (Aktywuj).

\* Jeśli jesteś wywołanie punktu końcowego HTTP i odbierania odpowiedzi JSON, użyj _Przeanalizuj dane JSON_ akcji można przeanalizować odpowiedź w formacie JSON. Bez tego kroku _Filtruj tablicę_ będzie tylko treści i nie rozumienie struktury ładunek JSON.

## <a name="query-action"></a>Akcja kwerendy
Poniżej przedstawiono szczegółowe informacje dotyczące akcji, która obsługuje ten łącznik. Łącznik ma jedną możliwą akcją.

| Akcja | Opis |
| --- | --- |
| Filtruj tablicę |Sprawdza warunek dla każdego elementu w tablicy i zwraca wyniki |

## <a name="action-details"></a>Szczegóły akcji
Akcja kwerendy jest dostarczany za pomocą jednej akcji można. W poniższych tabelach opisano wymagane i opcjonalne pola danych wejściowych dla akcji i odpowiednie szczegóły danych wyjściowych, które są skojarzone z korzystania z akcji.

### <a name="filter-array"></a>Filtruj tablicę
Dostępne są następujące pola wejściowe dla działania, co sprawia, że wychodzące żądania HTTP.
A * oznacza, że jest polem wymaganym.

| Nazwa wyświetlana | Nazwa właściwości | Opis |
| --- | --- | --- |
| Z * |z |Tablica do filtrowania |
| Warunek * |gdzie |Warunek do oceny dla każdego elementu |

<br>

### <a name="output-details"></a>Szczegóły danych wyjściowych
Poniżej przedstawiono szczegóły danych wyjściowych dla odpowiedzi HTTP.

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Filtrowane tablicy |tablica |Tablica, która zawiera obiekt dla każdego odfiltrowanych wyników |

## <a name="next-steps"></a>Kolejne kroki
Teraz wypróbuj platformę i [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Możesz zapoznać się z innych dostępnych łączników w usłudze logic apps, analizując naszych [listy interfejsów API](apis-list.md).


---
title: Dodawanie akcji zapytania w usłudze Logic Apps
description: Przegląd akcji zapytania na potrzeby wykonywania akcji takich jak Filtruj tablicę
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/20/2016
tags: connectors
ms.openlocfilehash: 2e5c9d371a280b776699b2e10d3e8e94b5f41f6f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74787136"
---
# <a name="get-started-with-the-query-action"></a>Rozpocznij pracę z akcją zapytania

Za pomocą akcji zapytania można współpracować z partiami i tablicami w celu wykonywania przepływów pracy w celu:

* Utwórz zadanie dla wszystkich rekordów o wysokim priorytecie z bazy danych.
* Zapisuj wszystkie załączniki PDF dla wiadomości e-mail w obiekcie blob platformy Azure.

Aby rozpocząć pracę z akcją zapytania w aplikacji logiki, zobacz [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>Korzystanie z akcji zapytania

Akcja jest operacją wykonywaną przez przepływ pracy zdefiniowany w aplikacji logiki. 
[Dowiedz się więcej o akcjach](../connectors/apis-list.md).  

Akcja zapytania zawiera obecnie jedną operację, nazywaną tablicą filtru, która jest ujawniana w projektancie. Dzięki temu można badać tablicę i zwracać zestaw przefiltrowanych wyników.

Oto jak można dodać go w aplikacji logiki:

1. Wybierz przycisk **nowy krok** .
2. Wybierz pozycję **Dodaj akcję**.
3. W polu wyszukiwania akcji wpisz **Filtr** , aby wyświetlić listę akcji **filtrowania tablicy** .
   
    ![Wybierz akcję zapytania](./media/connectors-native-query/using-action-1.png)
4. Wybierz tablicę do filtrowania. (Poniższy zrzut ekranu przedstawia tablicę wyników wyszukiwania w usłudze Twitter).
5. Utwórz warunek do obliczenia dla każdego elementu. (Poniższy zrzut ekranu filtruje tweety od użytkowników, którzy mają ponad 100 obserwatorów).
   
    ![Ukończ akcję zapytania](./media/connectors-native-query/using-action-2.png)
   
    Akcja spowoduje wyjście z nowej tablicy, która zawiera tylko wyniki spełniające wymagania dotyczące filtru.
6. Kliknij lewym górnym rogu paska narzędzi, aby zapisać, a aplikacja logiki będzie zapisywać i publikować (aktywować).

\* Jeśli wywołujesz punkt końcowy HTTP i otrzymujesz odpowiedź JSON, użyj akcji Analizuj dane _JSON_ , aby przeanalizować odpowiedź JSON. Bez wykonywania tego kroku _Filtr Array_ będzie widział tylko treść i nie rozumie struktury ładunku JSON.

## <a name="query-action"></a>Akcja zapytania
Poniżej znajdują się szczegółowe informacje o akcji obsługiwanej przez ten łącznik. Łącznik ma jedną możliwą akcję.

| Działanie | Opis |
| --- | --- |
| Filtruj tablicę |Oblicza warunek dla każdego elementu w tablicy i zwraca wyniki |

## <a name="action-details"></a>Szczegóły akcji
Akcja zapytania zawiera jedną możliwą akcję. W poniższych tabelach opisano wymagane i opcjonalne pola wejściowe dla akcji oraz odpowiednie szczegóły wyjściowe, które są skojarzone z tą akcją.

### <a name="filter-array"></a>Filtruj tablicę
Poniżej znajdują się pola wejściowe dla akcji, które wysyłają żądanie HTTP wychodzące.
A * oznacza, że jest to pole wymagane.

| Nazwa wyświetlana | Nazwa właściwości | Opis |
| --- | --- | --- |
| Wniosek |wniosek |Tablica do filtrowania |
| Rozgrzewa |miejscu |Warunek do obliczenia dla każdego elementu |

<br>

### <a name="output-details"></a>Szczegóły danych wyjściowych
Poniżej znajdują się szczegóły danych wyjściowych odpowiedzi HTTP.

| Nazwa właściwości | Typ danych | Opis |
| --- | --- | --- |
| Filtrowana tablica |tablica |Tablica zawierająca obiekt dla każdego przefiltrowanego wyniku |

## <a name="next-steps"></a>Następne kroki
Teraz wypróbuj platformę i [Utwórz aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Możesz eksplorować inne dostępne łączniki w usłudze Logic Apps, przeglądając nasze [listę interfejsów API](apis-list.md).


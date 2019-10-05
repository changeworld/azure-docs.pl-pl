---
title: Dodaj akcję zapytania w usłudze Logic Apps | Microsoft Docs
description: Przegląd akcji zapytania dotyczącej wykonywania akcji, takich jak Filtruj tablicę.
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 10332b95e0d385d7155003efcc52b4bae3969313
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973763"
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


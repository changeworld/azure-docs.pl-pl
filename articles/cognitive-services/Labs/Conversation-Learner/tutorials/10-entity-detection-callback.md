---
title: Jak używać wywołania zwrotnego wykrywania jednostki z aplikacją uczeń konwersacji - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak użyć wywołania zwrotnego wykrywania jednostki z aplikacją uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e41ea5930ff0c8395d0c93aa42e224ebfc894ba8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348632"
---
# <a name="how-to-use-entity-detection-callback"></a>Jak używać wywołania zwrotnego wykrywania jednostki

Ten samouczek pokazuje wywołania zwrotnego wykrywania jednostki i przedstawiono typowe wzorzec rozpoznawania jednostek.

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa bot "tutorialEntityDetectionCallback".

    npm run tutorial-entity-detection

## <a name="details"></a>Szczegóły
Wywołanie zwrotne wykrywania jednostki umożliwia przy użyciu kodu niestandardowego do obsługi reguł biznesowych, powiązane z jednostkami. Z tego pokazu użyjemy wywołania zwrotne i programowe jednostki do rozpoznania nazwę miejscowości wprowadzony przez użytkownika na nazwę kanoniczną — na przykład rozpoznawania "big firmy apple" do "Warszawa".

### <a name="open-the-demo"></a>Otwórz pokaz

Polecenie Samouczek-10-EntityDetenctionCallback na liście aplikacji. 

### <a name="entities"></a>Jednostki

Trzy jednostki zdefiniowaniu w aplikacji.

![](../media/tutorial10_entities.PNG)

1. Miasto jest niestandardowej jednostki, który dostarczy użytkownika jako dane wejściowe tekstu.
2. CityUnknown jest programowe jednostki. To zostanie wypełniony przez system. Jeśli system nie może określić miasto, które jest spowoduje to skopiowanie danych wejściowych użytkownika.
3. CityResolved jest miasto, w którym system wiedzieć o. Jest to nazwa kanoniczna w mieście, na przykład rozwiąże "apple big" do "Warszawa".

### <a name="actions"></a>Akcje

Utworzono trzy czynności. 

![](../media/tutorial10_actions.PNG)

1. Pierwszą akcją jest "Miasto, które chcesz?"
2. Drugą jest wartość "I nie wiadomo, nazwa miejscowości, $CityUknown. Miasto, które chcesz? "
3. Trzeci jest "powiedział $City i rozpoznać który do $CityResolved."

### <a name="callback-code"></a>Kod wywołania zwrotnego

Oto kod. Metoda EntityDetectionCallback można znaleźć w C:\<installedpath > \src\demos\tutorialSessionCallbacks.ts pliku.

![](../media/tutorial10_callbackcode.PNG)

Ta funkcja jest wywoływana po rozpoznawanie jednostek.
 
- Pierwszą czynnością, którą będzie wykonywał jest $CityUknown Wyczyść. $CityUknown pozostanie tylko dla jednego Włącz zawsze pobiera ona wyczyszczone na początku.
- Następnie uzyskujemy listę miast, które zostały uznane. Zająć pierwsza z nich, a następnie spróbuj go rozwiązać.
- Zdefiniowano funkcję, która usuwa go (resolveCity) dalsze powyżej w kodzie. Ma listę nazw Miasto kanonicznej. Nazwa miasta zostanie znaleziona na liście, zwraca go. W przeciwnym wypadku jest wyszukiwany w "cityMap" i zwraca nazwę mapowane. Nie można znaleźć miejscowości, zwraca wartość null.
- Ponadto jeśli miasta zostało rozwiązane na nazwę, przechowujemy go w jednostce $CityKnown. W przeciwnym wypadku wyczyść, co użytkownik ma dana i wypełnić $CityUknown jednostki.

### <a name="train-dialogs"></a>Szkolenie okien dialogowych

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
2. Wpisz tekst "hello".
3. Kliknij wynik akcji i wybierz pozycję "Miasto, które chcesz?"
2. Wprowadź "Warszawa".
    - Należy pamiętać, że pobiera rozpoznawane jako element miasta.
5. Kliknij przycisk wynik akcji
    - Należy pamiętać, że miejscowość i CityResolved zostały wypełnione.
6. Wybierz "powiedział $City i rozpoznać który do $CityResolved".
7. Kliknij przycisk Done nauczania.

Dodaj inny przykład okno dialogowe:

1. Kliknij przycisk Nowe okno dialogowe pociągu.
2. Wpisz tekst "hello".
3. Kliknij wynik akcji i wybierz pozycję "Miasto, które chcesz?"
2. Wprowadź "big apple".
    - Należy pamiętać, że pobiera rozpoznawane jako element miasta.
5. Kliknij przycisk wynik akcji
    - Należy pamiętać, że CityResolved przedstawia wynik wykonywania kodu.
6. Wybierz "powiedział $City i rozpoznać który do $CityResolved".
7. Kliknij przycisk Done nauczania.

Dodaj inny przykład okno dialogowe:

1. Kliknij przycisk Nowe okno dialogowe pociągu.
2. Wpisz tekst "hello".
3. Kliknij wynik akcji i wybierz pozycję "Miasto, które chcesz?"
2. Wprowadź "foo".
    - To jest przykład miasta, który nie ma systemu. 
5. Kliknij przycisk wynik akcji
6. Wybierz opcję "nie wiem, nazwa miejscowości, $CityUknown. Miasto, które chcesz? ".
7. Wprowadź "Warszawa".
8. Kliknij przycisk wynik akcji.
    - Zauważ, że został wyczyszczony CityUknown, i CityResolved jest wypełnione.
6. Wybierz "powiedział $City i rozpoznać który do $CityResolved".
7. Kliknij przycisk Done nauczania.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wywołania zwrotne sesji](./11-session-callbacks.md)

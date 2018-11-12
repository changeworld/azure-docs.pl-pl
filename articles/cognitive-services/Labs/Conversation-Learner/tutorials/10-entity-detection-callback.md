---
title: Jak używać wywołania zwrotnego wykrywania jednostek z modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak wywołania zwrotnego wykrywania jednostki za pomocą modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e9a3b0a2be58313266b949b907e4eb49a318a6dc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260078"
---
# <a name="how-to-use-entity-detection-callback"></a>Jak używać wywołania zwrotnego wykrywania jednostki

Ten samouczek pokazuje wywołanie zwrotne wykrywania jednostki oraz przedstawia typowy wzorzec rozpoznawania jednostek.

## <a name="video"></a>Połączenia wideo

[![Samouczek 10 (wersja zapoznawcza)](https://aka.ms/cl-tutorial-10-preview)](https://aka.ms/blis-tutorial-10)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby `tutorialEntityDetectionCallback` bot jest uruchomiona.

    npm run tutorial-entity-detection

## <a name="details"></a>Szczegóły
Wywołanie zwrotne wykrywania jednostki umożliwia obsługę reguł biznesowych, powiązane z jednostkami za pomocą kodu niestandardowego. Ten pokaz ustala nazwę miejscowości wprowadzonej przez użytkownika na nazwę kanoniczną — na przykład rozpoznawanie "big Data firmy apple" do "new york" przy użyciu wywołania zwrotne i programowe jednostek.

### <a name="open-the-demo"></a>Otwórz wersję demonstracyjną

Na liście modeli kliknij samouczek-10-EntityDetectionCallback. 

### <a name="entities"></a>Jednostki

Trzy jednostki są zdefiniowane w modelu.

![](../media/tutorial10_entities.PNG)

1. Miasto jest jednostki niestandardowej, która zapewni użytkownika jako tekst wejściowy.
2. CityUnknown jest programowe jednostki. Ta jednostka zostanie wypełniony przez system. Spowoduje to skopiowanie danych wejściowych użytkownika, jeśli system nie zna miasta, która jest.
3. CityResolved to miasto, w którym system wiedzieć o. Ta jednostka będzie nazwa kanoniczna Miasto firmy, na przykład 'big Data firmy apple' zostanie rozpoznana "new york".

### <a name="actions"></a>Akcje

Trzy czynności są zdefiniowane w modelu.

![](../media/tutorial10_actions.PNG)

1. Pierwszą akcją jest "Miasto, której chcesz użyć?"
2. Drugą jest wartość "nie wiem, to miasto $CityUknown. Miasto, której chcesz użyć? "
3. Trzecia będzie "powiedział $City i rozwiązany, aby $CityResolved."

### <a name="callback-code"></a>Wywołanie zwrotne kodu

Przyjrzyjmy się kod. Metoda EntityDetectionCallback można znaleźć w C:\<installedpath > \src\demos\tutorialSessionCallbacks.ts pliku.

![](../media/tutorial10_callbackcode.PNG)

Ta funkcja jest wywoływana po przeprowadzeniu rozpoznawania jednostek.
 
- Pierwszą rzeczą, jaką będzie on wykonywać jest $CityUknown Wyczyść. $CityUknown tylko będzie utrzymywać się we pojedynczego Włącz zawsze pobiera je wyczyszczone na początku.
- Następnie Uzyskaj listę miast, które zostały rozpoznane. Wykonaj pierwszy z nich, a następnie spróbować rozwiązać ten problem.
- Funkcja, która rozwiązuje to (resolveCity) jest zdefiniowany więcej powyżej, w kodzie. Ma listę nazwy kanonicznej miast. Nazwa miasta zostanie znaleziona na liście, zwraca go. W przeciwnym wypadku wygląda w "cityMap" i zwraca nazwę zamapowany. Jeśli nie znajdzie, Miasto, zwraca wartość null.
- Na koniec Jeśli miasto został rozwiązany na nazwę, przechowujemy go w jednostce $CityKnown. W przeciwnym wypadku należy wyczyścić, co użytkownik ma powiedział i wypełnić $CityUknown jednostki.

### <a name="train-dialogs"></a>Szkolenie w oknach dialogowych

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
2. Wpisz "hello".
3. Kliknij wynik akcji, a następnie wybierz pozycję "Miasto, której chcesz użyć?"
2. Wprowadź "Warszawa".
    - Tekst jest rozpoznawany jako jednostka miasta.
5. Kliknij wynik akcji
    - `City` i `CityResolved` zostały wypełnione.
6. Wybierz pozycję "powiedział $City i rozwiązany, aby $CityResolved".
7. Kliknij przycisk Gotowe, nauczania.

Dodaj inny przykład okno dialogowe:

1. Kliknij okno dialogowe Nowy pociągu.
2. Wpisz "hello".
3. Kliknij wynik akcji, a następnie wybierz pozycję "Miasto, której chcesz użyć?"
2. Wprowadź "apple duże".
    - Tekst jest rozpoznawany jako jednostka miasta.
5. Kliknij wynik akcji
    - `CityResolved` przedstawia wynik wykonywania kodu.
6. Wybierz pozycję "powiedział $City i rozwiązany, aby $CityResolved".
7. Kliknij przycisk Gotowe, nauczania.

Dodaj inny przykład okno dialogowe:

1. Kliknij okno dialogowe Nowy pociągu.
2. Wpisz "hello".
3. Kliknij wynik akcji, a następnie wybierz pozycję "Miasto, której chcesz użyć?"
2. Wprowadź "foo".
    - Jest to przykład miasta, do którego nie ma systemu. 
5. Kliknij wynik akcji
6. Wybierz opcję "nie wiem, to miasto $CityUknown. Miasto, której chcesz użyć? ".
7. Wprowadź "Warszawa".
8. Kliknij wynik akcji.
    - `CityUknown` został wyczyszczony, i `CityResolved` jest wypełnione.
6. Wybierz pozycję "powiedział $City i rozwiązany, aby $CityResolved".
7. Kliknij przycisk Gotowe, nauczania.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wywołania zwrotne sesji](./11-session-callbacks.md)

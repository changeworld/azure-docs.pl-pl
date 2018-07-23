---
title: Jak używać rozgałęzianie i cofanie operacji przy użyciu modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać rozgałęzianie i Cofnij operacje przy użyciu modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 05140693026e21a73b756ed0ea7bc9936bef067e
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173302"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Jak używać rozgałęzianie i operacji cofania
W tym samouczku przejdziemy przez operacje Cofnij i operacje rozgałęziania.


## <a name="details"></a>Szczegóły
- Cofnij: umożliwia deweloperowi "Cofnij" wybór danych wejściowych lub akcji użytkownika. "W tle cofnąć" faktycznie tworzy nowe okno dialogowe i ponownie odgrywa do poprzedniego kroku.  Oznacza to, że wywołanie zwrotne wykrywania jednostki i interfejs API wywołuje w oknie dialogowym zostanie ponownie wywołany.

- Gałąź: tworzy nowe okno dialogowe szkolenie, który zaczyna się w taki sam sposób, jak istniejące uczenie okna dialogowego — spowoduje to zapisanie nakład pracy podczas włącza ręcznie ponownego wprowadzania okna dialogowego. W tle "gałąź" tworzy nowe okno dialogowe i ponownie odgrywa istniejącego okna dialogowego train do zaznaczonego kroku.  Oznacza to, że wywołanie zwrotne wykrywania jednostki i interfejs API wywołuje w oknie dialogowym zostanie ponownie wywołany.


## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa pizza bot kolejności:

    npm run demo-pizza

### <a name="open-the-demo"></a>Otwórz wersję demonstracyjną

Na liście modelu interfejsu użytkownika sieci web kliknij TutorialDemo Pizza zamówienia. 

Szczegółowe informacje dotyczące pokaz Pizza zamówienia zobacz samouczek kolejności Pizza.

## <a name="undo"></a>Cofnij

Firma Microsoft będzie Cofnij części okna dialogowego, a następnie utworzyć ją ponownie z tego kroku.

### <a name="training-dialogs"></a>Szkolenie w oknach dialogowych
Zacznijmy od sesji szkoleniowych. 

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
1. Wprowadź "order pizza".
2. Kliknij wynik akcji.
3. Kliknij przycisk do wybierz opcję "co chcesz umieścić w swojej pizza?"
4. Wprowadź "grzyby oraz miejscowych serów".
5. Kliknij wynik akcji.
3. Kliknij "masz $Toppings na Twoje pizza".
6. Wybierz pozycję "Czy chcesz jeszcze jakieś?"
7. Wprowadź "Usuń grzyby i Dodaj ogrodnictwu".
    - Wybierz grzyby i usuń zaznaczenie jednostki Toppings. Tworzymy akcję, która zostaną cofnięte.
2. Kliknij krok cofnięcia.
    - Ostatni wpis jest usuwany, a następnie możemy ponownie możliwe w "Czy chcesz jeszcze jakieś?"  (Poniższy zrzut ekranu)
2. Wprowadź "Usuń grzyby i Dodaj ogrodnictwu".
8. Kliknij, aby wybrać "masz $Toppings na Twoje pizza"
    - Upewnij się, że obie te jednostki są zaznaczone poprawnie.
2. Kliknij wynik akcji. Można kontynuować teraz za pomocą okna dialogowego poprawiony.
4. Kliknij przycisk Gotowe, nauczania.

Teraz wiesz jak używać akcji i Cofnij, aby usunąć dane wejściowe użytkownika.

![](../media/tutorial15_undo.PNG)

## <a name="branch"></a>Rozgałęzienie

Na przykład umożliwia otwarcie istniejącego okna dialogowego szkolenie i utworzyć okno dialogowe train przez rozgałęzianie.

1. Kliknij przycisk Train okien dialogowych, a następnie "nowe zamówienie" Aby otworzyć okno dialogowe istniejących. 
2. Kliknij pozycję ostatniego "nie" w oknie dialogowym (zobacz poniższy zrzut ekranu).
3. Kliknij gałąź.
    - "no" zostanie usunięta, a całe okno do tego punktu jest kopiowana do nowej. 
    - Pozwala to zaoszczędzić możesz ponownego wprowadzania poprzednim włącza Eksplorowanie nowej "gałąź" od tej pory.
1. Wprowadź wartość "tak".
2. Kliknij wynik akcji.
3. Wybierz pozycję "Masz $Toppings na Twoje pizza".
6. Wybierz pozycję "Czy chcesz jeszcze jakieś?"
7. Wprowadź "no".
4. Kliknij przycisk Gotowe, nauczania.

![](../media/tutorial15_branch.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przechowywanie wersji i znakowanie](./16-versioning-and-tagging.md)

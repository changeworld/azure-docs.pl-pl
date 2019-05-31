---
title: Jak używać rozgałęzianie i cofanie operacji przy użyciu modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać rozgałęzianie i Cofnij operacje przy użyciu modelu uczeń konwersacji.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 6ffa0881df07e453c8beb175b8580deebbfc1ec9
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389893"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Jak używać rozgałęzianie działań i Cofnij operacje
W tym samouczku przejdziemy przez operacje Cofnij i operacje rozgałęziania.


## <a name="details"></a>Szczegóły
### <a name="undo"></a>Cofnij
Umożliwia deweloperowi "Cofnij" ostatni wybór danych wejściowych lub akcji użytkownika. "W tle cofnąć" faktycznie tworzy nowe okno dialogowe i powoduje ponowne uruchomienie do poprzedniego kroku.  Oznacza to, że wywołanie zwrotne wykrywania jednostki i interfejs API wywołuje w oknie dialogowym zostanie ponownie wywołany.

### <a name="branch"></a>Branch
Tworzy nowy train okno dialogowe, który zaczyna się w taki sam sposób, jak istniejące uczenie okna dialogowego — spowoduje to zapisanie nakład pracy podczas włącza ręcznie ponownego wprowadzania okna dialogowego. W tle "gałąź" tworzy nowe okno dialogowe i odtwarza istniejącego okna dialogowego train do zaznaczonego kroku.  Oznacza to, że wywołanie zwrotne wykrywania jednostki i interfejs API wywołuje w oknie dialogowym zostanie ponownie wywołany.


## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa Bot, wykorzystującej pizza zamówień:

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>Otwórz lub zaimportować wersję demonstracyjną

Jeśli już znasz już instrukcje z samouczka szeregowania głosi, następnie po prostu otwórz modelu z listy w internetowym interfejsie użytkownika. W przeciwnym razie konieczne będzie kliknij "Samouczki Import" i wybierz pozycję modelu o nazwie "Pokaz PizzaOrder".

## <a name="undo"></a>Cofnij

Poniżej przedstawiono przykładowy sposób zobaczyć `Undo` w działaniu:

### <a name="training-dialogs"></a>Szkolenie w oknach dialogowych
1. W lewym panelu, kliknij przycisk "Okien dialogowych Train", a następnie kliknij `New Train Dialog` przycisku.
2. Typ, "Zamów pizza".
3. Kliknij przycisk `Score Actions`.
4. Kliknij przycisk do wybierz opcję "Co chcesz umieścić w swojej pizza?"
5. Wpisz "wszystko".
6. Kliknij przycisk `Undo`.
    - Ostatni wpis zostanie usunięty, pozostawiając ostatnia odpowiedź Bot "Co chcesz umieścić w swojej pizza?"

## <a name="branch"></a>Branch

Dla tej wersji demonstracyjnej możemy otworzyć istniejące okno dialogowe Train i utworzyć nowe okno dialogowe szkolenie z niego przez rozgałęzianie.

1. Na lewym panelu kliknij przycisk "Okien dialogowych Train".
2. Zwróć uwagę, siatki, powinien zostać wyświetlony tylko jeden, szkolenia, który zaczyna się od "nowe zamówienie".
3. W siatce, kliknij pozycję "nowe zamówienie" Aby otworzyć istniejące okno dialogowe pociągu.
4. Kliknij ostatni "no" w oknie dialogowym.
5. Kliknij ikonę "Gałąź", jego jest zakreślony na czerwono na tej ilustracji:
    - ![](../media/tutorial15_branch.PNG)
    - Całe okno Train przed "no" jest kopiowana do nowego okna dialogowego pociągu.
    - Pozwala to zaoszczędzić możesz ponownego wprowadzania poprzednim przechodzi zapoznaj się z nową konwersację "gałąź" od tej pory.
6. Wpisz "tak", trafienia wprowadź.
7. Kliknij przycisk `Score Actions`.
    - W tym momencie Bot automatycznie wybiera odpowiedzi, ale firma Microsoft nie podoba Ci odpowiedzi, dlatego użyjemy go zmienić.
8. Kliknij ostatnią odpowiedź Botów.
    - Daj nam wybierze inny odpowiedzi.
9. Wybierz pozycję "UseLastToppings".
10. Kliknij przycisk `Score Actions`.
    - Ponownie Bot automatycznie wybiera odpowiedzi. Jego powinien powiedzieć "Masz produkcji kiełbas oraz miejscowych serów grzyby na Twoje pizza.". 
    - Teraz firma Microsoft, takich jak odpowiedzi, zostanie ona zachowana.
11. Kliknij przycisk `Score Actions`.
    - Ponownie Bot automatycznie wybiera odpowiedzi powinna być widoczna nazwa, "Czy chcesz jeszcze jakieś?"
12. Wpisz "no".
13. Kliknij przycisk `Save Branch`.
14. Należy zauważyć, że siatka zawiera teraz dwa szkoleniach, które zaczyna się od "nowe zamówienie".
    - Jeden z nich jest ten, który umożliwia gałęzie elementu.
    - I jeden z nich jest rozgałęziony wersji, który został zapisany.
    - Kliknij każdą z nich, aby sprawdzić jej oczekiwania.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przechowywanie wersji i znakowanie](./18-version-tag.md)

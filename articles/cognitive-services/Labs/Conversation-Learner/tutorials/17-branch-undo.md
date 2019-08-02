---
title: Jak używać rozgałęzień i operacji cofania z modelem Conversation Learner — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak używać operacji rozgałęziania i cofania z modelem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f4f3024451696dbd0244d9da39cba67b49447af1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703637"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Jak używać rozgałęzień i operacji cofania
W tym samouczku przejdziemy do operacji cofania i rozgałęziania.


## <a name="details"></a>Szczegóły
### <a name="undo"></a>Cofnij
Zezwala deweloperowi na "cofanie" ostatnio wprowadzonych danych wejściowych użytkownika lub akcji. W tle "Cofnij" powoduje utworzenie nowego okna dialogowego i odtwarzanie go do poprzedniego kroku.  Oznacza to, że wykrycie wywołania zwrotnego i wywołań interfejsu API w oknie dialogowym zostanie wywołane ponownie.

### <a name="branch"></a>Rozgałęzienie
Tworzy nowe okno dialogowe uczenia, które zaczyna się w taki sam sposób jak istniejące okno dialogowe szkolenia — spowoduje to zaoszczędzenie nakładu pracy ręcznej ponownej zmiany okna dialogowego. W tle "gałąź" tworzy nowe okno dialogowe i odtwarza istniejące okno dialogowe szkolenia do wybranego kroku.  Oznacza to, że wykrycie wywołania zwrotnego i wywołań interfejsu API w oknie dialogowym zostanie wywołane ponownie.


## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby bot, który pobiera Pizza zamówienia, jest uruchomiony:

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>Otwórz lub zaimportuj demonstrację

Jeśli już pracowałeś w samouczku porządkowania Pizza, po prostu otwórz ten model z listy w interfejsie użytkownika sieci Web. W przeciwnym razie trzeba kliknąć pozycję "Importowanie samouczków" i wybrać model o nazwie "Demonstracja-PizzaOrder".

## <a name="undo"></a>Cofnij

Oto przykład sposobu wyświetlania `Undo` funkcji w działaniu:

### <a name="training-dialogs"></a>Okna dialogowe szkoleń
1. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie `New Train Dialog` kliknij przycisk.
2. Wpisz "Order a Pizza".
3. Kliknij przycisk `Score Actions`.
4. Kliknij, aby wybrać "co chcesz zrobić w Twoim Pizza?".
5. Wpisz "cokolwiek".
6. Kliknij przycisk `Undo`.
    - Ostatni wpis zostanie usunięty, pozostawiając ostatnią odpowiedź bot "co chcesz zrobić w Twoim pizzae?".

## <a name="branch"></a>Rozgałęzienie

Na potrzeby tego pokazu zostanie otwarte okno dialogowe pouczenie i zostanie utworzone nowe okno dialogowe uczenia z tego rozgałęzienia.

1. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego".
2. Zwróć uwagę na siatkę, więc zobaczysz tylko jedno szkolenie, które rozpoczyna się od "New Order".
3. W siatce kliknij pozycję "Nowa kolejność", aby otworzyć okno dialogowe istniejące szkolenie.
4. Kliknij ostatni "No" w oknie dialogowym.
5. Kliknij ikonę "gałąź", która jest zakreślona na czerwono na tym obrazie:
    - ![](../media/tutorial15_branch.PNG)
    - Całe okno dialogowe szkolenia przed elementem "No" jest kopiowane do okna dialogowego nowego szkolenia.
    - Spowoduje to zaoszczędzenie ponownego wprowadzenia poprzedniej zmiany w celu zbadania nowej konwersacji "Branch" (rozgałęzienie) od tego momentu.
6. Wpisz "yes", naciśnij klawisz ENTER.
7. Kliknij przycisk `Score Actions`.
    - W tym momencie bot automatycznie wybiera odpowiedź, ale nie Przypominamy odpowiedzi, więc zamierzamy ją zmienić.
8. Kliknij ostatnią odpowiedź bot.
    - Pozwoli to nam wybrać inną odpowiedź.
9. Wybierz pozycję "UseLastToppings".
10. Kliknij przycisk `Score Actions`.
    - Ponownie bot automatycznie wybiera odpowiedź. Powinno to powiedzieć, że na Twojej Pizza masz kiełbasy, sery i grzyby. 
    - Tym razem podoba nam się odpowiedź, dlatego będziemy ją zachować.
11. Kliknij przycisk `Score Actions`.
    - Ponownie bot automatycznie wybiera odpowiedź, powinien powiedzieć, "czy chcesz coś innego?"
12. Wpisz "No".
13. Kliknij przycisk `Save Branch`.
14. Należy zauważyć, że siatka ma teraz dwa szkolenia, które zaczynają się od "New Order".
    - Jeden z nich jest używany do rozgałęziania.
    - Jest to rozgałęzienie, która właśnie została zapisana.
    - Kliknij każdy z nich, aby sprawdzić te oczekiwania.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przechowywanie wersji i tagowanie](./18-version-tag.md)

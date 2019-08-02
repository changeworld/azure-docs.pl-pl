---
title: Jak korzystać z kart z modelem Conversation Learner, część 1 — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak używać kart z modelem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 426d7c8de29abeb88833e94962a7291a641702ac
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703742"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Jak korzystać z kart (część 1 z 2)

W tym samouczku pokazano, jak dodać prostą kartę w botie i korzystać z niej.

> [!NOTE]
> Conversation Learner obecnie oczekuje, że pliki definicji kart mają znajdować się w katalogu o nazwie "karty", który znajduje się w katalogu, w którym uruchomiono bot.

## <a name="video"></a>Połączenia wideo

[![Samouczek dotyczący kart](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby ogólny samouczek bot był uruchomiony

    npm run tutorial-general

## <a name="details"></a>Szczegóły

Karty to elementy interfejsu użytkownika, które umożliwiają użytkownikowi wybranie opcji w konwersacji. 

### <a name="open-the-demo"></a>Otwórz demonstrację

W interfejsie użytkownika sieci Web kliknij pozycję "Importowanie samouczków" i wybierz model o nazwie "samouczek-15-Cards".

### <a name="the-card"></a>Karta

Definicja karty znajduje się w następującej lokalizacji: C:\<installedpath\>\src\cards\prompt.json.

System oczekuje na znalezienie definicji karty w tym katalogu "karty".

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Zwróć uwagę na typ treści "TextBlock" i symbol zastępczy "{{pyta}}" w polu tekstowym.
> Istnieją dwa przyciski przesyłania i tekst, który jest przesyłany dla każdego z nich.

### <a name="actions"></a>Akcje

Utworzyliśmy trzy akcje. Jak widać poniżej, pierwsza akcja to karta.

![](../media/tutorial13_actions.PNG)

Zobaczmy, jak utworzono typ akcji karty:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> Karta zawiera trzy różne parametry — dane wejściowe pytania, przycisk 1 i przycisk 2. Te elementy są odwołaniami do szablonu na karcie, w których wprowadzasz pytanie i odpowiadające im odpowiedzi. Można także odwoływać się do jednostek lub kombinacji tekstu i jednostek.

Ikona oka pokazuje, jak wygląda karta.

### <a name="practicing-creating-card-actions"></a>Praktyczne Tworzenie akcji karty

1. Na panelu po lewej stronie kliknij pozycję akcje, a następnie przycisk "nowa akcja".
2. Wybierz pozycję "Karta" dla "typ akcji".
3. Wybierz pozycję "Monituj" z listy "template" (szablon).
4. W polu "pytanie" wpisz "przejdź do lewej lub w prawo"
5. W polu "Button1" wpisz "Left"
6. W polu "Button2" wpisz "Right"
7. Kliknij przycisk "Anuluj".

### <a name="train-dialog-using-an-adaptive-card"></a>Okno dialogowe uczenia przy użyciu karty adaptacyjnej

1. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie przycisk "okno dialogowe nowego uczenia".
2. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Witaj"
3. Kliknij przycisk "akcje oceny".
4. Wybierz odpowiedź, "Prompt: pytanie: Przejdź w lewo lub w prawo? "
    - Ikona oka może służyć do wyświetlania podglądu karty
5. W panelu rozmowa kliknij przycisk "lewy" w wierszu renderowane.
6. Kliknij przycisk "akcje oceny".
7. Wybierz odpowiedź, "z lewej"
8. Kliknij przycisk "Zapisz".
9. Wybierz odpowiedź, "Prompt: pytanie: Przejdź w lewo lub w prawo? "
10. W panelu rozmowa kliknij przycisk "prawy" w wierszu renderowane.
11. Kliknij przycisk "akcje oceny".
12. Wybierz odpowiedź, "Right"

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Botów hybrydowe](./16-hybrid-bots.md)

---
title: Jak używać alternatywnych danych wejściowych z Conversation Learner-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak używać alternatywnych danych wejściowych z Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7416939db8d6552f79fba700b8432de2ad228846
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704211"
---
# <a name="how-to-use-alternative-inputs"></a>Jak używać alternatywnych wejść

W tym samouczku pokazano, jak używać pola alternatywne dane wejściowe dla użytkownika wyrażenia długości w interfejsie nauczania.

## <a name="video"></a>Połączenia wideo

[![Wersja zapoznawcza samouczka dotyczącego wejść](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby ogólny samouczek bot był uruchomiony

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Alternatywne dane wejściowe są alternatywne, semantycznie równoważne wyrażenia długości użytkownika, który użytkownik może mieć w konkretnym punkcie w oknie dialogowym szkolenia. Te alternatywne dane wejściowe umożliwiają bardziej kompaktowe Określanie wariantów wyrażenia długości bez konieczności podawania poszczególnych zmian w osobnych oknach dialogowych szkoleniowych.

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W interfejsie użytkownika sieci Web kliknij pozycję "nowy model".
2. W polu "nazwa" wpisz "AlternativeInputs" i naciśnij klawisz ENTER.
3. Kliknij przycisk "Utwórz".

### <a name="entity-creation"></a>Tworzenie jednostki

1. Na panelu po lewej stronie kliknij pozycję "jednostki", a następnie przycisk "Nowa jednostka".
2. Wybierz pozycję "niestandardowa przeszkolony" dla "typu jednostki".
3. Wpisz "miasto" dla "Nazwa jednostki".
4. Kliknij przycisk "Utwórz".

![](../media/T10_actions.png)

Teraz Utwórzmy trzy akcje.

### <a name="create-the-first-action"></a>Utwórz pierwszą akcję

1. Na panelu po lewej stronie kliknij pozycję akcje, a następnie przycisk "nowa akcja".
2. W odpowiedzi "bot"... " Wpisz "to miasto?"
3. W "oczekiwana jednostka w odpowiedzi użytkownika..." Wpisz "miasto".
4. W polu "niekwalifikujące się uprawniania" wpisz "miasto".
5. Kliknij przycisk "Utwórz".

![](../media/T10_action_create_1.png)

### <a name="create-the-second-action"></a>Utwórz drugą akcję

1. Na panelu po lewej stronie kliknij pozycję akcje, a następnie przycisk "nowa akcja".
2. W odpowiedzi "bot"... " Wpisz "Pogoda w $city prawdopodobnie Sunny".
3. Kliknij przycisk "Utwórz".

![](../media/T10_action_create_2.png)

### <a name="create-the-third-action"></a>Utwórz trzecią akcję

1. Na panelu po lewej stronie kliknij pozycję akcje, a następnie przycisk "nowa akcja".
2. W odpowiedzi "bot"... " Wpisz "Spróbuj poprosić o Pogoda".
3. W polu "niekwalifikujące się uprawniania" wpisz "miasto".
4. Kliknij przycisk "Utwórz".

![](../media/T10_action_create_3.png)

Masz teraz trzy akcje.

![](../media/T10_actions.png)

### <a name="train-the-model"></a>Uczenie modelu

1. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie przycisk "okno dialogowe nowego uczenia".
2. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "co to jest pogoda?".
3. Kliknij przycisk "akcje oceny".
4. Wybierz odpowiedź (miasto?)
5. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Denver"
6. Kliknij przycisk "akcje oceny".
7. Wybierz odpowiedź, "Pogoda w Denver prawdopodobnie Sunny".
8. Kliknij przycisk "Prześlij zmiany".

![](../media/T10_training_1.png)

Przekażmy modelowi więcej, tworząc kolejne okno dialogowe szkolenia.

### <a name="second-model-train-dialog"></a>Okno dialogowe szkolenia drugiego modelu

1. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie przycisk "okno dialogowe nowego uczenia".
2. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz w "co możesz zrobić?"
3. Kliknij przycisk "akcje oceny".
4. Wybierz odpowiedź "Wypróbuj pytanie o Pogoda".
5. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "co to jest Pogoda w Seattle?"
6. Kliknij pozycję "Seattle", a następnie kliknij pozycję "miasto" na liście jednostek.
7. Kliknij przycisk "akcje oceny".
8. Wybierz odpowiedź, "Pogoda w Seattle jest prawdopodobnie Sunny".
9. Kliknij przycisk "Prześlij zmiany".

![](../media/T10_training_2.png)

### <a name="third-model-train-dialog-using-alternative-input"></a>Okno dialogowe szkolenia w trzecim modelu przy użyciu alternatywnego Wejścia

1. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie przycisk "okno dialogowe nowego uczenia".
2. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "pomoc"
3. Kliknij przycisk "akcje oceny".
    - Model jest nieokreślony dla najlepszej opcji, dlatego domyślnie wybiera najwyższy percentyl.
4. Kliknij przycisk "Porzuć uczenie", a następnie przycisk "Potwierdź".

![](../media/T10_training_3.png)

Lepiej dostosować system przy użyciu alternatywnych danych wejściowych. Możesz dodać alternatywne dane wejściowe podczas nauczania lub w późniejszym czasie.

1. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie wybierz pozycję "co możesz zrobić?". z listy okien dialogowych uczenia.
1. Kliknij "co możesz zrobić?" wypowiedź w panelu rozmowy.
1. W polu "Dodaj alternatywne dane wejściowe..." Wpisz tekst "Help" i naciśnij klawisz ENTER.
1. Kliknij przycisk "Zapisz zmiany".

![](../media/T10_training_4.png)

Dodajmy kolejne alternatywne dane wejściowe do obsługi Houston.

1. Kliknij "co to jest Pogoda w Seattle?" wypowiedź w panelu rozmowy.
1. W polu "Dodaj alternatywne dane wejściowe..." Wpisz "Prognoza dla Houston" i naciśnij klawisz ENTER.
   - Komunikat o błędzie wyróżnia fakt alternatywny danych wejściowych musi być semantycznie równoważny i zawierać te same jednostki, co oryginalne wypowiedź; nie tylko te same wartości jednostek. Obecność tych samych jednostek jest wymagana.
1. Kliknij pozycję "Houston" i wybierz pozycję "miasto" z listy jednostek.
1. W polu "Dodaj alternatywne dane wejściowe..." Wpisz "Prognoza dla Seattle" i naciśnij klawisz ENTER.
1. Kliknij pozycję "Seattle" i wybierz pozycję "miasto" z listy jednostek.
1. Kliknij przycisk "Zapisz zmiany".
1. Kliknij przycisk "Zapisz Edytuj".

![](../media/T10_training_5.png)

### <a name="testing-the-model"></a>Testowanie modelu

1. Na panelu po lewej stronie kliknij pozycję "dzienniki okien dialogowych", a następnie "nowe okno dialogowe dziennika".
2. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Pomóż mi"
3. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "REGLINX for Denver"

![](../media/T10_logdialog.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Okna dialogowe dziennika](./11-log-dialogs.md)

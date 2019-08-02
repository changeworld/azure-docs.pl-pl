---
title: Jak używać wywołania zwrotnego sesji z modelem Conversation Learner — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak używać wywołania zwrotnego sesji z modelem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 656d028082d56b8f094e83363e5189b163581c53
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703959"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Jak używać wywołania zwrotnego sesji z modelem Conversation Learner

W tym samouczku przedstawiono sesje, sposób ich obsługi oraz Conversation Learner wywołania zwrotne onSessionStart i onSessionEnd.

## <a name="video"></a>Połączenia wideo

[![Samouczek wywołania zwrotnego sesji — wersja zapoznawcza](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby tutorialSessionCallbacks "bot" działa.

    npm run tutorial-session-callbacks

## <a name="details"></a>Szczegóły
Ten samouczek obejmuje koncepcję sesji, sposób obsługi sesji domyślnie, a także sposób przesłonięcia tego zachowania.

W Conversation Learner sesja reprezentuje jedną, nieprzerwaną interaktywną wymianę z bot. Sesje mogą mieć wiele operacji, ale program programowo zakończył działanie z powodu nieaktywności, jeśli upłynął okres dłuższy niż 30 minut.  Zapoznaj się z tematem "limity", aby uzyskać informacje na temat zmiany tego domyślnego limitu czasu sesji.

Ten długi czas braku aktywności spowoduje utworzenie nowej sesji przez bot i zresetowanie sieci neuronowych do stanu początkowego. Domyślnie wszystkie wartości jednostki zostaną wyczyszczone. To domyślne zachowanie czyszczenia wartości jednostek można zmienić, jak pokazano poniżej.

### <a name="load-the-demo-model"></a>Załaduj model demonstracyjny

W interfejsie użytkownika sieci Web kliknij pozycję "Importowanie samouczków" i wybierz model o nazwie "samouczek-13-SessionCallbacks".

### <a name="code-for-the-callbacks"></a>Kod wywołań zwrotnych

Przykładowy kod dla dwóch wywołań zwrotnych tego modelu można znaleźć w: `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`.

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback: Ta metoda ustawia jednostkę BotName.
- OnSessionEndCallback: można określić, co chcesz zachować. Spowoduje to wyczyszczenie wszystkich jednostek z wyjątkiem nazwy użytkownika i telefonu użytkownika.

Każde wywołanie zwrotne jest opcjonalne.

### <a name="actions"></a>Akcje

Cztery akcje są zdefiniowane w modelu. Istniejące akcje są wyświetlane w widoku siatki dla "akcje"

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>Tworzenie akcji kończenia sesji (dla wywołania wywołania zwrotnego)

1. Na panelu po lewej stronie kliknij pozycję akcje, a następnie przycisk "nowa akcja".
2. Wybierz pozycję "ENDSESSION" dla "typ jednostki".
3. W "dane..." Wpisz "gotowe"
4. Kliknij przycisk "Utwórz".

### <a name="edit-an-existing-action"></a>Edytuj istniejącą akcję

1. Wybierz akcję "tak, $UserName, jesteś w $UserLocation" w widoku siatki.
2. Usuń zaznaczenie pola wyboru "Czekaj na odpowiedź".
3. Kliknij przycisk "Zapisz".

### <a name="chaining-actions"></a>Akcje łańcucha

1. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie przycisk "okno dialogowe nowego uczenia".
2. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Witaj".
3. Kliknij przycisk "akcje oceny".
4. Wybierz odpowiedź "Witaj, jestem botty. Co to jest Twoja nazwa? "
5. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Lars"
6. Wybierz odpowiedź "Witaj Lars. Jaki jest Twój numer telefonu?
7. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz w "555-555-5555"
8. Kliknij przycisk "akcje oceny".
9. Wybierz odpowiedź "czy możesz powiedzieć, botty lokalizację, Lars?".
10. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Seattle"
11. Kliknij przycisk "akcje oceny".
12. Wybierz odpowiedź "So, Lars, jesteś w Seattle"
13. Wybierz odpowiedź "gotowe"
14. Kliknij przycisk "Zapisz".

### <a name="testing-the-model"></a>Testowanie modelu

1. Na panelu po lewej stronie kliknij pozycję "dzienniki okien dialogowych", a następnie przycisk "nowe okno dialogowe rejestrowania".
2. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Witaj"
3. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Lars"
4. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz w "555-555-5555"
5. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Seattle"
    - W tym momencie wszystkie wartości jednostek, które wykluczają lokalizację, powinny zostać zachowane.
6. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Hello"
7. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Detroit"
8. Kliknij przycisk "limit czasu sesji".
    - Kliknięcie tego przycisku powoduje wykonanie odpowiedzi Bot na długie okresy braku aktywności
9. Kliknij przycisk "OK".
10. Kliknij przycisk "gotowe do testowania".

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wywołania interfejsu API](./14-api-calls.md)

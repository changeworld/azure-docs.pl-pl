---
title: Jak używać wywołań zwrotnych sesji z modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak używać wywołań zwrotnych sesji przy użyciu modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 246b87e46029c2bf4d7361540939181b3b209acc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635684"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Jak używać wywołań zwrotnych sesji przy użyciu modelu uczeń konwersacji

W tym samouczku przedstawiono sesjach informacyjnych, jak są obsługiwane i onSessionStart uczeń konwersacji i wywołania zwrotne onSessionEnd.

## <a name="video"></a>Połączenia wideo

[![Sesja wywołania zwrotne samouczka (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, że bot "tutorialSessionCallbacks" jest uruchomiona.

    npm run tutorial-session-callbacks

## <a name="details"></a>Szczegóły
W tym samouczku opisano pojęcia sesji obsługi sesji domyślnie i jak można zastąpić to zachowanie.

W uczeń konwersacji sesję reprezentuje jeden, nieprzerwany interaktywne programu exchange przy użyciu bota. Sesje może mieć wiele włącza, ale można programowo zakończone z powodu braku aktywności, jeśli jest większa niż 30 minut wygasł.  Zobacz stronę pomocy na "Ograniczenia", aby uzyskać informacje o zmienianiu to domyślny czas trwania limit czasu sesji.

Ta dłuższym okresie nieaktywności spowoduje, że bot utworzyć nową sesję i zresetować powtarzającego się sieci neuronowej do stanu początkowego. Domyślnie zostaną wyczyszczone wszystkie wartości jednostki. Wyczyszczenie wartości jednostki to domyślne zachowanie można zmienić, jak pokazano poniżej.

### <a name="load-the-demo-model"></a>Ładowanie modelu pokaz

W internetowym interfejsie użytkownika kliknij pozycję "Importuj samouczki" i wybierz model o nazwie "Samouczek-13-SessionCallbacks".

### <a name="code-for-the-callbacks"></a>Kod dla wywołania zwrotne

Przykładowy kod dla tego modelu dwóch wywołań zwrotnych znajdują się w: `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`.

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback: Ta metoda ustawia jednostkę BotName.
- OnSessionEndCallback: można określić mają zostać zachowane. Spowoduje to wyczyszczenie wszystkich jednostek, z wyjątkiem nazwy użytkownika i telefonu użytkownika.

Każdego wywołania zwrotnego jest opcjonalne.

### <a name="actions"></a>Akcje

Cztery akcje są zdefiniowane w modelu. Istniejące akcje są wyświetlane w widoku siatki dla "Akcje"

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>Tworzenie Zakończ sesję działanie (w przypadku wywołania zwrotnego)

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. Wybierz pozycję "ENDSESSION" dla "Typu jednostek".
3. W polu "Dane..." wpisz "Gotowe"
4. Kliknij przycisk "Utwórz".

### <a name="edit-an-existing-action"></a>Edytowanie istniejącej akcji

1. Wybierz pozycję "tak, $UserName, jesteś w $UserLocation" akcji z widoku siatki.
2. Zaznaczenie pole wyboru "Oczekiwania na odpowiedź".
3. Kliknij przycisk "Zapisz".

### <a name="chaining-actions"></a>Tworzenie łańcuchów działań

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "hi."
3. Kliknij przycisk "Wynik akcje".
4. Wybierz odpowiedź, "cześć, jestem Botty. Jak się Nazywasz?"
5. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Hurtowi"
6. Wybierz odpowiedź, "Hi hurtowi. Co to jest Twój numer telefonu?"
7. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "555-555-5555"
8. Kliknij przycisk "Wynik akcje".
9. Wybierz odpowiedź "Można stwierdzić, Botty swojej lokalizacji hurtowi?"
10. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Seattle"
11. Kliknij przycisk "Wynik akcje".
12. Wybierz odpowiedź, "Aby hurtowi, są w Seattle"
13. Wybierz odpowiedź "Gotowe"
14. Kliknij przycisk "Zapisz".

### <a name="testing-the-model"></a>Testowanie modelu

1. W lewym panelu kliknij przycisk "Okien dialogowych dziennika", a następnie przycisk "Nowy dziennik Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "hi"
3. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Hurtowi"
4. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "555-555-5555"
5. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Seattle"
    - W tym momencie wszystkie wartości jednostki, z wyjątkiem lokalizacji powinny zostały zachowane.
6. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "hello"
7. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Detroit"
8. Kliknij przycisk "Limit czasu sesji".
    - Kliknięcie tego przycisku wykonuje odpowiedzi bot długie okresy braku aktywności
9. Kliknij przycisk "OK".
10. Kliknij przycisk "Testowanie gotowe".

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wywołania interfejsu API](./14-api-calls.md)

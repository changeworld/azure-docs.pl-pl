---
title: Wprowadzenie do szkolenia Conversation Learner model — Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Dowiedz się, jak szkolić model, w tym rozgałęzianie i edytowanie poprzedniego szkolenia za pośrednictwem Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c657025ce588363cf76ce10868d809a9aff69222
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705628"
---
# <a name="introduction-to-training"></a>Wprowadzenie do szkolenia

W tym samouczku przedstawiono podstawowe informacje dotyczące uczenia modelu, rozgałęziania nowego szkolenia na podstawie poprzedniego szkolenia i edytowania odpowiedzi bot w celu ich zmiany.

## <a name="video"></a>Połączenia wideo

[![Wprowadzenie do szkolenia samouczka w wersji zapoznawczej](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga, aby ogólny samouczek bot był uruchomiony

    npm run tutorial-general

## <a name="details"></a>Szczegóły

- Akcje: Odpowiedź Bot na dane wejściowe użytkownika.
- Train: Sposób, w jaki bot się reagować na dane wejściowe użytkownika.
- Rozgałęzianie Modyfikacja danych wprowadzonych przez użytkownika w oknie dialogowym zapisanego szkolenia na potrzeby tworzenia nowego okna dialogowego uczenia, które uruchamia się w taki sam sposób, jak oryginalne, ale wykonuje konwersację w innym kierunku.

## <a name="steps"></a>Kroki

### <a name="create-a-new-model"></a>Utwórz nowy model

1. W interfejsie użytkownika sieci Web kliknij pozycję Nowy model.
2. Dla elementu "name" wpisz "" bot ". Następnie kliknij przycisk Utwórz.

### <a name="create-an-action"></a>Tworzenie akcji

1. Na panelu po lewej stronie kliknij pozycję akcje, a następnie przycisk "nowa akcja".
2. W polu "odpowiedź bot" wpisz "Witaj! Czy chcesz już dzisiaj zainspirowanić? ".
    - Pozostaw wszystkie pozostałe pola i pola wyboru w domyślnym ustawieniu.
3. Kliknij pozycję Utwórz.

### <a name="first-training-and-creating-another-action-while-training"></a>Pierwsze szkolenie i Tworzenie kolejnej akcji podczas szkolenia

1. Na panelu po lewej stronie kliknij pozycję "szkolenie okna dialogowego", a następnie przycisk "okno dialogowe nowego uczenia".
2. W panelu rozmowa, gdzie mówi "wpisz wiadomość...", wpisz "Hello". 
    - To symuluje po stronie rozmowy.
3. Kliknij pozycję "wyniki akcji".
4. Wybierz odpowiedź "Witaj! Czy chcesz już dzisiaj zainspirowanić? ".
5. W miarę jak użytkownik reaguje na, "tak".
6. Kliknij pozycję "wyniki akcji".
7. Kliknij przycisk "+ Akcja". 
    - Spowoduje to przejście do znanego okna dialogowego "Tworzenie akcji".
8. Wpisz odpowiedź bot jako "Jesteś awesome!"
9. Kliknij pozycję Utwórz.
10. Zwróć uwagę, że bot reaguje natychmiast.
11. Kliknij przycisk "Zapisz".

### <a name="branch-a-second-training-off-of-the-first-training"></a>Rozgałęzianie drugiego szkolenia od pierwszego szkolenia
1. Kliknij wiersz siatki, który podsumowuje pierwsze szkolenie. 
    - Pozwala to wyświetlać i edytować istniejące szkolenie.
2. Kliknij odpowiedź użytkownika "tak". 
    - Spowoduje to udostępnienie kontrolek edycji.
3. Kliknij ikonę rozgałęzienia. 
    - Spowoduje to wyświetlenie monitu o inne dane wejściowe użytkownika dla nowej konwersacji.
4. Wpisz "No", naciśnij klawisz ENTER lub kliknij przycisk "Utwórz". 
    - W tym momencie będziesz mieć nowe wystąpienie okna dialogowego uczenia, oryginalne pozostanie niezmienione.
5. Kliknij pozycję "wyniki akcji".
6. Kliknij nieprawidłową odpowiedź bot.
7. Kliknij przycisk "+ Akcja" 
    - Dzięki temu możemy utworzyć nową akcję Bot na odpowiedź.
8. Wpisz bot odpowiedź jako "nie ma problemu! Masz doskonały dzień! "
9. Kliknięcie pozycji Utwórz
10. Zwróć uwagę, że bot reaguje natychmiast.
11. Kliknij przycisk "Zapisz".

### <a name="test-the-trainings"></a>Testowanie szkoleń
1. Na panelu po lewej stronie kliknij pozycję "dzienniki okien dialogowych", a następnie polecenie "nowe okno dialogowe dziennika".
2. Wpisz komunikat "Hi". 
3. Zwróć uwagę, że bot reaguje automatycznie w sposób, w jaki został przeszkolony.
4. Wpisz odpowiedź użytkownika, "tak".
5. Zwróć uwagę na odpowiedź Botą, że pierwsze szkolenie działa.
6. Kliknij przycisk "limit czasu sesji". Oznacza to, Conversation Learner chcemy ponownie rozpocząć pracę, ignorując przemieszczenie rozmowy, które miały miejsce.
7. Wpisz komunikat "Hi". 
8. Zwróć uwagę, że bot reaguje automatycznie w sposób, w jaki został przeszkolony.
9. Wpisz odpowiedź użytkownika "nie".
10. Zwróć uwagę na odpowiedź bot, która pokazuje, że drugie szkolenie działa.
11. Kliknij przycisk "gotowe do testowania".

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Akcje oczekiwania i nieoczekiwanie](./03-wait-vs-nonwait-actions.md)

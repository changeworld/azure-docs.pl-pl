---
title: Jak rejestrować okien dialogowych w modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak rejestrować okien dialogowych w modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 6ceeb9683a979256a8a52347fc74ab758fd1d348
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171439"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Jak rejestrować okien dialogowych w modelu uczeń konwersacji

W tym samouczku przedstawiono sposób wykonywania testów w ramach interfejsu uczeń konwersacji; użytkownika końcowego jak okna dialogowe są rejestrowane; a jak wprowadzać poprawki, aby rejestrowane okien dialogowych w celu poprawy modelu.

## <a name="video"></a>Połączenia wideo

[![Samouczek 9 (wersja zapoznawcza)](http://aka.ms/cl-tutorial-09-preview)](http://aka.ms/blis-tutorial-09)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Aby przejrzeć i wprowadzić korekty do okien dialogowych przeprowadzanej użytkownikom końcowym, można użyć okien dialogowych dziennika.  W szczególności można naprawić etykiety jednostki i wybór akcji tak, aby zwiększyć wydajność uczonego modelu i całego systemu. 

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W Interfejsie użytkownika sieci Web kliknij przycisk Nowy Model
2. W polu Nazwa wprowadź LogDialogs. Następnie kliknij przycisk Utwórz.

### <a name="create-an-entity"></a>Tworzenie jednostki

1. Kliknij przycisk jednostki, a następnie nowej jednostki.
2. W nazwie podmiotu Wprowadź miasto.
3. Kliknij pozycję Utwórz.

### <a name="create-two-actions"></a>Utwórz dwie akcje

1. Kliknij pozycję operacje, a następnie nowa akcja
2. W odpowiedzi wpisz "Które miasta?".
3. W jednostkach dyskwalifikacji wprowadź $city.
3. Kliknięcie pozycji Utwórz

Następnie należy utworzyć drugą akcję:

1. Kliknij pozycję operacje, a następnie nową akcję.
3. W odpowiedzi wpisz "pogody w $city jest prawdopodobnie sunny".
4. Wymagane jednostki, wprowadź $city.
4. Kliknij pozycję Utwórz.

Masz teraz dwie akcje.

### <a name="train-the-bot"></a>Uczenie bota

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
2. Wpisz "co to jest pogody".
3. Kliknij wynik akcji, a następnie wybierz pozycję "Które miasta?"
2. Wprowadź "Seattle".
3. Kliknij dwukrotnie ikonę na "Seattle", a następnie wybierz miasto.
    - To oznacza je jako jednostkę miasta.
5. Kliknij wynik akcji
6. Wybierz pozycję "pogody w $city jest prawdopodobnie sunny".
7. Kliknij przycisk Gotowe, nauczania.

Dodaj inny przykład okno dialogowe:

1. Kliknij nową akcję, a następnie okno dialogowe Nowy pociągu.
2. Wpisz "co to jest pogody w Seattle?". Zwróć uwagę, że Seattle zostanie oznaczony jako jednostka.
5. Kliknij wynik akcji 
6. Wybierz pozycję "pogody w $city jest prawdopodobnie sunny".
7. Kliknij przycisk Gotowe, nauczania.

### <a name="try-the-bot-as-the-user"></a>Spróbuj bota jako użytkownik
Załóżmy, że dla użytkowników po wdrożeniu ten bot.

1. Kliknij dziennik w oknach dialogowych.
2. Kliknij nową sesję rozmowy.
    - Przedstawia bot, ponieważ może doświadczyć użytkownika w kontrolce rozmów w sieci web po lewej stronie interfejsu użytkownika. Możesz zignorować obszarze odstępu, po prawej stronie.
3. Wpisz "hello".
4. Bot odpowiedzi: "które miasta?"
4. Typ "Boston".
5. Bot odpowiedzi: "które miasta?"
    - To prawdopodobnie nie prawo. Więc Zapisz to okno dialogowe.
2. Kliknij przycisk Done testowania.

Zacznijmy od nowej sesji:

2. Kliknij nową sesję rozmowy.
3. Typ "Prognozowanie Boston".
4. Bot odpowiedzi: "które miasta?"
2. Kliknij przycisk Gotowe, nauczania.

Teraz możemy wprowadzić korekty do okna dialogowego drugiego:

1. Kliknij pozycję "prognozy dla Boston" w obszarze dziennik okien dialogowych.
    - Spowoduje to otwarcie konwersacji.
    - Kliknięcie po stronie użytkownika konwersacji (tutaj na "prognozy dla Boston"), można zmienić jednostki etykiety.
    - Po kliknięciu na stronie system (tutaj na które miasta), możesz zmienić akcję, która jest zaznaczone.
5. Kliknij pozycję "prognozy dla Boston". 
    - Przyczyny w tym miejscu jest, czy Boston nie zostało oznaczone jako jednostki. Należy zmienić.
    - Kliknij dwukrotnie pozycję "Boston", a następnie wybierz miasto.
    - Kliknij przycisk Prześlij zmiany, a następnie kliknij przycisk Zapisz. Spowoduje to utworzenie okna dialogowego szkolenia na podstawie wprowadzonych zmian i upuść możesz do szkolenia okien dialogowych w punkcie dokonanych zmian.
6. Wybierz pozycję "pogody w $city jest prawdopodobnie sunny".
7. Kliknij przycisk Gotowe, nauczania. Jeśli przejdziesz do okien dialogowych Train teraz, zobaczysz, że została dodana nowa akcja.

![](../media/tutorial9_logdiag1.PNG)

Teraz możemy wprowadzić korekty do okna dialogowego:

1. Kliknij pozycję "hello", w obszarze dziennik okien dialogowych.
    - Spowoduje to otwarcie konwersacji.
3. Odpowiedź "hello" jest które miasta. Ale chcemy zmienić tę instrukcję coś, co sprawia, że bardziej zrozumiałe. Lepsza odpowiedź będzie coś, takich jak "Witaj, jestem bot pogody". Ale nie ma akcji wykonującej, dzięki czemu będziemy mogli ją utworzyć.
4. Kliknij akcję.
    - W odpowiedzi, wpisz "Jestem bot o pogodzie. Mogę pomóc przy użyciu prognoz. "
6. Zaznaczenie oczekiwania dla pola wyboru odpowiedzi się akcja-wait.
7. Kliknij pozycję Utwórz.
8. Kliknij, aby zaznaczyć tej nowej akcji. Następnie kliknij przycisk Save (Zapisz).
    - Dzięki temu masz dostęp możesz do tego momentu sesji szkoleniowych.
6. Kliknij opcję "które miasta?"
7. Wpisz "Boston". Kliknij dwukrotnie tagu Boston jako jednostki, jeśli jeszcze nie jest zalogowany.
8. Kliknij wynik akcji.
9. Kliknij, aby wybrać "pogody w $city jest prawdopodobnie sunny".
10. Kliknij przycisk Gotowe, nauczania.

![](../media/tutorial9_addnewaction.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wywołanie zwrotne wykrywania jednostki](./10-entity-detection-callback.md)

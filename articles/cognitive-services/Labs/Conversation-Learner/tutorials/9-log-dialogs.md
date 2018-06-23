---
title: Jak rejestrować okien dialogowych w aplikacji uczeń konwersacji - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak zalogować okien dialogowych aplikacji uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 477545c48aeca05d56fdae28ac65a8f381a482fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348693"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-application"></a>Jak rejestrować okien dialogowych w aplikacji uczeń konwersacji

Ten samouczek pokazuje, jak to zrobić przez użytkownika końcowego testowania w interfejsie uczeń konwersacji; jak są rejestrowane okien dialogowych; i jak poprawić rejestrowane okien dialogowych w celu ulepszania modelu.

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczka

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Aby przejrzeć i poprawić dokonywanych użytkownikom końcowym, okna dialogowe, można użyć w oknach dialogowych dziennika.  W szczególności można naprawić etykiety jednostki i wybór akcji tak, aby poprawić wydajność uczonego modelu i całego systemu. 

## <a name="steps"></a>Kroki

### <a name="create-the-application"></a>Tworzenie aplikacji

1. W Interfejsie użytkownika sieci Web kliknij przycisk nowej aplikacji
2. W polu Nazwa wprowadź LogDialogs. Następnie kliknij przycisk Utwórz.

### <a name="create-an-entity"></a>Tworzenie jednostki

1. Kliknij przycisk jednostek, a następnie nowej jednostki.
2. W nazwie podmiotu wprowadź miasta.
3. Kliknij pozycję Utwórz.

### <a name="create-two-actions"></a>Utwórz dwie akcje

1. Kliknij przycisk akcje, a następnie nowa akcja
2. W odpowiedzi wpisz "Który Miasto?".
3. W dyskwalifikacji jednostek wprowadź $city.
3. Kliknięcie pozycji Utwórz

Następnie należy utworzyć drugą akcję:

1. Kliknij przycisk akcje, a następnie nowej akcji.
3. W odpowiedzi wpisz "pogody w $city jest prawdopodobnie słoneczna".
4. Wymagane jednostek, wprowadź $city.
4. Kliknij pozycję Utwórz.

Masz teraz dwie akcje.

### <a name="train-the-bot"></a>Szkolenie bot

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
2. Wpisz "co to jest pogody".
3. Kliknij wynik akcji i wybierz pozycję "Który Miasto?"
2. Wprowadź "Seattle".
3. Kliknij dwukrotnie "Seattle" i wybierz miasta.
    - To oznacza je jako element miasta.
5. Kliknij przycisk wynik akcji
6. Wybierz opcję "pogody w $city jest prawdopodobnie słoneczna".
7. Kliknij przycisk Done nauczania.

Dodaj inny przykład okno dialogowe:

1. Kliknij nową akcję, a następnie okno dialogowe nowego pociągu.
2. Wpisz "nowości pogody w Seattle". Należy zauważyć, że Seattle jest oznaczone jako jednostki.
5. Kliknij przycisk wynik akcji 
6. Wybierz opcję "pogody w $city jest prawdopodobnie słoneczna".
7. Kliknij przycisk Done nauczania.

### <a name="try-the-bot-as-the-user"></a>Spróbuj bot jako użytkownik
Teraz załóżmy, możemy wdrożyć ten robot dla użytkowników.

1. Kliknij dziennik w oknach dialogowych.
2. Kliknij przycisk Nowa sesja rozmowy.
    - Przedstawia bot, jak użytkownika będzie on wystąpić w formancie rozmów w sieci web po lewej stronie interfejsu użytkownika. Można zignorować w obszarze biały znak.
3. Wpisz tekst "hello".
4. Odpowiedź BOT: "które Miasto?"
4. Typ "Boston".
5. Odpowiedź BOT: "które Miasto?"
    - To wygląda na prawo. Dlatego teraz zapisać tego okna dialogowego.
2. Kliknij przycisk Done testowania.

Ta funkcja pozwala uruchomić nową sesję:

2. Kliknij przycisk Nowa sesja rozmowy.
3. Typ "prognozy dla Boston".
4. Odpowiedź BOT: "które Miasto?"
2. Kliknij przycisk Done nauczania.

Teraz można wprowadzić korekty do okna dialogowego drugi:

1. Kliknij pozycję "prognozy dla Boston" w obszarze okna z dziennika.
    - Spowoduje to otwarcie konwersacji.
    - Jeśli kliknięciu po stronie użytkownika konwersacji (tutaj "prognozy dla Boston"), można zmienić etykiety jednostki.
    - Jeśli klikniesz przycisk na stronie system (tutaj na które Miasto), można zmienić, jakie działania jest zaznaczone.
5. Kliknij pozycję "prognozy dla Boston". 
    - Główną przyczynę tutaj jest, że Boston nie zostało oznaczone jako jednostki. Należy zmienić.
    - Kliknij dwukrotnie "Boston", a następnie wybierz miasta.
    - Kliknij przycisk Prześlij zmiany, a następnie kliknij przycisk Zapisz. Spowoduje to utworzenie okna dialogowego szkolenia na podstawie wprowadzonych zmian i upuść można w oknach dialogowych szkolenia w punkcie dokonanych zmian.
6. Wybierz opcję "pogody w $city jest prawdopodobnie słoneczna".
7. Kliknij przycisk Done nauczania. Jeśli przejdziesz do pociągu okna teraz, zobaczysz, że została dodana nowa akcja.

![](../media/tutorial9_logdiag1.PNG)

Teraz można wprowadzić korekty do okna dialogowego:

1. Kliknij na "hello" w obszarze okna z dziennika.
    - Spowoduje to otwarcie konwersacji.
3. Należy pamiętać, że odpowiedź "hello" jest które miasta. Ale chcemy się zmienić do zasobu, który lepszym rozwiązaniem. Lepsze odpowiedzi będą coś, takich jak "Witaj, jestem bot pogody". Ale nie akcji, który wykonuje który, dzięki czemu będziemy mogli go utworzyć.
4. Kliknij akcję.
    - W odpowiedzi, wpisz "Jestem bot pogody. I może ułatwić prognoz. "
6. Usuń zaznaczenie oczekiwanie na odpowiedź zaznaczenie dokonanie akcji bez oczekiwania.
7. Kliknij pozycję Utwórz.
8. Kliknij, aby zaznaczyć tej nowej akcji. Następnie kliknij przycisk Save (Zapisz).
    - Wybranie tej opcji powoduje należy do tego punktu w sesji szkolenia.
6. Kliknij opcję "który Miasto?"
7. Typ "Boston". Kliknij dwukrotnie w znaczniku Boston jako jednostka Jeśli jeszcze nie.
8. Kliknij przycisk wynik akcji.
9. Kliknij, aby wybrać "pogody w $city jest prawdopodobnie słoneczna".
10. Kliknij przycisk Done nauczania.

![](../media/tutorial9_addnewaction.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wywołanie zwrotne wykrywania jednostki](./10-entity-detection-callback.md)

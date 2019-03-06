---
title: Rozwiązujący jednostki w modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak korzystać z rozpoznawania nazw jednostki w uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ebe555bfd7b34efd87d400d786049964665c76e6
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451063"
---
# <a name="entity-resolvers"></a>Mechanizmy rozpoznawania elementów jednostki

W tym samouczku pokazano, jak używać mechanizmów rozpoznawania jednostek w uczeń konwersacji

## <a name="video"></a>Połączenia wideo

[![Jednostki rozwiązujący samouczka (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły

- Typ mechanizmu rozpoznawania jest opcjonalna właściwość jednostki niestandardowe.
- Rozwiązujący jednostki wykorzystywać możliwości wstępnie przeszkolonych jednostki aparatów rozpoznawania w LUIS, aby podać dodatkowe szczegóły i przejrzystości dla jednostki niestandardowe.

## <a name="steps"></a>Kroki

### <a name="create-a-new-model"></a>Utwórz nowy Model

1. W Interfejsie użytkownika sieci Web kliknij przycisk "Nowy Model".
2. W polu "Name" wpisz "Rozpoznawania nazw jednostki", wprowadź trafień, lub kliknij przycisk "Utwórz".

### <a name="create-a-pair-of-entities"></a>Tworzenie pary jednostek

1. W lewym panelu kliknij przycisk "Jednostki", a następnie kliknij przycisk "Nowa jednostka".
2. W polu "Nazwa jednostki" wpisz "opuszczenie firmy".
3. W menu "Typ mechanizmu rozpoznawania" rozwijanym wybierz pozycję "datetimeV2".
4. Kliknij przycisk "Utwórz".
5. Kliknij przycisk "OK", po odczycie popup informacji.
6. Stosując te same czynności należy utworzyć inny obiekt o nazwie "return", która także ma typ mechanizmu rozpoznawania "datetimeV2".

### <a name="create-a-pair-of-actions"></a>Tworzenie pary akcji

1. W lewym panelu kliknij przycisk "Akcje", a następnie kliknij przycisk "Nowa akcja".
2. W "response Botów" typ pola "pozostawienia na $departure i na $return".
    - WAŻNE - w przypadku wpisując "$[entityName]" musisz trafień wprowadź lub kliknij jednostkę na liście rozwijanej, w przeciwnym razie uczeń konwersacji uzna tego działania jako tekst, a nie jednostki.
    - Należy zauważyć, że pole "Wymagane jednostki" otrzymają również te jednostki i nie można usunąć. Ta akcja staje się dostępne, dopóki oba wymaganych jednostek znajdują się w ten sposób.
3. Kliknij przycisk "Utwórz".
4. Kliknij przycisk "Nowa akcja" ponownie w celu utworzenia drugiej akcji.
5. W "Botów odpowiedzi" pola typu "Kiedy są planowane do pokonania?".
6. "Jednostki dyskwalifikacji" typ pola, "wyjścia" i również typ "return".
    - Wie, nasze Bot nie wykonywała tę akcję w przypadku dowolnego z tych jednostek zawiera wartość.
7. Kliknij przycisk "Utwórz".


### <a name="training"></a>Szkolenia

1. Obejrzyj "szkolenia: [Status]" na u góry z lewej części strony i oczekiwaniem na jej się "ukończone".
    - Możesz kliknąć link "Odśwież", jeśli trwa to zbyt długo.
    - Szkolenie stan "Completed" to naszym rozwiązujący jednostki pracy po uczenie modelu.
2. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie kliknij przycisk "Nowy Train Dialog".
3. Typ w pierwszym wypowiedź użytkownika, "Zarezerwuj mnie lot". 
4. Kliknij przycisk "Wynik akcje".
5. Wybierz odpowiedź "Kiedy są planowane do pokonania?".
6. Jako użytkownik należy odpowiedzieć, "opuszczania jutro i zwracanie niedziela w następnym tygodniu".
    - Zwróć uwagę, jak uczeń konwersacji wykrył, że włączenie dwie jednostki "Wstępnie przeszkolonych daty" tego użytkownika.
7. W panelu "Jednostki wykrywania" "jutro" Zaznacz tekst i przypisuj jej etykiety jako "wyjścia"
8. Opisać tekst "Niedziela w następnym tygodniu" jako "Wróć"
9. Kliknij przycisk "Wynik akcje".
    - Zwróć uwagę, jak okienko "Pamięć" zawiera wyjścia i zwrócenie daty.
    - Umieść kursor nad każdym z nich i obserwuj, jak jednostki są obiekty daty, które wyraźnie przechwytywania datę w kalendarzu rzeczywiste zamiast "Niedziela" lub "jutro".
10. Wybierz pozycję "Opuszczasz na..." Bot odpowiedź.
11. Kliknij przycisk "Zapisz".

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Alternatywne danych wejściowych](./10-alternative-inputs.md)

---
title: Wprowadzenie do uczenia modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak do nauczenia modelu, w tym gałęzi i szkolenie za pośrednictwem uczeń konwersacji do edycji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 1e6dea521207ec00efe948a879c705bd28a75d57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707635"
---
# <a name="introduction-to-training"></a>Wprowadzenie do szkolenia

W tym samouczku przedstawiono podstawy uczenia modelu, rozgałęzienia, poza nowego szkolenia dotyczącego na podstawie poprzednich szkolenia i edycji odpowiedzi Bot, aby można było je zmienić.

## <a name="video"></a>Połączenia wideo

[![Wprowadzenie do szkolenia samouczka (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły

- Akcje: Bot odpowiedzi na dane wejściowe użytkownika.
- Train: Sposób możemy nauczyć Bot odpowiedzieć na dane wejściowe użytkownika.
- Rozgałęzianie: Modyfikacja dane wejściowe użytkownika poziomu zapisane okna dialogowego Train na potrzeby tworzenia nowego okna dialogowego szkolenie, który rozpoczyna się taka sama jak oryginał, ale trwa konwersacji w innym kierunku.

## <a name="steps"></a>Kroki

### <a name="create-a-new-model"></a>Utwórz nowy model

1. W Interfejsie użytkownika sieci Web kliknij przycisk Nowy Model
2. Aby uzyskać "Name" wpisz "Inspiruj Bot". Następnie kliknij przycisk Utwórz.

### <a name="create-an-action"></a>Tworzenie akcji

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "response Botów" Wprowadź "cześć! Czy chcesz inspiracja już dzisiaj? ".
    - Pozostaw inne pola i pola wyboru ich ustawienie domyślne.
3. Kliknij pozycję Utwórz.

### <a name="first-training-and-creating-another-action-while-training"></a>Najpierw szkolenia i Tworzenie kolejnej akcji podczas szkolenia

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "hello". 
    - Symuluje to użytkownika po stronie konwersacji.
3. Kliknij przycisk "Wynik akcji".
4. Wybierz odpowiedź "cześć! Czy chcesz inspiracja już dzisiaj? ".
5. Jako użytkownik odpowie, "yes".
6. Kliknij przycisk "Wynik akcji".
7. Kliknij pozycję "+ akcji" przycisk. 
    - Spowoduje to przejście do znanego okna dialogowego "Tworzenie Action".
8. Wpisz odpowiedź Botów jako "Jesteś awesome!"
9. Kliknij pozycję Utwórz.
10. Zwróć uwagę, natychmiast reaguje robota.
11. Kliknij przycisk "Zapisz".

### <a name="branch-a-second-training-off-of-the-first-training"></a>Gałąź drugi szkolenia poza pierwszą szkolenia
1. Kliknij wiersz siatki, który podsumowuje pierwszy szkolenia. 
    - Dzięki temu można wyświetlać i edytować istniejące szkolenia.
2. Kliknij pozycję "yes" odpowiedź użytkownika. 
    - Udostępni to formanty edycji.
3. Kliknij ikonę gałęzi. 
    - Pojawi się monit o podanie różnych danych wejściowych użytkownika dla nowego konwersacji.
4. Typ w "no", wprowadź trafień, lub kliknij przycisk "Utwórz". 
    - W tym momencie masz nowe wystąpienie klasy okna dialogowego szkolenie, oryginalnego pozostaje bez zmian.
5. Kliknij przycisk "Wynik akcji".
6. Polecenie Bot nieprawidłowa odpowiedź, którą właśnie.
7. Kliknij pozycję "+ akcji" przycisk 
    - dzięki czemu możemy utworzyć nową akcję dla Bot odpowiadać przy użyciu.
8. Wpisz odpowiedź Botów jako "nie ma problemu! Masz Miłego dnia!"
9. Kliknięcie pozycji Utwórz
10. Zwróć uwagę, natychmiast reaguje robota.
11. Kliknij przycisk "Zapisz".

### <a name="test-the-trainings"></a>Testowanie szkoleniach
1. W lewym panelu kliknij przycisk "Okien dialogowych dziennika", a następnie "Okno dialogowe Nowy dziennik".
2. Wpisz komunikat, "hi". 
3. Zwróć uwagę, Bot odpowiada automatycznie w taki sposób, szkoliliśmy go.
4. Wpisz "yes" w odpowiedzi użytkownika.
5. Zwróć uwagę, odpowiedź Bot pokazuje działa pierwszy szkolenia.
6. Kliknij przycisk "Limit czasu sesji". Informuje uczeń konwersacji że chcemy ponownie rozpocząć ignorowanie włącza konwersacji, które po prostu miały miejsce.
7. Wpisz komunikat, "hi". 
8. Zwróć uwagę, Bot odpowiada automatycznie w taki sposób, szkoliliśmy go.
9. Wpisz odpowiedź użytkownika "no".
10. Zwróć uwagę, odpowiedź Bot pokazuje działa drugi szkolenia.
11. Kliknij przycisk "Testowanie gotowe".

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Poczekaj chwilę i akcje-wait](./03-wait-vs-nonwait-actions.md)

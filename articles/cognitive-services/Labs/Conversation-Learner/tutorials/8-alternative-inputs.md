---
title: Jak używać alternatywnego danych wejściowych z uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak użyć alternatywnych danych wejściowych z uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f344424c372f65cd8bfe5779cc65f0e866e58a11
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633975"
---
# <a name="how-to-use-alternative-inputs"></a>Jak używać alternatywnego danych wejściowych

W tym samouczku pokazano, jak używać pola "alternatywne dane wejściowe" dla danych wejściowych użytkownika w interfejsie nauczania.

## <a name="video"></a>Połączenia wideo

[![Samouczek 8 (wersja zapoznawcza)](https://aka.ms/cl-tutorial-08-preview)](https://aka.ms/blis-tutorial-08)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły
"Alternatywne dane wejściowe" są wypowiedzi alternatywne użytkownika, które użytkownik może mieć powiedział w określonym punkcie w oknie dialogowym szkolenia. Alternatywne dane wejściowe umożliwiają bardziej bardziej kompaktowy określenie wariantów co użytkownik może powiedzieć, bez konieczności listy poszczególnych odmian w oknie dialogowym oddzielne szkolenia.

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W Interfejsie użytkownika sieci Web kliknij przycisk Nowy Model
2. W polu Nazwa wprowadź AlternativeInputs. Następnie kliknij przycisk Utwórz.

### <a name="create-an-entity"></a>Tworzenie jednostki

1. Kliknij przycisk jednostki, a następnie nowej jednostki.
2. W nazwie podmiotu Wprowadź miasto.
3. Kliknij pozycję Utwórz.

### <a name="create-three-actions"></a>Utwórz trzy czynności

1. Kliknij pozycję operacje, a następnie nowa akcja
2. W odpowiedzi wpisz "Miasto, której chcesz użyć?".
3. W jednostkach dyskwalifikacji wprowadź $city.
3. Kliknięcie pozycji Utwórz

Następnie należy utworzyć drugą akcję:

1. Kliknij pozycję operacje, a następnie nową akcję.
3. W odpowiedzi wpisz "pogody w $city jest prawdopodobnie sunny".
4. Wymagane jednostki, wprowadź $city.
4. Kliknij pozycję Utwórz.

Utwórz akcję trzeci:

1. Kliknij pozycję operacje, a następnie nową akcję.
3. W odpowiedzi wpisz "Try z prośbą o pogody".
    - Takie rozwiązanie byłoby w odpowiedzi na pytanie użytkownika takich jak "co system zrobić?"
4. W jednostkach dyskwalifikacji wprowadź $city.
4. Kliknięcie pozycji Utwórz

Masz teraz trzy czynności.

### <a name="train-the-bot"></a>Uczenie bota

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
2. Wpisz "co to jest pogody".
3. Kliknij wynik akcji, a następnie wybierz pozycję "Miasto, której chcesz użyć?"
2. Wprowadź "denver".
3. Kliknij dwukrotnie pozycję "denver", a następnie wybierz miasto.
    - To oznacza je jako jednostkę miasta.
5. Kliknij wynik akcji
    - "denver" znajduje się teraz w jednostce miasta. 
6. Wybierz pozycję "pogody w $city jest prawdopodobnie sunny".
7. Kliknij przycisk Gotowe, nauczania.

Dodaj inny przykład okno dialogowe:

1. Kliknij nową akcję, a następnie okno dialogowe Nowy pociągu.
2. Wpisz "co można zrobić?".
3. Kliknij wynik akcji, a następnie wybierz pozycję "Spróbuj skorzystać z pogody"
2. Wprowadź "Co to jest pogody w seattle".
3. Kliknij dwukrotnie ikonę na "seattle", a następnie wybierz miasto.
    - To oznacza je jako jednostkę miasta.
5. Kliknij wynik akcji
    - "seattle" znajduje się teraz w jednostce miasta. 
6. Wybierz pozycję "pogody w $city jest prawdopodobnie sunny".
7. Kliknij przycisk Gotowe, nauczania.

Zobaczmy, co się stanie, jeśli użytkownik odpowie coś semantycznie podobne do powyżej:

1. Kliknij nową akcję, a następnie okno dialogowe Nowy pociągu.
2. Wpisz "pomoc".
3. Kliknij wynik akcji.
    - Wyniki dla dwóch potencjalne odpowiedzi są bardzo Zamknij. To informuje NAS, czy model jest pomylić o granic między dwoma działaniami.
6. Kliknij przycisk Abandon nauczania i potwierdź.

![](../media/tutorial8_closescores.png)

W takim przypadku może pomóc Dodaj alternatywne dane wejściowe do okien dialogowych. Możesz dodać je jako robią nauczanie. Można także wrócić i je dodać później.

2. Kliknij pozycję "Co można zrobić?" w oknach dialogowych pociągu.
2. W oknie dialogowym kliknij pozycję "co można zrobić?" Aby go zaznaczyć.
    1. W prawym okienku w obszarze wykrywania jednostki w danych wejściowych alternatywnych Dodaj wprowadź kilka alternatyw:
    1. Wprowadź "jakie są moje opcje?"
    2. Wprowadź "Powiedz mi, Moje opcje".
    3. Wprowadź "help"
    1. Kliknij przycisk Prześlij zmiany.


![](../media/tutorial8_helpalternates.png)

2. Teraz kliknij "co to jest pogody w seattle".
    1. Dodaj alternatywne danych wejściowych wprowadź "prognozy dla seattle".
    2. Kliknij dwukrotnie ikonę na "seattle", a następnie wybierz miasto. Jednostki dla danych wejściowych alternatywnych powinien być obecny i ma ten sam zestaw jednostek. Jest dobrym rozwiązaniem, jeśli różni się zawartość jednostki.
    3. Dodaj alternatywne danych wejściowych wprowadź "będzie on deszczowa już dziś w Warszawie".
    4. Kliknij pozycję "denver", a następnie wybierz miasto.
    5. Kliknij przycisk Prześlij zmiany i gotowe.


Dodajmy alternatywne dane wejściowe do okna dialogowego pierwszego:

1. Kliknij przycisk szkolenie w oknach dialogowych.
2. Kliknij w oknie dialogowym, rozpoczynając od "co to jest pogody".
2. Kliknij, aby w okienku po lewej stronie wybierz "co to jest pogody":
    1. Dodaj alternatywne danych wejściowych wprowadź "prognozę pogody".
    2. Wprowadź "będzie on deszczowa?"
    3. Kliknij przycisk Prześlij zmiany.
4. Kliknij, aby w okienku po lewej stronie wybierz pozycję "denver":
    1. Dodaj alternatywne danych wejściowych wprowadź "denver".
    1. Wprowadź "Prognozowanie austin".
        - Pojawi się komunikat o błędzie: "Odmiany tekstu musi zawierać takie same jednostki wykryte jako oryginalny tekst wejściowy".
        - Kliknij wyraz "austin".
        - Pojawi się okno podręczne, umożliwiające wybranie jednostki dla "austin". Kliknij przycisk **Miasto**.
        - Kliknij przycisk Prześlij zmiany.
    1. Kliknij przycisk Gotowe, co spowoduje modelu doskonalenie.

![](../media/tutorial8_altcities.png)

Wypróbujmy zmiany:

1. Kliknij okno dialogowe Nowy pociągu.
2. Wpisz "co to są możesz możliwości".
3. Kliknij wynik akcji.
    - Wyniki są teraz bardziej decydujące o tym następnej akcji, który wskazuje pewności modelu.
2. Wybierz pozycję "Try z prośbą o pogodzie".
6. Kliknij przycisk Gotowe, nauczania

Masz teraz alternatywny sposób wyświetlenia w danych wejściowych może służyć do wskazania innych rzeczy, które użytkownik może mieć mówi. Ułatwiają one należy unikać tworzenia wiele okien dialogowych, które na wiele sposobów są takie same, przez rozwijanie i zwijanie je w jednym oknie dialogowym wyliczanie co powiedzieć użytkownika.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Okna dialogowe dziennika](./9-log-dialogs.md)

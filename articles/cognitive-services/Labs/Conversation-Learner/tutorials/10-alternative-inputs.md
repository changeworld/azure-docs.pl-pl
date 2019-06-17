---
title: Jak używać alternatywnego danych wejściowych z uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak użyć alternatywnych danych wejściowych z uczeń konwersacji.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 383c063e1ba7a29986e4b1c48024072799234414
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387944"
---
# <a name="how-to-use-alternative-inputs"></a>Jak używać alternatywnego danych wejściowych

W tym samouczku przedstawiono sposób użycia pola alternatywnej dane wejściowe dla wypowiedzi użytkowników w interfejsie nauczania.

## <a name="video"></a>Połączenia wideo

[![Samouczek alternatywnych danych wejściowych (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Alternatywne dane wejściowe są wypowiedzi alternatywne, semantycznie równoważne użytkownika, które użytkownik może mieć powiedział w określonym punkcie w oknie dialogowym szkolenia. Te alternatywne dane wejściowe umożliwiają bardziej bardziej kompaktowy określenie odmiany wypowiedzi bez konieczności adresów poszczególnych odmian w oddzielnych szkolenia w oknach dialogowych.

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W Interfejsie użytkownika sieci Web kliknij przycisk "Nowy Model".
2. W polu "Nazwa" wpisz "AlternativeInputs" i naciśnij klawisz enter.
3. Kliknij przycisk "Utwórz".

### <a name="entity-creation"></a>Tworzenie jednostki

1. Na lewym panelu kliknij przycisk "Jednostki", a następnie przycisku "Nowa jednostka".
2. Wybierz pozycję "Uczony niestandardowe" dla "Typu jednostek".
3. Wpisz "city", "Nazwa jednostki".
4. Kliknij przycisk "Utwórz".

![](../media/T10_actions.png)

Teraz Utwórzmy trzy czynności.

### <a name="create-the-first-action"></a>Tworzenie pierwszej akcji

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi..." pola, wpisz "Które city"?
3. W polu "Oczekiwano jednostki w odpowiedzi użytkownik..." wpisz "Miasto".
4. W polu "Dyskwalifikacji uprawnia" wpisz "Miasto".
5. Kliknij przycisk "Utwórz".

![](../media/T10_action_create_1.png)

### <a name="create-the-second-action"></a>Tworzenie drugiej akcji

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi..." pola, wpisz "pogody w $city jest prawdopodobnie sunny".
3. Kliknij przycisk "Utwórz".

![](../media/T10_action_create_2.png)

### <a name="create-the-third-action"></a>Utwórz akcję trzeci

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi..." pola, wpisz "Spróbuj skorzystać z pogody."
3. W polu "Dyskwalifikacji uprawnia" wpisz "Miasto".
4. Kliknij przycisk "Utwórz".

![](../media/T10_action_create_3.png)

Masz teraz trzy czynności.

![](../media/T10_actions.png)

### <a name="train-the-model"></a>Uczenie modelu

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Co to jest pogody?"
3. Kliknij przycisk "Wynik akcje".
4. Wybierz odpowiedź "Które city"?
5. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Denver"
6. Kliknij przycisk "Wynik akcje".
7. Wybierz odpowiedź "pogody w Warszawie jest prawdopodobnie sunny".
8. Kliknij przycisk "Prześlij zmiany".

![](../media/T10_training_1.png)

Spróbujmy trenowania więcej przez utworzenie innego train okna dialogowego.

### <a name="second-model-train-dialog"></a>Drugie okno dialogowe Trenowanie modelu

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Co można zrobić?"
3. Kliknij przycisk "Wynik akcje".
4. Wybierz odpowiedź, "Try z prośbą o pogodzie."
5. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Co to jest pogody w Seattle?"
6. Kliknij "Seattle", a następnie kliknij przycisk "city" z listy jednostek.
7. Kliknij przycisk "Wynik akcje".
8. Wybierz odpowiedź "pogody w Seattle jest prawdopodobnie sunny".
9. Kliknij przycisk "Prześlij zmiany".

![](../media/T10_training_2.png)

### <a name="third-model-train-dialog-using-alternative-input"></a>Trzeci modelu Train okna dialogowego, używając alternatywnego danych wejściowych

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Wpisz wiadomość...", wpisz "help"
3. Kliknij przycisk "Wynik akcje".
    - Model jest pewności co do najlepszym rozwiązaniem, więc go wybrać najwyższy percentyl domyślnie.
4. Kliknij przycisk "Nauczania Porzuć", a następnie przycisk "Potwierdź".

![](../media/T10_training_3.png)

Teraz lepiej dostosować systemu przy użyciu alternatywnych danych wejściowych. Można dodać alternatywne dane wejściowe, podczas dydaktycznych lub nowszej.

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie wybierz "Co można zrobić?" z listy okien dialogowych pociągu.
1. Kliknij przycisk "co można zrobić?" Wypowiedź w panelu rozmowy.
1. W "Dodaj alternatywne dane wejściowe..." pole, typ "pomoc" i naciśnij klawisz należy wprowadzić.
1. Kliknij przycisk "Zapisz zmiany".

![](../media/T10_training_4.png)

Dodajmy inną alternatywnych dane wejściowe do obsługi Houston.

1. Kliknij przycisk "co to jest pogody w Seattle?" Wypowiedź w panelu rozmowy.
1. W polu "Dodaj alternatywne dane wejściowe..." typu "prognozy Houston", a następnie naciśnij klawisz enter.
   - Najważniejsze wiadomości błąd danych wejściowych alternatywnych fakt musi być semantycznie równoważne i zawierać tej samej jednostki jako oryginalnego wypowiedź; nie tylko tych samych wartości jednostki. Wymagana jest obecność tych samych jednostek.
1. Kliknij pozycję "Houston" i wybierz "city" z listy jednostek.
1. W polu "Dodaj alternatywne dane wejściowe..." typu "prognozy Seattle" i naciśnij klawisz enter.
1. Kliknij na "Seattle" i wybierz "city" z listy jednostek.
1. Kliknij przycisk "Zapisz zmiany".
1. Kliknij przycisk "Edit Zapisz".

![](../media/T10_training_5.png)

### <a name="testing-the-model"></a>Testowanie modelu

1. W lewym panelu kliknij przycisk "Okien dialogowych dziennika", a następnie "Nowe Dialog dziennika."
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Pomóż mi"
3. W panelu rozmowy, w której wyświetlany jest tekst "Wpisz wiadomość...", wpisz "forecast dla Denver"

![](../media/T10_logdialog.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Okna dialogowe dziennika](./11-log-dialogs.md)

---
title: Jak używać alternatywnego danych wejściowych z uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak użyć alternatywnych danych wejściowych z uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c60dc2ca93547b93ce2ee457393570479069c899
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216272"
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

Teraz Utwórzmy trzy czynności.

### <a name="create-the-first-action"></a>Tworzenie pierwszej akcji

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi..." pola, wpisz "Które city"?
3. W polu "Oczekiwano jednostki w odpowiedzi użytkownik..." wpisz "Miasto".
4. W polu "Dyskwalifikacji uprawnia" wpisz "Miasto".
5. Kliknij przycisk "Utwórz".

### <a name="create-the-second-action"></a>Tworzenie drugiej akcji

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi..." pola, wpisz "pogody w $city jest prawdopodobnie sunny".
3. Kliknij przycisk "Utwórz".

### <a name="create-the-third-action"></a>Utwórz akcję trzeci

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi..." pola, wpisz "Spróbuj skorzystać z pogody."
3. W polu "Dyskwalifikacji uprawnia" wpisz "Miasto".
4. Kliknij przycisk "Utwórz".

Masz teraz trzy czynności.

### <a name="train-the-model"></a>Uczenie modelu

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Co to jest pogody?"
3. Kliknij przycisk "Wynik akcje".
4. Wybierz odpowiedź "Które city"?
5. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Denver"
6. Kliknij przycisk "Wynik akcje".
7. Wybierz odpowiedź "pogody w Warszawie jest prawdopodobnie sunny".
8. Kliknij przycisk "Zapisz".

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
9. Kliknij przycisk "Zapisz".

### <a name="third-model-train-dialog-using-alternative-input"></a>Trzeci modelu Train okna dialogowego, używając alternatywnego danych wejściowych

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Wpisz wiadomość...", wpisz "help"
3. Kliknij przycisk "Wynik akcje".
    - Model jest pewności co do najlepszym rozwiązaniem, więc go wybrać najwyższy percentyl domyślnie.
4. Kliknij przycisk "Nauczania Porzuć", a następnie przycisk "Potwierdź".

![](../media/tutorial8_closescores.png)

Teraz lepiej dostosować systemu przy użyciu alternatywnych danych wejściowych. Można dodać alternatywne dane wejściowe, podczas dydaktycznych lub nowszej.

5. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie wybierz "Co można zrobić?" z listy okien dialogowych pociągu.
6. Kliknij przycisk "co można zrobić?" Wypowiedź w panelu rozmowy.
7. W "Dodaj alternatywne dane wejściowe..." pole, typ "pomoc" i naciśnij klawisz należy wprowadzić.
8. Kliknij przycisk "Zapisz zmiany".

![](../media/tutorial8_helpalternates.png)

Dodajmy inną alternatywnych dane wejściowe do obsługi Houston.

9. Kliknij przycisk "co to jest pogody w Seattle?" Wypowiedź w panelu rozmowy.
10. W polu "Dodaj alternatywne dane wejściowe..." typu "prognozy Houston", a następnie naciśnij klawisz enter.
    - Najważniejsze wiadomości błąd danych wejściowych alternatywnych fakt musi być semantycznie równoważne i zawierać tej samej jednostki jako oryginalnego wypowiedź; nie tylko tych samych wartości jednostki. Wymagana jest obecność tych samych jednostek.
11. Kliknij pozycję "Houston" i wybierz "city" z listy jednostek.
12. W polu "Dodaj alternatywne dane wejściowe..." typu "prognozy Seattle" i naciśnij klawisz enter.
13. Kliknij na "Seattle" i wybierz "city" z listy jednostek.
14. Kliknij przycisk "Zapisz zmiany".
15. Kliknij przycisk "Edit Zapisz".

### <a name="testing-the-model"></a>Testowanie modelu

1. W lewym panelu kliknij przycisk "Okien dialogowych dziennika", a następnie "Nowe Dialog dziennika."
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Pomóż mi"
3. W panelu rozmowy, w której wyświetlany jest tekst "Wpisz wiadomość...", wpisz "forecast dla Denver"

![](../media/tutorial8_altcities.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Okna dialogowe dziennika](./11-log-dialogs.md)

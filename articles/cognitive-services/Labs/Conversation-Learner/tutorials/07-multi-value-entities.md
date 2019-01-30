---
title: Jak używać wielowartościowych jednostek z modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak wielowartościowych jednostek za pomocą modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8544d63f38f88a0e623dff343bf8b5133931b70b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228308"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Jak używać wielowartościowych jednostek z modelu uczeń konwersacji
Ten samouczek pokazuje właściwości wielu wartości jednostki.

## <a name="video"></a>Połączenia wideo

[![Wiele wartości jednostki samouczka (wersja zapoznawcza)](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Jednostki wielowartościowych wzrosnąć wartości listy, zamiast magazynować pojedynczej wartości.  Te jednostki są przydatne, gdy użytkownicy mogą określić więcej niż jedną wartość. Toppings na głosi, na przykład.

Jednostki oznaczone jako wielowartościowy odniesie każdego rozpoznane wystąpienia jednostki dołączana do listy w pamięci Botów. Rozpoznawanie kolejnych dołącza do jednostki wartości, zamiast zastępowania.

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W Interfejsie użytkownika sieci Web kliknij przycisk "Nowy Model".
2. W polu "Nazwa" wpisz "MultiValueEntities" i naciśnij klawisz enter.
3. Kliknij przycisk "Utwórz".

### <a name="entity-creation"></a>Tworzenie jednostki

1. Na lewym panelu kliknij przycisk "Jednostki", a następnie przycisku "Nowa jednostka".
2. Wybierz pozycję "Uczony niestandardowe" dla "Typu jednostek".
3. Wpisz "toppings", "Nazwa jednostki".
4. Sprawdź "Wielowartościowe" pole wyboru.
    - Wielowartościowy jednostki są gromadzone co najmniej jednej wartości w jednostce.
5. Zaznacz to pole wyboru "Negatable".
    - Właściwość "Negatable" zostało omówione w innym samouczku.
6. Kliknij przycisk "Utwórz".

![](../media/tutorial6_entities.PNG)

### <a name="create-the-first-action"></a>Tworzenie pierwszej akcji

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi...", wpisz "Oto Twoja toppings: $toppings"
    - Wiodący znak dolara wskazuje odwołanie do jednostki
3. Kliknij przycisk "Utwórz".

### <a name="create-the-second-action"></a>Tworzenie drugiej akcji

1. Na lewym panelu kliknij przycisk "Akcje", a następnie przycisku "Nowa akcja".
2. W "Botów odpowiedzi..." pola, wpisz "jakie toppings chcesz użyć?"
3. W polu "Dyskwalifikacji uprawnia" wpisz "toppings."
4. Kliknij przycisk "Utwórz".

Masz teraz dwie akcje.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-model"></a>Uczenie modelu

1. W lewym panelu kliknij przycisk "Okien dialogowych Train", a następnie przycisk "Nowy Train Dialog".
2. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "hi."
3. Kliknij przycisk "Wynik akcje".
4. Wybierz odpowiedź "jakie toppings czy?"
    - Percentyla wynosi 100%, zgodnie z jedynymi prawidłowymi akcji na podstawie ograniczeń.
5. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "ser i grzyby"
6. Kliknij przycisk "ser", a następnie wybierz etykietę "+ toppings"
7. Kliknij przycisk "grzyby", a następnie wybierz etykietę "+ toppings"
8. Kliknij przycisk "Wynik akcje".
9. Wybierz odpowiedź, "Oto Twoja toppings: $toppings"
10. W panelu rozmowy, w której wyświetlany jest tekst "Typ komunikatu...", wpisz "Dodaj chili"
11. Kliknij przycisk "chili", a następnie wybierz etykietę "+ toppings"
12. Kliknij przycisk "Wynik akcje".
13. Wybierz odpowiedź, "Oto Twoja toppings: $toppings"
14. W panelu rozmowy, w której wyświetlany jest tekst "Wpisz wiadomość...", wpisz "Usuń ser"
15. Kliknij przycisk "ser", a następnie wybierz etykietę "-toppings"
16. Kliknij przycisk "Wynik akcje".
17. Wybierz odpowiedź, "Oto Twoja toppings: $toppings"

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wstępnie przeszkolonych jednostek](./08-pre-trained-entities.md)

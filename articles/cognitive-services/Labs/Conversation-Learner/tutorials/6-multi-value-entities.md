---
title: Jak używać wielowartościowych jednostek z modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak wielowartościowych jednostek za pomocą modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a80577bb226cbec080edf5e06dbd0f31c80a5890
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321431"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Jak używać wielowartościowych jednostek z modelu uczeń konwersacji
W tym samouczku przedstawiono właściwości "value wielu", jednostek.

## <a name="video"></a>Połączenia wideo

[![Samouczek 6 (wersja zapoznawcza)](http://aka.ms/cl-tutorial-06-preview)](http://aka.ms/blis-tutorial-06)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Jednostką znajdującą się "wielowartościowych" gromadzi wartości listy, zamiast magazynować pojedynczej wartości.  Jest to przydatne w przypadku jednostek, gdzie użytkownik może określić więcej niż jedną wartość, takich jak toppings na pizza.

Konkretnie Jeśli jednostka jest oznaczony jako "wielowartościowych", następnie każdy rozpoznane wystąpienia jednostki zostaną dołączone do listy w botów pamięci (zamiast zastępowania wartości pojedynczej jednostki).

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W Interfejsie użytkownika sieci Web kliknij przycisk Nowy Model
2. W polu Nazwa wprowadź MultiValueEntities. Następnie kliknij przycisk Utwórz.

### <a name="create-an-entity"></a>Tworzenie jednostki

1. Kliknij przycisk jednostki, a następnie nowej jednostki.
2. W nazwie podmiotu wprowadź Toppings.
3. Sprawdź wielowartościowych.
    - Wielowartościowy jednostki są gromadzone co najmniej jednej wartości w jednostce.
2. Można negować wyboru.  
    - Umożliwi to użytkownikowi usunięcie toppings z ich listy toppings pizza skumulowana.
3. Kliknij pozycję Utwórz.

![](../media/tutorial6_entities.PNG)

### <a name="create-two-actions"></a>Utwórz dwie akcje

1. Kliknij pozycję operacje, a następnie nowa akcja
2. W odpowiedzi wpisz "jakie toppings chcesz użyć?".
3. W jednostkach dyskwalifikacji wprowadź Toppings.
3. Kliknięcie pozycji Utwórz

Następnie utwórz drugiej akcji.

1. Kliknij pozycję operacje, a następnie nową akcję do utworzenia drugiej akcji.
3. W odpowiedzi, wpisz "Oto Twoja toppings: $Toppings".
4. Kliknięcie pozycji Utwórz

Masz teraz dwie akcje.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-bot"></a>Uczenie bota

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
2. Wpisz "hello".
3. Kliknij wynik akcji, a następnie wybierz pozycję "jakie toppings chcesz użyć?"
2. Wprowadź "grzyby oraz miejscowych serów". 
    - Możesz oznaczyć zero, jeden lub więcej niż jednej jednostki.
3. Kliknij przycisk "grzyby", a następnie wybierz Toppings.
4. Kliknij przycisk "ser", a następnie wybierz Toppings.
5. Kliknij wynik akcji
    - Te dwie wartości są teraz obecne w jednostce Toppings. 
6. Wybierz pozycję "Oto Twoja toppings: $Toppings".

Możemy dodać więcej tak:

7. Wprowadź "Dodaj ogrodnictwu".
    - Kliknij pozycję "peppers, w obszarze wykrywania jednostki, a następnie wybierz Toppings.
3. Kliknij wynik akcji.
    - "ogrodnictwu" teraz wyświetlane jako dodatkowe wartości w Toppings.
6. Wybierz pozycję "Oto Twoja toppings: $Toppings".

Umożliwia usunięcie nadmiarów i Dodaj jeden:

2. Wpisz "Usuń ogrodnictwu i Dodaj produkcji kiełbas".
1. Kliknij pozycję "ogrodnictwu" i kliknij czerwony znak x, usuń go.
2. Kliknij pozycję "ogrodnictwu", a następnie wybierz pozycję "-Toppings.
3. Kliknij wynik akcji.
    - "ogrodnictwu" został usunięty i dodano produkcji "kiełbas".
6. Wybierz pozycję "Oto Twoja toppings: $Toppings".

Teraz Wypróbujmy usunięcie wszystkiego:

6. Wprowadź "Usuń grzyby, Usuń ser i Usuń produkcji kiełbas".
7. Kliknij każdy z trzech, a następnie wybierz pozycję "-Toppings.
7. Kliknij wynik akcji.
    - Wszystkie toppings zostaną wyczyszczone.
2. Wybierz pozycję "jakie toppings chcesz użyć?"
3. Kliknij przycisk Gotowe, nauczania

![](../media/tutorial6_dialogs.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wbudowanych jednostek](./7-built-in-entities.md)

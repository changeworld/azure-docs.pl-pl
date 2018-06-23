---
title: Jak używać jednostek można negować z aplikacją uczeń konwersacji - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak można negować jednostek za pomocą aplikacji uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3d65376c9c43ee1407468f3e8bf3e058048bd556
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348661"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-application"></a>Jak używać jednostek można negować z aplikacją uczeń konwersacji

Ten samouczek przedstawia "można negować" właściwości jednostek.

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczka

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Oznacz akcji jako "można negować", jeśli użytkownika można "Wyczyść" wartości jednostki, jak "Nie, nie chcę $entity" lub "nie, nie $entity." Na przykład "nie, nie chcę opuścić Boston."

Konkretnie Jeśli ustawiono właściwość "można negować" podmiotu:

- Podczas etykietowania uwagi jednostki, pozwala na etykiecie normalnego wystąpienia jednostki (dodatnia) i "ujemny" wystąpienia jednostki
- LUIS uzyskuje informacje o dwa modele jednostki: jeden dla wystąpień dodatnią i drugi dla wystąpień ujemna
- Powoduje ujemna wystąpienie jednostki można wyczyścić tej wartości od jednostki zmiennej (jeśli istnieje)

## <a name="steps"></a>Kroki

### <a name="create-the-application"></a>Tworzenie aplikacji

1. W Interfejsie użytkownika sieci Web kliknij przycisk nowej aplikacji
2. W polu Nazwa wprowadź NegatableEntity. Następnie kliknij przycisk Utwórz.

### <a name="create-an-entity"></a>Tworzenie jednostki

1. Kliknij przycisk jednostek, a następnie nowej jednostki.
2. W nazwie podmiotu wprowadź nazwę.
3. Można negować wyboru.
    - Oznacza to, użytkownik będzie mógł podać wartość dla jednostki lub że dany element jest *nie* wartości jednostki. W drugim przypadku spowoduje usunięcie pasującej wartości jednostki.
3. Kliknij pozycję Utwórz.

![](../media/tutorial5_entities.PNG)

### <a name="create-two-actions"></a>Utwórz dwie akcje

1. Kliknij przycisk akcje, a następnie nowa akcja
2. W odpowiedzi wpisz "Nie wiadomo nazwę".
3. W dyskwalifikacji jednostek wprowadź nazwę.
3. Kliknięcie pozycji Utwórz

Następnie należy utworzyć drugą akcję.

1. Kliknij przycisk akcje, a następnie nową akcję w celu utworzenia drugiej akcji.
3. W odpowiedzi, wpisz "sprawdzić nazwę. Jest $name ".
4. Kliknięcie pozycji Utwórz

Teraz masz dwie akcje.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-bot"></a>Szkolenie bot

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
2. Wpisz tekst "hello".
3. Kliknij wynik akcji, a następnie wybierz opcję "Nie wiadomo nazwę"
    - Należy zauważyć, że będzie niedługo wynik 100%, ponieważ jest on tylko prawidłowe działanie.
2. Wprowadź "mojej nazwy jest Dominik"
3. Wybierz opcję "Dominik" i wybierz etykietę "+ Nazwa"
    - Zwróć uwagę, istnieją dwa wystąpienia 'name': "+ Nazwa" i "-name".  Dodatkowo oznacza, że zapewniamy tej wartości. Minus — oznacza, że firma Microsoft informuje system, że element nie jest wartością.
5. Kliknij przycisk wynik akcji
    - Uwaga nazwy wartości jest teraz bot pamięci.
    - "Sprawdzić nazwę. Jest $name "jest dostępne tylko w odpowiedzi. 
6. Wybierz opcję "sprawdzić nazwę. Jest $name ".

Spróbujmy wyczyszczenie jednostki można negować:

7. Wprowadź "Mój nazwa nie jest Dominik".
    - Powiadomienie 'not' został wybrany jako nazwa oparta na użyciu wcześniejszego wzorca. Który jest nieprawidłowy.
2. Kliknij przycisk "nie", a następnie czerwony znak x. 
3. Kliknij pozycję "Dominik".
    - Teraz jest podmiot ujemna komunikacji, że nie jest to wartość nazwy podmiotu.
2. Wybierz opcję "-name".
3. Kliknij przycisk wynik akcji.
    - Należy zauważyć, że wartość zostało wyczyszczone z pamięci.
2. Wybierz "I nie jest znana nazwa", która jest jedyną akcją.

Następnie zostanie przedstawiony wprowadzania nową wartość dla nazwy.

3. Wprowadź "Jan" jako nazwy. Wybierz 'john' i kliknij nazwę.
4. Kliknij przycisk wynik akcji.
5. Wybierz opcję "sprawdzić nazwę. Jest $name ".

Teraz spróbuj, zastępując podana nazwa.

6. Wprowadź "mojej nazwy jest susan".
7. Wybierz opcję "sprawdzić nazwę. Jest $name ".
7. Kliknij przycisk wynik akcji.
8. Powiadomienie **susan** zastąpiły **Jan** w wartości jednostki.
9. Wprowadź "Mój nazwa nie jest susan".
    - Należy zauważyć, że system ma oznaczenie to ujemna wystąpienia.
2. Kliknij przycisk wynik akcji.
3. Wybierz "I nie jest znana nazwa", która jest jedyną akcją.
7. Kliknij przycisk Done nauczania.

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wielowartościowych jednostek](./6-multi-value-entities.md)

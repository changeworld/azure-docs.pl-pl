---
title: Jak można negować jednostek za pomocą modelu uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak można negować jednostek za pomocą modelu uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2fd00d53755e44e3a3d86782c40aa6a53ff4d378
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171405"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Jak można negować jednostek za pomocą modelu uczeń konwersacji

Ten samouczek przedstawia "można negować" właściwość jednostki.

## <a name="video"></a>Połączenia wideo

[![Samouczek 5 (wersja zapoznawcza)](http://aka.ms/cl-tutorial-05-preview)](http://aka.ms/blis-tutorial-05)

## <a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczek

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Oznacz akcję jako "można negować", jeśli użytkownik może "Wyczyść" wartości jednostki, tak jak "Nie, nie chcę $entity" lub "nie ma nie $entity." Na przykład "nie, nie chcę zrezygnować z poziomu Boston."

Konkretnie Jeśli ustawiono właściwość "można negować" jednostki:

- Podczas etykietowania wzmianki jednostki, pozwalają na etykiety normalny (pozytywna) wystąpień jednostki i "ujemny" wystąpień jednostki
- Usługa LUIS uczy się dwa modele jednostki: jeden dla pozytywnych wystąpień i sekundę ujemna wystąpień
- Ujemna wystąpienie jednostki powoduje, aby wyczyścić tę wartość ze zmiennej jednostki (jeśli istnieje)

## <a name="steps"></a>Kroki

### <a name="create-the-model"></a>Tworzenie modelu

1. W Interfejsie użytkownika sieci Web kliknij przycisk Nowy Model
2. W polu Nazwa wprowadź NegatableEntity. Następnie kliknij przycisk Utwórz.

### <a name="create-an-entity"></a>Tworzenie jednostki

1. Kliknij przycisk jednostki, a następnie nowej jednostki.
2. W nazwie podmiotu wprowadź nazwę.
3. Można negować wyboru.
    - Ta właściwość wskazuje, użytkownik będzie mógł dostarczyć wartości dla jednostki lub Załóżmy, że coś jest nie *nie* wartość jednostki. W tym ostatnim przypadku spowoduje usunięcie o pasującej wartości jednostki.
3. Kliknij pozycję Utwórz.

![](../media/tutorial5_entities.PNG)

### <a name="create-two-actions"></a>Utwórz dwie akcje

1. Kliknij pozycję operacje, a następnie nowa akcja
2. W odpowiedzi wpisz "Nie wiem, Twoja nazwa".
3. W jednostkach dyskwalifikacji wprowadź nazwę.
3. Kliknięcie pozycji Utwórz

Następnie utwórz drugiej akcji.

1. Kliknij pozycję operacje, a następnie nową akcję do utworzenia drugiej akcji.
3. W odpowiedzi, wpisz "wiem, swoją nazwę. Jest $name ".
4. Kliknięcie pozycji Utwórz

Masz teraz dwie akcje.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-bot"></a>Uczenie bota

1. Kliknij przycisk okien dialogowych szkolenie, polecenie nowe okno pociągu.
2. Wpisz "hello".
3. Kliknij wynik akcji, a następnie wybierz pozycję "Nie wiem, swoją nazwę"
    - Wynik to 100%, ponieważ jest to jedyne prawidłowe Akcja.
2. Wprowadź "Moja nazwa jest david"
3. Wybierz pozycję "david" i wybierz etykietę "+ Nazwa"
    - Istnieją dwa wystąpienia atrybutu "name": "+ Nazwa" i "-name".  (+) A także dodaje lub zastępuje wartość. (-) Znak minus Usuwa wartość.
5. Kliknij wynik akcji
    - Wartość nazwy jest teraz pamięci botów.
    - "Wiem, swoją nazwę. Jest $name "jest jedyną dostępną odpowiedzi. 
6. Wybierz pozycję "wiem, swoją nazwę. Jest $name ".

Wypróbujmy wyczyszczenie można negować jednostki:

7. Wprowadź "Moja nazwa nie david".
    - Zwróć uwagę wybrano "not" jako nazwę opartą na poprzedni wzorzec. Ta etykieta jest nieprawidłowa.
2. Kliknij pozycję "not", a następnie czerwony znak x. 
3. Kliknij pozycję "david".
    - Teraz jest jednostką ujemna podczas komunikowania się, że nie jest to wartość jednostki nazwa.
2. Wybierz pozycję "-name".
3. Kliknij wynik akcji.
    - Zwróć uwagę, że wartość został wyczyszczony z pamięci.
2. Wybierz "Nie wiem, swoją nazwę", który jest jedyną akcją.

Następnie samouczków pokazano, jak można wprowadzić nową wartość dla nazwy.

3. Wprowadź nazwę "Jan". Następnie wybierz pozycję "Jan" i kliknij nazwę.
4. Kliknij wynik akcji.
5. Wybierz pozycję "wiem, swoją nazwę. Jest $name ".

Teraz spróbuj, zastępując podana nazwa.

6. Wprowadź "Moja nazwa jest susan".
7. Wybierz pozycję "wiem, swoją nazwę. Jest $name ".
7. Kliknij wynik akcji.
8. Zwróć uwagę **susan** zastąpiły **john** w wartości jednostki.
9. Wprowadź "Moja nazwa nie susan".
    - Zwróć uwagę, że system ma oznaczeniem to wystąpienie ujemna.
2. Kliknij wynik akcji.
3. Wybierz "Nie wiem, swoją nazwę", który jest jedyną akcją.
7. Kliknij przycisk Gotowe, nauczania.

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wartość wielu jednostek](./6-multi-value-entities.md)

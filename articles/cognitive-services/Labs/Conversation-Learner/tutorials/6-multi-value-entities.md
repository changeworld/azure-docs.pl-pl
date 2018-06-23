---
title: Jak używać jednostek wielowartościowych z aplikacją uczeń konwersacji - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak użyć wielowartościowych jednostek z aplikacją uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 865b50747b2c9574b5f88d4902bea9e4c8e0e032
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348624"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-application"></a>Jak używać jednostek wielowartościowych z aplikacją uczeń konwersacji
W tym samouczku przedstawiono właściwości "wielowartościowych" jednostek.

##<a name="requirements"></a>Wymagania
Ten samouczek wymaga działa ogólne bot samouczka

    npm run tutorial-general

## <a name="details"></a>Szczegóły
Jednostką znajdującą się "wielowartościowych" akumuluje wartości listy, a nie przechowywania pojedynczą wartość.  Jest to przydatne dla obiektów, których użytkownik może określić więcej niż jedną wartość, takich jak toppings na pizza.

Konkretnie Jeśli jednostka jest oznaczona jako "wielowartościowych", następnie każdego uznane wystąpienia jednostki zostaną dodane do listy w bot pamięci (zamiast zastępowanie wartości pojedynczej jednostki).

## <a name="steps"></a>Kroki

### <a name="create-the-application"></a>Tworzenie aplikacji

1. W Interfejsie użytkownika sieci Web kliknij przycisk nowej aplikacji
2. W polu Nazwa wprowadź MultiValueEntities. Następnie kliknij przycisk Utwórz.

### <a name="create-an-entity"></a>Tworzenie jednostki

1. Kliknij przycisk jednostek, a następnie nowej jednostki.
2. W nazwie podmiotu wprowadź Toppings.
3. Sprawdź wielowartościowych.
    - Jednostek wielowartościowych gromadzone co najmniej jedna wartość w jednostce.
2. Można negować wyboru.  
    - Dzięki temu użytkownikowi na usuwanie toppings z listy toppings pizza skumulowana.
3. Kliknij pozycję Utwórz.

![](../media/tutorial6_entities.PNG)

### <a name="create-two-actions"></a>Utwórz dwie akcje

1. Kliknij przycisk akcje, a następnie nowa akcja
2. W odpowiedzi wpisz "toppings, jakie mają?".
3. W dyskwalifikacji jednostek wprowadź Toppings.
3. Kliknięcie pozycji Utwórz

Następnie należy utworzyć drugą akcję.

1. Kliknij przycisk akcje, a następnie nową akcję w celu utworzenia drugiej akcji.
3. W odpowiedzi, wpisz "w tym miejscu są Twoje toppings: $Toppings".
4. Kliknięcie pozycji Utwórz

Teraz masz dwie akcje.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-bot"></a>Szkolenie bot

1. Kliknij przycisk Train okien dialogowych, następnie nowe okno pociągu.
2. Wpisz tekst "hello".
3. Kliknij wynik akcji i wybierz pozycję "toppings, jakie mają?"
2. Wprowadź "grzyby i ser". 
    - Można opisać zero, jeden lub więcej niż jeden z obiektów.
3. Kliknij przycisk "grzyby", a następnie wybierz Toppings.
4. Kliknij przycisk "ser", a następnie wybierz Toppings.
5. Kliknij przycisk wynik akcji
    - Należy pamiętać, że te dwie wartości są obecne w jednostce Toppings. 
6. Wybierz opcję "w tym miejscu są Twoje toppings: $Toppings".

Można dodać więcej do poniższego:

7. Wprowadź "Dodaj papryki".
    - Kliknij na "papryka, w obszarze jednostki wykrywania i wybierz Toppings.
3. Kliknij przycisk wynik akcji.
    - Należy pamiętać, że papryka teraz wyświetlany jako dodatkowe wartości w Toppings.
6. Wybierz opcję "w tym miejscu są Twoje toppings: $Toppings".

Umożliwia usunięcie nadmiarów i dodaj je:

2. Wpisz "Usuń papryki i dodać produkcji kiełbas".
1. Kliknij pozycję "papryka" i kliknij czerwony znak x, aby usunąć go.
2. Kliknij "papryka" i wybierz polecenie "-Toppings.
3. Kliknij przycisk wynik akcji.
    - Zauważ, że "papryka" został usunięty i produkcji "kiełbas' został dodany.
6. Wybierz opcję "w tym miejscu są Twoje toppings: $Toppings".

Teraz załóżmy, spróbuj usunąć wszystkie elementy:

6. Wprowadź "Usuń grzyby, Usuń ser i Usuń produkcji kiełbas".
7. Kliknij każdy z tych trzech i wybierz polecenie "-Toppings.
7. Kliknij przycisk wynik akcji.
    - Należy pamiętać, że wszystkie toppings zostały wyczyszczone.
2. Wybierz opcję "toppings, jakie mają?"
3. Kliknij przycisk Done nauczania

![](../media/tutorial6_dialogs.PNG)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wbudowane jednostek](./7-built-in-entities.md)

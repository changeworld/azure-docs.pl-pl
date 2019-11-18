---
title: Przykład wypowiedź jednostki etykiety
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak dodać etykietę do jednostki uczenia maszynowego za pomocą podskładników w przykładowej wypowiedź na stronie szczegółów obiektu w portalu LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 87b7375b2da0effbc18fff6a7e9d67383c93a403
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74135110"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Etykieta jednostki doznanej przez maszynę w przykładzie wypowiedź

Etykieta jednostki w przykładzie wypowiedź pokazuje, że LUIS zawiera przykład jednostki i miejsce, w którym jednostka może być wyświetlana w wypowiedź. 

## <a name="labeling-machine-learned-entity"></a>Etykieta jednostki uczenia maszynowego

Rozważ wyrażenie `hi, please I want a cheese pizza in 20 minutes`. 

1. Zaznacz tekst z lewej strony, a następnie wybierz prawym przyciskiem myszy tekst jednostki. _Pełna kolejność_ jest oznaczona na poniższej ilustracji.

    > [!div class="mx-imgBorder"]
    > Etykieta ![Pełna jednostka poznania maszynowego](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Wybierz jednostkę z okna podręcznego. Jednostka etykiety pełna kolejność Pizza obejmuje wszystkie słowa (od lewej do prawej w języku angielskim), które są oznaczone etykietami. 

> [!TIP]
> Jednostki dostępne w oknie podręcznym odnoszą się do kontekstu, w którym pojawia się tekst. Na przykład jeśli masz zainstalowaną maszynę 5-poziomą jednostkę i wybierasz tekst na poziomie trzeciego (wskazywanym przez nazwę jednostki oznaczonej etykietą w przykładzie wypowiedź), jednostki dostępne w oknie podręcznym są ograniczone do kontekstu podskładników trzeciego poziom (podskładniki poziomu czwartego). 

## <a name="review-labeled-text"></a>Przejrzyj tekst z etykietą

Po etykietowaniu zapoznaj się z przykładem wypowiedź. LUIS stosuje bieżący model do przykład wypowiedź po etykiecie. Linia ciągła wskazuje, że tekst został oznaczony etykietą. 

> [!div class="mx-imgBorder"]
> ![oznaczona jako pełna jednostka poznania maszynowego](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="when-to-train"></a>Kiedy nauczenie

Jeśli bieżący model powinien obsługiwać jednostkę oznaczoną etykietą, ale przykład wypowiedź nadal wyświetla tekst jako predykcyjny, ale nie jest oznaczony etykietą, nauczenie aplikacji.  

## <a name="confirm-predicted-entity"></a>Potwierdź jednostkę predykcyjną

Jeśli wskaźnik wizualizacji znajduje się powyżej wypowiedź, oznacza to, że tekst jest przewidywany, ale _nie jest jeszcze oznaczony_. Aby włączyć prognozowanie do etykiety, wybierz wypowiedź, a następnie wybierz pozycję **Potwierdź przewidywania jednostek**.

> [!div class="mx-imgBorder"]
> ![przewidywanie kompletnej jednostki, na którą nauczysz się](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

## <a name="label-subcomponent-entity-by-painting-with-entity-palette-cursor"></a>Oznacz jednostkę elementu podskładnikowego przez malowanie kursorem palety jednostek

1. Aby poprawić przewidywania (jednostki, które pojawiają się powyżej przykładu wypowiedź), Otwórz paletę Entity. 

    > [!div class="mx-imgBorder"]
    > ![paleta jednostek dla jednostki, która jest pouczenia maszynowego](media/label-utterances/pizza-entity-palette-with-pizza-type-selected.png)

1. Wybierz podskładnik jednostki. Ta akcja jest wskazywana wizualnie z nowym kursorem. Kursor jest podążany podczas przenoszenia w portalu. 

    > [!div class="mx-imgBorder"]
    > ![paleta jednostek dla jednostki, która jest pouczenia maszynowego](media/label-utterances/pizza-type-entity-palette-cursor.png)

1. W przykładzie wypowiedź, _maluj_ jednostki za pomocą kursora. 

    > [!div class="mx-imgBorder"]
    > ![paleta jednostek dla jednostki, która jest pouczenia maszynowego](media/label-utterances/paint-subcomponent-with-entity-palette-cursor.png)

## <a name="labeling-matching-text-entities-to-a-machine-learned-entity"></a>Etykietowanie jednostek dopasowywania tekstu do jednostki uczeniej maszynowej

Jednostki dopasowywania tekstu obejmują wstępnie utworzone jednostki, jednostki wyrażeń regularnych i jednostki listy. Te informacje są dodawane do jednostki, która jest wykorzystana przez maszynę jako warunki ograniczające do podskładniku, podczas tworzenia lub edytowania jednostki, której dotyczy dana maszyna. 

**Po dodaniu tych ograniczeń nie trzeba etykietować pasującego tekstu w przykładzie wypowiedź.**

## <a name="entity-prediction-errors"></a>Błędy przewidywania jednostek

Błędy przewidywania jednostek pokazują wskaźnik ostrzegawczy. Wskazuje to, że przewidywana jednostka nie jest zgodna z oznaczona jednostką. 

> [!div class="mx-imgBorder"]
> ![paleta jednostek dla jednostki, która jest pouczenia maszynowego](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Następne kroki

Użyj [pulpitu nawigacyjnego](luis-how-to-use-dashboard.md) i [Przejrzyj wyrażenia długości punktu końcowego](luis-how-to-review-endpoint-utterances.md) , aby zwiększyć jakość przewidywania aplikacji.

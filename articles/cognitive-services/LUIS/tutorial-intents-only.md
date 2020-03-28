---
title: 'Samouczek: Przewidywanie intencji — usługa LUIS'
description: W tym samouczku utworzysz niestandardową aplikację, która będzie przewidywać intencje użytkownika. Ta aplikacja to najprostszy rodzaj aplikacji usługi LUIS, ponieważ nie wyodrębnia ona z tekstu wypowiedzi różnych elementów danych, takich jak adresy e-mail czy daty.
ms.topic: tutorial
ms.date: 03/24/2020
ms.openlocfilehash: c58c96f717de77c065d7f844928714eb4fb3e4db
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80286748"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Samouczek: Tworzenie aplikacji usługi LUIS w celu określenia intencji użytkownika

W tym samouczku utworzysz niestandardową aplikację, która przewiduje zamiar użytkownika na podstawie wypowiedź (tekst).

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie nowej aplikacji
> * Tworzenie intencji
> * Dodawanie przykładowych wypowiedzi
> * Szkolenie aplikacji
> * Publikowanie aplikacji
> * Pobierz przewidywanie intencji z punktu końcowego

## <a name="user-intentions-as-intents"></a>Zamiary użytkownika jako intencje

Zadaniem aplikacji jest ustalenie intencji konwersacyjnego tekstu w języku naturalnym:

`I'd like to order a veggie pizza with a salad on the side.`

Intencje są podzielone na kategorie **intencji**.

|Intencja|Przeznaczenie|
|--|--|
|`ModifyOrder`|Określ zamówienie pizzy użytkownika.|
|`Greeting`|Rozpocznij konwersację bota.|
|`ConfirmOrder`|Potwierdź zamówienie pizzy.|
|`None`|Sprawdź, czy użytkownik pyta o coś, na co aplikacja nie powinna odpowiadać. Ta intencja jest dostarczana jako część procesu tworzenia aplikacji i nie można jej usunąć. |

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Tworzenie nowej intencji

Intencja jest używana do klasyfikowania wypowiedzi użytkownika na podstawie intencji użytkownika, określone na podstawie tekstu języka naturalnego.

Aby sklasyfikować wypowiedź, intencji potrzebuje przykładów wypowiedzi użytkownika, które powinny być klasyfikowane z tej intencji.

1. W sekcji **Kompilacja** na stronie **Intencje** wybierz pozycję **+ Utwórz,** aby utworzyć nową intencję. Wprowadź nową nazwę `OrderPizza`intencji, a następnie wybierz pozycję **Gotowe**.

    Intencja `OrderPizza` jest przewidywana, gdy użytkownik chce zamówić pizzę.

1. Dodaj kilka przykładowych wypowiedzi do tej intencji — takich, których spodziewasz się ze strony użytkownika:

    |`OrderPizza`przykładowe wypowiedzi|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    ![Dodawanie przykładowych wypowiedzi](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Podając _wypowiedzi przykład ,_ uczysz usługi LUIS o jakie rodzaje wypowiedzi powinny być przewidywane dla tej intencji.

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>Tworzenie pozostałych intencji

1. Utwórz `Greeting` intencji i dodać następujące wypowiedzi przykład. Jest to zamiar, aby ustalić, czy użytkownik rozpoczyna nową konwersację zamówienia pizzy.

    |`Greeting`przykładowe wypowiedzi|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Utwórz `Confirm` intencji i dodać następujące wypowiedzi przykład. Jest to zamiar, aby ustalić, czy użytkownik jest gotowy zamawiania i akceptuje szczegóły zamówienia.

    |`Confirm`przykładowe wypowiedzi|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Brak intencji przykład wypowiedzi

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>Uczenie aplikacji

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Publikowanie aplikacji

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>Pobierz przewidywanie intencji

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Przejdź na koniec adresu URL na pasku adresu i wprowadź:

    `get a medium vegetarian pizza for delivery`

    Nie jest to dokładnie takie samo jak wypowiedź przykład, więc jest to dobry test, aby sprawdzić, czy usługa LUIS może dowiedzieć się, co należy przewidzieć z tym zamiarem.

    Ostatni parametr ciągu zapytania to `query`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź nie przypomina żadnej z przykładowych wypowiedzi. Jest to dobry test, który powinien zwrócić intencję `OrderPizza` jako najwyżej ocenioną.

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    Tablica jednostek jest pusta, ponieważ ta aplikacja obecnie nie ma żadnych jednostek (jednostka danych wewnątrz wypowiedź do wyodrębnienia).

    Wynik JSON identyfikuje najwyższej intencji punktacji jako **`prediction.topIntent`** właściwość. Wszystkie wyniki są od 1 do 0, z lepszym wynikiem jest bliżej 1.

1. Zmień parametr **zapytania** url, aby kierować na intencję **powitania:**

    `Howdy`

    Nie jest to dokładnie takie samo jak wypowiedź przykład, więc jest to dobry test, aby sprawdzić, czy usługa LUIS może dowiedzieć się, co należy przewidzieć z tym zamiarem.

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }
    ```

    Ta prognoza ma 44% wskaźnik zaufania. Aby zwiększyć wynik zaufania, dodaj między 15 i 30 wypowiedzi przykład.

## <a name="client-application-next-steps"></a>Następne czynności dotyczące aplikacji klienckiej

Gdy usługa LUIS zwróci odpowiedź w formacie JSON, żądanie zostanie wykonane. Usługa LUIS nie odpowiada na wypowiedzi użytkowników, tylko określa, o jakie informacje proszą oni w języku naturalnym. Dalsza konwersacja jest udostępniana przez aplikację kliencką, na przykład Azure Bot.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Typy jednostek](luis-concept-entity-types.md)
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono aplikację usługi LUIS, utworzono intencje, dodano przykładowe wypowiedzi do każdego zamiaru, dodano przykładowe wypowiedzi do intencji Brak, przeszkolonych, opublikowanych i przetestowanych w punkcie końcowym. Są to podstawowe kroki tworzenia modelu usługi LUIS.

> [!div class="nextstepaction"]
> [Dodawanie do tej aplikacji jednostki rozkładającej się](tutorial-machine-learned-entity.md)

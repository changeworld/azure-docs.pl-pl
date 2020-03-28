---
title: 'Samouczek: Jednostka listy — usługa LUIS'
description: Pobieraj dane, które są zgodne ze wstępnie zdefiniowaną listą elementów. Każdy element na liście może mieć synonimy, które są również dokładnie zgodne
ms.topic: tutorial
ms.date: 03/12/2020
ms.openlocfilehash: 1cfeccbd54e8ef8ec315d53fc7a766760c92a0d1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79297411"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>Samouczek: Pobieranie dokładnych danych dopasowanych do tekstu z wypowiedź z jednostką listy

W tym samouczku dowiedzieć się, jak uzyskać dane, które dokładnie pasuje do wstępnie zdefiniowanej listy elementów.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importowanie aplikacji i używanie istniejących intencji
> * Dodawanie jednostki listy
> * Szkolenie, publikowanie i wykonywanie zapytań w aplikacji w celu uzyskania wyodrębnionych danych

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Co to jest jednostka listy?

Jednostka listy jest dokładnym dopasowaniem tekstu do słów w wypowiedzi. Każdy element na liście może zawierać listę synonimów. Użyj encji listy, jeśli chcesz dokładnie dopasować.

W przypadku tej importowanej aplikacji do pizzy utwórz jednostkę listy dla różnych typów skorupy pizzy.

Jednostka listy jest dobrym rozwiązaniem w przypadku tego typu danych, jeśli:

* Wartości danych należą do znanego zestawu.
* Zestaw nie przekracza maksymalnych [granic](luis-boundaries.md) usługi LUIS dla tego typu jednostki.
* Tekst w wypowiedź jest dopasowanie bez uwzględniania wielkości liter z synonimem lub nazwy kanonicznej. Usługa LUIS nie używa listy poza dopasowaniem. Analiza słowotwórcza, liczba mnoga i inne wariacje nie są rozpoznawane przy użyciu tylko jednostki listy. Aby zarządzać wariacjami, rozważ użycie [wzorca](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) z opcjonalną składnią tekstu.

> [!CAUTION]
> Jeśli nie masz pewności, czy chcesz, aby encja listy lub jednostka nauczona maszynowo z listą fraz jako deskryptorem, najlepszą i najbardziej elastyczną praktyką jest użycie jednostki nauczonej maszynowo z listą fraz jako deskryptorem. Ta metoda umożliwia usługi LUIS dowiedzieć się i rozszerzyć wartości danych do wyodrębnienia.

## <a name="import-example-json-and-add-utterances"></a>Importowanie przykładów .json i dodawanie wypowiedzi

1.  Pobierz i zapisz [plik JSON aplikacji](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. Zaimportowana aplikacja `OrderPizza` ma zamiar. Wybierz tę intencję i dodaj kilka wypowiedzi z nowymi typami skorupy:

    |Nowe wypowiedzi|
    |--|--|
    |proszę zamówić patelnię skorupę małej pizzy pepperoni|
    |3 cienkie skorupy hawajskie pizze|
    |dostarczyć 2 nadziewane pizze skorupy z pałeczkami chleba|
    |jedna gruba pizza skorupowa do odbioru|
    |jedna pizza pepperoni z głębokim daniem|

## <a name="crust-list-entity"></a>Jednostka listy skorup

Teraz, gdy **intencji OrderPizza** ma wypowiedzi przykład z typów skorupy, usługa LUIS musi zrozumieć, które słowa reprezentują typy skorupy.

Przykłady nazwy podstawowej i synonimów to:

|Nazwa kanoniczna|Synonimy|
|--|--|
|Głębokie naczynie|Głębokie<br>skorupa naczyń głębokich<br>Grube<br>gruba skorupa|
|Panoramowanie|Regularne<br>Oryginał<br>Normalne<br>zwykła skorupa<br>oryginalna skorupa<br>normalna skorupa|
|Nadziewane|nadziewane skorupy|
|Elastyczne|cienka skorupa<br>Szczupła<br>chuda skorupa|

1. Wybierz pozycję **Entities** (Jednostki) w lewym panelu.

1. Wybierz **+ Utwórz**.

1. W oknie podręcznym jednostki wprowadź ciąg `CrustList` jako nazwę jednostki i wartość **List** (Lista) jako typ jednostki. Wybierz **pozycję Dalej**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający podręczne okno dialogowe tworzenia nowej jednostki](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. Na stronie **Tworzenie encji listy** wprowadź nazwy kanoniczne i synonimy dla każdej nazwy kanonicznej, a następnie wybierz pozycję **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający dodawanie elementów do encji listy](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    Po dodaniu jednostki listy do aplikacji usługi LUIS nie trzeba [oznaczać](label-entity-example-utterance.md) tekstu encją listy. Jest stosowany do wszystkich wypowiedzi we wszystkich intencji.

## <a name="train-the-app-before-testing-or-publishing"></a>Trenowanie aplikacji przed testowaniem lub publikowaniem

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikowanie aplikacji w celu wysyłania zapytań z punktu końcowego

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Pobieranie przewidywania intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Przejdź na koniec adresu URL w adresie i wprowadź następujący wypowiedź:

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    Ostatnim parametrem querystring jest `query` **kwerenda**wypowiedź .


    ```json
    {
        "query": "Deliver 2 deep dish hawaiian pizzas and a thin pepperoni",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.9957229
                },
                "None": {
                    "score": 0.016832687
                },
                "Confirm": {
                    "score": 0.0015708931
                },
                "Greeting": {
                    "score": 0.00057060417
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "CrustList": [
                    [
                        "Deep dish"
                    ],
                    [
                        "Thin"
                    ]
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "CrustList": [
                        {
                            "type": "CrustList",
                            "text": "deep dish",
                            "startIndex": 10,
                            "length": 9,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "CrustList",
                            "text": "thin",
                            "startIndex": 42,
                            "length": 4,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    Typy skorupy zostały znalezione jako dokładne dopasowanie tekstu i zwrócone w odpowiedzi JSON. Te informacje są używane przez aplikację kliencką do przetwarzania zamówienia.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Lista jednostki](luis-concept-entity-types.md#list-entity) — informacje koncepcyjne
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)
* [Koncepcja - jednostki](luis-concept-entity-types.md)
* [Odwołanie JSON do encji wyrażenia regularnego](reference-entity-regular-expression.md?tabs=V3)
* [Jak dodać jednostki do wyodrębniania danych](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Następne kroki
W tym samouczku dodano wypowiedzi przykład, a następnie utworzono jednostkę listy, aby wyodrębnić dokładne dopasowania tekstu z wypowiedzi. Po wyszkoleniu i opublikowaniu aplikacji zapytanie do punktu końcowego zidentyfikowało intencję i zwróciło wyodrębnione dane.

> [!div class="nextstepaction"]
> [Dodawanie wstępnie utworzonej jednostki z rolą](tutorial-entity-roles.md)


---
title: 'Samouczek: list Entity-LUIS'
titleSuffix: Azure Cognitive Services
description: Pobieraj dane, które są zgodne ze wstępnie zdefiniowaną listą elementów. Każdy element na liście może mieć synonimy, które są również dokładnie zgodne
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 056c64657f42d56879928f518598206d45493f60
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447784"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>Samouczek: uzyskiwanie dokładnych danych dopasowanych do tekstu z wypowiedź za pomocą jednostki listy

W tym samouczku dowiesz się, jak pobierać dane, które dokładnie pasują do wstępnie zdefiniowanej listy elementów.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importuj aplikację i Użyj istniejącego zamiaru
> * Dodawanie jednostki listy
> * Uczenie, publikowanie i tworzenie zapytań o aplikacje w celu pobrania wyodrębnionych danych

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Co to jest jednostka listy?

Jednostka listy jest dokładnym dopasowaniem tekstu do słów w wypowiedzi. Każdy element na liście może zawierać listę synonimów. Użyj jednostki listy, jeśli chcesz dokładnie dopasować.

Dla tej zaimportowanej aplikacji Pizza Utwórz jednostkę listy dla różnych typów Pizza crust.

Jednostka listy jest dobrym rozwiązaniem w przypadku tego typu danych, jeśli:

* Wartości danych należą do znanego zestawu.
* Zestaw nie przekracza maksymalnych [granic](luis-boundaries.md) usługi LUIS dla tego typu jednostki.
* Tekst w wypowiedzi to dokładne dopasowanie synonimu lub nazwy kanonicznej. Usługa LUIS nie korzysta z listy poza dokładnymi dopasowaniami tekstu. Analiza słowotwórcza, liczba mnoga i inne wariacje nie są rozpoznawane przy użyciu tylko jednostki listy. Aby zarządzać wariacjami, rozważ użycie [wzorca](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) z opcjonalną składnią tekstu.

> [!CAUTION]
> Jeśli nie masz pewności, czy chcesz, aby jednostka listy lub obiekt, który został wystawiony przez maszynę z listą fraz jako deskryptora, najlepszym i najbardziej elastycznym rozwiązaniem jest użycie jednostki obsługiwanej przez maszynę z listą fraz jako deskryptora. Ta metoda umożliwia LUISom naukę i zwiększanie wartości danych do wyodrębnienia.

## <a name="import-example-json-and-add-utterances"></a>Importuj plik example. JSON i Dodaj wyrażenia długości

1.  Pobierz i Zapisz [plik JSON aplikacji](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. Zaimportowana aplikacja ma zamiar `OrderPizza`. Wybierz ten cel i Dodaj kilka wyrażenia długości z nowymi typami Crust:

    |Nowy wyrażenia długości|
    |--|--|
    |Zamów Crust na małą Pepperoni Pizza|
    |3 cienkie Crust hawajski pizzami|
    |Dostarcz 2 Crust pizzami z odciskiem chleba|
    |jedna Gruba Crust Pizza do odbioru|
    |jedna głęboka Pepperoni Pizza|

## <a name="crust-list-entity"></a>Jednostka listy Crust

Teraz, gdy zamiara **OrderPizza** ma przykład wyrażenia długości z typami Crust, Luis musi zrozumieć, które wyrazy reprezentują typy crust.

Przykłady podstawowej nazwy i synonimów to:

|Nazwa kanoniczna|Synonimy|
|--|--|
|Głębokie nadania|niskim<br>głęboki Crust naczyń<br>cieńsz<br>Gruby Crust|
|Przesuwanie|regularne<br>Oryginał<br>typow<br>Regularna Crust<br>oryginalny Crust<br>normalne Crust|
|Maskotka|Crusty|
|Elastyczne|crust cienki<br>skinny<br>skinny crust|

1. Wybierz pozycję **Entities** (Jednostki) w lewym panelu.

1. Wybierz pozycję **+ Utwórz**.

1. W oknie podręcznym jednostki wprowadź ciąg `CrustList` jako nazwę jednostki i wartość **List** (Lista) jako typ jednostki. Wybierz opcję **Dalej**.

    > [!div class="mx-imgBorder"]
    > Zrzut ekranu przedstawiający ![tworzenia nowego okna podręcznego nowej jednostki](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. Na stronie **Tworzenie jednostki listy** wprowadź nazwy kanoniczne i synonimy dla każdej nazwy kanonicznej, a następnie wybierz pozycję **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![zrzut ekranu przedstawiający Dodawanie elementów do jednostki listy](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    Gdy dodajesz jednostkę listy do aplikacji LUIS, nie musisz dodawać [etykiet](label-entity-example-utterance.md) do tekstu za pomocą jednostki listy. Jest on stosowany do wszystkich wyrażenia długości we wszystkich intencjach.

## <a name="train-the-app-before-testing-or-publishing"></a>Trenowanie aplikacji przed testowaniem lub publikowaniem

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikowanie aplikacji w celu wysyłania zapytań z punktu końcowego

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Pobieranie przewidywania intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Przejdź na koniec adresu URL w adresie i wprowadź następujący wypowiedź:

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    Ostatni parametr ciągu zapytania to `query`, czyli **query** (zapytanie) wypowiedzi.


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

    Typy Crust zostały znalezione jako dokładne dopasowania tekstu i zwrócone w odpowiedzi JSON. Te informacje są używane przez aplikację kliencką do przetwarzania zamówienia.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Lista jednostki](luis-concept-entity-types.md#list-entity) — informacje koncepcyjne
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)
* [Koncepcje — jednostki](luis-concept-entity-types.md)
* [Odwołanie JSON jednostki wyrażenia regularnego](reference-entity-regular-expression.md?tabs=V3)
* [Jak dodać jednostki w celu wyodrębnienia danych](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Następne kroki
W tym samouczku dodano przykład wyrażenia długości, a następnie utworzono jednostkę listy, aby wyodrębnić dokładne dopasowania tekstu z wyrażenia długości. Po wyszkoleniu i opublikowaniu aplikacji zapytanie do punktu końcowego zidentyfikowało intencję i zwróciło wyodrębnione dane.

> [!div class="nextstepaction"]
> [Dodaj wstępnie utworzoną jednostkę z rolą](tutorial-entity-roles.md)


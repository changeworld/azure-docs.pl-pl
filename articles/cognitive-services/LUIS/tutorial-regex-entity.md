---
title: 'Samouczek: Jednostka wyrażenia regularnego — usługa LUIS'
titleSuffix: Azure Cognitive Services
description: Wyodrębnij konsekwentnie sformatowane dane z wypowiedź przy użyciu jednostki wyrażenia regularnego.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 0ca6f2a67e01e4c604c2dcc8f8eaa9ffe8bad045
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75381532"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Samouczek: Uzyskaj dobrze sformatowane dane z wypowiedź
W tym samouczku utwórz jednostkę wyrażenia regularnego, aby wyodrębnić konsekwentnie sformatowane dane z wypowiedź.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importowanie aplikacji
> * Dodawanie intencji
> * Dodawanie jednostki wyrażenia regularnego
> * Szkolenie, publikowanie i wykonywanie zapytań w aplikacji w celu uzyskania wyodrębnionych danych

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Jednostki wyrażenia regularnego

Użyj jednostki wyrażenia regularnego, aby wyciągnąć dobrze sformatowany tekst z wypowiedź. Podczas gdy intencja wypowiedzi jest zawsze określana za pomocą uczenia maszynowego, ten specyficzny typ jednostki nie jest określany za pomocą uczenia maszynowego. Dobrym zastosowaniem dla encji wyrażenia regularnego jest dowolny tekst, który może być konsekwentnie reprezentowany przez [wyrażenie regularne](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).

`Send pizza delivery time to x123432`

W tym przykładzie użyto _krótkiego kodu_ do wysyłania wiadomości tekstowych. Ten krótki kod jest 5 lub 6-cyfrowy kod numeryczny, poprzedzony x, i `x\d{5,6}`mogą być opisane za pomocą wyrażenia regularnego .

Po dodaniu jednostki wyrażenia regularnego do aplikacji usługi LUIS nie trzeba [oznaczać](label-entity-example-utterance.md) tekstu jednostką zwykłego wyrażenia. Jest stosowany do wszystkich wypowiedzi we wszystkich intencji.

## <a name="import-example-json-to-begin-app"></a>Importowanie przykładu .json, aby rozpocząć aplikację

1.  Pobierz i zapisz [plik JSON aplikacji](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>Tworzenie intencji wysyłania wiadomości tekstowych z potwierdzeniem

1. Wybierz **+ Utwórz,** aby utworzyć nowy zamiar klasyfikacji intencji wypowiedź do wysyłania tekstu potwierdzenia.

1. Wprowadź ciąg `ConfirmationText` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe).

1. Dodaj przykładowe wypowiedzi do intencji.

    |Przykładowe wypowiedzi|
    |--|
    |Wyślij czas dostawy pizzy do x123432|
    |Txt x234567 na czas|
    |x23987 dla ogłoszenia|

    Aby wyodrębnić jednostki nauczone maszynowo, należy podać przykłady, które obejmują jednostki w różnych wypowiedzi, ale z tej jednostki nienauczone maszynowo, odmiana nie jest ważne. Tak długo, jak tekst pasuje do wyrażenia regularnego, zostanie wyodrębniony.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Używanie jednostki wyrażenia regularnego na potrzeby prawidłowo sformatowanych danych
Utwórz encję wyrażenia regularnego, aby dopasować go do numeru tekstowego. To wyrażenie regularne dopasowuje tekst, ale ignoruje warianty sprawy i kultury.

1. Wybierz pozycję **Entities** (Jednostki) w lewym panelu.

1. Wybierz **pozycję + Utwórz** na stronie listy Jednostki.

1. W oknie podręcznym wprowadź nową nazwę `ConfirmationTextRegEx`encji , wybierz **opcję RegEx** jako typ encji, a następnie wybierz pozycję **Dalej**.

    > [!div class="mx-imgBorder"]
    > ![Rozpoczynanie kroków tworzenia encji dla encji wyrażenia regularnego](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. W polu **Utwórz encję wyrażenia regularnego**wprowadź `x\d{5,6}` jako wartość **Regex,** a następnie wybierz pozycję **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Wprowadź wyrażenie regularne, aby wyodrębnić dane z przykładowej wypowiedzi](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. Wybierz **intencje** z lewego menu, a następnie **ConfirmationText** intencji, aby zobaczyć wyrażenie regularne oznaczone w wypowiedzi.

    > [!div class="mx-imgBorder"]
    > ![Wyświetlanie wyrażenia regularnego oznaczonego w przykładowych wypowiedziach](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    Ponieważ jednostka nie jest jednostką nauczaną maszynowo, jednostka jest stosowana do wypowiedzi i wyświetlana w portalu usługi LUIS zaraz po jej utworzeniu.

## <a name="train-the-app-before-testing-or-publishing"></a>Trenowanie aplikacji przed testowaniem lub publikowaniem

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikowanie aplikacji w celu wysyłania zapytań z punktu końcowego

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Pobieranie przewidywania intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Przejdź na koniec adresu URL w adresie i wprowadź następujący wypowiedź:

    `Text my pizza delivery to x23456 x234567 x12345`

    Ostatnim parametrem querystring jest `query` **kwerenda**wypowiedź .

    ```json
    {
        "query": "Text my pizza delivery to x23456 x234567 x12345",
        "prediction": {
            "topIntent": "ConfirmationText",
            "intents": {
                "ConfirmationText": {
                    "score": 0.7061845
                },
                "ModifyOrder": {
                    "score": 0.196021989
                },
                "None": {
                    "score": 0.02342912
                },
                "Test-Pizza": {
                    "score": 0.01213586
                },
                "CancelOrder": {
                    "score": 0.0063042324
                },
                "Confirmation": {
                    "score": 0.0058615827
                },
                "Greetings": {
                    "score": 0.00398947531
                }
            },
            "entities": {
                "ConfirmationTextRegEx": [
                    "x23456",
                    "x234567",
                    "x12345"
                ],
                "$instance": {
                    "ConfirmationTextRegEx": [
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x23456",
                            "startIndex": 26,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x234567",
                            "startIndex": 33,
                            "length": 7,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x12345",
                            "startIndex": 41,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
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

    Używając jednostki wyrażenia regularnego, aplikacja LUIS wyodrębnia nazwane dane, co jest bardziej programowo przydatne dla aplikacji klienckiej, która otrzymuje odpowiedzi JSON.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Koncepcja - jednostki](luis-concept-entity-types.md)
* [Odwołanie JSON do encji wyrażenia regularnego](reference-entity-regular-expression.md?tabs=V3)
* [Jak dodać jednostki do wyodrębniania danych](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono nową intencję, dodano przykładowe wypowiedzi, a następnie utworzono jednostkę wyrażenia regularnego, aby wyodrębnić z wypowiedzi poprawnie sformatowane dane. Po wyszkoleniu i opublikowaniu aplikacji zapytanie do punktu końcowego zidentyfikowało intencję i zwróciło wyodrębnione dane.

> [!div class="nextstepaction"]
> [Learn about the list entity](tutorial-list-entity.md) (Informacje na temat jednostki listy)


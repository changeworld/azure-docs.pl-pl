---
title: 'Samouczek: wyrażenie regularne — LUIS'
titleSuffix: Azure Cognitive Services
description: Wyodrębnij spójnie sformatowane dane z wypowiedź przy użyciu jednostki wyrażenia regularnego.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: diberry
ms.openlocfilehash: 8da47899e2d3d2c1d04e9cf4768a968e7893ce96
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851869"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Samouczek: pobieranie dobrze sformatowanych danych z wypowiedź
W tym samouczku utworzysz jednostkę wyrażenia regularnego w celu wyodrębnienia spójnie sformatowanych danych z wypowiedź.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importuj aplikację
> * Dodawanie intencji
> * Dodawanie jednostki wyrażenia regularnego
> * Uczenie, publikowanie i tworzenie zapytań o aplikacje w celu pobrania wyodrębnionych danych

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Jednostki wyrażenia regularnego

Użyj jednostki wyrażenia regularnego, aby pobrać dobrze sformatowany tekst z wypowiedź. Podczas gdy intencja wypowiedzi jest zawsze określana za pomocą uczenia maszynowego, ten specyficzny typ jednostki nie jest określany za pomocą uczenia maszynowego. Dobrym zastosowaniem dla jednostki wyrażenia regularnego jest dowolny tekst, który może być spójny przez [wyrażenie regularne](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).

`Send pizza delivery time to x123432`

Ten przykład używa _krótkiego kodu_ do wysyłania wiadomości tekstowych. Ten krótki kod jest 5-lub 6-cyfrowym kodem numerycznym poprzedzonym znakiem x i może być opisany przy użyciu wyrażenia regularnego `x\d{5,6}`.

Po dodaniu jednostki wyrażenia regularnego do aplikacji LUIS nie ma potrzeby [oznaczania](label-entity-example-utterance.md) tekstu za pomocą zwykłej jednostki Express. Jest on stosowany do wszystkich wyrażenia długości we wszystkich intencjach.

## <a name="import-example-json-to-begin-app"></a>Importuj plik example. JSON, aby rozpocząć aplikację

1.  Pobierz i Zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>Utwórz cel wysyłania wiadomości SMS z potwierdzeniem

1. Wybierz pozycję **+ Utwórz** , aby utworzyć nowy cel do klasyfikowania zamiaru wypowiedź do wysyłania tekstu potwierdzenia.

1. Wprowadź ciąg `ConfirmationText` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe).

1. Dodaj przykładowe wypowiedzi do intencji.

    |Przykładowe wypowiedzi|
    |--|
    |Wyślij czas dostarczania Pizza do x123432|
    |X234567 txt dla czasu|
    |x23987 na powiadomienie|

    Aby wyodrębnić jednostki, które są obsługiwane przez maszynę, należy podać przykłady, które obejmują jednostkę w różnych wyrażenia długościach, ale z tą jednostką, która nie jest zadana maszynowo. Tak długo, jak tekst jest zgodny z wyrażeniem regularnym, zostanie wyodrębniony.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Używanie jednostki wyrażenia regularnego na potrzeby prawidłowo sformatowanych danych
Utwórz jednostkę wyrażenia regularnego, aby dopasować ją do numeru tekstu. To wyrażenie regularne dopasowuje tekst, ale ignoruje warianty wielkości liter i kultur.

1. Wybierz pozycję **Entities** (Jednostki) w lewym panelu.

1. Na stronie listy jednostki wybierz pozycję **+ Utwórz** .

1. W podręcznym oknie dialogowym wprowadź nazwę nowej jednostki `ConfirmationTextRegEx`, wybierz **wyrażenie regularne** jako typ jednostki, a następnie wybierz przycisk **dalej**.

    > [!div class="mx-imgBorder"]
    > ![rozpoczęcie kroków tworzenia jednostki dla jednostki wyrażenia regularnego](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. W polu **Utwórz jednostkę wyrażenia regularnego**wprowadź `x\d{5,6}` jako wartość **wyrażenia regularnego** , a następnie wybierz pozycję **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![wprowadź wyrażenie regularne, aby wyodrębnić dane z przykładu wypowiedź](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. Wybierz pozycję **intencje** w menu po lewej stronie, a następnie **ConfirmationText** zamiar zobaczyć wyrażenie regularne oznaczone w wyrażenia długości.

    > [!div class="mx-imgBorder"]
    > ![Wyświetl wyrażenie regularne z etykietą na przykład wyrażenia długości](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    Ponieważ jednostka nie jest jednostką uczenia maszynowego, jednostka jest stosowana do wyrażenia długości i wyświetlana w portalu LUIS zaraz po jego utworzeniu.

## <a name="train-the-app-before-testing-or-publishing"></a>Trenowanie aplikacji przed testowaniem lub publikowaniem

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikowanie aplikacji w celu wysyłania zapytań z punktu końcowego

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Pobieranie przewidywania intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Przejdź na koniec adresu URL w adresie i wprowadź następujący wypowiedź:

    `Text my pizza delivery to x23456 x234567 x12345`

    Ostatni parametr ciągu zapytania to `query`, czyli **query** (zapytanie) wypowiedzi.

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

* [Koncepcje — jednostki](luis-concept-entity-types.md)
* [Odwołanie JSON jednostki wyrażenia regularnego](reference-entity-regular-expression.md?tabs=V3)
* [Jak dodać jednostki w celu wyodrębnienia danych](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono nową intencję, dodano przykładowe wypowiedzi, a następnie utworzono jednostkę wyrażenia regularnego, aby wyodrębnić z wypowiedzi poprawnie sformatowane dane. Po wyszkoleniu i opublikowaniu aplikacji zapytanie do punktu końcowego zidentyfikowało intencję i zwróciło wyodrębnione dane.

> [!div class="nextstepaction"]
> [Learn about the list entity](tutorial-list-entity.md) (Informacje na temat jednostki listy)


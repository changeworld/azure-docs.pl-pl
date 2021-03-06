---
title: 'Samouczek: Ustawienia publikowania — usługa LUIS'
description: W tym samouczku zmień ustawienia publikowania, aby uzyskać lepsze prognozy.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 19913d16ecb1457ad4edb93ea34e4b96a590aca0
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545773"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Samouczek: Dodawanie analizy tonacji jako ustawienia publikowania

W tym samouczku zmodyfikuj ustawienia publikowania, aby wyodrębnić analizę tonacji, a następnie przesuń zapytanie do punktu końcowego usługi LUIS, aby wyświetlić zwrócony tonację wypowiedzi użytkownika.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Dodawanie analizy tonacji jako ustawienia publikowania
> * Uzyskaj opinię wypowiedź z opublikowanego punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Analiza tonacji to ustawienie publikowania

Następujące wypowiedzi przedstawiają przykłady tonacji:

|Opinia|Wynik|Wypowiedź|
|:--|:--|:--|
|negative|0,01 |Nie znalazłeś(a) to pizzeria.|
|pozytywna|0.97 |Pizza serowa była wspaniała.|

Analiza tonacji to ustawienie publikowania, które ma zastosowanie do każdej wypowiedzi. Po ustawieniu aplikacja zwraca tonację wypowiedź bez konieczności etykietowania danych.

Ponieważ jest to ustawienie publikowania, nie jest widoczne na stronach intencji lub jednostek. Jest ono widoczne w okienku [interactive test](luis-interactive-test.md#view-sentiment-results) (Test interaktywny) lub podczas testowania pod adresem URL punktu końcowego.

## <a name="import-example-json-to-begin-app"></a>Importowanie przykładu .json, aby rozpocząć aplikację

1.  Pobierz i zapisz [plik JSON aplikacji](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>Uczenie aplikacji

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurowanie aplikacji do uwzględnienia analizy tonacji

1. Z górnego menu wybierz **pozycję Publikuj.** Analiza tonacji jest ustawieniem publikowania.

1. Wybierz **miejsce produkcyjne,** a następnie wybierz pozycję **Zmień ustawienia**.
1. Ustaw ustawienie Analiza tonacji na **Włączone**.

    ![Włączanie analizy tonacji jako ustawienia publikowania](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Uzyskiwanie tonacji wypowiedzi z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Przejdź na koniec adresu URL na pasku adresu i zastąp _YOUR_QUERY_HERE:_

    `Deliver 2 of the best cheese pizzas ever!!!`

    Ta wypowiedź jest inna niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić intencję `OrderPizza` z wyodrębnioną analizą tonacji.

    ```json
    {
        "query": "Deliver 2 of the best cheese pizzas ever!!!",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.4659952
                },
                "None": {
                    "score": 0.16901511
                },
                "Confirm": {
                    "score": 0.00421415
                },
                "Greeting": {
                    "score": 0.00109914376
                }
            },
            "entities": {
                "number": [
                    2
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
                    ]
                }
            },
            "sentiment": {
                "label": "positive",
                "score": 0.861665964
            }
        }
    }
    ```

    Analiza nastrojów jest pozytywna z wynikiem 86%.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* Analiza tonacji jest udostępniana za pośrednictwem funkcji [analizy tekstu](../Text-Analytics/index.yml) usługi Cognitive Service. Ta funkcja jest ograniczona do [obsługiwanych języków](luis-language-support.md#languages-supported) przez analizę tekstu.
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Następne kroki
W tym samouczku dodaliśmy analizę tonacji jako ustawienie publikowania, aby wyodrębnić wartości tonacji z wypowiedzi jako całości.

> [!div class="nextstepaction"]
> [Przejrzyj wypowiedzi punktu końcowego w aplikacji HR](luis-tutorial-review-endpoint-utterances.md)


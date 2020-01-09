---
title: 'Szybki Start: Tworzenie aplikacji — LUIS'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia aplikacji LUIS, która używa wstępnie skompilowanej domeny `HomeAutomation` do włączania i wyłączania świateł i urządzeń. Ta wstępnie utworzona domena zapewnia intencje, jednostki i przykładowe wypowiedzi. Po zakończeniu samouczka punkt końcowy usługi LUIS będzie działał w chmurze.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 302321a36a6ce7526ad5e3144f87b88edbfaaec7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448098"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Szybki start: używanie wstępnie utworzonej aplikacji Home Automation

W tym przewodniku Szybki start utworzysz aplikację usługi LUIS, która używa wstępnie utworzonej domeny `HomeAutomation` na potrzeby włączania i wyłączania świateł i urządzeń. Ta wstępnie utworzona domena zapewnia intencje, jednostki i przykładowe wypowiedzi. Po zakończeniu samouczka punkt końcowy usługi LUIS będzie działał w chmurze.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji
Tworzenie aplikacji i zarządzanie nimi jest możliwe w obszarze **My Apps** (Moje aplikacje).

1. W portalu LUIS na liście Moje aplikacje wybierz pozycję **+ Utwórz**.

    ![W portalu LUIS na liście Moje aplikacje wybierz pozycję "+ Utwórz".](./media/create-app-in-portal.png)

1. W oknie dialogowym Nadaj nazwę aplikacji `Home Automation` a następnie wybierz pozycję **gotowe**. LUIS tworzy aplikację. Opis jest opcjonalny i nie jest używany do tworzenia ani przewidywania. Zasób predykcyjny jest również opcjonalny podczas tworzenia aplikacji LUIS. Po opublikowaniu aplikacji w środowisku produkcyjnym należy przypisać zasób predykcyjny, aby aplikacja mogła obsługiwać wiele żądań.

    ![W oknie dialogowym Nazwij aplikację "Automatyzacja domu"](./media/create-new-app-details.png)

    >[!NOTE]
    >Kultury nie można zmienić po utworzeniu aplikacji.

## <a name="add-prebuilt-domain"></a>Dodawanie wstępnie utworzonej domeny

Wybierz opcję **prekompilowane domeny** , a następnie wyszukaj ciąg **HomeAutomation**. Wybierz pozycję **Dodaj domenę** na karcie HomeAutomation.

![Wybierz pozycję "prekompilowane domeny", a następnie wyszukaj ciąg "HomeAutomation". Wybierz pozycję "Dodaj domenę" na karcie HomeAutomation.](media/luis-quickstart-new-app/home-automation.png)

Po pomyślnym dodaniu domeny pole wstępnie utworzonej domeny wyświetla przycisk **Remove domain** (Usuń domenę).

## <a name="intents-and-entities"></a>Intencje i jednostki

Wybierz **intencje** , aby przejrzeć intencje domeny HomeAutomation. Prebudowane intencje domeny mają przykładowe wyrażenia długości.

![Zrzut ekranu listy intencji HomeAutomation](media/luis-quickstart-new-app/home-automation-intents.png "Zrzut ekranu listy intencji HomeAutomation")

> [!NOTE]
> **None** (Brak) to intencja zapewniana przez wszystkie aplikacje usługi LUIS. Używa się jej do obsługi wypowiedzi, które nie odnoszą się do funkcji zapewnianej przez aplikację.

Wybierz intencję **HomeAutomation.TurnOff**. Możesz zobaczyć, że intencja zawiera listę wypowiedzi, które są oznaczone przy użyciu jednostek.

[![Zrzut ekranu z intencją HomeAutomation. wy](media/luis-quickstart-new-app/home-automation-turnoff.png "Zrzut ekranu z intencją HomeAutomation. wy")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Testowanie aplikacji
Po przeprowadzeniu uczenia aplikacji możesz ją przetestować. Wybierz pozycję **Testuj**. Wpisz wypowiedź testu, taki jak `Turn off the lights`, do interaktywnego okienka testów, a następnie naciśnij klawisz ENTER.

```
Turn off the lights
```

Sprawdź, czy intencja o najwyższym wyniku odpowiada oczekiwanej intencji dla każdej wypowiedzi testowej.

W tym przykładzie `Turn off the lights` jest prawidłowo zidentyfikowany jako górny cel oceniania **HomeAutomation.** .

![Zrzut ekranu przedstawiający Panel testowy z wyróżnioną pozycją wypowiedź](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Wybierz pozycję **Sprawdź** , aby przejrzeć więcej informacji na temat przewidywania.

![Zrzut ekranu przedstawiający Panel testowy z informacjami o inspekcji](media/luis-quickstart-new-app/test.png)

Ponownie wybierz opcję **Test** (Testuj), aby zwinąć okienko testowania.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Zbadaj punkt końcowy przewidywania interfejsu API v3

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

1. Na pasku adresu przeglądarki dla ciągu zapytania upewnij się, że w adresie URL znajdują się następujące nazwy i słupki wartości. Jeśli nie znajdują się w ciągu zapytania, Dodaj je:

    |Para nazwa/wartość|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

1. Na pasku adresu przeglądarki przejdź na koniec adresu URL i wprowadź `turn off the living room light` dla wartości _zapytania_ , a następnie naciśnij klawisz ENTER.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.984315455
                },
                "HomeAutomation.QueryState": {
                    "score": 0.009912962
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.00626645749
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.00572059769
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.00379381469
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00366983772
                },
                "None": {
                    "score": 0.000623856
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.907323956,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
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

    Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

Punkt końcowy możesz wywołać z kodu:

> [!div class="nextstepaction"]
> [Wywoływanie punktu końcowego usługi LUIS przy użyciu kodu](luis-get-started-cs-get-intent.md)

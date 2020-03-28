---
title: 'Szybki start: tworzenie aplikacji — USŁUGA LUIS'
description: Ten przewodnik Szybki start pokazuje, jak utworzyć aplikację `HomeAutomation` usługi LUIS, która używa wstępnie utworzonej domeny do włączania i wyłączania świateł i urządzeń. Ta wstępnie utworzona domena zapewnia intencje, jednostki i przykładowe wypowiedzi. Po zakończeniu samouczka punkt końcowy usługi LUIS będzie działał w chmurze.
ms.topic: quickstart
ms.date: 03/24/2020
ms.openlocfilehash: de6cf5e95ee63fc9500cf1b5edab78597bdb18af
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80287802"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Szybki start: używanie wstępnie utworzonej aplikacji Home Automation

W tym przewodniku Szybki start utworzysz aplikację usługi LUIS, która używa wstępnie utworzonej domeny `HomeAutomation` na potrzeby włączania i wyłączania świateł i urządzeń. Ta wstępnie utworzona domena zapewnia intencje, jednostki i przykładowe wypowiedzi. Po zakończeniu samouczka punkt końcowy usługi LUIS będzie działał w chmurze.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji
Tworzenie aplikacji i zarządzanie nimi jest możliwe w obszarze **My Apps** (Moje aplikacje).

1. Na liście Moje aplikacje wybierz pozycję **+ Nowa aplikacja do konwersacji**.

1. W oknie dialogowym nazwij aplikację `Home Automation`. Wybierz **język angielski** jako kulturę. Opis jest opcjonalny i nie jest używany do tworzenia lub przewidywania. Zasób przewidywania jest również opcjonalne podczas tworzenia aplikacji usługi LUIS. Wybierz pozycję **Done** (Gotowe).

    Usługa LUIS tworzy aplikację. Podczas publikowania aplikacji w procesach produkcyjnych należy przypisać zasób przewidywania, aby aplikacja mogła obsługiwać wiele żądań.

    ![W oknie dialogowym nazwij aplikację "Automatyka domowa"](./media/create-new-app-details.png)

    >[!NOTE]
    >Kultury nie można zmienić po utworzeniu aplikacji.

## <a name="add-prebuilt-domain"></a>Dodawanie wstępnie utworzonej domeny

Wybierz **wstępnie utworzone domeny,** a następnie wyszukaj **pozycję HomeAutomation**. Wybierz **pozycję Dodaj domenę** na karcie HomeAutomation.

![Wybierz "Wstępnie utworzone domeny", a następnie wyszukaj hasło "HomeAutomation". Wybierz opcję "Dodaj domenę" na karcie HomeAutomation.](media/luis-quickstart-new-app/home-automation.png)

Po pomyślnym dodaniu domeny pole wstępnie utworzonej domeny wyświetla przycisk **Remove domain** (Usuń domenę).

## <a name="intents-and-entities"></a>Intencje i jednostki

Wybierz **opcję Intencje,** aby przejrzeć intencje domeny HomeAutomation. Wstępnie utworzone intencje domeny mają wypowiedzi próbki.

![Zrzut ekranu przedstawiający listę intencji homeautomation](media/luis-quickstart-new-app/home-automation-intents.png "Zrzut ekranu przedstawiający listę intencji homeautomation")

> [!NOTE]
> **None** (Brak) to intencja zapewniana przez wszystkie aplikacje usługi LUIS. Używa się jej do obsługi wypowiedzi, które nie odnoszą się do funkcji zapewnianej przez aplikację.

Wybierz intencję **HomeAutomation.TurnOff**. Możesz zobaczyć, że intencja zawiera listę wypowiedzi, które są oznaczone przy użyciu jednostek.

[![Zrzut ekranu przedstawiający intencję HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Zrzut ekranu przedstawiający intencję HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Testowanie aplikacji
Po przeprowadzeniu uczenia aplikacji możesz ją przetestować.

1. Wybierz **opcję Testuj** z nawigacji w prawym górnym rogu. 1. Wpisz wypowiedź `Turn off the lights` testu, jak w interaktywnym okienku testu, a następnie naciśnij klawisz Enter.

    ```
    Turn off the lights
    ```

    Sprawdź, czy intencja o najwyższym wyniku odpowiada oczekiwanej intencji dla każdej wypowiedzi testowej.

    W tym `Turn off the lights` przykładzie jest poprawnie identyfikowany jako najwyższej intencji punktacji **HomeAutomation.TurnOff**.

    ![Zrzut ekranu przedstawiający panel testowania z wyróżnioną wypowiedzią](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Wybierz **sprawdź,** aby przejrzeć więcej informacji na temat prognozowania.

    ![Zrzut ekranu przedstawiający panel testowy z informacjami o inspekcji](media/luis-quickstart-new-app/test.png)

1. Zamknij okienko testowe.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Zapytanie o punkt końcowy przewidywania interfejsu API systemu V3

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. Na pasku adresu przeglądarki dla ciągu zapytania upewnij się, że w adresie URL znajdują się następujące paski nazw i wartości. Jeśli nie znajdują się one w ciągu zapytania, dodaj je:

    |Para nazwa/wartość|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. Na pasku adresu przeglądarki przejdź na koniec `turn off the living room light` adresu URL i wprowadź wartość _kwerendy,_ a następnie naciśnij klawisz Enter.

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

    Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

Punkt końcowy możesz wywołać z kodu:

> [!div class="nextstepaction"]
> [Wywoływanie punktu końcowego usługi LUIS przy użyciu kodu](luis-get-started-cs-get-intent.md)

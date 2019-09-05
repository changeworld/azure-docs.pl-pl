---
title: 'Szybki Start: Tworzenie aplikacji — LUIS'
titleSuffix: Azure Cognitive Services
description: Utworzysz aplikację LUIS używającą wstępnie utworzonej domeny `HomeAutomation` na potrzeby włączania i wyłączania świateł i urządzeń. Ta wstępnie utworzona domena zapewnia intencje, jednostki i przykładowe wypowiedzi. Po zakończeniu samouczka punkt końcowy usługi LUIS będzie działał w chmurze.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 1704b62cae6375d376fc43fb7a2940cd9c717072
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382504"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Szybki start: używanie wstępnie utworzonej aplikacji do automatyzacji domu

W tym przewodniku Szybki start utworzysz aplikację usługi LUIS, która używa wstępnie utworzonej domeny `HomeAutomation` na potrzeby włączania i wyłączania świateł i urządzeń. Ta wstępnie utworzona domena zapewnia intencje, jednostki i przykładowe wypowiedzi. Po zakończeniu samouczka punkt końcowy usługi LUIS będzie działał w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

Na potrzeby tego artykułu musisz dysponować bezpłatnym kontem usługi LUIS utworzonym w portalu usługi LUIS pod adresem [https://www.luis.ai](https://www.luis.ai). 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji
Tworzenie aplikacji i zarządzanie nimi jest możliwe w obszarze **My Apps** (Moje aplikacje). 

2. Wybierz opcję **Create new app** (Utwórz nową aplikację).

    [![Zrzut ekranu przedstawiający listę aplikacji](media/luis-quickstart-new-app/app-list.png "Zrzut ekranu przedstawiający listę aplikacji")](media/luis-quickstart-new-app/app-list.png)

3. W oknie dialogowym podaj dla aplikacji nazwę „Home Automation”.

    [![Zrzut ekranu przedstawiający wyskakujące okno dialogowe Tworzenie nowej aplikacji](media/luis-quickstart-new-app/create-new-app-dialog.png "Zrzut ekranu przedstawiający wyskakujące okno dialogowe Tworzenie nowej aplikacji")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. Wybierz kulturę aplikacji. Na potrzeby tej aplikacji Home Automation wybierz język angielski. Następnie wybierz pozycję **Done** (Gotowe). Usługa LUIS utworzy aplikację Home Automation. 

    >[!NOTE]
    >Kultury nie można zmienić po utworzeniu aplikacji. 

## <a name="add-prebuilt-domain"></a>Dodawanie wstępnie utworzonej domeny

Wybierz pozycję **Prebuilt domains** (Wstępnie utworzone domeny) w okienku nawigacyjnym po lewej stronie. Następnie wyszukaj frazę „Home”. Wybierz opcję **Add domain** (Dodaj domenę).

[![Zrzut ekranu przedstawiający wywołanie domeny Home Automation w menu wstępnie utworzonej domeny](media/luis-quickstart-new-app/home-automation.png "Zrzut ekranu przedstawiający wywołanie domeny Home Automation w menu wstępnie utworzonej domeny")](media/luis-quickstart-new-app/home-automation.png)

Po pomyślnym dodaniu domeny pole wstępnie utworzonej domeny wyświetla przycisk **Remove domain** (Usuń domenę).

[![Zrzut ekranu przedstawiający domenę Home Automation z przyciskiem usuwania](media/luis-quickstart-new-app/remove-domain.png "Zrzut ekranu przedstawiający domenę Home Automation z przyciskiem usuwania")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Intencje i jednostki

Wybierz opcję **Intents** (Intencje) w okienku nawigacji po lewej stronie, aby sprawdzić intencje domeny HomeAutomation. Każda intencja zawiera przykładowe wypowiedzi.

![Zrzut ekranu listy intencji HomeAutomation](media/luis-quickstart-new-app/home-automation-intents.png "Zrzut ekranu przedstawiający listę intencji HomeAutomation")]

> [!NOTE]
> **None** (Brak) to intencja zapewniana przez wszystkie aplikacje usługi LUIS. Używa się jej do obsługi wypowiedzi, które nie odnoszą się do funkcji zapewnianej przez aplikację. 

Wybierz intencję **HomeAutomation.TurnOff**. Możesz zobaczyć, że intencja zawiera listę wypowiedzi, które są oznaczone przy użyciu jednostek.

[![Zrzut ekranu przedstawiający intencję HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Zrzut ekranu przedstawiający intencję HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Testowanie aplikacji
Po przeprowadzeniu uczenia aplikacji możesz ją przetestować. Wybierz opcję **Test** (Testuj) w górnym menu nawigacyjnym. Wpisz wypowiedź testową, np. „Turn off the lights” (Wyłącz światło) w okienku Interactive Testing (Testowanie interaktywne), a następnie naciśnij klawisz Enter. 

```
Turn off the lights
```

Sprawdź, czy intencja o najwyższym wyniku odpowiada oczekiwanej intencji dla każdej wypowiedzi testowej.

W tym przykładzie `Turn off the lights` jest poprawnie zidentyfikowany jako najpopularniejszy cel oceniania **HomeAutomation.** .

[![Zrzut ekranu przedstawiający panel Test z wyróżnioną wypowiedzią](media/luis-quickstart-new-app/test.png "Zrzut ekranu przedstawiający panel Test z wyróżnioną wypowiedzią")](media/luis-quickstart-new-app/test.png)


Wybierz pozycję **Sprawdź** , aby przejrzeć więcej informacji na temat przewidywania.

![Zrzut ekranu przedstawiający Panel testowy z wyróżnioną pozycją wypowiedź](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Ponownie wybierz opcję **Test** (Testuj), aby zwinąć okienko testowania. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-v2-api-prediction-endpoint"></a>Wysyłanie zapytań do punktu końcowego przewidywania interfejsu API v2

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Przejdź na koniec tego adresu URL i wprowadź ciąg `turn off the living room light`, a następnie naciśnij klawisz Enter. W przeglądarce zostanie wyświetlona wersja **interfejsu API v2** w punkcie końcowym protokołu HTTP.

    ```json
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.9753089
      },
      "intents": [
        {
          "intent": "HomeAutomation.TurnOff",
          "score": 0.9753089
        },
        {
          "intent": "HomeAutomation.QueryState",
          "score": 0.01027893
        },
        {
          "intent": "HomeAutomation.TurnUp",
          "score": 0.006881481
        },
        {
          "intent": "HomeAutomation.SetDevice",
          "score": 0.006786365
        },
        {
          "intent": "HomeAutomation.TurnDown",
          "score": 0.005145787
        },
        {
          "intent": "HomeAutomation.TurnOn",
          "score": 0.004114749
        },
        {
          "intent": "None",
          "score": 0.000598924
        }
      ],
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Location",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.94558233
        },
        {
          "entity": "living room light",
          "type": "HomeAutomation.DeviceName",
          "startIndex": 13,
          "endIndex": 29,
          "resolution": {
            "values": [
              "living room light"
            ]
          }
        },
        {
          "entity": "light",
          "type": "HomeAutomation.DeviceType",
          "startIndex": 25,
          "endIndex": 29,
          "resolution": {
            "values": [
              "light"
            ]
          }
        }
      ]
    }
    ```
    
## <a name="query-the-v3-api-prediction-endpoint"></a>Zbadaj punkt końcowy przewidywania interfejsu API v3

W przypadku [zapytania interfejsu API v3](luis-migration-api-v3.md)w przeglądarce Zmień żądanie https metody get, zmieniając wartości w nawiasy kątowe dla własnych wartości. 

**Adres URL v2 z metodą Get**:

Region\<https://>. API. poznawcze. Microsoft. com/Luis/**v 2.0**/Apps/\<AppID >? verbosed = true & Subscription-Key =\<YOUR_KEY > &**q =\<User-wypowiedź-Text >**

**Adres URL v3 z metodą Get**:

Region\<https://>. API. poznawcze. Microsoft. com/Luis/**v 3.0 — wersja zapoznawcza**\</Apps/AppID >/**gniazd**/**produkcyjnych**/ **? verbose**= true & Subscription-Key =\<YOUR_KEY > &**Query =\<User-wypowiedź-Text >**

W przeglądarce jest wyświetlana wersja **interfejsu API v3** odpowiedzi w punkcie końcowym protokołu HTTP.

```json
{
    "query": "turn off the lights",
    "prediction": {
        "normalizedQuery": "turn off the lights",
        "topIntent": "HomeAutomation.TurnOff",
        "intents": {
            "HomeAutomation.TurnOff": {
                "score": 0.99649024
            }
        },
        "entities": {
            "HomeAutomation.DeviceType": [
                [
                    "light"
                ]
            ],
            "$instance": {
                "HomeAutomation.DeviceType": [
                    {
                        "type": "HomeAutomation.DeviceType",
                        "text": "lights",
                        "startIndex": 13,
                        "length": 6,
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

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

Punkt końcowy możesz wywołać z kodu:

> [!div class="nextstepaction"]
> [Wywoływanie punktu końcowego usługi LUIS przy użyciu kodu](luis-get-started-cs-get-intent.md)

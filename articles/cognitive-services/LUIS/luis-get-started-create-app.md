---
title: 'Szybki Start: Tworzenie aplikacji — LUIS'
titleSuffix: Azure Cognitive Services
description: Utwórz aplikację LUIS, która używa wstępnie skompilowanej domeny `HomeAutomation` do włączania i wyłączania świateł i urządzeń. Ta preskompilowana domena oferuje intencje, jednostki i przykład wyrażenia długości. Po zakończeniu będziesz mieć punkt końcowy LUIS uruchomiony w chmurze.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 748c51e74db20ac101dc2dff0d924567acded114
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703235"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Szybki Start: korzystanie z prebudowanej aplikacji automatyzacji domowej

W tym przewodniku szybki start utworzysz aplikację LUIS, która używa wstępnie skompilowanej domeny `HomeAutomation` w celu włączania i wyłączania świateł i urządzeń. Ta preskompilowana domena oferuje intencje, jednostki i przykład wyrażenia długości. Po zakończeniu będziesz mieć punkt końcowy LUIS uruchomiony w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule potrzebujesz bezpłatnego konta usługi LUIS, które zostało utworzone w portalu LUIS w [https://www.luis.ai](https://www.luis.ai). 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-a-new-app"></a>Utwórz nową aplikację
Możesz tworzyć aplikacje i zarządzać nimi w **aplikacjach**. 

1. Wybierz pozycję **Utwórz nową aplikację**.

    [![Zrzut ekranu]przedstawiający(media/luis-quickstart-new-app/app-list.png "zrzut ekranu") listy aplikacji](media/luis-quickstart-new-app/app-list.png)

1. W oknie dialogowym Nadaj nazwę aplikacji "Automatyzacja domu".

    [Zrzut ekranu przedstawiający zrzut ekranu ![okna dialogowego Tworzenie nowej aplikacji]okno(media/luis-quickstart-new-app/create-new-app-dialog.png "dialogowe Tworzenie nowej aplikacji podręcznej")](media/luis-quickstart-new-app/create-new-app-dialog.png)

1. Wybierz kulturę aplikacji. Dla tej aplikacji w usłudze Automation Home wybierz pozycję angielski. Następnie wybierz pozycję **gotowe**. LUIS tworzy aplikację Automation Home. 

    >[!NOTE]
    >Nie można zmienić kultury po utworzeniu aplikacji. 

## <a name="add-prebuilt-domain"></a>Dodaj prezbudowaną domenę

Wybierz opcję **prekompilowane domeny** w okienku nawigacji po lewej stronie. Następnie wyszukaj ciąg "Strona główna". Wybierz pozycję **Dodaj domenę**.

[![Zrzut ekranu przedstawiający domenę automatyzacji domowej o nazwie out w menu prekompilowanej domeny](media/luis-quickstart-new-app/home-automation.png ", zrzut ekranu przedstawiający domenę automatyzacji głównej o nazwie z prekompilowanego menu domeny")](media/luis-quickstart-new-app/home-automation.png)

Po pomyślnym dodaniu domeny, w polu prebudowane domeny zostanie wyświetlony przycisk **Usuń domenę** .

[![Zrzut ekranu przedstawiający domenę automatyzacji domowej z przyciskiem Usuń](media/luis-quickstart-new-app/remove-domain.png "zrzut ekranu w domenie automatyzacji domowej z przyciskiem Usuń")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Intencje i jednostki

Wybierz pozycję **intencje** w okienku nawigacji po lewej stronie, aby przejrzeć intencje domeny HomeAutomation. Każdy cel ma przykład wyrażenia długości.

![Zrzut ekranu]przedstawiający zrzut ekranu przedstawiający listę(media/luis-quickstart-new-app/home-automation-intents.png "elementów HomeAutomation z listy")intencji HomeAutomation]

> [!NOTE]
> **Żadna** z nich nie jest zamiarem podanym przez wszystkie aplikacje Luis. Służy do obsługi wyrażenia długości, które nie odpowiadają funkcjom oferowanym przez aplikację. 

Wybierz zamiar **HomeAutomation.** wyznaczania. Można zobaczyć, że zamiara zawiera listę wyrażenia długości, które są oznaczone jednostką.

[Zrzut ekranu przedstawiający(media/luis-quickstart-new-app/home-automation-turnoff.png "zrzut ekranu") ![opcji HomeAutomation.]wy.](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Testowanie aplikacji
Po przeszkoleniu aplikacji możesz ją przetestować. Wybierz **test** w górnym obszarze nawigacji. Wpisz wypowiedź testu, jak "Wyłącz światła" w okienku interaktywne testowanie, a następnie naciśnij klawisz ENTER. 

```
Turn off the lights
```

Sprawdź, czy najczęstsze przeznaczenie oceniania odpowiada zamierzonemu zamierzeniu dla każdej testowej wypowiedź.

W tym przykładzie `Turn off the lights` jest prawidłowo zidentyfikowany jako górny cel oceniania **HomeAutomation.** wyskakujące.

[![Zrzut ekranu przedstawiający Panel testowy z wyróżnionym](media/luis-quickstart-new-app/test.png "zrzutem ekranu panelu testów z wyróżnioną") pozycją wypowiedź](media/luis-quickstart-new-app/test.png)


Wybierz pozycję **Sprawdź** , aby przejrzeć więcej informacji na temat przewidywania.

![Zrzut ekranu przedstawiający Panel testowy z wyróżnioną pozycją wypowiedź](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Wybierz ponownie pozycję **Testuj** , aby zwinąć okienko testowania. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Opublikuj aplikację, aby uzyskać adres URL punktu końcowego

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-v2-api-prediction-endpoint"></a>Wysyłanie zapytań do punktu końcowego przewidywania interfejsu API v2

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Przejdź na koniec adresu URL w adresie i wprowadź `turn off the living room light`, a następnie naciśnij klawisz ENTER. 

    #### <a name="v2-prediction-endpointtabv2"></a>[Punkt końcowy przewidywania wersji 2](#tab/V2)

    `https://<region>.api.cognitive.microsoft.com/luis/**v2.0**/apps/<appID>?subscription-key=<YOUR_KEY>&**q=<user-utterance-text>**`

    W przeglądarce zostanie wyświetlona wersja **interfejsu API v2** w punkcie końcowym protokołu HTTP.

    ```json
    {
      "query": "turn off the lights",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.995867
      },
      "entities": [
        {
          "entity": "lights",
          "type": "HomeAutomation.DeviceType",
          "startIndex": 13,
          "endIndex": 18,
          "resolution": {
            "values": [
              "light"
            ]
          }
        }
      ]
    }
    ```
    
    #### <a name="v3-prediction-endpointtabv3"></a>[Punkt końcowy przewidywania v3](#tab/V3)

    W przypadku [zapytania interfejsu API v3](luis-migration-api-v3.md)w przeglądarce Zmień żądanie https metody get, zmieniając wartości w nawiasy kątowe dla własnych wartości.     

    `https://<region>.api.cognitive.microsoft.com/luis/**v3.0-preview**/apps/<appID>/**slots**/**production**/**predict**?subscription-key=<YOUR_KEY>&**query=<user-utterance-text>**`

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
                ]
            }
        }
    }
    ```


    Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).
    
    * * * 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

Punkt końcowy można wywołać z kodu:

> [!div class="nextstepaction"]
> [Wywoływanie punktu końcowego LUIS przy użyciu kodu](luis-get-started-cs-get-intent.md)

---
title: Jednostka wyrażenia regularnego
titleSuffix: Azure Cognitive Services
description: Wyodrębnij spójnie sformatowane dane z wypowiedzi przy użyciu jednostki Regular Expression (Wyrażenie regularne).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: diberry
ms.openlocfilehash: 5e79de8dc8b4e81f427925b6e3d662bd4931804d
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497026"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Samouczek: uzyskiwanie prawidłowo sformatowanych danych z wypowiedzi
W tym samouczku utworzysz aplikację, która wyodrębnia spójnie sformatowane dane z wypowiedzi przy użyciu jednostki **Regular Expression** (Wyrażenie regularne).

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Tworzenie nowej aplikacji 
> * Dodawanie intencji
> * Dodawanie jednostki wyrażenia regularnego 
> * Szkolenie
> * Publikowanie
> * Pobieranie intencji i jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Jednostki wyrażenia regularnego

Ta aplikacja używa jednostki wyrażenia regularnego, aby wyodrębnić z wypowiedzi prawidłowo sformatowane numery formularzy działu zasobów ludzkich. Podczas gdy intencja wypowiedzi jest zawsze określana za pomocą uczenia maszynowego, ten specyficzny typ jednostki nie jest określany za pomocą uczenia maszynowego. 

**Przykładowe wypowiedzi mogą być następujące:**

|Przykładowe wypowiedzi|
|--|
|Where is HRF-123456? (Gdzie jest formularz HRF-123456?)|
|Who authored HRF-123234? (Kto jest autorem formularza HRF-123456?)|
|HRF-456098 is published in French? (Czy formularz HRF-123456 jest publikowany w języku francuskim?)|
|HRF 456098|
|Data HRF-456098?|
 
Wyrażenie regularne jest dobrym rozwiązaniem w przypadku tego typu danych, jeśli:

* dane są prawidłowo sformatowane.


## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-finding-form"></a>Tworzenie intencji do znajdowania formularza

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Wybierz pozycję **Create new intent** (Utwórz nową intencję). 

1. Wprowadź ciąg `FindForm` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe). 

    ![Zrzut ekranu przedstawiający okno dialogowe tworzenia nowej intencji z wyrazem Utilities (Narzędzia) w polu wyszukiwania](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

1. Dodaj przykładowe wypowiedzi do intencji.

    |Przykładowe wypowiedzi|
    |--|
    |Jaki jest adres URL dla formularza hrf-123456?|
    |Gdzie jest formularz hrf-345678?|
    |Kiedy formularz hrf-456098 został zaktualizowany?|
    |Czy Jan Kowalski zaktualizował formularz hrf-234639 w zeszłym tygodniu?|
    |Ile jest wersji formularza hrf-345123?|
    |Kto musi zatwierdzić formularz hrf-123456?|
    |Ile osób musi zatwierdzić formularz hrf-345678?|
    |Data hrf-234123?|
    |Autor hrf-546234?|
    |Tytuł hrf-456234?|

    [![Zrzut ekranu przedstawiający stronę Intent (Intencja) z wyróżnionymi nowymi wypowiedziami](./media/luis-quickstart-intents-regex-entity/findform-intent.png)](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Używanie jednostki wyrażenia regularnego na potrzeby prawidłowo sformatowanych danych
Jednostka wyrażenia regularnego pasująca do numeru formularza to `hrf-[0-9]{6}`. To wyrażenie regularne dopasowuje znaki literałów `hrf-`, ale ignoruje wielkość liter i warianty kulturowe. Dopasowuje cyfry z zakresu 0–9 dla dokładnie 6 cyfr.

HRF to akronim wyrażenia `human resources form`.

Usługa LUIS tokenizuje wypowiedź, jeśli zostanie ona dodana do intencji. Tokenizacja dla tych wypowiedzi dodaje spacje przed i za łącznikiem, `Where is HRF - 123456?`. Wyrażenie regularne jest stosowane do wypowiedzi w postaci nieprzetworzonej, przed tokenizacją. Ponieważ wyrażenie regularne jest stosowane do postaci _nieprzetworzonej_, nie musi obsługiwać ograniczeń wyrazów. 

W poniższych krokach utwórz jednostkę wyrażenia regularnego, aby określić dla aplikacji LUIS, jaki jest format ciągu HRF-number:

1. Wybierz pozycję **Entities** (Jednostki) w lewym panelu.

1. Wybierz przycisk **Create new entity** (Utwórz nową jednostkę) na stronie Jednostki. 

1. W podręcznym oknie dialogowym wprowadź nazwę nowej jednostki `HRF-number`, wybierz typ jednostki **RegEx**, wprowadź ciąg `hrf-[0-9]{6}` jako wartość **wyrażenia regularnego**, a następnie wybierz pozycję **Done** (Gotowe).

    ![Zrzut ekranu przedstawiający podręczne okno dialogowe z ustawieniami właściwości nowej jednostki](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

1. Wybierz pozycję **Intents** (Intencje) w menu po lewej stronie, a następnie wybierz intencję **FindForm**, aby wyświetlić wyrażenie regularne oznaczone etykietą w wypowiedziach. 

    [![Zrzut ekranu przedstawiający oznaczanie wypowiedzi za pomocą istniejącego wzorca jednostki i wyrażenia regularnego](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Ta jednostka nie jest jednostką nauczoną maszynowo, dlatego jest stosowana do wypowiedzi i wyświetlana w witrynie usługi LUIS, jak tylko zostanie utworzona.

## <a name="add-example-utterances-to-the-none-intent"></a>Dodawanie przykładowych wypowiedzi do intencji None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Trenowanie aplikacji przed testowaniem lub publikowaniem

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikowanie aplikacji w celu wysyłania zapytań z punktu końcowego

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Pobieranie przewidywania intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Przejdź na końcu adresu URL w adres, a następnie wprowadź następujące wypowiedź:

    `When were HRF-123456 and hrf-234567 published in the last year?`

    Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź jest inna, niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić intencję `FindForm` z dwoma numerami formularzy: `HRF-123456` i `hrf-234567`.

    ```json
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9988884
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9988884
        },
        {
          "intent": "None",
          "score": 0.00204812363
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        }
      ]
    }
    ```

    Używając jednostki wyrażenia regularnego, aplikacja LUIS wyodrębnia nazwane dane, co jest bardziej programowo przydatne dla aplikacji klienckiej, która otrzymuje odpowiedzi JSON.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Jednostka wyrażenia regularnego](luis-concept-entity-types.md#regular-expression-entity) — pojęcia
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku utworzono nową intencję, dodano przykładowe wypowiedzi, a następnie utworzono jednostkę wyrażenia regularnego, aby wyodrębnić z wypowiedzi poprawnie sformatowane dane. Po wyszkoleniu i opublikowaniu aplikacji zapytanie do punktu końcowego zidentyfikowało intencję i zwróciło wyodrębnione dane.

> [!div class="nextstepaction"]
> [Learn about the list entity](luis-quickstart-intent-and-list-entity.md) (Informacje na temat jednostki listy)


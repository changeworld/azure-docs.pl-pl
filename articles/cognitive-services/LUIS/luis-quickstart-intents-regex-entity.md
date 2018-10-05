---
title: 'Samouczek 3: Dane dopasowane na podstawie wyrażenia regularnego — wyodrębnianie poprawnie sformatowanych danych'
titleSuffix: Azure Cognitive Services
description: Wyodrębnij spójnie sformatowane dane z wypowiedzi przy użyciu jednostki Regular Expression (Wyrażenie regularne).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 06e212ef756fda9224b38b41c69c7c4eccfb9796
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159860"
---
# <a name="tutorial-3-extract-well-formatted-data"></a>Samouczek 3: Wyodrębnianie poprawnie sformatowanych danych
W tym samouczku zmodyfikujesz aplikację Human Resources, aby wyodrębnić spójnie sformatowane dane z wypowiedzi przy użyciu jednostki **Regular Expression** (Wyrażenie regularne).

Przeznaczeniem jednostki jest wyodrębnienie ważnych danych zawartych w wypowiedzi. Ta aplikacja używa jednostki wyrażenia regularnego, aby wyodrębnić z wypowiedzi sformatowane numery formularzy działu zasobów ludzkich. Podczas gdy intencja wypowiedzi jest zawsze określana za pomocą uczenia maszynowego, ten specyficzny typ jednostki nie jest określany za pomocą uczenia maszynowego. 

**Przykładowe wypowiedzi mogą być następujące:**

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
Wyrażenie regularne jest dobrym rozwiązaniem w przypadku tego typu danych, jeśli:

* dane są prawidłowo sformatowane.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Korzystanie z istniejącej aplikacji samouczka
> * Dodawanie intencji FindForm
> * Dodawanie jednostki wyrażenia regularnego 
> * Szkolenie
> * Publikowanie
> * Pobieranie intencji i jednostek z punktu końcowego

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Korzystanie z istniejącej aplikacji
Przejdź do aplikacji o nazwie **HumanResources** utworzonej w ostatnim samouczku. 

Jeśli nie masz aplikacji HumanResources z poprzedniego samouczka, wykonaj następujące kroki:

1. Pobierz i zapisz [plik JSON aplikacji](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-prebuilts-HumanResources.json).

2. Zaimportuj plik JSON do nowej aplikacji.

3. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `regex`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL. 

## <a name="findform-intent"></a>Intencja FindForm

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Wybierz pozycję **Create new intent** (Utwórz nową intencję). 

3. Wprowadź ciąg `FindForm` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe). 

    ![Zrzut ekranu przedstawiający okno dialogowe tworzenia nowej intencji z wyrazem Utilities (Narzędzia) w polu wyszukiwania](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. Dodaj przykładowe wypowiedzi do intencji.

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

    [ ![Zrzut ekranu przedstawiający stronę Intent (Intencja) z wyróżnionymi nowymi wypowiedziami](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    Aplikacja zawiera wstępnie utworzoną jednostkę numeru dodaną w poprzednim samouczku, zatem każdy numer formularza jest otagowany. Może to być wystarczające w przypadku Twojej aplikacji klienckiej, ale numer nie zostanie oznaczona etykietą typu numeru. Utworzenie nowej jednostki z odpowiednią nazwę umożliwia aplikacji klienckiej właściwe przetworzenie jednostki zwróconej z aplikacji LUIS.

    [!include[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="regular-expression-entity"></a>Jednostka wyrażenia regularnego 
Jednostka wyrażenia regularnego pasująca do numeru formularza to `hrf-[0-9]{6}`. To wyrażenie regularne dopasowuje znaki literałów `hrf-`, ale ignoruje wielkość liter i warianty kulturowe. Dopasowuje cyfry z zakresu 0–9 dla dokładnie 6 cyfr.

HRF to akronim wyrażenia `human resources form`.

Usługa LUIS tokenizuje wypowiedź, jeśli zostanie ona dodana do intencji. Tokenizacja dla tych wypowiedzi dodaje spacje przed i za łącznikiem, `Where is HRF - 123456?`. Wyrażenie regularne jest stosowane do wypowiedzi w postaci nieprzetworzonej, przed tokenizacją. Ponieważ wyrażenie regularne jest stosowane do postaci _nieprzetworzonej_, nie musi obsługiwać ograniczeń wyrazów. 

W poniższych krokach utwórz jednostkę wyrażenia regularnego, aby określić dla aplikacji LUIS, jaki jest format ciągu HRF-number:

1. Wybierz pozycję **Entities** (Jednostki) w lewym panelu.

2. Wybierz przycisk **Create new entity** (Utwórz nową jednostkę) na stronie Jednostki. 

3. W podręcznym oknie dialogowym wprowadź nazwę nowej jednostki `HRF-number`, wybierz typ jednostki **RegEx**, wprowadź ciąg `hrf-[0-9]{6}` jako wartość **wyrażenia regularnego**, a następnie wybierz pozycję **Done** (Gotowe).

    ![Zrzut ekranu przedstawiający podręczne okno dialogowe z ustawieniami właściwości nowej jednostki](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. Wybierz pozycję **Intents** (Intencje) w menu po lewej stronie, a następnie wybierz intencję **FindForm**, aby wyświetlić wyrażenie regularne oznaczone etykietą w wypowiedziach. 

    [![Zrzut ekranu przedstawiający oznaczanie wypowiedzi za pomocą istniejącego wzorca jednostki i wyrażenia regularnego](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Ta jednostka nie jest jednostką nauczoną maszynowo, dlatego etykieta jest stosowana do wypowiedzi i wyświetlana w witrynie usługi LUIS, jak tylko zostanie utworzona.

## <a name="train"></a>Szkolenie

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikowanie

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Pobieranie intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `When were HRF-123456 and hrf-234567 published in the last year?`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź jest inna, niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić intencję `FindForm` z dwoma numerami formularzy: `HRF-123456` i `hrf-234567`.

    ```JSON
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9993477
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9993477
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0206110049
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00533067342
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.004215215
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00209096959
        },
        {
          "intent": "None",
          "score": 0.0017655947
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00109490135
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.0005704638
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.000525338168
        }
      ],
      "entities": [
        {
          "entity": "last year",
          "type": "builtin.datetimeV2.daterange",
          "startIndex": 53,
          "endIndex": 61,
          "resolution": {
            "values": [
              {
                "timex": "2017",
                "type": "daterange",
                "start": "2017-01-01",
                "end": "2018-01-01"
              }
            ]
          }
        },
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
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    Numery w wypowiedzi są zwracane dwa razy, raz jako nowa jednostka `hrf-number` i raz jako wstępnie utworzona jednostka `number`. Jak pokazuje ten przykład, wypowiedź może zawierać więcej niż jedną jednostkę i więcej niż jedną jednostkę tego samego typu. Używając jednostki wyrażenia regularnego, aplikacja LUIS wyodrębnia nazwane dane, co jest bardziej programowo przydatne dla aplikacji klienckiej, która otrzymuje odpowiedzi JSON.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono nową intencję, dodano przykładowe wypowiedzi, a następnie utworzono jednostkę wyrażenia regularnego, aby wyodrębnić z wypowiedzi poprawnie sformatowane dane. Po wyszkoleniu i opublikowaniu aplikacji zapytanie do punktu końcowego zidentyfikowało intencję i zwróciło wyodrębnione dane.

> [!div class="nextstepaction"]
> [Learn about the list entity](luis-quickstart-intent-and-list-entity.md) (Informacje na temat jednostki listy)


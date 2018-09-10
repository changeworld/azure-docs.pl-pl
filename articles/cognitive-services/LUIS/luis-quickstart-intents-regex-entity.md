---
title: Samouczek dotyczący tworzenia aplikacji LUIS w celu uzyskania danych dopasowanych na podstawie wyrażenia regularnego — Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć prostą aplikację LUIS, używając intencji oraz jednostki wyrażenia regularnego do wyodrębnienia danych.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 9672215c8cc5f95775e3b7fba74b27379a58ff49
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162934"
---
# <a name="tutorial-3-add-regular-expression-entity"></a>Samouczek 3. Dodawanie jednostki wyrażenia regularnego
W tym samouczku utworzysz aplikację, która pokazuje, jak można wyodrębnić spójnie sformatowane dane z wypowiedzi przy użyciu jednostki **Regular Expression** (Wyrażenie regularne).


<!-- green checkmark -->
> [!div class="checklist"]
> * Omówienie jednostek wyrażenia regularnego 
> * Używanie aplikacji LUIS dla domeny zasobów ludzkich (HR, Human Resources) z intencją FindForm
> * Dodawanie jednostki wyrażenia regularnego w celu wyodrębniania numeru formularza z wypowiedzi
> * Uczenie i publikowanie aplikacji
> * Wysyłanie zapytań do punktu końcowego aplikacji w celu wyświetlenia odpowiedzi JSON usługi LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli nie masz aplikacji Human Resources z samouczka dotyczącego [wstępnie utworzonych jednostek](luis-tutorial-prebuilt-intents-entities.md), [zaimportuj](luis-how-to-start-new-app.md#import-new-app) obiekt JSON do nowej aplikacji w witrynie usługi [LUIS](luis-reference-regions.md#luis-website) z repozytorium Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-prebuilts-HumanResources.json).

Jeśli chcesz zachować oryginalną aplikację Human Resources, sklonuj tę wersję na stronie [Settings](luis-how-to-manage-versions.md#clone-a-version) (Ustawienia) i nadaj jej nazwę `regex`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. 


## <a name="purpose-of-the-regular-expression-entity"></a>Przeznaczenie jednostki wyrażenia regularnego
Przeznaczeniem jednostki jest uzyskanie ważnych danych zawartych w wypowiedzi. Aplikacja używa jednostki wyrażenia regularnego, aby wyodrębnić z wypowiedzi sformatowane numery formularzy działu zasobów ludzkich. Nie korzysta ona z uczenia maszynowego. 

Proste przykładowe wypowiedzi mogą być następujące:

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
```

Wersje wypowiedzi ze skrótami lub żargonem mogą być następujące:

```
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
Jednostka wyrażenia regularnego pasująca do numeru formularza to `hrf-[0-9]{6}`. To wyrażenie regularne dopasowuje znaki literałów `hrf -`, ale ignoruje wielkość liter i warianty kulturowe. Dopasowuje cyfry z zakresu 0–9 dla dokładnie 6 cyfr.

HRF to angielski skrót od wyrażenia „formularz zasobów ludzkich” (human resources form).

### <a name="tokenization-with-hyphens"></a>Tokenizacja za pomocą łączników
Aplikacja LUIS tokenizuje wypowiedź, jeśli wypowiedź zostanie dodana do intencji. Tokenizacja dla tych wypowiedzi dodaje spacje przed i za łącznikiem, `Where is HRF - 123456?`. Wyrażenie regularne jest stosowane do wypowiedzi w postaci nieprzetworzonej, przed tokenizacją. Ponieważ wyrażenie regularne jest stosowane do postaci _nieprzetworzonej_, nie musi obsługiwać ograniczeń wyrazów. 


## <a name="add-findform-intent"></a>Dodawanie intencji FindForm

1. Upewnij się, że aplikacja Human Resources znajduje się w sekcji **Build** (Kompilacja) aplikacji LUIS. Możesz przejść do tej sekcji, wybierając pozycję **Build** (Kompilacja) na górnym pasku menu po prawej stronie. 

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

## <a name="create-an-hrf-number-regular-expression-entity"></a>Tworzenie jednostki wyrażenia regularnego HRF-number 
W poniższych krokach utwórz jednostkę wyrażenia regularnego, aby określić dla aplikacji LUIS, jaki jest format ciągu HRF-number:

1. Wybierz pozycję **Entities** (Jednostki) w lewym panelu.

2. Wybierz przycisk **Create new entity** (Utwórz nową jednostkę) na stronie Jednostki. 

3. W podręcznym oknie dialogowym wprowadź nazwę nowej jednostki `HRF-number`, wybierz typ jednostki **RegEx**, wprowadź ciąg `hrf-[0-9]{6}` jako wyrażenie regularne, a następnie wybierz pozycję **Done** (Gotowe).

    ![Zrzut ekranu przedstawiający podręczne okno dialogowe z ustawieniami właściwości nowej jednostki](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. Wybierz pozycję **Intents** (Intencje), a następnie wybierz intencję **FindForm**, aby wyświetlić wyrażenie regularne oznaczone etykietą w wypowiedziach. 

    [![Zrzut ekranu przedstawiający oznaczanie wypowiedzi za pomocą istniejącego wzorca jednostki i wyrażenia regularnego](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Ta jednostka nie jest jednostką nauczoną maszynowo, dlatego etykieta jest stosowana do wypowiedzi i wyświetlana w witrynie usługi LUIS, jak tylko zostanie utworzona.

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą różnych wypowiedzi

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `When were HRF-123456 and hrf-234567 published in the last year?`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź jest inna, niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić intencję `FindForm` z dwoma numerami formularzy: `HRF-123456` i `hrf-234567`.

    ```
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

## <a name="what-has-this-luis-app-accomplished"></a>Co wykonała ta aplikacja LUIS?
Ta aplikacja zidentyfikowała intencję i zwróciła wyodrębnione dane. 

Twój czatbot ma teraz wystarczająco dużo informacji, aby określić akcję główną, `FindForm`, i numery formularzy w wyszukiwaniu. 

## <a name="where-is-this-luis-data-used"></a>Gdzie są używane te dane usługi LUIS? 
Usługa LUIS skończyła obsługiwać to żądanie. Aplikacja wywołująca, taka jak czatbot, może pobrać wynik topScoringIntent (najwyżej oceniana intencja) oraz numery formularzy i przeszukać interfejs API innej firmy. Aplikacja LUIS nie wykonuje tej pracy. Aplikacja LUIS określa jedynie intencję użytkownika i wyodrębnia dane na temat tej intencji. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Learn about the list entity](luis-quickstart-intent-and-list-entity.md) (Informacje na temat jednostki listy)


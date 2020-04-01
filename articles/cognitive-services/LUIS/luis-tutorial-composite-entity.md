---
title: 'Samouczek: Samouczek jednostki złożonej — USŁUGA LUIS'
description: W tym samouczku dodaj jednostkę złożoną, aby połączyć wyodrębnione dane różnych typów do jednej jednostki zawierającej. Łącząc dane, aplikacja kliencka może łatwo wyodrębnić powiązane dane w różnych typach danych.
ms.topic: tutorial
ms.date: 03/31/2020
ms.openlocfilehash: 5b8185a56c54ec92ce8ceaf1cd029dd31f6e709c
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478685"
---
# <a name="tutorial-group-and-extract-related-data"></a>Samouczek: Grupowanie i wyodrębnianie powiązanych danych
W tym samouczku dodaj jednostkę złożoną, aby połączyć wyodrębnione dane różnych typów do jednej jednostki zawierającej. Łącząc dane, aplikacja kliencka może łatwo wyodrębnić powiązane dane w różnych typach danych.

Celem jednostki złożonej jest grupowanie powiązanych jednostek w jednostkę kategorii nadrzędnej. Informacje istnieją jako oddzielne jednostki przed utworzeniem kompozytu.

Jednostka złożona jest dobrze dopasowana do tego typu danych, ponieważ dane:

* Są ze sobą spokrewnione.
* Użyj różnych typów encji.
* Te informacje należy grupować i przetwarzać jako całość w aplikacji klienckiej.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Tworzenie intencji
> * Dodawanie jednostki złożonej
> * Szkolenie
> * Publikowanie
> * Pobieranie intencji i jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importowanie aplikacji przykładowej

1.  Pobierz i zapisz [plik JSON aplikacji](
https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json?raw=true) z listy jednostki samouczka.

2. Zaimportuj JSON do nowej aplikacji za pomocą [portalu usługi LUIS](https://www.luis.ai).

3. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `composite`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

## <a name="composite-entity"></a>Jednostka złożona

W tej aplikacji nazwa działu jest zdefiniowana w jednostce **lista działów** i zawiera synonimy.

**Intencja transferu TransferEmployeeToDepartment** ma przykładowe wypowiedzi, aby zażądać przeniesienia pracownika do nowego działu.

Przykładowe wypowiedzi dla tej intencji obejmują:

|Przykładowe wypowiedzi|
|--|
|Move John W. Smith to the accounting department (Przenieś Johna W. Smitha do działu księgowości)|
|Transfer Jill Jones from to R&D (Przenieś Jill Jones z do zespołu badawczo-rozwojowego)|

Żądanie przeniesienia powinno zawierać nazwę działu i nazwę pracownika.

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Dodaj wstępnie skompilowany encję PersonName, aby ułatwić wyodrębnianie typu wspólnego

Usługa LUIS zawiera kilka wstępnie utworzonych jednostek na potrzeby typowych działań związanych z wyodrębnianiem danych.

1. Wybierz **pozycję Zbuduj** z górnej nawigacji, a następnie wybierz pozycję **Elementy** z lewego menu nawigacyjnego.

1. Naciśnij przycisk **Manage prebuilt entity** (Zarządzaj wstępnie utworzoną jednostką).

1. Wybierz **[personname](luis-reference-prebuilt-person.md)** z listy wstępnie utworzonych elementów, a następnie wybierz pozycję **Gotowe**.

    ![Zrzut ekranu przedstawiający pozycję number (liczba) wybraną w oknie dialogowym wstępnie skompilowanych jednostek](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Ta jednostka ułatwia dodawanie rozpoznawania nazw do aplikacji klienckiej.

## <a name="create-composite-entity-from-example-utterances"></a>Tworzenie jednostki złożonej na podstawie wypowiedzi przykładowych

1. Wybierz pozycję **Intents** (Intencje) w obszarze nawigacji po lewej stronie.

1. Wybierz **pozycję TransferEmployeeToDepartment** z listy intencji.

1. W wypowiedź `place John Jackson in engineering`, wybierz personName jednostki, `John Jackson`a następnie wybierz Wrap w **złożonej jednostki** na liście menu podręcznego dla następującego wypowiedź.

    ![Zrzut ekranu przedstawiający wybieranie kompozytu zawinięcia w oknie dialogowym rozwijanego](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Następnie natychmiast wybierz ostatnią `engineering` jednostkę, w wypowiedź. Zielony pasek jest rysowany pod wybranymi wyrazami wskazującymi element złożony. W menu podręcznym wprowadź nazwę `TransferEmployeeInfo` złożoną, a następnie wybierz enter.

    ![Zrzut ekranu przedstawiający wprowadzanie nazwy złożonej w oknie dialogowym listy rozwijanej](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. W **jaki typ encji chcesz utworzyć?**, wszystkie wymagane pola `personName` znajdują `Department`się na liście: i . Wybierz pozycję **Done** (Gotowe). Należy zauważyć, że wstępnie utworzony element, personName, został dodany do jednostki złożonej. Jeśli można mieć wstępnie utworzone jednostki pojawiają się między początku i końcowego tokenów jednostki złożonej, jednostka złożona musi zawierać te wstępnie utworzone jednostki. Jeśli wstępnie utworzone jednostki nie są uwzględniane, jednostka złożona nie jest poprawnie przewidywane, ale każdy pojedynczy element jest.

    ![Zrzut ekranu przedstawiający wprowadzanie nazwy złożonej w oknie dialogowym listy rozwijanej](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Etykieta przykładowe wypowiedzi z jednostki złożonej

1. W każdym wypowiedź przykład wybierz jednostki po lewej stronie, która powinna być w kompozyt. Następnie wybierz **pozycję Zawijanie w element złożony**.

1. Zaznacz ostatnie słowo w encji złożonej, a następnie wybierz **polecenie TransferEmployeeInfo** z wyskakującego menu.

1. Sprawdź wszystkie wypowiedzi w intencji są oznaczone za pomocą jednostki złożonej.

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trenowanie aplikacji w celu umożliwienia testowania zmian w intencji

Aby wyszkolić aplikację, wybierz opcję **Pociąg**. Szkolenia stosuje zmiany, takie jak nowe jednostki i wypowiedzi oznaczone etykietą, do aktywnego modelu.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publikowanie aplikacji w celu uzyskania do niej dostępu z punktu końcowego HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Pobieranie przewidywania intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Move Jill Jones to DevOps`. Ostatni parametr ciągu zapytania to `q`, czyli query (zapytanie) wypowiedzi.

    Ponieważ ten test jest sprawdzenie kompozyt jest wyodrębniany poprawnie, test może zawierać istniejące wypowiedź próbki lub nowy wypowiedź. Dobrym testem jest uwzględnienie wszystkich jednostek podrzędnych w jednostce złożonej.

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

   Ten wypowiedź zwraca tablicę jednostek złożonych. Każda jednostka otrzymuje typ i wartość. Aby znaleźć większą precyzję dla każdej jednostki podrzędnej, należy użyć kombinacji typu i wartości z elementu tablicy złożonej, aby znaleźć odpowiedni element w tablicy jednostek.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Samouczek encji lista](luis-quickstart-intents-only.md)
* Informacje koncepcyjne [jednostki złożonej](luis-concept-entity-types.md)
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono encję złożoną do hermetyzacji istniejących jednostek. Dzięki temu aplikacja kliencka, aby znaleźć grupę powiązanych danych w różnych typów danych, aby kontynuować konwersację. Aplikacja kliencka dla tej aplikacji Zasobów Ludzkich może zapytać, jaki dzień i godzina musi rozpocząć i zakończyć przeniesienie. Może również zapytać o inne logistyki ruchu, takich jak telefon fizyczny.

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów z niepewnymi przewidywaniami przez przeglądanie wypowiedzi punktu końcowego](luis-tutorial-review-endpoint-utterances.md)

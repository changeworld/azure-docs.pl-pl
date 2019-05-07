---
title: Samouczek złożonego jednostki
titleSuffix: Azure Cognitive Services
description: Dodaj złożonego jednostki pakietów wyodrębnione dane różnych typów do pojedynczej jednostki zawierającej. Przez tworzenie pakietów danych, aplikacja kliencka może łatwo wyodrębnić powiązane dane w różnych typów danych.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/19/2019
ms.author: diberry
ms.openlocfilehash: a5cedec4937da276a4848498ad29a723f6e41ba3
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073700"
---
# <a name="tutorial-group-and-extract-related-data"></a>Samouczek: Grupowanie i wyodrębnianie powiązanych danych
W tym samouczku należy dodać złożonego jednostki pakietów wyodrębnione dane różnych typów do pojedynczej jednostki zawierającej. Przez tworzenie pakietów danych, aplikacja kliencka może łatwo wyodrębnić powiązane dane w różnych typów danych.

Złożone jednostki ma na celu grupowanie powiązanych jednostek w encji kategorii nadrzędnej. Taka informacja istnieje jako osobne jednostki, przed utworzeniem złożonego. 

Złożone jednostki jest odpowiednia dla danych tego typu, ponieważ dane:

* Są ze sobą powiązane. 
* Używać różnych typów jednostek.
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

1.  Pobierz i Zapisz [pliku JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json) z samouczka jednostki listy.

2. Zaimportuj plik JSON do nowej aplikacji.

3. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `composite`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

## <a name="composite-entity"></a>Złożone jednostki

W tej aplikacji, nazwa działu jest zdefiniowana w **działu** listy jednostek i obejmuje synonimy. 

**TransferEmployeeToDepartment** celem ma wypowiedzi przykładowe żądanie, pracownik można przenieść do nowego działu. 

Przykład wypowiedzi konwersji między innymi:

|Przykładowe wypowiedzi|
|--|
|Move John W. Smith to the accounting department (Przenieś Johna W. Smitha do działu księgowości)|
|Transfer Jill Jones from to R&D (Przenieś Jill Jones z do zespołu badawczo-rozwojowego)|
 
Żądanie przeniesienia powinien zawierać nazwę działu i nazwiska pracownika. 

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Dodaj PersonName wstępnie utworzone jednostki ułatwiające wykonywanie typowych wyodrębniania typu danych

Usługa LUIS zawiera kilka wstępnie utworzonych jednostek na potrzeby typowych działań związanych z wyodrębnianiem danych. 

1. Wybierz **kompilacji** w górnym menu nawigacyjnym i w wybierz **jednostek** menu nawigacji po lewej stronie.

1. Naciśnij przycisk **Manage prebuilt entity** (Zarządzaj wstępnie utworzoną jednostką).

1. Wybierz **[PersonName](luis-reference-prebuilt-person.md)** wybierz z listy wstępnie utworzonych jednostek następnie **gotowe**.

    ![Zrzut ekranu przedstawiający pozycję number (liczba) wybraną w oknie dialogowym wstępnie skompilowanych jednostek](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Ta jednostka ułatwia dodawanie rozpoznawania nazwy do aplikacji klienta.

## <a name="create-composite-entity-from-example-utterances"></a>Tworzenie złożonego jednostki na podstawie przykład wypowiedzi

1. Wybierz pozycję **Intents** (Intencje) w obszarze nawigacji po lewej stronie.

1. Wybierz **TransferEmployeeToDepartment** z listy opcji.

1. W polu wypowiedź `place John Jackson in engineering`, wybierz jednostkę personName `John Jackson`, a następnie wybierz **opakować w jednostce złożone** na liście menu podręczne dla następujących wypowiedź. 

    ![Zrzut ekranu przedstawiający wybierając zawijania złożonego w listy rozwijanej w oknie dialogowym](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Następnie od razu wybierz ostatni obiekt `engineering` w wypowiedź. Zielony pasek jest rysowana w ramach wybranego słowa wskazujący złożonego jednostki. W menu podręcznym wprowadź nazwę złożonego `TransferEmployeeInfo` a następnie wybierz enter. 

    ![Zrzut ekranu przedstawiający wprowadzanie nazwy w liście rozwijanej okna dialogowego](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. W **jakiego typu jednostki, czy chcesz utworzyć?**, wszystkie pola wymagane znajdują się na liście: `personName` i `Department`. Wybierz pozycję **Done** (Gotowe). Zwróć uwagę, czy wstępnie utworzone jednostki, personName, został dodany do obiektu złożonego. Jeśli może mieć wstępnie utworzone jednostki, pojawiają się między początkowe i końcowe tokenów złożonych jednostki, jednostki złożonego musi zawierać tych wstępnie utworzonych jednostek. Nie dołączono ze wstępnie utworzonych jednostek, złożonego jednostki nie jest poprawnie przewidzieć, ale jest każdego pojedynczego elementu.

    ![Zrzut ekranu przedstawiający wprowadzanie nazwy w liście rozwijanej okna dialogowego](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Wypowiedzi przykład etykiety ze złożonego jednostki

1. W każdym wypowiedź przykład wybierz jednostki najdalej po lewej stronie, która powinna znajdować się w złożonego. Następnie wybierz pozycję **opakować w jednostce złożone**.

1. Wybierz ostatni wyraz w jednostce złożone, a następnie wybierz **TransferEmployeeInfo** z menu podręcznego. 

1. Sprawdź, czy wszystkie wypowiedzi w celem są oznaczone etykietami z jednostką złożonego. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trenowanie aplikacji w celu umożliwienia testowania zmian w intencji 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikowanie aplikacji w celu umożliwienia wysyłania zapytań z punktu końcowego do trenowanego modelu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Pobieranie przewidywania intencji i jednostek z punktu końcowego 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Move Jill Jones to DevOps`. Ostatni parametr querystring jest `q`, zapytanie wypowiedź. 

    Ponieważ ten test jest upewnij się, że złożonego jest wyodrębniany poprawnie, test można dołączyć istniejącego wypowiedź próbki lub nowe wypowiedź. Dobry test umożliwiający jest uwzględnienie wszystkich obiektów podrzędnych w jednostce złożone.

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

   Ta wypowiedź zwraca tablicę złożonego jednostek. Każda jednostka jest danego typu i wartości. Aby uzyskać większą dokładność dla każdej jednostki podrzędne, należy użyć kombinacji typu i wartości z elementu tablicy złożone Aby znaleźć odpowiedni element w tablicy jednostek.  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Samouczek jednostki](luis-quickstart-intents-only.md)
* [Złożone jednostki](luis-concept-entity-types.md) informacje koncepcyjne
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku tworzony złożonego jednostki do hermetyzacji istniejących jednostek. Dzięki temu aplikacja kliencka można znaleźć grupy powiązanych danych w różnych typach danych, aby kontynuować rozmowę. Aplikacja kliencka dla tej aplikacji do zarządzania zasobami ludzkimi zapytać, jakie datę i godzinę przeniesienie musi rozpoczynać się i kończyć. Można również zadać pytanie dotyczące innych Logistyka przenoszenia, takim jak telefon fizycznych. 

> [!div class="nextstepaction"] 
> [Dowiedz się, jak dodać jednostki prostej listy fraz](luis-quickstart-primary-and-secondary-data.md)  

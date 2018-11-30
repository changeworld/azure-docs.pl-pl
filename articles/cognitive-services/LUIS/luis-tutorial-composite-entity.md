---
title: 'Samouczek 6: Wyodrębnianie danych złożonych z jednostką złożonego usługi LUIS'
titleSuffix: Azure Cognitive Services
description: Dodaj złożonego jednostki pakietów wyodrębnione dane różnych typów do pojedynczej jednostki zawierającej. Przez tworzenie pakietów danych, aplikacja kliencka może łatwo wyodrębnić powiązane dane w różnych typów danych.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 8f7edecf1abd1f01a2f40f1420a6a85224271239
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52423505"
---
# <a name="tutorial-6-group-and-extract-related-data"></a>Samouczek 6: Grupowanie i Wyodrębnij powiązanych danych
W tym samouczku należy dodać złożonego jednostki pakietów wyodrębnione dane różnych typów do pojedynczej jednostki zawierającej. Przez tworzenie pakietów danych, aplikacja kliencka może łatwo wyodrębnić powiązane dane w różnych typów danych.

Złożone jednostki ma na celu grupowanie powiązanych jednostek w encji kategorii nadrzędnej. Taka informacja istnieje jako osobne jednostki, przed utworzeniem złożonego. Jest podobny do hierarchicznych jednostki, ale może zawierać różne typy jednostek. 

Złożone jednostki jest odpowiednia dla danych tego typu, ponieważ dane:

* Są ze sobą powiązane. 
* Używać różnych typów jednostek.
* Te informacje należy grupować i przetwarzać jako całość w aplikacji klienckiej.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Korzystanie z istniejącej aplikacji samouczka
> * Dodawanie jednostki złożonej 
> * Szkolenie
> * Publikowanie
> * Pobieranie intencji i jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Korzystanie z istniejącej aplikacji
Przejdź do aplikacji o nazwie **HumanResources** utworzonej w ostatnim samouczku. 

Jeśli nie masz aplikacji HumanResources z poprzedniego samouczka, wykonaj następujące kroki:

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-hier-HumanResources.json).

2. Zaimportuj plik JSON do nowej aplikacji.

3. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `composite`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.


## <a name="composite-entity"></a>Złożone jednostki
Tworzenie złożonego jednostki, gdy osobne jednostki mogą być grupowane logicznie to logiczne grupowanie przydaje się do aplikacji klienckiej. 

W tej aplikacji, nazwa pracowników jest zdefiniowana w **pracowników** listy jednostek i obejmuje synonimy, nazwa, adres e-mail, firmy numer wewnętrzny, numer telefonu komórkowego i Stanach Zjednoczonych Identyfikator podatkowej. 

**MoveEmployee** celem ma wypowiedzi przykładowe żądanie, pracownik można przenieść z jednego budynku i pakietu office do innego. Tworzenie nazwy są alfabetyczne: "", "B", itp. natomiast urzędy są liczbowe: "1234", "13245". 

Przykład wypowiedzi w **MoveEmployee** intencji obejmują:

|Przykładowe wypowiedzi|
|--|
|Przesuń W Jan. Nowak mógł a-2345|
|shift x12345 to h-1234 tomorrow|
 
Żądanie przeniesienia powinna zawierać pracowników (przy użyciu dowolnej synonim) i lokalizacji końcowej budynku i pakietu office. Żądanie może również obejmować źródłowy pakietu office, a także określonej dacie przeniesienie powinno mieć miejsce. 

Wyodrębnione dane z punktu końcowego powinna zawierać te informacje i zwrócić go w `RequestEmployeeMove` złożonego jednostki:

```JSON
"compositeEntities": [
  {
    "parentType": "RequestEmployeeMove",
    "value": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
    "children": [
      {
        "type": "builtin.datetimeV2.datetime",
        "value": "march 3 2 p.m"
      },
      {
        "type": "Locations::Destination",
        "value": "z - 2345"
      },
      {
        "type": "Employee",
        "value": "jill jones"
      },
      {
        "type": "Locations::Origin",
        "value": "a - 1234"
      }
    ]
  }
]
```

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Na **intencji** wybierz opcję **MoveEmployee** intencji. 

3. Wybierz ikonę lupy, na pasku narzędzi, aby filtrować listę wypowiedzi. 

    [![](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "Zrzut ekranu usługi LUIS na intencje \"MoveEmployee\" z wyróżnionym przyciskiem szkło powiększające i")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. Wprowadź `tomorrow` w polu tekstowym filtru, aby znaleźć wypowiedź `shift x12345 to h-1234 tomorrow`.

    [![](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "Zrzut ekranu usługi LUIS na intencje \"MoveEmployee\" za pomocą filtru \"jutro\" wyróżniony")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    Inną metodą jest filtrowanie jednostki według datetimeV2, wybierając **filtry jednostki** polecenie **datetimeV2** z listy. 

5. Wybierz pierwszy obiekt `Employee`, a następnie wybierz **opakować w jednostce złożone** na liście menu podręcznego. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-1.png "Zrzut ekranu usługi LUIS na intencje \"MoveEmployee\", wybierając pierwszy obiekt złożony wyróżnione")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. Następnie od razu wybierz ostatni obiekt `datetimeV2` w wypowiedź. Zielony pasek jest rysowana w ramach wybranego słowa wskazujący złożonego jednostki. W menu podręcznym wprowadź nazwę złożonego `RequestEmployeeMove` a następnie wybierz enter. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-2.png "Zrzut ekranu usługi LUIS na intencje \"MoveEmployee\", wybierając ostatni obiekt w jednostce złożone i tworzenia wyróżniony")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. W **jakiego typu jednostki, czy chcesz utworzyć?**, prawie wszystkie pola wymagane znajdują się na liście. Brak tylko lokalizacji źródłowej. Wybierz **Dodawanie jednostki podrzędne**, wybierz opcję **Locations::Origin** z listy istniejących jednostek, następnie wybierz pozycję **gotowe**. 

    Należy zauważyć, że wstępnie utworzone jednostki, number, została dodana do obiektu złożonego. Jeśli może mieć wstępnie utworzone jednostki, pojawiają się między początkowe i końcowe tokenów złożonych jednostki, jednostki złożonego musi zawierać tych wstępnie utworzonych jednostek. Nie dołączono ze wstępnie utworzonych jednostek, złożonego jednostki nie jest poprawnie przewidzieć, ale jest każdego pojedynczego elementu.

    ![Zrzut ekranu usługi LUIS na intencje "MoveEmployee" dodanie innej jednostki, w oknie podręcznym](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. Wybierz ikonę lupy na pasku narzędzi, aby usunąć filtr. 

9. Usuń słowo `tomorrow` z filtru, aby ponownie wyświetlić wszystkie wyrażenia o przykład. 

## <a name="label-example-utterances-with-composite-entity"></a>Wypowiedzi przykład etykiety ze złożonego jednostki


1. W każdym wypowiedź przykład wybierz jednostki najdalej po lewej stronie, która powinna znajdować się w złożonego. Następnie wybierz pozycję **opakować w jednostce złożone**.

    [![](media/luis-tutorial-composite-entity/hr-label-entity-1.png "Zrzut ekranu usługi LUIS na intencje \"MoveEmployee\", wybierając pierwszy obiekt złożony wyróżnione")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. Wybierz ostatni wyraz w jednostce złożone, a następnie wybierz **RequestEmployeeMove** z menu podręcznego. 

    [![](media/luis-tutorial-composite-entity/hr-label-entity-2.png "Zrzut ekranu usługi LUIS na intencje \"MoveEmployee\", wybierając ostatni obiekt złożony wyróżniony")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. Sprawdź, czy wszystkie wypowiedzi w celem są oznaczone etykietami z jednostką złożonego. 

    [![](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "Zrzut ekranu usługi LUIS na \"MoveEmployee\" przy użyciu wszystkich wypowiedzi etykietą")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train"></a>Szkolenie

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikowanie

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Pobieranie intencji i jednostek z punktu końcowego 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`. Ostatni parametr querystring jest `q`, zapytanie wypowiedź. 

    Ponieważ ten test jest upewnij się, że złożonego jest wyodrębniany poprawnie, test można dołączyć istniejącego wypowiedź próbki lub nowe wypowiedź. Dobry test umożliwiający jest uwzględnienie wszystkich obiektów podrzędnych w jednostce złożone.

    ```JSON
    {
      "query": "Move Jill Jones from a-1234 to z-2345 on March 3  2 p.m",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9959525
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9959525
        },
        {
          "intent": "GetJobInformation",
          "score": 0.009858314
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00728598563
        },
        {
          "intent": "FindForm",
          "score": 0.0058053555
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.005371796
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00266987388
        },
        {
          "intent": "None",
          "score": 0.00123299169
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00116407464
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00102653319
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0006628214
        }
      ],
      "entities": [
        {
          "entity": "march 3 2 p.m",
          "type": "builtin.datetimeV2.datetime",
          "startIndex": 41,
          "endIndex": 54,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2018-03-03 14:00:00"
              },
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2019-03-03 14:00:00"
              }
            ]
          }
        },
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 5,
          "endIndex": 14,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "z - 2345",
          "type": "Locations::Destination",
          "startIndex": 31,
          "endIndex": 36,
          "score": 0.9690751
        },
        {
          "entity": "a - 1234",
          "type": "Locations::Origin",
          "startIndex": 21,
          "endIndex": 26,
          "score": 0.9713137
        },
        {
          "entity": "-1234",
          "type": "builtin.number",
          "startIndex": 22,
          "endIndex": 26,
          "resolution": {
            "value": "-1234"
          }
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 32,
          "endIndex": 36,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 47,
          "endIndex": 47,
          "resolution": {
            "value": "3"
          }
        },
        {
          "entity": "2",
          "type": "builtin.number",
          "startIndex": 50,
          "endIndex": 50,
          "resolution": {
            "value": "2"
          }
        },
        {
          "entity": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "type": "RequestEmployeeMove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "RequestEmployeeMove",
          "value": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "children": [
            {
              "type": "builtin.datetimeV2.datetime",
              "value": "march 3 2 p.m"
            },
            {
              "type": "Locations::Destination",
              "value": "z - 2345"
            },
            {
              "type": "Employee",
              "value": "jill jones"
            },
            {
              "type": "Locations::Origin",
              "value": "a - 1234"
            }
          ]
        }
      ]
    }
    ```

  Ta wypowiedź zwraca tablicę złożonego jednostek. Każda jednostka jest danego typu i wartości. Aby uzyskać większą dokładność dla każdej jednostki podrzędne, należy użyć kombinacji typu i wartości z elementu tablicy złożone Aby znaleźć odpowiedni element w tablicy jednostek.  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku tworzony złożonego jednostki do hermetyzacji istniejących jednostek. Dzięki temu aplikacja kliencka można znaleźć grupy powiązanych danych w różnych typach danych, aby kontynuować rozmowę. Aplikacja kliencka dla tej aplikacji do zarządzania zasobami ludzkimi zapytać, jakie datę i godzinę przeniesienie musi rozpoczynać się i kończyć. Go również zadawać dotyczące innych Logistyka movesuch jako fizyczny telefon. 

> [!div class="nextstepaction"] 
> [Dowiedz się, jak dodać jednostki prostej listy fraz](luis-quickstart-primary-and-secondary-data.md)  

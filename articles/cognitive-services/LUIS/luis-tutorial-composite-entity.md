---
title: Samouczek dotyczący tworzenia złożonych jednostki można wyodrębnić złożonych danych — Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia złożonych jednostki w aplikacją usługi LUIS do wyodrębniania różnych typów danych jednostki.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 5f11409ff49830be97d9a13a0ab7f033d9cc1041
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494469"
---
# <a name="tutorial-6-add-composite-entity"></a>Samouczek: 6. Dodaj jednostkę złożone 
W tym samouczku należy dodać złożonego jednostki pakietów wyodrębnione dane do jednostki zawierającej.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

<!-- green checkmark -->
> [!div class="checklist"]
> * Informacje o jednostkach złożone 
> * Dodaj jednostkę złożone, aby wyodrębnić dane
> * Uczenie i publikowanie aplikacji
> * Wysyłanie zapytań do punktu końcowego aplikacji w celu wyświetlenia odpowiedzi JSON usługi LUIS

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli nie masz aplikacji Human Resources z samouczka dotyczącego [jednostki hierarchicznej](luis-quickstart-intent-and-hier-entity.md), [zaimportuj](luis-how-to-start-new-app.md#import-new-app) kod JSON do nowej aplikacji w witrynie internetowej usługi [LUIS](luis-reference-regions.md#luis-website). Aplikacja do zaimportowania znajduje się w repozytorium [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json) usługi Github.

Jeśli chcesz zachować oryginalną aplikację Human Resources, sklonuj tę wersję na stronie [Settings](luis-how-to-manage-versions.md#clone-a-version) (Ustawienia) i nadaj jej nazwę `composite`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji.  

## <a name="composite-entity-is-a-logical-grouping"></a>Złożone jednostki to logiczna grupa 
Złożone jednostki ma na celu grupowanie powiązanych jednostek w encji kategorii nadrzędnej. Taka informacja istnieje jako osobne jednostki, przed utworzeniem złożonego. Jest podobny do hierarchicznych jednostki, ale może zawierać większą liczbę typów jednostek. 

 Tworzenie złożonego jednostki, gdy osobne jednostki mogą być grupowane logicznie to logiczne grupowanie przydaje się do aplikacji klienckiej. 

W tej aplikacji, nazwa pracowników jest zdefiniowana w **pracowników** listy jednostek i obejmuje synonimy, nazwa, adres e-mail, firmy numer wewnętrzny, numer telefonu komórkowego i Stanach Zjednoczonych Identyfikator podatkowej. 

**MoveEmployee** celem ma wypowiedzi przykładowe żądanie, pracownik można przenieść z jednego budynku i pakietu office do innego. Tworzenie nazwy są alfabetyczne: "", "B", itp. natomiast urzędy są liczbowe: "1234", "13245". 

Przykład wypowiedzi w **MoveEmployee** intencji obejmują:

|Przykładowe wypowiedzi|
|--|
|Przesuń W Jan. Nowak mógł a-2345|
|shift x12345 to h-1234 tomorrow|
 
Żądanie przeniesienia powinien zawierać co najmniej pracowników (przy użyciu dowolnej synonim) i lokalizacji końcowej budynku i pakietu office. Żądanie może również obejmować źródłowy pakietu office, a także określonej dacie przeniesienie powinno mieć miejsce. 

Wyodrębnione dane z punktu końcowego powinna zawierać te informacje i zwróć w w `RequestEmployeeMove` złożonego jednostki. 

## <a name="create-composite-entity"></a>Tworzenie złożonego jednostki
1. Upewnij się, że aplikacja Human Resources znajduje się w sekcji **Build** (Kompilacja) aplikacji LUIS. Możesz przejść do tej sekcji, wybierając pozycję **Build** (Kompilacja) na górnym pasku menu po prawej stronie. 

2. Na **intencji** wybierz opcję **MoveEmployee** intencji. 

3. Wybierz ikonę lupy, na pasku narzędzi, aby filtrować listę wypowiedzi. 

    [![](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "Zrzut ekranu usługi LUIS na intencje \"MoveEmployee\" z wyróżnionym przyciskiem szkło powiększające i")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. Wprowadź `tomorrow` w polu tekstowym filtru, aby znaleźć wypowiedź `shift x12345 to h-1234 tomorrow`.

    [![](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "Zrzut ekranu usługi LUIS na intencje \"MoveEmployee\" za pomocą filtru \"jutro\" wyróżniony")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    Inną metodą jest filtrowanie jednostki według datetimeV2, wybierając **filtry jednostki** polecenie **datetimeV2** z listy. 

5. Wybierz pierwszy obiekt `Employee`, a następnie wybierz **opakować w jednostce złożone** na liście menu podręcznego. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-1.png "Zrzut ekranu usługi LUIS na intencje \"MoveEmployee\", wybierając pierwszy obiekt złożony wyróżnione")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. Następnie od razu wybierz ostatni obiekt `datetimeV2` w wypowiedź. Zielony pasek jest rysowana w ramach wybranego słowa wskazujący złożonego jednostki. W menu podręcznym wprowadź nazwę złożonego `RequestEmployeeMove` polecenie **Utwórz nowe złożone** na w menu podręcznym. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-2.png "Zrzut ekranu usługi LUIS na intencje \"MoveEmployee\", wybierając ostatni obiekt w jednostce złożone i tworzenia wyróżniony")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. W **jakiego typu jednostki, czy chcesz utworzyć?**, prawie wszystkie pola wymagane znajdują się na liście. Brak tylko lokalizacji źródłowej. Wybierz **Dodawanie jednostki podrzędne**, wybierz opcję **Locations::Origin** z listy istniejących jednostek, następnie wybierz pozycję **gotowe**. 

    ![Zrzut ekranu usługi LUIS na intencje "MoveEmployee" dodanie innej jednostki, w oknie podręcznym](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. Wybierz ikonę lupy na pasku narzędzi, aby usunąć filtr. 

## <a name="label-example-utterances-with-composite-entity"></a>Wypowiedzi przykład etykiety ze złożonego jednostki
1. W każdym wypowiedź przykład wybierz jednostki najdalej po lewej stronie, która powinna znajdować się w złożonego. Następnie wybierz pozycję **opakować w jednostce złożone**.

    [![](media/luis-tutorial-composite-entity/hr-label-entity-1.png "Zrzut ekranu usługi LUIS na intencje \"MoveEmployee\", wybierając pierwszy obiekt złożony wyróżnione")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. Wybierz ostatni wyraz w jednostce złożone, a następnie wybierz **RequestEmployeeMove** z menu podręcznego. 

    [![](media/luis-tutorial-composite-entity/hr-label-entity-2.png "Zrzut ekranu usługi LUIS na intencje \"MoveEmployee\", wybierając ostatni obiekt złożony wyróżniony")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. Sprawdź, czy wszystkie wypowiedzi w celem są oznaczone etykietami z jednostką złożonego. 

    [![](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "Zrzut ekranu usługi LUIS na \"MoveEmployee\" przy użyciu wszystkich wypowiedzi etykietą")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint"></a>Kwerendy punktu końcowego 

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

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
          "type": "requestemployeemove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "requestemployeemove",
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
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
    }
    ```

  Ta wypowiedź zwraca tablicę złożonego jednostek. Każda jednostka jest danego typu i wartości. Aby uzyskać większą dokładność dla każdej jednostki podrzędne, należy użyć kombinacji typu i wartości z elementu tablicy złożone Aby znaleźć odpowiedni element w tablicy jednostek.  

## <a name="what-has-this-luis-app-accomplished"></a>Co wykonała ta aplikacja LUIS?
Ta aplikacja zidentyfikowane zamiar zapytań języka naturalnego i zwrócony wyodrębnione dane jako nazwanej grupy. 

Twoje chatbot ma teraz wystarczających informacji do określenia akcji głównej i powiązanych szczegółów w wypowiedź. 

## <a name="where-is-this-luis-data-used"></a>Gdzie są używane te dane usługi LUIS? 
Usługa LUIS skończyła obsługiwać to żądanie. Aplikacja wywołująca, taka jak czatbot, może pobrać wynik topScoringIntent (najwyżej oceniana intencja) oraz dane z jednostki, aby wykonać kolejny krok. Usługa LUIS nie wykonuje tej pracy programowej dla bota ani dla aplikacji wywołującej. Usługa LUIS określa jedynie intencję użytkownika. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"] 
> [Dowiedz się, jak dodać jednostki prostej listy fraz](luis-quickstart-primary-and-secondary-data.md)  
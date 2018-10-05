---
title: 'Samouczek 5: relacje nadrzędny/podrzędny — jednostka hierarchiczna usługi LUIS dla danych uczonych kontekstowo'
titleSuffix: Azure Cognitive Services
description: Znajdziesz powiązane elementy danych na podstawie kontekstu. Na przykład powiązane są lokalizacje początkowa i docelowa dla fizycznego przeniesienia z jednego budynku i biura do innego budynku i biura.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 92b6327cbb97ed871cd4b10977bcd73a81494e20
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042129"
---
# <a name="tutorial-5-extract-contextually-related-data"></a>Samouczek 5: wyodrębnianie danych powiązanych kontekstowo
W tym samouczku znajdziesz powiązane elementy danych na podstawie kontekstu. Na przykład powiązane są lokalizacje początkowa i docelowa dla fizycznego przeniesienia z jednego budynku i biura do innego budynku i biura. Aby wygenerować polecenie służbowe, potrzebne są oba elementy danych. Są one powiązane ze sobą.  

Ta aplikacja określa, dokąd pracownik ma zostać przeniesiony z lokalizacji źródłowej (budynek i biuro) do lokalizacji docelowej (budynek i biuro). Używa ona jednostki hierarchicznej do określania lokalizacji w wypowiedzi. Celem jednostki **hierarchicznej** jest znalezienie powiązanych danych w wypowiedzi na podstawie kontekstu. 

Jednostka hierarchiczna jest odpowiednia dla tego typu danych, ponieważ oba elementy danych:

* Są prostymi jednostkami.
* Są ze sobą powiązane w kontekście wypowiedzi.
* Używają określonych wybranych wyrazów w celu wskazania poszczególnych lokalizacji. Przykładowe wyrazy tego typu to: from/to (od/do), leaving/headed to (opuszczać/kierować się do), away from/toward (w kierunku od/do).
* Obie lokalizacje często znajdują się w tej samej wypowiedzi. 
* Te informacje należy grupować i przetwarzać jako całość w aplikacji klienckiej.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Korzystanie z istniejącej aplikacji samouczka
> * Dodawanie intencji 
> * Dodawanie jednostki hierarchicznej lokalizacji z elementami podrzędnymi miejsca początkowego i docelowego
> * Szkolenie
> * Publikowanie
> * Pobieranie intencji i jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Korzystanie z istniejącej aplikacji
Przejdź do aplikacji o nazwie **HumanResources** utworzonej w ostatnim samouczku. 

Jeśli nie masz aplikacji HumanResources z poprzedniego samouczka, wykonaj następujące kroki:

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-list-HumanResources.json).

2. Zaimportuj plik JSON do nowej aplikacji.

3. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `hier`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL. 

## <a name="remove-prebuilt-number-entity-from-app"></a>Usuwanie wstępnie skompilowanej jednostki numeru z aplikacji
Aby zobaczyć całą wypowiedź i oznaczyć hierarchiczne elementy podrzędne, tymczasowo usuń wstępnie skompilowaną jednostkę numeru.

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Wybierz pozycję **Entities** (Jednostki) w menu po lewej stronie.

3. Wybierz wielokropek ***(...)*** po prawej stronie jednostki numeru na liście. Wybierz pozycję **Usuń**. 

## <a name="add-utterances-to-moveemployee-intent"></a>Dodawanie wypowiedzi do intencji MoveEmployee

1. Wybierz pozycję **Intents** (Intencje) z menu po lewej.

2. Wybierz pozycję **MoveEmployee** z listy intencji.

3. Dodaj następujące przykładowe wypowiedzi:

    |Przykładowe wypowiedzi|
    |--|
    |Move John W. Smith **to** a-2345|
    |Direct Jill Jones **to** b-3499|
    |Organize the move of x23456 **from** hh-2345 **to** e-0234|
    |Begin paperwork to set x12345 **leaving** a-3459 **headed to** f-34567|
    |Displace 425-555-0000 **away from** g-2323 **toward** hh-2345|

    [ ![Zrzut ekranu usługi LUIS z nowymi wypowiedziami w intencji MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

    W samouczku dotyczącym [jednostki listy](luis-quickstart-intent-and-list-entity.md) wyznaczany jest pracownik według nazwy, adresu e-mail, numeru wewnętrznego, numeru telefonu komórkowego lub federalnego numeru ubezpieczenia społecznego (Stany Zjednoczone). Te numery pracowników są używane w wypowiedziach. Poprzednie przykładowe wypowiedzi przedstawiają różne sposoby wskazywania lokalizacji źródłowych, które wyróżniono za pomocą pogrubienia. Celowo w niektórych wypowiedziach znajdują się tylko miejsca docelowe. Pomaga to usłudze LUIS zrozumieć, jak te lokalizacje są umieszczane w wypowiedzi, jeśli nie określono źródła.     

    [!include[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="create-a-location-entity"></a>Tworzenie jednostki Location (Lokalizacja)
Usługa LUIS musi zrozumieć, czym jest lokalizacja, oznaczając źródło i miejsce docelowe w wypowiedziach. Jeśli chcesz zobaczyć wypowiedź w widoku tokenu (nieprzetworzonym), na pasku nad wypowiedziami wybierz przełącznik oznaczony etykietą **Entities View** (Widok jednostek). Po przełączeniu przełącznika kontrolka będzie mieć etykietę **Tokens View** (Widok tokenów).

Przeanalizujmy następującą wypowiedź:

```JSON
mv Jill Jones from a-2349 to b-1298
```

W wypowiedzi są podane dwie lokalizacje: `a-2349` i `b-1298`. Załóżmy, że litera odpowiada nazwie budynku, a numer wskazuje biuro w tym budynku. Dobrym rozwiązaniem jest pogrupowanie ich jako elementów podrzędnych jednostki hierarchicznej `Locations`, ponieważ oba elementy danych muszą zostać wyodrębnione z wypowiedzi w celu wykonania żądania w aplikacji klienckiej i są ze sobą powiązane. 
 
Jeśli obecny jest tylko jeden element podrzędny jednostki hierarchicznej (lokalizacja początkowa lub docelowa), też jest wyodrębniany. Nie jest konieczne znalezienie wszystkich elementów podrzędnych, aby wyodrębnić jeden lub część z nich. 

1. W wypowiedzi `Displace 425-555-0000 away from g-2323 toward hh-2345` zaznacz wyraz `g-2323`. Zostanie wyświetlone menu rozwijane z polem tekstowym w górnej części. Wprowadź nazwę jednostki `Locations` w polu tekstowym, a następnie wybierz polecenie **Create new entity** (Utwórz nową jednostkę) w menu rozwijanym. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "Zrzut ekranu przedstawiający tworzenie nowej jednostki na stronie intencji")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. W oknie podręcznym wybierz typ jednostki **Hierarchical** (Hierarchiczna) z elementami `Origin` i `Destination` jako podrzędnymi. Wybierz pozycję **Done** (Gotowe).

    ![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Zrzut ekranu z podręcznym oknem dialogowym tworzenia jednostki dla nowej jednostki Location (Lokalizacja)")

3. Etykieta dla elementu `g-2323` jest oznaczona jako `Locations`, ponieważ usługa LUIS nie może ustalić, czy termin jest lokalizacją początkową, docelową czy żadną z nich. Wybierz pozycję `g-2323`, wybierz pozycję **Locations** (Lokalizacje), a następnie przejdź do menu z prawej strony i wybierz pozycję `Origin`.

    [![](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "Zrzut ekranu z podrzędnym oknem dialogowym etykietowania lokalizacji w celu zmiany elementu podrzędnego jednostki Locations (Lokalizacje)")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. Aby oznaczyć etykietą inne lokalizacje we wszystkich innych wypowiedziach, wybierz budynek i biuro w wypowiedzi, a następnie wybierz pozycję Locations (Lokalizacje) i w menu z prawej strony wybierz pozycję `Origin` lub `Destination`. Po oznaczeniu etykietami wszystkich lokalizacji wypowiedzi w obszarze **Tokens View** (widok tokenów) będą wyglądać podobnie do wzorca. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "Zrzut ekranu z jednostką Locations (Lokalizacje) oznaczoną etykietą w wypowiedziach")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>Dodawanie wstępnie skompilowanej jednostki numeru do aplikacji
Dodaj wstępnie skompilowaną jednostkę numeru z powrotem do aplikacji.

1. Wybierz pozycję **Entities** (Jednostki) w menu nawigacji po lewej stronie.

2. Wybierz przycisk **Manage prebuilt entities** (Zarządzaj wstępnie skompilowanymi jednostkami).

3. Wybierz pozycję **number** (liczba) z listy wstępnie skompilowanych jednostek, a następnie wybierz pozycję **Done** (Gotowe).

    ![Zrzut ekranu przedstawiający pozycję number (liczba) wybraną w oknie dialogowym wstępnie skompilowanych jednostek](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą różnych wypowiedzi

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


2. Przejdź na koniec adresu URL na pasku adresu i wprowadź ciąg `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź jest inna niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić intencję `MoveEmployee` z wyodrębnioną jednostką hierarchiczną.

    ```JSON
    {
      "query": "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9966052
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9966052
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0325253047
        },
        {
          "intent": "FindForm",
          "score": 0.006137873
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00462633232
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00415637763
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00382325822
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00249120337
        },
        {
          "intent": "None",
          "score": 0.00130756292
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00119622645
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.26910036E-05
        }
      ],
      "entities": [
        {
          "entity": "jill - jones @ mycompany . com",
          "type": "Employee",
          "startIndex": 18,
          "endIndex": 41,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "x - 2345",
          "type": "Locations::Origin",
          "startIndex": 48,
          "endIndex": 53,
          "score": 0.8520272
        },
        {
          "entity": "g - 23456",
          "type": "Locations::Destination",
          "startIndex": 58,
          "endIndex": 64,
          "score": 0.974032
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 49,
          "endIndex": 53,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "-23456",
          "type": "builtin.number",
          "startIndex": 59,
          "endIndex": 64,
          "resolution": {
            "value": "-23456"
          }
        }
      ]
    }
    ```
    
    Przewidywana jest poprawna intencja, a tablica jednostek zawiera wartości lokalizacji początkowej i docelowej w odpowiedniej właściwości **entity** (jednostka).
    

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>Czy można było użyć wyrażenia regularnego dla każdej lokalizacji?
Tak, utwórz jednostkę wyrażenia regularnego z rolami źródłowymi i docelowymi, a następnie użyj jej we wzorcu.

Lokalizacje w tym przykładzie, takie jak `a-1234`, są zgodnie ze specyficznym formatem obejmującym jedną lub dwie litery z kreską, po których następuje seria 4 lub 5 wartości liczbowych. Te dane można przedstawić jako jednostkę wyrażenia regularnego z rolą dla każdej lokalizacji. Role są dostępne tylko w przypadku wzorców. Można utworzyć wzorce w oparciu o te wypowiedzi, a następnie utworzyć wyrażenie regularne dla formatu lokalizacji i dodawać je do wzorców. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono nową intencję i dodano przykładowe wypowiedzi dla uczonych kontekstowo danych dotyczących lokalizacji początkowej i docelowej. Gdy aplikacja zostanie wyszkolona i opublikowana, aplikacja kliencka będzie mogła używać tych informacji do tworzenia biletu przeniesienia z odpowiednimi informacjami.

> [!div class="nextstepaction"] 
> [Dowiedz się, jak dodać jednostkę złożoną](luis-tutorial-composite-entity.md) 
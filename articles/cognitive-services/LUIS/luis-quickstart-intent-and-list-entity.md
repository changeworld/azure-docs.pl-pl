---
title: 'Samouczek 4: Dokładne dopasowanie tekstu — jednostka listy usługi LUIS'
titleSuffix: Azure Cognitive Services
description: Pobieraj dane, które są zgodne ze wstępnie zdefiniowaną listą elementów. Każdy element na liście może mieć synonimy, które są również dokładnie zgodne
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: a4e294687b6c3ea2ba6ff8003e7a8f1ac69ea639
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425075"
---
# <a name="tutorial-4-extract-exact-text-matches"></a>Samouczek 4: Wyodrębnianie dokładnych dopasowań tekstu
W tym samouczku dowiesz się, jak uzyskać dane zgodne ze wstępnie zdefiniowaną listą elementów. Każdy element na liście może zawierać listę synonimów. W przypadku aplikacji Human Resources pracownik może być identyfikowany za pomocą kilku kluczowych informacji, takich jak nazwa, adres e-mail, numer telefonu i identyfikator TID. 

Aplikacja Human Resources musi określić, który pracownik jest przenoszony z jednego budynku do innego. W przypadku wypowiedzi o przeniesieniu pracownika usługa LUIS określa intencję i wyodrębnia pracownika, aby aplikacja kliencka mogła utworzyć standardowe polecenie przeniesienia pracownika.

Ta aplikacja używa jednostki listy do wyodrębnienia pracownika. Do pracownika można odwoływać się przy użyciu nazwy, wewnętrznego numeru telefonu służbowego,numeru telefonu komórkowego, adresu e-mail lub federalnego numer ubezpieczenia społecznego (USA). 

Jednostka listy jest dobrym rozwiązaniem w przypadku tego typu danych, jeśli:

* Wartości danych należą do znanego zestawu.
* Zestaw nie przekracza maksymalnych [granic](luis-boundaries.md) usługi LUIS dla tego typu jednostki.
* Tekst w wypowiedzi to dokładne dopasowanie synonimu lub nazwy kanonicznej. 

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Korzystanie z istniejącej aplikacji samouczka
> * Dodawanie intencji MoveEmployee
> * Dodawanie jednostki listy 
> * Szkolenie 
> * Publikowanie
> * Pobieranie intencji i jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Korzystanie z istniejącej aplikacji
Przejdź do aplikacji o nazwie **HumanResources** utworzonej w ostatnim samouczku. 

Jeśli nie masz aplikacji HumanResources z poprzedniego samouczka, wykonaj następujące kroki:

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-regex-HumanResources.json).

2. Zaimportuj plik JSON do nowej aplikacji.

3. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `list`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL. 


## <a name="moveemployee-intent"></a>Intencja MoveEmployee

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Wybierz pozycję **Create new intent** (Utwórz nową intencję). 

3. Wprowadź ciąg `MoveEmployee` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe). 

    ![Zrzut ekranu z wyskakującym oknem dialogowym Create new intent (Tworzenie nowej intencji)](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Dodaj przykładowe wypowiedzi do intencji.

    |Przykładowe wypowiedzi|
    |--|
    |move John W. Smith from B-1234 to H-4452|
    |mv john.w.smith@mycompany.com from office b-1234 to office h-4452|
    |shift x12345 to h-1234 tomorrow|
    |place 425-555-1212 in HH-2345|
    |move 123-45-6789 from A-4321 to J-23456|
    |mv Jill Jones from D-2345 to J-23456|
    |shift jill-jones@mycompany.com to M-12345|
    |x23456 to M-12345|
    |425-555-0000 to h-4452|
    |234-56-7891 to hh-2345|

    [ ![Zrzut ekranu przedstawiający stronę Intent (Intencja) z wyróżnionymi nowymi wypowiedziami](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png) ](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png#lightbox)

    Należy pamiętać, że liczba i jednostka datetimeV2 zostały dodane w poprzednim samouczku i zostaną automatycznie oznaczone etykietą, kiedy zostaną znalezione w dowolnej przykładowej wypowiedzi.

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="employee-list-entity"></a>Jednostka listy pracowników
Teraz, gdy intencja **MoveEmployee** ma przykładowe wypowiedzi, usługa LUIS musi zrozumieć, czym jest pracownik. 

Podstawowa nazwa _kanoniczna_ każdego elementu to numer pracownika. Dla tej domeny przykładami synonimów każdej nazwy kanonicznej są: 

|Cel synonimu|Wartość synonimu|
|--|--|
|Name (Nazwa)|John W. Smith|
|Email address (Adres e-mail)|john.w.smith@mycompany.com|
|Phone extension (Numer wewnętrzny)|x12345|
|Numer osobistego telefonu komórkowego|425-555-1212|
|Federalny numer ubezpieczenia społecznego (USA)|123-45-6789|


1. Wybierz pozycję **Entities** (Jednostki) w lewym panelu.

2. Wybierz pozycję **Create new entity** (Utwórz nową jednostkę).

3. W oknie podręcznym jednostki wprowadź ciąg `Employee` jako nazwę jednostki i wartość **List** (Lista) jako typ jednostki. Wybierz pozycję **Done** (Gotowe).  

    [![](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "Zrzut ekranu przedstawiający podręczne okno dialogowe tworzenia nowej jednostki")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. Na stronie jednostki Employee (Pracownik) wprowadź `Employee-24612` jako nową wartość.

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "Zrzut ekranu przedstawiający wprowadzanie wartości")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. W obszarze Synonyms (Synonimy) dodaj następujące wartości:

    |Cel synonimu|Wartość synonimu|
    |--|--|
    |Name (Nazwa)|John W. Smith|
    |Email address (Adres e-mail)|john.w.smith@mycompany.com|
    |Phone extension (Numer wewnętrzny)|x12345|
    |Numer osobistego telefonu komórkowego|425-555-1212|
    |Federalny numer ubezpieczenia społecznego (USA)|123-45-6789|

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "Zrzut ekranu przedstawiający wprowadzanie synonimów")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. Wprowadź `Employee-45612` jako nową wartość.

7. W obszarze Synonyms (Synonimy) dodaj następujące wartości:

    |Cel synonimu|Wartość synonimu|
    |--|--|
    |Name (Nazwa)|Jill Jones|
    |Email address (Adres e-mail)|jill-jones@mycompany.com|
    |Phone extension (Numer wewnętrzny)|x23456|
    |Numer osobistego telefonu komórkowego|425-555-0000|
    |Federalny numer ubezpieczenia społecznego (USA)|234-56-7891|

## <a name="train"></a>Szkolenie

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikowanie

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Pobieranie intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `shift 123-45-6789 from Z-1242 to T-54672`. Ostatni parametr ciągu zapytania to `q`, czyli **q**uery (zapytanie) wypowiedzi. Ta wypowiedź jest inna niż wszystkie pozostałe wypowiedzi oznaczone etykietami, dlatego jest dobra do testowania i powinna zwrócić intencję `MoveEmployee` z wyodrębnionym elementem `Employee`.

  ```JSON
  {
    "query": "shift 123-45-6789 from Z-1242 to T-54672",
    "topScoringIntent": {
      "intent": "MoveEmployee",
      "score": 0.9882801
    },
    "intents": [
      {
        "intent": "MoveEmployee",
        "score": 0.9882801
      },
      {
        "intent": "FindForm",
        "score": 0.016044287
      },
      {
        "intent": "GetJobInformation",
        "score": 0.007611245
      },
      {
        "intent": "ApplyForJob",
        "score": 0.007063288
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00684710965
      },
      {
        "intent": "None",
        "score": 0.00304174074
      },
      {
        "intent": "Utilities.Help",
        "score": 0.002981
      },
      {
        "intent": "Utilities.Confirm",
        "score": 0.00212222221
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.00191026414
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0007461446
      }
    ],
    "entities": [
      {
        "entity": "123 - 45 - 6789",
        "type": "Employee",
        "startIndex": 6,
        "endIndex": 16,
        "resolution": {
          "values": [
            "Employee-24612"
          ]
        }
      },
      {
        "entity": "123",
        "type": "builtin.number",
        "startIndex": 6,
        "endIndex": 8,
        "resolution": {
          "value": "123"
        }
      },
      {
        "entity": "45",
        "type": "builtin.number",
        "startIndex": 10,
        "endIndex": 11,
        "resolution": {
          "value": "45"
        }
      },
      {
        "entity": "6789",
        "type": "builtin.number",
        "startIndex": 13,
        "endIndex": 16,
        "resolution": {
          "value": "6789"
        }
      },
      {
        "entity": "-1242",
        "type": "builtin.number",
        "startIndex": 24,
        "endIndex": 28,
        "resolution": {
          "value": "-1242"
        }
      },
      {
        "entity": "-54672",
        "type": "builtin.number",
        "startIndex": 34,
        "endIndex": 39,
        "resolution": {
          "value": "-54672"
        }
      }
    ]
  }
  ```

  Pracownik został znaleziony i zwrócony jako typ `Employee` o rozpoznanej wartości `Employee-24612`.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono nową intencję, dodano przykładowe wypowiedzi, a następnie utworzono jednostkę listy, aby wyodrębnić z wypowiedzi dokładne dopasowania tekstu. Po wyszkoleniu i opublikowaniu aplikacji zapytanie do punktu końcowego zidentyfikowało intencję i zwróciło wyodrębnione dane.

> [!div class="nextstepaction"]
> [Dodawanie jednostki hierarchicznej do aplikacji](luis-quickstart-intent-and-hier-entity.md)


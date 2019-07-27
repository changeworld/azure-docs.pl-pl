---
title: Dokładne dopasowanie tekstu — LUIS
titleSuffix: Azure Cognitive Services
description: Pobieraj dane, które są zgodne ze wstępnie zdefiniowaną listą elementów. Każdy element na liście może mieć synonimy, które są również dokładnie zgodne
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a5aeeb74d26ce633beb0e9feef20a2315bc0ff1d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560408"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>Samouczek: Uzyskiwanie dokładnych danych dopasowanych na podstawie tekstu z wypowiedzi

W tym samouczku dowiesz się, jak uzyskać dane jednostki zgodne ze wstępnie zdefiniowaną listą elementów. 

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Tworzenie aplikacji
> * Dodawanie intencji
> * Dodawanie jednostki listy 
> * Szkolenie 
> * Publikowanie
> * Pobieranie intencji i jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Co to jest jednostka listy?

Jednostka listy jest dokładnym dopasowaniem tekstu do słów w wypowiedzi. 

Każdy element na liście może zawierać listę synonimów. W przypadku aplikacji Human Resources dział firmy może być identyfikowany za pomocą kilku kluczowych informacji takich jak oficjalna nazwa, typowe akronimy i kody działu rozliczeń. 

Aplikacja Human Resources musi określić dział, do którego przechodzi pracownik. 

Jednostka listy jest dobrym rozwiązaniem w przypadku tego typu danych, jeśli:

* Wartości danych należą do znanego zestawu.
* Zestaw nie przekracza maksymalnych [granic](luis-boundaries.md) usługi LUIS dla tego typu jednostki.
* Tekst w wypowiedzi to dokładne dopasowanie synonimu lub nazwy kanonicznej. Usługa LUIS nie korzysta z listy poza dokładnymi dopasowaniami tekstu. Analiza słowotwórcza, liczba mnoga i inne wariacje nie są rozpoznawane przy użyciu tylko jednostki listy. Aby zarządzać wariacjami, rozważ użycie [wzorca](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) z opcjonalną składnią tekstu. 

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>Tworzenie intencji przeniesienia pracowników do innego działu

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Wybierz pozycję **Create new intent** (Utwórz nową intencję). 

3. Wprowadź ciąg `TransferEmployeeToDepartment` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe). 

    ![Zrzut ekranu z wyskakującym oknem dialogowym Create new intent (Tworzenie nowej intencji)](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Dodaj przykładowe wypowiedzi do intencji.

    |Przykładowe wypowiedzi|
    |--|
    |Move John W. Smith to the accounting department (Przenieś Johna W. Smitha do działu księgowości)|
    |Transfer Jill Jones from to R&D (Przenieś Jill Jones z do zespołu badawczo-rozwojowego)|
    |Dept 1234 has a new member named Bill Bradstreet (Dział 1234 ma nowego członka o imieniu Bill Bradstreet)|
    |Place John Jackson in Engineering (Umieść Johna Jacksona w zespole inżynieryjnym) |
    |Move Debra Doughtery to Inside Sales (Przenieś Debrę Doughtery do wewnętrznego działu sprzedaży)|
    |Mv Jill Jones to IT (Przenieś Jill Jones do działu IT)|
    |Shift Alice Anderson to DevOps (Przenieś Alice Anderson do działu DevOps)|
    |Carl Chamerlin to Finance (Carl Chamerlin do działu finansów)|
    |Steve Standish to 1234 (Steve Standish do 1234)|
    |Tanner Thompson to 3456 (Tanner Thompson do 3456)|

    [![Zrzut ekranu przedstawiający intencję z przykładowymi wypowiedziami](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "Zrzut ekranu przedstawiający intencję z przykładowymi wypowiedziami")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>Jednostka listy działów

Teraz, gdy intencja **TransferEmployeeToDepartment** ma przykładowe wypowiedzi, usługa LUIS musi zrozumieć, czym jest dział. 

Podstawowa nazwa _kanoniczna_ każdego elementu to nazwa działu. Przykłady synonimów każdej nazwy kanonicznej są następujące: 

|Nazwa kanoniczna|Synonimy|
|--|--|
|Księgowość|ksg<br>ksgwsc<br>3456|
|Development Operations|DevOps<br>4949|
|Inżynieria|inz<br>inznr<br>4567|
|Finanse|fin<br>2020|
|Technologie informatyczne|it<br>2323|
|Sprzedaż wewnętrzna|sprzedazw<br>sprzedazwewn<br>1414|
|Badania i projektowanie|R&D<br>1234|

1. Wybierz pozycję **Entities** (Jednostki) w lewym panelu.

1. Wybierz pozycję **Create new entity** (Utwórz nową jednostkę).

1. W oknie podręcznym jednostki wprowadź ciąg `Department` jako nazwę jednostki i wartość **List** (Lista) jako typ jednostki. Wybierz pozycję **Done** (Gotowe).  

    [![Zrzut ekranu przedstawiający wyskakujące okno dialogowe tworzenia nowej jednostki](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "Zrzut ekranu przedstawiający wyskakujące okno dialogowe tworzenia nowej jednostki")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. Na stronie jednostki Department (Dział) wprowadź `Accounting` jako nową wartość.

1. W przypadku synonimów dodaj synonimy z poprzedniej tabeli.

1. Kontynuuj dodawanie wszystkich nazw kanonicznych i ich synonimów. 

## <a name="add-example-utterances-to-the-none-intent"></a>Dodawanie przykładowych wypowiedzi do intencji None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trenowanie aplikacji w celu umożliwienia testowania zmian w intencji 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikowanie aplikacji w celu umożliwienia wysyłania zapytań z punktu końcowego do trenowanego modelu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Pobieranie przewidywania intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Przejdź na koniec tego adresu URL i wprowadź ciąg `shift Joe Smith to IT`. Ostatni parametr ciągu zapytania to `q`, czyli **q**uery (zapytanie) wypowiedzi. Ta wypowiedź jest inna niż wszystkie pozostałe wypowiedzi oznaczone etykietami, dlatego jest dobra do testowania i powinna zwrócić intencję `TransferEmployeeToDepartment` z wyodrębnionym elementem `Department`.

   ```json
    {
      "query": "shift Joe Smith to IT",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9775754
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9775754
        },
        {
          "intent": "None",
          "score": 0.0154493852
        }
      ],
      "entities": [
        {
          "entity": "it",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 20,
          "resolution": {
            "values": [
              "Information Technology"
            ]
          }
        }
      ]
    }
   ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Lista jednostki](luis-concept-entity-types.md#list-entity) — informacje koncepcyjne
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Następne kroki
W tym samouczku utworzono nową intencję, dodano przykładowe wypowiedzi, a następnie utworzono jednostkę listy, aby wyodrębnić z wypowiedzi dokładne dopasowania tekstu. Po wyszkoleniu i opublikowaniu aplikacji zapytanie do punktu końcowego zidentyfikowało intencję i zwróciło wyodrębnione dane.

Kontynuuj pracę z tą aplikacją, [dodając jednostkę złożoną](luis-tutorial-composite-entity.md).

> [!div class="nextstepaction"]
> [Dodawanie wstępnie skompilowanej jednostki z rolą do aplikacji](tutorial-entity-roles.md)


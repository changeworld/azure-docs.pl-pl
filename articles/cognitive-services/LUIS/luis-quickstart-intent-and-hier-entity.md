---
title: Jednostka hierarchiczna
titleSuffix: Azure Cognitive Services
description: Znajdziesz powiązane elementy danych na podstawie kontekstu. Na przykład powiązane są lokalizacje początkowa i docelowa dla fizycznego przeniesienia z jednego budynku i biura do innego budynku i biura.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 91a9646e88adbfaf6d3c3fc0b06b341c647e773f
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753697"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Samouczek: wyodrębnianie danych powiązanych kontekstowo z wypowiedzi

W tym samouczku znajdziesz powiązane elementy danych na podstawie kontekstu. Na przykład lokalizację początkową i docelową dla przeniesienia z jednego miasta do innego. Potrzebne mogą być oba elementy danych. Są one powiązane ze sobą.  

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie nowej aplikacji
> * Dodawanie intencji 
> * Dodawanie jednostki hierarchicznej lokalizacji z elementami podrzędnymi miejsca początkowego i docelowego
> * Szkolenie
> * Publikowanie
> * Pobieranie intencji i jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="hierarchical-data"></a>Dane hierarchiczne

Ta aplikacja określa, dokąd ma zostać przeniesiony pracownik z miasta źródłowego do miasta docelowego. Używa ona jednostki hierarchicznej do określania lokalizacji w wypowiedzi. 

Jednostka hierarchiczna jest odpowiednia dla tego typu danych, ponieważ oba elementy danych, lokalizacje podrzędne:

* Są prostymi jednostkami.
* Są ze sobą powiązane w kontekście wypowiedzi.
* Używają określonych wybranych wyrazów w celu wskazania poszczególnych jednostek. Przykładowe wyrazy tego typu to: from/to (od/do), leaving/headed to (opuszczać/kierować się do), away from/toward (w kierunku od/do).
* Oba elementy podrzędne często znajdują się w tej samej wypowiedzi. 
* Te informacje należy grupować i przetwarzać jako całość w aplikacji klienckiej.

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Tworzenie intencji przeniesienia pracowników między miastami

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Wybierz pozycję **Create new intent** (Utwórz nową intencję). 

1. Wprowadź ciąg `MoveEmployeeToCity` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe). 

    ![Zrzut ekranu z wyskakującym oknem dialogowym Create new intent (Tworzenie nowej intencji)](./media/luis-quickstart-intent-and-hier-entity/create-new-intent-move-employee-to-city.png)

1. Dodaj przykładowe wypowiedzi do intencji.

    |Przykładowe wypowiedzi|
    |--|
    |move John W. Smith leaving Seattle headed to Dallas (przenieś Johna W. Smitha z Seattle do Dallas)|
    |transfer Jill Jones from Seattle to Cairo (przenieś Jill Jones z Seattle do Kairu)|
    |Place John Jackson away from Tampa, coming to Atlanta (Umieść Johna Jacksona opuszczającego Tampę w Atlancie) |
    |move Debra Doughtery to Tulsa from Dallas (przenieś Debrę Doughtery do Tulsy z Dallas)|
    |mv Jill Jones leaving Cairo headed to Tampa (przesuń Jill Jones z Kairu do Tampy)|
    |Shift Alice Anderson to Oakland from Redmond (Transferuj Alice Anderson do Oakland z Redmond)|
    |Carl Chamerlin from San Francisco to Redmond (Carl Chamerlin z San Francisco do Redmond)|
    |Transfer Steve Standish from San Diego toward Bellevue (Transferuj Steve’a Standisha z San Diego do Bellevue) |
    |lift Tanner Thompson from Kansas city and shift to Chicago (Tannera Thompsona z Kansas City przenieś do Chicago)|

    [ ![Zrzut ekranu usługi LUIS z nowymi wypowiedziami w intencji MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

## <a name="create-a-location-entity"></a>Tworzenie jednostki Location (Lokalizacja)
Usługa LUIS musi zrozumieć, czym jest lokalizacja, oznaczając źródło i miejsce docelowe w wypowiedziach. Jeśli chcesz zobaczyć wypowiedź w widoku tokenu (nieprzetworzonym), na pasku nad wypowiedziami wybierz przełącznik oznaczony etykietą **Entities View** (Widok jednostek). Po przełączeniu przełącznika kontrolka będzie mieć etykietę **Tokens View** (Widok tokenów).

Przeanalizujmy następującą wypowiedź:

```json
move John W. Smith leaving Seattle headed to Dallas
```

W wypowiedzi są podane dwie lokalizacje: `Seattle` i `Dallas`. Obie są zgrupowane jako elementy podrzędne jednostki hierarchicznej `Location`, ponieważ oba elementy danych muszą zostać wyodrębnione z wypowiedzi w celu wykonania żądania w aplikacji klienckiej i są ze sobą powiązane. 
 
Jeśli obecny jest tylko jeden element podrzędny jednostki hierarchicznej (lokalizacja początkowa lub docelowa), też jest wyodrębniany. Nie jest konieczne znalezienie wszystkich elementów podrzędnych, aby wyodrębnić jeden lub część z nich. 

1. W wypowiedzi `move John W. Smith leaving Seattle headed to Dallas` zaznacz wyraz `Seattle`. Zostanie wyświetlone menu rozwijane z polem tekstowym w górnej części. Wprowadź nazwę jednostki `Location` w polu tekstowym, a następnie wybierz polecenie **Create new entity** (Utwórz nową jednostkę) w menu rozwijanym. 

    [![Zrzut ekranu przedstawiający tworzenie nowej jednostki na stronie intencji](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png "Zrzut ekranu przedstawiający tworzenie nowej jednostki na stronie intencji")](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png#lightbox)

1. W oknie podręcznym wybierz typ jednostki **Hierarchical** (Hierarchiczna) z elementami `Origin` i `Destination` jako podrzędnymi. Wybierz pozycję **Done** (Gotowe).

    ![Zrzut ekranu przedstawiający okno dialogowe tworzenia jednostki dla nowej jednostki lokalizacji](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Zrzut ekranu przedstawiający okno dialogowe tworzenia jednostki dla nowej jednostki lokalizacji")

1. Etykieta dla elementu `Seattle` jest oznaczona jako `Location`, ponieważ usługa LUIS nie może ustalić, czy termin jest lokalizacją początkową, docelową czy żadną z nich. Wybierz pozycję `Seattle`, wybierz pozycję **Location** (Lokalizacja), a następnie przejdź do menu z prawej strony i wybierz pozycję `Origin`.

    [![Zrzut ekranu okna dialogowego tworzenia etykiety jednostki w celu zmiany elementu podrzędnego jednostki lokalizacji](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png "Zrzut ekranu okna dialogowego tworzenia etykiety jednostki w celu zmiany elementu podrzędnego jednostki lokalizacji")](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png#lightbox)

1. Dodaj etykiety do innych lokalizacji we wszystkich innych wypowiedziach. Po oznaczeniu wszystkich lokalizacji wypowiedzi będą wyglądać podobnie do wzorca. 

    [![Zrzut ekranu jednostki lokalizacji oznaczonej etykietą w wypowiedziach](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png "Zrzut ekranu jednostki lokalizacji oznaczonej etykietą w wypowiedziach")](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png#lightbox)

    Czerwone podkreślenie wskazuje, że usługa LUIS nie ma pewności co do jednostki. Rozstrzygnie to szkolenie. 

## <a name="add-example-utterances-to-the-none-intent"></a>Dodawanie przykładowych wypowiedzi do intencji None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trenowanie aplikacji w celu umożliwienia testowania zmian w intencji 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikowanie aplikacji w celu umożliwienia wysyłania zapytań z punktu końcowego do trenowanego modelu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Pobieranie przewidywania intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Przejdź na koniec adresu URL na pasku adresu i wprowadź ciąg `Please move Carl Chamerlin from Tampa to Portland`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź jest inna niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić intencję `MoveEmployee` z wyodrębnioną jednostką hierarchiczną.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.979823351
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.979823351
        },
        {
          "intent": "None",
          "score": 0.0156363435
        }
      ],
      "entities": [
        {
          "entity": "portland",
          "type": "Location::Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "tampa",
          "type": "Location::Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    Przewidywana jest poprawna intencja, a tablica jednostek zawiera wartości lokalizacji początkowej i docelowej w odpowiedniej właściwości **entities** (jednostki).
    
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Jednostki hierarchiczne](luis-concept-entity-types.md) — informacje koncepcyjne
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)
* [Role a jednostki hierarchiczne](luis-concept-roles.md#roles-versus-hierarchical-entities)
* [Ulepszanie przewidywań za pomocą wzorców](luis-concept-patterns.md)

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono nową intencję i dodano przykładowe wypowiedzi dla uczonych kontekstowo danych dotyczących lokalizacji początkowej i docelowej. Gdy aplikacja zostanie wyszkolona i opublikowana, aplikacja kliencka będzie mogła używać tych informacji do tworzenia biletu przeniesienia z odpowiednimi informacjami.

> [!div class="nextstepaction"] 
> [Dowiedz się, jak dodać jednostkę złożoną](luis-tutorial-composite-entity.md) 

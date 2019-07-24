---
title: Dane kontekstowe z rolami — Language Understanding
titleSuffix: Azure Cognitive Services
description: Znajdź powiązane dane na podstawie kontekstu. Na przykład powiązane są lokalizacje początkowa i docelowa dla fizycznego przeniesienia z jednego budynku i biura do innego budynku i biura.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 061bd94a839d83f75566412ac546ab3208543780
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467629"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Samouczek: wyodrębnianie danych powiązanych kontekstowo z wypowiedzi

W tym samouczku znajdziesz powiązane elementy danych na podstawie kontekstu. Na przykład lokalizację początkową i docelową dla przeniesienia z jednego miasta do innego. Potrzebne mogą być oba elementy danych. Są one powiązane ze sobą.  

Roli można używać z dowolnym prekompilowanym lub niestandardowym typem jednostki i używanym w obu przykładach wyrażenia długości i wzorców. 

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie nowej aplikacji
> * Dodawanie intencji 
> * Pobieranie informacji o źródle i miejscu docelowym przy użyciu ról
> * Szkolenie
> * Publikowanie
> * Pobierz intencje i role jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Pokrewne dane

Ta aplikacja określa, dokąd ma zostać przeniesiony pracownik z miasta źródłowego do miasta docelowego. Używa wstępnie skompilowanej jednostki GeographyV2 do identyfikowania nazw miast i używa ról do określenia typów lokalizacji (źródła i miejsca docelowego) w wypowiedź.

Rola powinna być używana, gdy dane jednostkowe mają zostać wyodrębnione:

* Jest powiązany ze sobą w kontekście wypowiedź.
* Używa określonego wyrazu, aby wskazać każdą rolę. Przykładowe wyrazy tego typu to: from/to (od/do), leaving/headed to (opuszczać/kierować się do), away from/toward (w kierunku od/do).
* Obie role są często w tym samym wypowiedź, dzięki czemu LUIS się uczyć tego częstego użycia kontekstowego.
* Te informacje należy grupować i przetwarzać jako całość w aplikacji klienckiej.

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Tworzenie intencji przeniesienia pracowników między miastami

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Wybierz pozycję **Create new intent** (Utwórz nową intencję). 

1. Wprowadź ciąg `MoveEmployeeToCity` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe). 

    ![Zrzut ekranu z wyskakującym oknem dialogowym Create new intent (Tworzenie nowej intencji)](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Dodaj przykładowe wypowiedzi do intencji.

    |Przykładowe wypowiedzi|
    |--|
    |Przenieś Jan W. Smith, pozostawiając Seattle jako Orlando|
    |transfer Jill Jones from Seattle to Cairo (przenieś Jill Jones z Seattle do Kairu)|
    |Place John Jackson away from Tampa, coming to Atlanta (Umieść Johna Jacksona opuszczającego Tampę w Atlancie) |
    |Przenieś Debra Doughtery do Tulsa z Chicago|
    |mv Jill Jones leaving Cairo headed to Tampa (przesuń Jill Jones z Kairu do Tampy)|
    |Shift Alice Anderson to Oakland from Redmond (Transferuj Alice Anderson do Oakland z Redmond)|
    |Carl Chamerlin from San Francisco to Redmond (Carl Chamerlin z San Francisco do Redmond)|
    |Transfer Steve Standish from San Diego toward Bellevue (Transferuj Steve’a Standisha z San Diego do Bellevue) |
    |lift Tanner Thompson from Kansas city and shift to Chicago (Tannera Thompsona z Kansas City przenieś do Chicago)|

    [![Zrzut ekranu usługi LUIS z nowymi wypowiedziami w intencji MoveEmployee](./media/tutorial-entity-roles/hr-enter-utterances.png)](./media/tutorial-entity-roles/hr-enter-utterances.png#lightbox)

## <a name="add-prebuilt-entity-geographyv2"></a>Dodaj wstępnie utworzony obiekt geographyV2

Wstępnie utworzona jednostka, geographyV2, wyodrębnia informacje o lokalizacji, w tym nazwy miast. Ponieważ wyrażenia długości mają dwie nazwy miast, odnoszące się do siebie w kontekście, należy użyć ról do wyodrębnienia tego kontekstu.

1. Wybierz **jednostki** z nawigacji po lewej stronie.

1. Wybierz pozycję **Dodaj prekompilowaną jednostkę**, `geo` a następnie wybierz pozycję na pasku wyszukiwania, aby odfiltrować wstępnie skompilowane jednostki. 

    ![Dodaj wstępnie utworzoną jednostkę geographyV2 do aplikacji](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)
1. Zaznacz pole wyboru i wybierz pozycję **gotowe**.
1. Na liście **jednostki** wybierz **geographyV2** , aby otworzyć nową jednostkę. 
1. Dodaj dwie role, `Origin`i. `Destination` 

    ![Dodaj role do wstępnie skompilowanej jednostki](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)
1. Wybierz  pozycję intencje z nawigacji po lewej stronie, a następnie wybierz opcję **MoveEmployeeToCity** . Zwróć uwagę na to, że nazwy miast są oznaczone wstępnie utworzoną jednostką **geographyV2**.
1. W pierwszej wypowiedź listy wybierz lokalizację pochodzenia. Zostanie wyświetlone menu rozwijane. Na liście wybierz pozycję **geographyV2** , a następnie postępuj zgodnie z menu, aby wybrać **Źródło**.
1. Użyj metody z poprzedniego kroku, aby oznaczyć wszystkie role lokalizacji we wszystkich wyrażenia długości. 


## <a name="add-example-utterances-to-the-none-intent"></a>Dodawanie przykładowych wypowiedzi do intencji None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trenowanie aplikacji w celu umożliwienia testowania zmian w intencji 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikowanie aplikacji w celu umożliwienia wysyłania zapytań z punktu końcowego do trenowanego modelu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Pobieranie przewidywania intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Przejdź na koniec adresu URL na pasku adresu i wprowadź ciąg `Please move Carl Chamerlin from Tampa to Portland`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ten wypowiedź nie jest taki sam jak żaden z wyrażenia długości z etykietą, więc jest dobrym testem i powinien zwrócić `MoveEmployee` cel z wyodrębnioną jednostką.

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
          "entity": "geographyV2",
          "role": "Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "geographyV2",
          "role": "Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    Przewidywalna jest poprawna wartość, a tablica jednostek ma zarówno rolę źródłową, jak i docelową we właściwości odpowiednich **jednostek** .
    
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Pojęcia dotyczące jednostek](luis-concept-entity-types.md)
* [Pojęcia dotyczące ról](luis-concept-roles.md)
* [Lista wstępnie utworzonych jednostek](luis-reference-prebuilt-entities.md)
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)
* [Role](luis-concept-roles.md)

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono nową intencję i dodano przykładowe wypowiedzi dla uczonych kontekstowo danych dotyczących lokalizacji początkowej i docelowej. Gdy aplikacja zostanie wyszkolona i opublikowana, aplikacja kliencka będzie mogła używać tych informacji do tworzenia biletu przeniesienia z odpowiednimi informacjami.

> [!div class="nextstepaction"] 
> [Dowiedz się, jak dodać jednostkę złożoną](luis-tutorial-composite-entity.md) 

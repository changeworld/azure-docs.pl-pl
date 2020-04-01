---
title: 'Samouczek: Dane kontekstowe z rolami — usługa LUIS'
description: Znajdź powiązane dane na podstawie kontekstu. Na przykład powiązane są lokalizacje początkowa i docelowa dla fizycznego przeniesienia z jednego budynku i biura do innego budynku i biura.
ms.topic: tutorial
ms.date: 03/30/2020
ms.openlocfilehash: fdb463896e531619ea7ebe7c384729763dc84138
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475825"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Samouczek: wyodrębnianie danych związanych z kontekstem z wypowiedź

W tym samouczku znajdziesz powiązane elementy danych na podstawie kontekstu. Na przykład lokalizacje pochodzenia i miejsca docelowe dla przeniesienia z jednego miasta do drugiego. Potrzebne mogą być oba elementy danych. Są one powiązane ze sobą.

Rola może być używana z dowolnym wstępnie utworzonym lub niestandardowym typem jednostki i używana zarówno w przykładowych wypowiedziach, jak i wzorcach.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie nowej aplikacji
> * Dodawanie intencji
> * Uzyskaj informacje o źródle pochodzenia i miejscu docelowym przy użyciu ról
> * Szkolenie
> * Publikowanie
> * Pobierz intencje i role encji z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Powiązane dane

Ta aplikacja określa, dokąd ma zostać przeniesiony pracownik z miasta źródłowego do miasta docelowego. Używa geographyV2 wstępnie utworzonej jednostki do identyfikowania nazw miast i używa ról do określenia typów lokalizacji (pochodzenia i miejsca docelowego) w wypowiedź.

Rola powinna być używana, gdy dane jednostki do wyodrębnienia:

* Jest ze sobą powiązane w kontekście wypowiedź.
* Używa określonego wyboru wyrazu, aby wskazać każdą rolę. Przykładowe wyrazy tego typu to: from/to (od/do), leaving/headed to (opuszczać/kierować się do), away from/toward (w kierunku od/do).
* Obie role są często w tej samej wypowiedź, dzięki czemu usługa LUIS uczyć się od tego częstego użycia kontekstowego.
* Te informacje należy grupować i przetwarzać jako całość w aplikacji klienckiej.

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

1. Zaloguj się do [portalu usługi LUIS **preview** ](https://preview.luis.ai).

1. Wybierz + Nowa aplikacja do `HumanResources` **konwersacji**, wprowadź nazwę i zachowaj domyślną kulturę, **angielski**. Pozostaw opis i zasób przewidywania puste. Wybierz pozycję **Done** (Gotowe).

## <a name="create-an-intent-to-move-employees-between-cities"></a>Tworzenie intencji przeniesienia pracowników między miastami

Intencja jest używana do klasyfikowania wypowiedzi użytkownika na podstawie intencji użytkownika, określone na podstawie tekstu języka naturalnego.

Aby sklasyfikować wypowiedź, intencji potrzebuje przykładów wypowiedzi użytkownika, które powinny być klasyfikowane z tej intencji.

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Wybierz **+ Utwórz**.

1. Wprowadź ciąg `MoveEmployeeToCity` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe).

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu z wyskakującym oknem dialogowym Create new intent (Tworzenie nowej intencji)](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Dodaj kilka wypowiedzi przykład do tego zamiaru, który oczekujesz, że użytkownik zapytać.

    |Przykładowe wypowiedzi|
    |--|
    |przenieść John W. Smith pozostawiając Seattle udał się do Orlando|
    |transfer Jill Jones from Seattle to Cairo (przenieś Jill Jones z Seattle do Kairu)|
    |Place John Jackson away from Tampa, coming to Atlanta (Umieść Johna Jacksona opuszczającego Tampę w Atlancie) |
    |przenieść Debra Doughtery do Tulsa z Chicago|
    |mv Jill Jones leaving Cairo headed to Tampa (przesuń Jill Jones z Kairu do Tampy)|
    |Shift Alice Anderson to Oakland from Redmond (Transferuj Alice Anderson do Oakland z Redmond)|
    |Carl Chamerlin from San Francisco to Redmond (Carl Chamerlin z San Francisco do Redmond)|
    |Transfer Steve Standish from San Diego toward Bellevue (Transferuj Steve’a Standisha z San Diego do Bellevue) |
    |lift Tanner Thompson from Kansas city and shift to Chicago (Tannera Thompsona z Kansas City przenieś do Chicago)|

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu usługi LUIS z nowymi wypowiedziami w intencji MoveEmployee](./media/tutorial-entity-roles/hr-enter-utterances.png)

## <a name="add-prebuilt-entity-geographyv2"></a>Dodawanie wstępnie utworzonej lokalizacji geograficznej encjiV2

Wstępnie utworzony **encja, geographyV2**, wyodrębnia informacje o lokalizacji, w tym nazwy miast. Ponieważ wypowiedzi mają dwie nazwy miast, odnoszące się do siebie w kontekście, użyj ról, aby wyodrębnić ten kontekst.

1. Wybierz **elementy** z nawigacji po lewej stronie.

1. Wybierz + Dodaj wstępnie utworzony `geo` **element**, a następnie wprowadź na pasku wyszukiwania, aby filtrować wstępnie utworzone elementy.

    > [!div class="mx-imgBorder"]
    > ![Dodawanie wstępnie utworzonej encji regionu GeographyV2 do aplikacji](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)

1. Zaznacz to pole wyboru i wybierz pozycję **Gotowe**.

## <a name="add-roles-to-prebuilt-entity"></a>Dodawanie ról do wstępnie utworzonej encji

1. Na liście **Jednostki** wybierz **geografięV2,** aby otworzyć nową encję.
1. Aby dodać rolę, **+** wybierz i dodaj `Origin`dwie `Destination`następujące role: , i .

    > [!div class="mx-imgBorder"]
    > ![Dodawanie ról do wstępnie utworzonej encji](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)

## <a name="label-entity-roles-in-example-utterances"></a>Etykietuj role jednostek w przykładowych wypowiedziach

1. Wybierz **opcji z** lewej strony nawigacji, a następnie wybierz **MoveEmployeeToCity** intencji. Zwróć uwagę, że nazwy miast są oznaczone wstępnie utworzonym elementem **geographyV2**.
1. Na pasku narzędzi kontekstu wybierz **paletę Element** z _ikoną ołówka_.

    > [!div class="mx-imgBorder"]
    > ![Wybieranie palety encji z paska narzędzi zawartości](media/tutorial-entity-roles/intent-detail-context-toolbar-select-entity-palette.png)

1. Wybierz wstępnie utworzony element, **geografiaV2**, a następnie wybierz **Inspektora encji**.
1. W **inspektorze jednostki**wybierz jedną rolę, **Miejsce docelowe**. Spowoduje to zmianę kursora myszy. Użyj kursora, aby oznaczyć tekst we wszystkich wypowiedziach, które są lokalizacją docelową.

    > [!div class="mx-imgBorder"]
    > ![Wybierz rolę w palecie jednostek](media/tutorial-entity-roles/entity-palette-select-entity-role.png)


1. Wróć do **inspektora podmiotu**, zmień na rolę **do pochodzenia**. Użyj kursora, aby oznaczyć tekst we wszystkich wypowiedziach, które są lokalizacją pochodzenia.

## <a name="add-example-utterances-to-the-none-intent"></a>Dodawanie przykładowych wypowiedzi do intencji None

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trenowanie aplikacji w celu umożliwienia testowania zmian w intencji

Aby wyszkolić aplikację, wybierz opcję **Pociąg**. Szkolenia stosuje zmiany, takie jak nowe jednostki i wypowiedzi oznaczone etykietą, do aktywnego modelu.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publikowanie aplikacji w celu uzyskania do niej dostępu z punktu końcowego HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]


## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Pobieranie przewidywania intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]


1. Przejdź na koniec adresu URL na pasku adresu i `Please move Carl Chamerlin from Tampa to Portland`zastąp _YOUR_QUERY_HERE_ .

Ta wypowiedź nie jest taka sama jak wszelkie wypowiedzi etykietowane, więc `MoveEmployee` jest dobrym testem i powinien zwrócić intencji z jednostki wyodrębnione.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.9706451
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.9706451
        },
        {
          "intent": "None",
          "score": 0.0307451729
        }
      ],
      "entities": [
        {
          "entity": "tampa",
          "type": "builtin.geographyV2.city",
          "startIndex": 32,
          "endIndex": 36,
          "role": "Origin"
        },
        {
          "entity": "portland",
          "type": "builtin.geographyV2.city",
          "startIndex": 41,
          "endIndex": 48,
          "role": "Destination"
        }
      ]
    }
    ```

    The correct intent is predicted and the entities array has both the origin and destination roles in the corresponding **entities** property.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Pojęcia dotyczące jednostek](luis-concept-entity-types.md)
* [Pojęcia ról](luis-concept-roles.md)
* [Lista wstępnie utworzonych encji](luis-reference-prebuilt-entities.md)
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)
* [Role](luis-concept-roles.md)

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono nową intencję i dodano przykładowe wypowiedzi dla uczonych kontekstowo danych dotyczących lokalizacji początkowej i docelowej. Gdy aplikacja zostanie wyszkolona i opublikowana, aplikacja kliencka będzie mogła używać tych informacji do tworzenia biletu przeniesienia z odpowiednimi informacjami.

> [!div class="nextstepaction"]
> [Dowiedz się, jak dodać jednostkę złożoną](luis-tutorial-composite-entity.md)

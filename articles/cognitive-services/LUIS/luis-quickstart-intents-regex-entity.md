---
title: Samouczek dotyczący tworzenia aplikacji LUIS w celu uzyskania danych dopasowanych na podstawie wyrażenia regularnego — Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć prostą aplikację LUIS, używając intencji oraz jednostki wyrażenia regularnego do wyodrębnienia danych.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: v-geberr
ms.openlocfilehash: f51c296b39fda08d0bcc704fb974e71465385d67
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929375"
---
# <a name="tutorial-3-add-regular-expression-entity"></a>Samouczek 3. Dodawanie jednostki wyrażenia regularnego
W tym samouczku utworzysz aplikację, która pokazuje, jak można wyodrębnić spójnie sformatowane dane z wypowiedzi przy użyciu jednostki **Regular Expression** (Wyrażenie regularne).


<!-- green checkmark -->
> [!div class="checklist"]
> * Omówienie jednostek wyrażenia regularnego 
> * Używanie aplikacji LUIS dla domeny zasobów ludzkich (HR, Human Resources) z intencją FindForm
> * Dodawanie jednostki wyrażenia regularnego w celu wyodrębniania numeru formularza z wypowiedzi
> * Uczenie i publikowanie aplikacji
> * Wysyłanie zapytania do punktu końcowego aplikacji w celu wyświetlenia odpowiedzi JSON usługi LUIS

Na potrzeby tego artykułu wymagane jest bezpłatne konto usługi [LUIS](luis-reference-regions.md#luis-website) w celu tworzenia aplikacji LUIS.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli nie masz aplikacji Human Resources z samouczka dotyczącego [wstępnie utworzonych jednostek](luis-tutorial-prebuilt-intents-entities.md), [zaimportuj](luis-how-to-start-new-app.md#import-new-app) obiekt JSON do nowej aplikacji w witrynie usługi [LUIS](luis-reference-regions.md#luis-website) z repozytorium Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-prebuilts-HumanResources.json).

Jeśli chcesz zachować oryginalną aplikację Human Resources, sklonuj tę wersję na stronie [Settings](luis-how-to-manage-versions.md#clone-a-version) (Ustawienia) i nadaj jej nazwę `regex`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. 


## <a name="purpose-of-the-regular-expression-entity"></a>Przeznaczenie jednostki wyrażenia regularnego
Przeznaczeniem jednostki jest uzyskanie ważnych danych zawartych w wypowiedzi. Aplikacja używa jednostki wyrażenia regularnego, aby wyodrębnić z wypowiedzi sformatowane numery formularzy działu zasobów ludzkich. Nie korzysta ona z uczenia maszynowego. 

Proste przykładowe wypowiedzi mogą być następujące:

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
```

Wersje wypowiedzi ze skrótami lub żargonem mogą być następujące:

```
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
Jednostka wyrażenia regularnego pasująca do numeru formularza to `hrf-[0-9]{6}`. To wyrażenie regularne dopasowuje znaki literałów `hrf -`, ale ignoruje wielkość liter i warianty kulturowe. Dopasowuje cyfry z zakresu 0–9 dla dokładnie 6 cyfr.

HRF to angielski skrót od wyrażenia „formularz zasobów ludzkich” (human resources form).

### <a name="tokenization-with-hyphens"></a>Tokenizacja za pomocą łączników
Aplikacja LUIS tokenizuje wypowiedź, jeśli wypowiedź zostanie dodana do intencji. Tokenizacja dla tych wypowiedzi dodaje spacje przed i za łącznikiem, `Where is HRF - 123456?`. Wyrażenie regularne jest stosowane do wypowiedzi w postaci nieprzetworzonej, przed tokenizacją. Ponieważ wyrażenie regularne jest stosowane do postaci _nieprzetworzonej_, nie musi obsługiwać ograniczeń wyrazów. 


## <a name="add-findform-intent"></a>Dodawanie intencji FindForm

1. Upewnij się, że aplikacja Human Resources znajduje się w sekcji **Build** (Kompilacja) aplikacji LUIS. Możesz przejść do tej sekcji, wybierając pozycję **Build** (Kompilacja) na górnym pasku menu po prawej stronie. 

    [ ![Zrzut ekranu aplikacji LUIS z wyróżnioną pozycją Build (Kompilacja) na górnym prawym pasku nawigacyjnym](./media/luis-quickstart-intents-regex-entity/first-image.png)](./media/luis-quickstart-intents-regex-entity/first-image.png#lightbox)

2. Wybierz pozycję**Create new intent** (Utwórz nową intencję). 

    [ ![Zrzut ekranu przedstawiający stronę intencji z wyróżnionym przyciskiem Create new intent (Utwórz nową intencję)](./media/luis-quickstart-intents-regex-entity/create-new-intent-button.png) ](./media/luis-quickstart-intents-regex-entity/create-new-intent-button.png#lightbox)

3. Wprowadź ciąg `FindForm` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe). 

    ![Zrzut ekranu przedstawiający okno dialogowe tworzenia nowej intencji z wyrazem Utilities (Narzędzia) w polu wyszukiwania](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. Dodaj przykładowe wypowiedzi do intencji.

    |Przykładowe wypowiedzi|
    |--|
    |Jaki jest adres URL dla formularza hrf-123456?|
    |Gdzie jest formularz hrf-345678?|
    |Kiedy formularz hrf-456098 został zaktualizowany?|
    |Czy Jan Kowalski zaktualizował formularz hrf-234639 w zeszłym tygodniu?|
    |Ile jest wersji formularza hrf-345123?|
    |Kto musi zatwierdzić formularz hrf-123456?|
    |Ile osób musi zatwierdzić formularz hrf-345678?|
    |Data hrf-234123?|
    |Autor hrf-546234?|
    |Tytuł hrf-456234?|

    [ ![Zrzut ekranu przedstawiający stronę Intent (Intencja) z wyróżnionymi nowymi wypowiedziami](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    Aplikacja zawiera wstępnie utworzoną jednostkę numeru dodaną w poprzednim samouczku, zatem każdy numer formularza jest otagowany. Może to być wystarczające w przypadku Twojej aplikacji klienckiej, ale numer nie zostanie oznaczona etykietą typu numeru. Utworzenie nowej jednostki z odpowiednią nazwę umożliwia aplikacji klienckiej właściwe przetworzenie jednostki zwróconej z aplikacji LUIS.

## <a name="create-a-hrf-number-regular-expression-entity"></a>Tworzenie jednostki wyrażenia regularnego HRF-number (numer HRF) 
W poniższych krokach utwórz jednostkę wyrażenia regularnego, aby określić dla aplikacji LUIS, jaki jest format ciągu HRF-number:

1. Wybierz pozycję **Entities** (Jednostki) w lewym panelu.

2. Wybierz przycisk **Create new entity** (Utwórz nową jednostkę) na stronie Jednostki. 

    [![Zrzut ekranu przedstawiający stronę jednostek z wyróżnionym przyciskiem Create new entity (Utwórz nową jednostkę)](./media/luis-quickstart-intents-regex-entity/create-new-entity-1.png)](./media/luis-quickstart-intents-regex-entity/create-new-entity-1.png#lightbox)

3. W podręcznym oknie dialogowym wprowadź nazwę nowej jednostki `HRF-number`, wybierz typ jednostki **RegEx**, wprowadź ciąg `hrf-[0-9]{6}` jako wyrażenie regularne, a następnie wybierz pozycję **Done** (Gotowe).

    ![Zrzut ekranu przedstawiający podręczne okno dialogowe z ustawieniami właściwości nowej jednostki](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. Wybierz pozycję **Intents** (Intencje), a następnie wybierz intencję **FindForm**, aby wyświetlić wyrażenie regularne oznaczone etykietą w wypowiedziach. 

    [![Zrzut ekranu przedstawiający oznaczanie wypowiedzi za pomocą istniejącego wzorca jednostki i wyrażenia regularnego](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Ta jednostka nie jest jednostką nauczoną maszynowo, dlatego etykieta jest stosowana do wypowiedzi i wyświetlana w witrynie usługi LUIS, jak tylko zostanie utworzona.

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS
Jednostka wyrażenia regularnego nie wymaga uczenia, ale nowe intencje i wypowiedzi wymagają. 

1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz).

    ![Obraz przycisku uczenia](./media/luis-quickstart-intents-regex-entity/train-button.png)

2. Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    ![Obraz paska powiadomień powodzenia](./media/luis-quickstart-intents-regex-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego
Aby uzyskać przewidywania usługi LUIS w czatbocie lub innej aplikacji, należy opublikować aplikację. 

1. W górnej części witryny usługi LUIS po prawej stronie wybierz przycisk **Publish** (Publikuj). 

    ![Zrzut ekranu przedstawiający stronę FindKnowledgeBase z wyróżnionym przyciskiem Publish (Publikuj) na górnym pasku nawigacyjnym](./media/luis-quickstart-intents-regex-entity/publish-button.png)

2. Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj).

    ![Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym przyciskiem publikowania w miejscu produkcyjnym](./media/luis-quickstart-intents-regex-entity/publish-to-production.png)

3. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą różnych wypowiedzi
1. Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. 

    ![Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym adresem URL punktu końcowego](./media/luis-quickstart-intents-regex-entity/publish-select-endpoint.png)

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `When were HRF-123456 and hrf-234567 published in the last year?`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź jest inna, niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić intencję `FindForm` z dwoma numerami formularzy: `HRF-123456` i `hrf-234567`.

    ```
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9993477
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9993477
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0206110049
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00533067342
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.004215215
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00209096959
        },
        {
          "intent": "None",
          "score": 0.0017655947
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00109490135
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.0005704638
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.000525338168
        }
      ],
      "entities": [
        {
          "entity": "last year",
          "type": "builtin.datetimeV2.daterange",
          "startIndex": 53,
          "endIndex": 61,
          "resolution": {
            "values": [
              {
                "timex": "2017",
                "type": "daterange",
                "start": "2017-01-01",
                "end": "2018-01-01"
              }
            ]
          }
        },
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    Numery w wypowiedzi są zwracane dwa razy, raz jako nowa jednostka `hrf-number` i raz jako wstępnie utworzona jednostka `number`. Jak pokazuje ten przykład, wypowiedź może zawierać więcej niż jedną jednostkę i więcej niż jedną jednostkę tego samego typu. Używając jednostki wyrażenia regularnego, aplikacja LUIS wyodrębnia nazwane dane, co jest bardziej programowo przydatne dla aplikacji klienckiej, która otrzymuje odpowiedzi JSON.

## <a name="what-has-this-luis-app-accomplished"></a>Co wykonała ta aplikacja LUIS?
Ta aplikacja zidentyfikowała intencję i zwróciła wyodrębnione dane. 

Twój czatbot ma teraz wystarczająco dużo informacji, aby określić akcję główną, `FindForm`, i numery formularzy w wyszukiwaniu. 

## <a name="where-is-this-luis-data-used"></a>Gdzie są używane te dane usługi LUIS? 
Usługa LUIS skończyła obsługiwać to żądanie. Aplikacja wywołująca, taka jak czatbot, może pobrać wynik topScoringIntent (najwyżej oceniana intencja) oraz numery formularzy i przeszukać interfejs API innej firmy. Aplikacja LUIS nie wykonuje tej pracy. Aplikacja LUIS określa jedynie intencję użytkownika i wyodrębnia dane na temat tej intencji. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Wybierz pozycję **My apps** (Moje aplikacje) z menu w lewym górnym rogu. Wybierz wielokropek (**...**) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie ***Delete*** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Learn about the list entity](luis-quickstart-intent-and-list-entity.md) (Informacje na temat jednostki listy)


---
title: Samouczek dotyczący tworzenia aplikacji LUIS w celu uzyskania tekstu dokładnie pasującego do danych na liście — Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć prostą aplikację LUIS, używając intencji i jednostek listy, aby wyodrębnić dane w tym przewodniku Szybki start.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 07/26/2018
ms.author: diberry
ms.openlocfilehash: 4b842f9a00587e8a9771e6ca92806c09e711e6db
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345784"
---
# <a name="tutorial-4-add-list-entity"></a>Samouczek 4. Dodawanie jednostki listy
W tym samouczku utworzysz aplikację, która pokazuje, jak uzyskać dane zgodne ze wstępnie zdefiniowaną listą. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Omówienie jednostek listy 
> * Tworzenie nowej aplikacji LUIS dla domeny zasobów ludzkich (HR, Human Resources) z intencją MoveEmployee
> * Dodawanie jednostki listy w celu wyodrębnienia pracowników z wypowiedzi
> * Uczenie i publikowanie aplikacji
> * Wysyłanie zapytań do punktu końcowego aplikacji w celu wyświetlenia odpowiedzi JSON usługi LUIS

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli nie masz aplikacji Human Resources z samouczka dotyczącego [jednostki wyrażenia regularnego](luis-quickstart-intents-regex-entity.md), [zaimportuj](luis-how-to-start-new-app.md#import-new-app) kod JSON do nowej aplikacji w witrynie internetowej usługi [LUIS](luis-reference-regions.md#luis-website). Aplikacja do zaimportowania znajduje się w repozytorium [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-regex-HumanResources.json) usługi Github.

Jeśli chcesz zachować oryginalną aplikację Human Resources, sklonuj tę wersję na stronie [Settings](luis-how-to-manage-versions.md#clone-a-version) (Ustawienia) i nadaj jej nazwę `list`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. 

## <a name="purpose-of-the-list-entity"></a>Przeznaczenie jednostki listy
Ta aplikacja prognozuje wypowiedzi dotyczące przenoszenia pracownika z jednego budynku do innego. Ta aplikacja używa jednostki listy do wyodrębnienia pracownika. Do pracownika można odwoływać się przy użyciu nazwy, numeru telefonu, adresu e-mail lub federalnego numer ubezpieczenia społecznego (USA). 

Jednostka listy może zawierać wiele elementów z synonimami każdego z nich. W małej lub średniej firmie jednostka listy służy do wyodrębniania informacji o pracownikach. 

Nazwa kanoniczna każdego elementu to numer pracownika. Przykłady synonimów w tej domenie: 

|Cel synonimu|Wartość synonimu|
|--|--|
|Name (Nazwa)|John W. Smith|
|Email address (Adres e-mail)|john.w.smith@mycompany.com|
|Phone extension (Numer wewnętrzny)|x12345|
|Numer osobistego telefonu komórkowego|425-555-1212|
|Federalny numer ubezpieczenia społecznego (USA)|123-45-6789|

Jednostka listy jest dobrym rozwiązaniem w przypadku tego typu danych, jeśli:

* Wartości danych należą do znanego zestawu.
* Zestaw nie przekracza maksymalnych [granic](luis-boundaries.md) usługi LUIS dla tego typu jednostki.
* Tekst w wypowiedzi to dokładne dopasowanie synonimu. 

Usługa LUIS wyodrębnia pracownika w taki sposób, że standardowe zlecenie przeniesienia pracownika można utworzyć w aplikacji klienta.
<!--
## Example utterances
Simple example utterances for a `MoveEmployee` inent:

```
move John W. Smith from B-1234 to H-4452
mv john.w.smith@mycompany from office b-1234 to office h-4452

```
-->

## <a name="add-moveemployee-intent"></a>Dodawanie intencji MoveEmployee

1. Upewnij się, że aplikacja Human Resources znajduje się w sekcji **Build** (Kompilacja) aplikacji LUIS. Możesz przejść do tej sekcji, wybierając pozycję **Build** (Kompilacja) na górnym pasku menu po prawej stronie. 

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

## <a name="create-an-employee-list-entity"></a>Tworzenie jednostki listy pracowników
Teraz, gdy intencja **MoveEmployee** ma wypowiedzi, usługa LUIS musi zrozumieć, czym jest pracownik. 

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

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS
Usługa LUIS nie wie o zmianach intencji i jednostek (modelu), dopóki nie zostanie ich nauczona. 

1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz).

    ![Uczenie aplikacji](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    ![Uczenie powiodło się](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą różnych wypowiedzi

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

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

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>Gdzie jest wykonywane przetwarzanie języka naturalnego w jednostce listy? 
Jednostka listy jest dokładnym dopasowaniem tekstu, dlatego nie opiera się na przetwarzaniu języka naturalnego (ani na uczeniu maszynowym). Aplikacja LUIS korzysta z przetwarzania języka naturalnego (lub uczenia maszynowego), aby wybrać właściwą najwyżej ocenianą intencję. Ponadto wypowiedź może zawierać kilka jednostek lub nawet kilka typów jednostek. Każda wypowiedź jest przetwarzana pod kątem wszystkich jednostek w aplikacji, w tym jednostek przetwarzania języka naturalnego (nauczonych maszynowo).

## <a name="what-has-this-luis-app-accomplished"></a>Co wykonała ta aplikacja LUIS?
Ta aplikacja z jednostką listy wyodrębniła właściwego pracownika. 

Twój czatbot ma teraz wystarczająco dużo informacji, aby określić akcję główną, `MoveEmployee`, i pracownika do przeniesienia. 

## <a name="where-is-this-luis-data-used"></a>Gdzie są używane te dane usługi LUIS? 
Usługa LUIS skończyła obsługiwać to żądanie. Aplikacja wywołująca, taka jak czatbot, może pobrać wynik topScoringIntent (najwyżej oceniana intencja) oraz dane z jednostki, aby wykonać kolejny krok. Usługa LUIS nie wykonuje tej pracy programowej dla bota ani dla aplikacji wywołującej. Usługa LUIS określa jedynie intencję użytkownika. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Wybierz pozycję **My apps** (Moje aplikacje) z menu w lewym górnym rogu. Wybierz wielokropek (**...**) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie ***Delete*** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie jednostki hierarchicznej do aplikacji](luis-quickstart-intent-and-hier-entity.md)


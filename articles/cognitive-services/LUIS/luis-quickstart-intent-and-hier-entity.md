---
title: Samouczek tworzenia aplikacji LUIS w celu uzyskania danych lokalizacji — Azure | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć prostą aplikację LUIS, używając intencji i jednostki hierarchicznej do wyodrębnienia danych.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 07/04/2018
ms.author: diberry
ms.openlocfilehash: fb29e0a22331ce279d3dc8fc5a0044ae794d260b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226088"
---
# <a name="tutorial-5-add-hierarchical-entity"></a>Samouczek: 5. Dodawanie jednostki hierarchicznej
W tym samouczku utworzysz aplikację, która pokazuje, jak znaleźć powiązane elementy danych na podstawie kontekstu. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Omówienie jednostek hierarchicznych i wywnioskowanych z kontekstu elementów podrzędnych 
> * Korzystanie z aplikacji usługi LUIS w domenie zasobów ludzkich (HR, Human Resources) 
> * Dodawanie jednostki hierarchicznej lokalizacji z elementami podrzędnymi miejsca początkowego i docelowego
> * Uczenie i publikowanie aplikacji
> * Wykonywanie względem punktu końcowego zapytania o aplikację w celu sprawdzenia odpowiedzi JSON usługi LUIS, w tym hierarchicznych elementów podrzędnych 

Na potrzeby tego artykułu jest wymagane bezpłatne konto usługi [LUIS](luis-reference-regions.md#luis-website), które umożliwia utworzenie aplikacji usługi LUIS.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli nie masz aplikacji Human Resources z samouczka dotyczącego [jednostek listy](luis-quickstart-intent-and-list-entity.md), [zaimportuj](luis-how-to-start-new-app.md#import-new-app) kod JSON do nowej aplikacji w witrynie internetowej usługi [LUIS](luis-reference-regions.md#luis-website). Aplikacja do zaimportowania znajduje się w repozytorium [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-list-HumanResources.json) usługi Github.

Jeśli chcesz zachować oryginalną aplikację Human Resources, sklonuj tę wersję na stronie [Settings](luis-how-to-manage-versions.md#clone-a-version) (Ustawienia) i nadaj jej nazwę `hier`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. 

## <a name="purpose-of-the-app-with-this-entity"></a>Przeznaczenie aplikacji z tą jednostką
Ta aplikacja określa, dokąd pracownik ma zostać przeniesiony z lokalizacji źródłowej (budynek i biuro) do lokalizacji docelowej (budynek i biuro). Używa ona jednostki hierarchicznej do określania lokalizacji w wypowiedzi. 

Jednostka hierarchiczna jest odpowiednia dla tego typu danych, ponieważ oba elementy danych:

* Są ze sobą powiązane w kontekście wypowiedzi.
* Używają określonych wybranych wyrazów w celu wskazania poszczególnych lokalizacji. Przykładowe wyrazy tego typu to: from/to (od/do), leaving/headed to (opuszczać/kierować się do), away from/toward (w kierunku od/do).
* Obie lokalizacje często znajdują się w tej samej wypowiedzi. 

Celem jednostki **hierarchicznej** jest znalezienie powiązanych danych w wypowiedzi na podstawie kontekstu. Przeanalizujmy następującą wypowiedź:

```JSON
mv Jill Jones from a-2349 to b-1298
```
W wypowiedzi są podane dwie lokalizacje: `a-2349` i `b-1298`. Załóżmy, że litera odpowiada nazwie budynku, a numer wskazuje biuro w tym budynku. Dobrym rozwiązaniem jest pogrupowanie ich jako elementów podrzędnych jednostki hierarchicznej `Locations`, ponieważ oba elementy danych muszą zostać wyodrębnione z wypowiedzi i są ze sobą powiązane. 
 
Jeśli obecny jest tylko jeden element podrzędny jednostki hierarchicznej (lokalizacja początkowa lub docelowa), też jest wyodrębniany. Nie jest konieczne znalezienie wszystkich elementów podrzędnych, aby wyodrębnić jeden lub część z nich. 

## <a name="remove-prebuilt-number-entity-from-app"></a>Usuwanie wstępnie skompilowanej jednostki numeru z aplikacji
Aby zobaczyć całą wypowiedź i oznaczyć hierarchiczne elementy podrzędne, tymczasowo usuń wstępnie skompilowaną jednostkę numeru.

1. Upewnij się, że aplikacja Human Resources znajduje się w sekcji **Build** (Kompilacja) aplikacji LUIS. Możesz przejść do tej sekcji, wybierając pozycję **Build** (Kompilacja) na górnym pasku menu po prawej stronie. 

    [ ![Zrzut ekranu aplikacji LUIS z wyróżnioną pozycją Build (Kompilacja) na górnym prawym pasku nawigacyjnym](./media/luis-quickstart-intent-and-hier-entity/hr-first-image.png)](./media/luis-quickstart-intent-and-hier-entity/hr-first-image.png#lightbox)

2. Wybierz pozycję **Entities** (Jednostki) w menu po lewej stronie.

    [ ![Zrzut ekranu aplikacji LUIS z wyróżnionym przyciskiem Entities (Jednostki) w lewym menu](./media/luis-quickstart-intent-and-hier-entity/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-entities-button.png#lightbox)


3. Wybierz wielokropek (***...***) po prawej stronie jednostki numeru na liście. Wybierz pozycję **Usuń**. 

    [ ![Zrzut ekranu aplikacji LUIS na stronie listy jednostek z przyciskiem Delete (Usuń) wyróżnionym dla wstępnie skompilowanej jednostki numeru](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png)](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png#lightbox)


## <a name="add-utterances-to-moveemployee-intent"></a>Dodawanie wypowiedzi do intencji MoveEmployee

1. Wybierz pozycję **Intents** (Intencje) z menu po lewej.

    [ ![Zrzut ekranu aplikacji LUIS z wyróżnioną pozycją Intents (Intencje) w lewym menu](./media/luis-quickstart-intent-and-hier-entity/hr-select-intents-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-intents-button.png#lightbox)

2. Wybierz pozycję **MoveEmployee** z listy intencji.

    [ ![Zrzut ekranu aplikacji LUIS z wyróżnioną intencją MoveEmployee w lewym menu](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png)](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png#lightbox)

3. Dodaj następujące przykładowe wypowiedzi:

    |Przykładowe wypowiedzi|
    |--|
    |Move John W. Smith **to** a-2345|
    |Direct Jill Jones **to** b-3499|
    |Organize the move of x23456 **from** hh-2345 **to** e-0234|
    |Begin paperwork to set x12345 **leaving** a-3459 **headed to** f-34567|
    |Displace 425-555-0000 **away from** g-2323 **toward** hh-2345|

    W samouczku dotyczącym [jednostki listy](luis-quickstart-intent-and-list-entity.md) można wyznaczyć pracownika według nazwy, adresu e-mail, numeru wewnętrznego, numer telefonu komórkowego lub federalnego numeru ubezpieczenia społecznego (Stany Zjednoczone). Te numery pracowników są używane w zniesławiających. Poprzednie przykładowe wypowiedzi przedstawiają różne sposoby wskazywania lokalizacji źródłowych, które wyróżniono za pomocą pogrubienia. Celowo w niektórych wypowiedziach znajdują się tylko miejsca docelowe. Pomaga to usłudze LUIS zrozumieć, jak te lokalizacje są umieszczane w wypowiedzi, jeśli nie określono źródła.

    [ ![Zrzut ekranu usługi LUIS z nowymi wypowiedziami w intencji MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)
     

## <a name="create-a-location-entity"></a>Tworzenie jednostki Location (Lokalizacja)
Usługa LUIS musi zrozumieć, czym jest lokalizacja, oznaczając źródło i miejsce docelowe w wypowiedziach. Jeśli chcesz zobaczyć wypowiedź w widoku tokenu (nieprzetworzonym), na pasku nad wypowiedziami wybierz przełącznik oznaczony etykietą **Entities View** (Widok jednostek). Po przełączeniu przełącznika kontrolka będzie mieć etykietę **Tokens View** (Widok tokenów).

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

    [ ![Zrzut ekranu przedstawiający przycisk Entities (Jednostki) wyróżniony na lewym pasku nawigacyjnym](./media/luis-quickstart-intent-and-hier-entity/hr-select-entity-button-from-intent-page.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-entity-button-from-intent-page.png#lightbox)

2. Wybierz przycisk **Manage prebuilt entities** (Zarządzaj wstępnie skompilowanymi jednostkami).

    [![Zrzut ekranu z listą Entities (Jednostki) i wyróżnioną pozycją Manage prebuilt entities (Zarządzaj wstępnie skompilowanymi jednostkami)](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png#lightbox)

3. Wybierz pozycję **number** (liczba) z listy wstępnie skompilowanych jednostek, a następnie wybierz pozycję **Done** (Gotowe).

    ![Zrzut ekranu przedstawiający pozycję number (liczba) wybraną w oknie dialogowym wstępnie skompilowanych jednostek](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS
Usługa LUIS nie wie o zmianach intencji i jednostek (modelu), dopóki nie zostanie ich nauczona. 

1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz).

    ![Uczenie aplikacji](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    ![Uczenie powiodło się](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego
Aby uzyskać przewidywania usługi LUIS w czatbocie lub innej aplikacji, należy opublikować aplikację. 

1. W górnej części witryny usługi LUIS po prawej stronie wybierz przycisk **Publish** (Publikuj). 

2. Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj).

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym przyciskiem publikowania w miejscu produkcyjnym")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą różnych wypowiedzi
1. Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym adresem URL punktu końcowego")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

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

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>Czy można było użyć wyrażenia regularnego dla każdej lokalizacji?
Tak, utwórz wyrażenie regularne z rolami źródłowymi i docelowymi, a następnie użyj go we wzorcu.

Lokalizacje w tym przykładzie, takie jak `a-1234`, są zgodnie ze specyficznym formatem obejmującym jedną lub dwie litery z kreską, po których następuje seria 4 lub 5 wartości liczbowych. Te dane można przedstawić jako jednostkę wyrażenia regularnego z rolą dla każdej lokalizacji. Role są dostępne w przypadku wzorców. Można utworzyć wzorce w oparciu o te wypowiedzi, a następnie utworzyć wyrażenie regularne dla formatu lokalizacji i dodawać je do wzorców. <!-- Go to this tutorial to see how that is done -->

## <a name="what-has-this-luis-app-accomplished"></a>Co wykonała ta aplikacja LUIS?
Ta aplikacja, zawierająca jedynie kilka intencji i jednostkę hierarchiczną, zidentyfikowała intencję zapytania w języku naturalnym i zwróciła wyodrębnione dane. 

Twój czatbot ma teraz wystarczająco dużo informacji, aby określić akcję główną (`MoveEmployee`) i informacje o lokalizacji znalezione w wypowiedzi. 

## <a name="where-is-this-luis-data-used"></a>Gdzie są używane te dane usługi LUIS? 
Usługa LUIS skończyła obsługiwać to żądanie. Aplikacja wywołująca, taka jak czatbot, może pobrać wynik topScoringIntent (najwyżej oceniana intencja) oraz dane z jednostki, aby wykonać kolejny krok. Usługa LUIS nie wykonuje tej pracy programowej dla bota ani dla aplikacji wywołującej. Usługa LUIS określa jedynie intencję użytkownika. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Aby to zrobić, wybierz wielokropek (**...**) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie ***Delete*** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"] 
> [Dowiedz się, jak dodać jednostkę złożoną](luis-tutorial-composite-entity.md) 
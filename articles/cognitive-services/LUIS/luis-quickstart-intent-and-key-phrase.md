---
title: Samouczek dotyczący tworzenia aplikacji LUIS zwracającej kluczowe frazy — Azure | Microsoft Docs
description: W tym samouczku nauczysz się, jak dodawać i zwracać jednostkę keyPhrase w aplikacji LUIS, aby analizować wypowiedzi pod kątem kluczowych treści.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 12c306b5199da5862302c28d1690b81c6e1edb0e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264619"
---
# <a name="tutorial-create-app-that-returns-keyphrases-entity-data-found-in-utterances"></a>Samouczek: tworzenie aplikacji, która zwraca dane jednostki keyPhrases znalezione w wypowiedzi
W tym samouczku utworzysz aplikację, która pokazuje, jak wyodrębniać kluczowe treści z wypowiedzi.

<!-- green checkmark -->
> [!div class="checklist"]
> * Omówienie jednostek keyPhrase 
> * Tworzenie nowej aplikacji LUIS dla domeny zasobów ludzkich
> * Dodawanie intencji _None_ i dodawanie przykładowych wypowiedzi
> * Dodawanie jednostki keyPhrase w celu wyodrębnienia zawartości z wypowiedzi
> * Uczenie i publikowanie aplikacji
> * Wysyłanie zapytania do punktu końcowego aplikacji w celu wyświetlenia odpowiedzi JSON usługi LUIS

Na potrzeby tego artykułu możesz użyć bezpłatnego konta usługi [LUIS][LUIS] w celu utworzenia aplikacji LUIS.

## <a name="keyphrase-entity-extraction"></a>Wyodrębnianie jednostki keyPhrase
Kluczowa zawartość jest dostarczana przez wstępnie utworzoną jednostkę **keyPhrase**. Ta jednostka zwraca kluczową treść w wypowiedzi

Następujące wypowiedzi przedstawiają przykłady kluczowych fraz:

|Wypowiedź|Wartości jednostki KeyPhrase|
|--|--|
|Czy w następnym roku jest oferowany nowy plan opieki medycznej z mniejszym wkładem własnym?|„mniejszym wkładem własnym”<br>„nowy plan opieki medycznej”<br>„roku”|
|Czy leczenie wzroku jest objęte planem opieki medycznej z wysokim wkładem własnym?|„planem opieki medycznej z wysokim wkładem własnym”<br>„leczenie wzroku”|

Twój czatbot może uwzględnić te wartości wraz z innymi wyodrębnionymi jednostkami podczas podejmowania decyzji o następnym kroku w konwersacji.

## <a name="download-sample-app"></a>Pobieranie przykładowej aplikacji
Pobierz aplikację [Human Resources](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) i zapisz ją w pliku z rozszerzeniem *.json. Ta przykładowa aplikacja rozpoznaje wypowiedzi dotyczące korzyści pracownika, schematów organizacyjnych i zasobów fizycznych.

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji
1. Zaloguj się w witrynie internetowej usługi [LUIS][LUIS]. Pamiętaj, aby zalogować się w [regionie][LUIS-regions], w którym mają zostać opublikowane punkty końcowe usługi LUIS.

2. W witrynie usługi [LUIS][LUIS] wybierz pozycję **Import new app** (Importuj nową aplikację), aby zaimportować aplikację Human Resources pobraną w poprzedniej sekcji. 

    [![](media/luis-quickstart-intent-and-key-phrase/app-list.png "Zrzut ekranu ze stroną App lists (Listy aplikacji)")](media/luis-quickstart-intent-and-key-phrase/app-list.png#lightbox)

3. W oknie dialogowym **Import new app** (Importowanie nowej aplikacji) nadaj aplikacji nazwę `Human Resources with Key Phrase entity`. 

    ![Obraz przedstawiający okno dialogowe tworzenia nowej aplikacji](./media/luis-quickstart-intent-and-key-phrase/import-new-app-inline.png)

    Po ukończeniu procesu tworzenia aplikacji usługa LUIS wyświetli listę intencji.

    [![](media/luis-quickstart-intent-and-key-phrase/intents-list.png "Zrzut ekranu przedstawiający stronę Intents lists (Listy intencji)")](media/luis-quickstart-intent-and-key-phrase/intents-list.png#lightbox)

## <a name="add-keyphrase-entity"></a>Dodawanie jednostki keyPhrase 
Dodaj wstępnie utworzoną jednostkę keyPhrase, aby wyodrębnić treść z wypowiedzi.

1. Wybierz pozycję **Entities** (Jednostki) w menu po lewej stronie.

    [ ![Zrzut ekranu przedstawiający pozycję Entities (Jednostki) na lewym pasku nawigacyjnym w sekcji Build (Kompilacja)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png#lightbox)

2. Wybierz pozycję **Manage prebuilt entities** (Zarządzaj wstępnie utworzonymi jednostkami).

    [ ![Zrzut ekranu przedstawiający podręczne okno dialogowe z listą jednostek](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png#lightbox)

3. W podręcznym oknie dialogowym wybierz pozycję **keyPhrase**, a następnie wybierz pozycję **Done** (Gotowe). 

    [ ![Zrzut ekranu przedstawiający podręczne okno dialogowe z listą jednostek](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS
Aplikacja LUIS nie będzie wiedzieć o tej zmianie modelu, dopóki nie zostanie tego nauczona. 

1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz).

    ![Zrzut ekranu przedstawiający wyróżniony przycisk Train (Ucz)](./media/luis-quickstart-intent-and-key-phrase/train-button-expanded.png)

2. Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    ![Zrzut ekranu przedstawiający pasek powiadomienia o powodzeniu uczenia ](./media/luis-quickstart-intent-and-key-phrase/trained-inline.png)

## <a name="publish-app-to-endpoint"></a>Publikowanie aplikacji w punkcie końcowym

1. Wybierz pozycję **Publish** (Publikuj) na prawym górnym pasku nawigacyjnym.

    ![Zrzut ekranu przedstawiający stronę Entity (Jednostka) z rozwiniętym przyciskiem Publish (Publikuj) ](./media/luis-quickstart-intent-and-key-phrase/publish-expanded.png)

2. Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj).

    [![](media/luis-quickstart-intent-and-key-phrase/publish-to-production-inline.png "Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym przyciskiem publikowania w miejscu produkcyjnym")](media/luis-quickstart-intent-and-key-phrase/publish-to-production-expanded.png#lightbox)

3. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

## <a name="query-the-endpoint-with-an-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą wypowiedzi

1. Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. 

    ![Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym adresem URL punktu końcowego](media/luis-quickstart-intent-and-key-phrase/endpoint-url-inline.png )

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Is there a new medical plan with a lower deductible offered next year?`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. 

```
{
  "query": "Is there a new medical plan with a lower deductible offered next year?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.216838628
  },
  "entities": [
    {
      "entity": "lower deductible",
      "type": "builtin.keyPhrase",
      "startIndex": 35,
      "endIndex": 50
    },
    {
      "entity": "new medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 26
    },
    {
      "entity": "year",
      "type": "builtin.keyPhrase",
      "startIndex": 65,
      "endIndex": 68
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Co wykonała ta aplikacja LUIS?
Ta aplikacja, wykrywająca jednostki keyPhrase, zidentyfikowała intencję zapytania języka naturalnego i zwróciła wyodrębnione dane zawierające treść główną. 

Twój czatbot ma teraz wystarczająco dużo informacji, aby określić następny krok w konwersacji. 

## <a name="where-is-this-luis-data-used"></a>Gdzie są używane te dane usługi LUIS? 
Usługa LUIS skończyła obsługiwać to żądanie. Aplikacja wywołująca, taka jak czatbot, może pobrać wynik topScoringIntent (najwyżej oceniana intencja) oraz dane jednostki keyPhrase z wypowiedzi, aby wykonać kolejny krok. Usługa LUIS nie wykonuje tej pracy programowej dla bota ani dla aplikacji wywołującej. Usługa LUIS określa jedynie intencję użytkownika. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Aby to zrobić, wybierz menu z trzema kropkami (...) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie **Delete** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji, która zwraca opinię wraz z przewidywaną intencją](luis-quickstart-intent-and-sentiment-analysis.md)

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions

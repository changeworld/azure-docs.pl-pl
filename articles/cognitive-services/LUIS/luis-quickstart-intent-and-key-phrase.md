---
title: Samouczek dotyczący tworzenia aplikacji LUIS zwracającej kluczowe frazy — Azure | Microsoft Docs
description: W tym samouczku nauczysz się, jak dodawać i zwracać jednostkę keyPhrase w aplikacji LUIS, aby analizować wypowiedzi pod kątem kluczowych treści.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: v-geberr
ms.openlocfilehash: ccefb4c2890d74978f340778cfab7cad979c9802
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929556"
---
# <a name="tutorial-8-add-keyphrase-entity"></a>Samouczek: 8. Dodawanie jednostki keyPhrase 
W tym samouczku użyjesz aplikacji, która pokazuje, jak wyodrębniać kluczowe treści z wypowiedzi.

<!-- green checkmark -->
> [!div class="checklist"]
> * Omówienie jednostek keyPhrase 
> * Korzystanie z aplikacji usługi LUIS w domenie zasobów ludzkich (HR, Human Resources) 
> * Dodawanie jednostki keyPhrase w celu wyodrębnienia zawartości z wypowiedzi
> * Uczenie i publikowanie aplikacji
> * Wykonywanie względem punktu końcowego zapytania o aplikację w celu sprawdzenia odpowiedzi JSON usługi LUIS, w tym kluczowych fraz

Na potrzeby tego artykułu możesz użyć bezpłatnego konta usługi [LUIS](luis-reference-regions.md#publishing-regions) w celu utworzenia aplikacji LUIS.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli nie masz aplikacji Human Resources z samouczka dotyczącego [prostej jednostki](luis-quickstart-primary-and-secondary-data.md), [zaimportuj](luis-how-to-start-new-app.md#import-new-app) kod JSON do nowej aplikacji w witrynie internetowej usługi [LUIS](luis-reference-regions.md#luis-website). Aplikacja do zaimportowania znajduje się w repozytorium [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-simple-HumanResources.json) usługi Github.

Jeśli chcesz zachować oryginalną aplikację Human Resources, sklonuj tę wersję na stronie [Settings](luis-how-to-manage-versions.md#clone-a-version) (Ustawienia) i nadaj jej nazwę `keyphrase`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. 

## <a name="keyphrase-entity-extraction"></a>Wyodrębnianie jednostki keyPhrase
Kluczowe treści są dostarczane przez wstępnie skompilowaną jednostkę **keyPhrase**. Ta jednostka zwraca kluczową treść w wypowiedzi.

Następujące wypowiedzi przedstawiają przykłady kluczowych fraz:

|Wypowiedź|Wartości jednostki KeyPhrase|
|--|--|
|Czy w następnym roku jest oferowany nowy plan opieki medycznej z mniejszym wkładem własnym?|„mniejszym wkładem własnym”<br>„nowy plan opieki medycznej”<br>„roku”|
|Czy leczenie wzroku jest objęte planem opieki medycznej z wysokim wkładem własnym?|„planem opieki medycznej z wysokim wkładem własnym”<br>„leczenie wzroku”|

Aplikacja kliencka może użyć tych wartości oraz innych wyodrębnionych jednostek w celu podjęcia decyzji dotyczącej kolejnego kroku rozmowy.

## <a name="add-keyphrase-entity"></a>Dodawanie jednostki keyPhrase 
Dodaj wstępnie utworzoną jednostkę keyPhrase, aby wyodrębnić treść z wypowiedzi.

1. Upewnij się, że aplikacja Human Resources znajduje się w sekcji **Build** (Kompilacja) aplikacji LUIS. Możesz przejść do tej sekcji, wybierając pozycję **Build** (Kompilacja) na górnym pasku menu po prawej stronie. 

    [ ![Zrzut ekranu aplikacji LUIS z wyróżnioną pozycją Build (Kompilacja) na górnym prawym pasku nawigacyjnym](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png)](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png#lightbox)

2. Wybierz pozycję **Entities** (Jednostki) w menu po lewej stronie.

    [ ![Zrzut ekranu przedstawiający pozycję Entities (Jednostki) na lewym pasku nawigacyjnym w sekcji Build (Kompilacja)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png#lightbox)

3. Wybierz pozycję **Manage prebuilt entities** (Zarządzaj wstępnie utworzonymi jednostkami).

    [ ![Zrzut ekranu przedstawiający podręczne okno dialogowe z listą jednostek](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png#lightbox)

4. W podręcznym oknie dialogowym wybierz pozycję **keyPhrase**, a następnie wybierz pozycję **Done** (Gotowe). 

    [ ![Zrzut ekranu przedstawiający podręczne okno dialogowe z listą jednostek](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->
5. Wybierz pozycję **Intents** (Intencje) z menu po lewej stronie, a następnie wybierz intencję **Utilities.Confirm**. Jednostka keyPhrase jest oznaczony etykietą w kilku wypowiedziach. 

    [ ![Zrzut ekranu przedstawiający intencję Utilities.Confirm z jednostkami keyPhrase oznaczonymi etykietą w wypowiedziach](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS
Nowa wersja `keyphrase` aplikacja wymaga uczenia.  

1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz).

    ![Uczenie aplikacji](./media/luis-quickstart-intent-and-key-phrase/train-button.png)

2. Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    ![Uczenie powiodło się](./media/luis-quickstart-intent-and-key-phrase/trained.png)

## <a name="publish-app-to-endpoint"></a>Publikowanie aplikacji w punkcie końcowym

1. Wybierz pozycję **Publish** (Publikuj) na prawym górnym pasku nawigacyjnym.

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png "Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym przyciskiem publikowania w miejscu produkcyjnym")](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png#lightbox)

2. Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj).

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png "Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym przyciskiem publikowania w miejscu produkcyjnym")](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png#lightbox)

3. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

## <a name="query-the-endpoint-with-an-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą wypowiedzi

1. Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. 

    ![Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym adresem URL punktu końcowego](media/luis-quickstart-intent-and-key-phrase/hr-endpoint-url-inline.png )

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `does form hrf-123456 cover the new dental benefits and medical plan`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. 

```
{
  "query": "does form hrf-123456 cover the new dental benefits and medical plan",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.9300641
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.9300641
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0359598845
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0141798034
    },
    {
      "intent": "MoveEmployee",
      "score": 0.0112197418
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00507669244
    },
    {
      "intent": "None",
      "score": 0.00238501839
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00202810857
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00102957746
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0008688423
    },
    {
      "intent": "Utilities.Confirm",
      "score": 3.557994E-05
    }
  ],
  "entities": [
    {
      "entity": "hrf-123456",
      "type": "HRF-number",git 
      "startIndex": 10,
      "endIndex": 19
    },
    {
      "entity": "new dental benefits",
      "type": "builtin.keyPhrase",
      "startIndex": 31,
      "endIndex": 49
    },
    {
      "entity": "medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 55,
      "endIndex": 66
    },
    {
      "entity": "hrf",
      "type": "builtin.keyPhrase",
      "startIndex": 10,
      "endIndex": 12
    },
    {
      "entity": "-123456",
      "type": "builtin.number",
      "startIndex": 13,
      "endIndex": 19,
      "resolution": {
        "value": "-123456"
      }
    }
  ]
}
```

Podczas wyszukiwania formularza użytkownik podał więcej informacji niż wymagał proces wyszukiwania formularza. Dodatkowe informacje są zwracane jako jednostka **builtin.keyPhrase**. Aplikacja kliencka może używać tych dodatkowych informacji na potrzeby kolejnego pytania, takiego jak „Czy chcesz porozmawiać z przedstawicielem działu zasobów ludzkich o zaletach nowej opieki stomatologicznej” lub udostępnić więcej opcji, w tym „Więcej informacji na temat zalet nowej opieki stomatologicznej lub plany medycznego”.

## <a name="what-has-this-luis-app-accomplished"></a>Co wykonała ta aplikacja LUIS?
Ta aplikacja, wykrywająca jednostki keyPhrase, zidentyfikowała intencję zapytania języka naturalnego i zwróciła wyodrębnione dane zawierające treść główną. 

Twój czatbot ma teraz wystarczająco dużo informacji, aby określić następny krok w konwersacji. 

## <a name="where-is-this-luis-data-used"></a>Gdzie są używane te dane usługi LUIS? 
Usługa LUIS skończyła obsługiwać to żądanie. Aplikacja wywołująca, taka jak czatbot, może pobrać wynik topScoringIntent (najwyżej oceniana intencja) oraz dane jednostki keyPhrase z wypowiedzi, aby wykonać kolejny krok. Usługa LUIS nie wykonuje tej pracy programowej dla bota ani dla aplikacji wywołującej. Usługa LUIS określa jedynie intencję użytkownika. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Wybierz pozycję **My apps** (Moje aplikacje) z menu w lewym górnym rogu. Wybierz wielokropek (**...**) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie ***Delete*** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie analizy tonacji do aplikacji](luis-quickstart-intent-and-sentiment-analysis.md)
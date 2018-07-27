---
title: Samouczek dotyczący tworzenia aplikacji LUIS zwracającej analizę tonacji — platforma Azure | Microsoft Docs
description: W tym samouczku nauczysz się, jak dodawać analizę tonacji do aplikacji LUIS, aby analizować wypowiedzi pod kątem pozytywnych, negatywnych i neutralnych uczuć.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: v-geberr
ms.openlocfilehash: 8dc6b8d4dd4cbe64841c4b36c498cf9021b4196f
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930610"
---
# <a name="tutorial-9--add-sentiment-analysis"></a>Samouczek: 9.  Dodawanie analizy tonacji
W tym samouczku utworzysz aplikację, która pokazuje, jak wyodrębniać pozytywne, negatywne i neutralne tonacje z wypowiedzi.

<!-- green checkmark -->
> [!div class="checklist"]
> * Interpretacja analizy tonacji
> * Korzystanie z aplikacji usługi LUIS w domenie zasobów ludzkich (HR, Human Resources) 
> * Dodawanie analizy tonacji
> * Uczenie i publikowanie aplikacji
> * Wysyłanie zapytania do punktu końcowego aplikacji w celu wyświetlenia odpowiedzi JSON usługi LUIS 

Na potrzeby tego artykułu wymagane jest bezpłatne konto usługi [LUIS](luis-reference-regions.md#luis-website) w celu tworzenia aplikacji LUIS.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli nie masz aplikacji Human Resources z samouczka dotyczącego [wstępnie skompilowanej jednostki keyPhrase](luis-quickstart-intent-and-key-phrase.md), [zaimportuj](luis-how-to-start-new-app.md#import-new-app) kod JSON do nowej aplikacji w witrynie internetowej usługi [LUIS](luis-reference-regions.md#luis-website). Aplikacja do zaimportowania znajduje się w repozytorium [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-keyphrase-HumanResources.json) usługi Github.

Jeśli chcesz zachować oryginalną aplikację Human Resources, sklonuj tę wersję na stronie [Settings](luis-how-to-manage-versions.md#clone-a-version) (Ustawienia) i nadaj jej nazwę `sentiment`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. 

## <a name="sentiment-analysis"></a>Analiza tonacji
Analiza tonacji to możliwość określenia, czy wypowiedź użytkownika jest pozytywna, negatywna, czy neutralna. 

Następujące wypowiedzi przedstawiają przykłady tonacji:

|Opinia|Wynik|Wypowiedź|
|:--|:--|:--|
|pozytywna|0,91 |John W. Smith did a great job on the presentation in Paris.|
|pozytywna|0,84 |jill-jones@mycompany.com did fabulous work on the Parker sales pitch.|

Analiza tonacji to ustawienie aplikacji, które ma zastosowanie do każdej wypowiedzi. Nie trzeba wyszukiwać wyrazów wskazujących tonację w wypowiedzi ani oznaczać ich etykietami, ponieważ analiza tonacji dotyczy całej wypowiedzi. 

## <a name="add-employeefeedback-intent"></a>Dodawanie intencji EmployeeFeedback 
Dodanie nowej intencji pozwala na przechwycenie opinii pracowników firmy. 

1. Upewnij się, że aplikacja Human Resources znajduje się w sekcji **Build** (Kompilacja) aplikacji LUIS. Możesz przejść do tej sekcji, wybierając pozycję **Build** (Kompilacja) na górnym pasku menu po prawej stronie. 

    [ ![Zrzut ekranu aplikacji LUIS z wyróżnioną pozycją Build (Kompilacja) na górnym prawym pasku nawigacyjnym](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png#lightbox)

2. Wybierz pozycję **Create new intent** (Utwórz nową intencję).

    [ ![Zrzut ekranu aplikacji LUIS z wyróżnioną pozycją Build (Kompilacja) na górnym prawym pasku nawigacyjnym](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png#lightbox)

3. Nadaj nowej intencji nazwę `EmployeeFeedback`.

    ![Okno dialogowe tworzenia nowej intencji z nazwą EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Dodaj kilka wypowiedzi wskazujących, że pracownik robi coś dobrze lub że musi poprawić swoje wyniki w określonym obszarze:

    Pamiętaj, że w tej aplikacji Human Resources pracownicy są definiowani w jednostce List (Lista), `Employee`, według nazwy, adresu e-mail, numeru wewnętrznego, numer telefonu komórkowego lub federalnego numeru ubezpieczenia społecznego (Stany Zjednoczone). 

    |Wypowiedzi|
    |--|
    |425-555-1212 did a nice job of welcoming back a co-worker from maternity leave|
    |234-56-7891 did a great job of comforting a co-worker in their time of grief.|
    |jill-jones@mycompany.com didn't have all the required invoices for the paperwork.|
    |john.w.smith@mycompany.com turned in the required forms a month late with no signatures|
    |x23456 didn't make it to the important marketing off-site meeting.|
    |x12345 missed the meeting for June reviews.|
    |Jill Jones rocked the sales pitch at Harvard|
    |John W. Smith did a great job on the presentation at Stanford|

    [ ![Zrzut ekranu aplikacji LUIS z przykładami wypowiedzi w intencji EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS
Usługa LUIS nie ma informacji dotyczących nowej intencji ani odpowiednich przykładowych wypowiedzi do momentu zakończenia uczenia. 

1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz).

    ![Zrzut ekranu przedstawiający wyróżniony przycisk Train (Ucz)](./media/luis-quickstart-intent-and-sentiment-analysis/train-button.png)

2. Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    ![Zrzut ekranu przedstawiający pasek powiadomienia o powodzeniu uczenia ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-trained-inline.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurowanie aplikacji pod kątem analizy tonacji
Analiza tonacji jest konfigurowana na stronie **Publish** (Publikowanie). 

1. Wybierz pozycję **Publish** (Publikuj) na prawym górnym pasku nawigacyjnym.

    ![Zrzut ekranu przedstawiający stronę Intent (Intencja) z rozwiniętym przyciskiem Publish (Publikuj) ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-button-in-top-nav-highlighted.png)

2. Wybierz pozycję **Enable Sentiment Analysis** (Włącz analizę tonacji). Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj).

    [![](media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-to-production-expanded.png "Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym przyciskiem publikowania w miejscu produkcyjnym")](media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-to-production-expanded.png#lightbox)

4. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

## <a name="query-the-endpoint-with-an-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą wypowiedzi

1. Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. 

    ![Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym adresem URL punktu końcowego](media/luis-quickstart-intent-and-sentiment-analysis/hr-endpoint-url-inline.png)

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Jill Jones work with the media team on the public portal was amazing`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź jest inna niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić intencję `EmployeeFeedback` z wyodrębnioną analizą tonacji.

```
{
  "query": "Jill Jones work with the media team on the public portal was amazing",
  "topScoringIntent": {
    "intent": "EmployeeFeedback",
    "score": 0.4983256
  },
  "intents": [
    {
      "intent": "EmployeeFeedback",
      "score": 0.4983256
    },
    {
      "intent": "MoveEmployee",
      "score": 0.06617523
    },
    {
      "intent": "GetJobInformation",
      "score": 0.04631853
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0103248553
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.007531875
    },
    {
      "intent": "FindForm",
      "score": 0.00344597152
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00337914471
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0026357458
    },
    {
      "intent": "None",
      "score": 0.00214573368
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00157622492
    },
    {
      "intent": "Utilities.Confirm",
      "score": 7.379545E-05
    }
  ],
  "entities": [
    {
      "entity": "jill jones",
      "type": "Employee",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          "Employee-45612"
        ]
      }
    },
    {
      "entity": "media team",
      "type": "builtin.keyPhrase",
      "startIndex": 25,
      "endIndex": 34
    },
    {
      "entity": "public portal",
      "type": "builtin.keyPhrase",
      "startIndex": 43,
      "endIndex": 55
    },
    {
      "entity": "jill jones",
      "type": "builtin.keyPhrase",
      "startIndex": 0,
      "endIndex": 9
    }
  ],
  "sentimentAnalysis": {
    "label": "positive",
    "score": 0.8694164
  }
}
```

Wynik analizy sentimentAnalysis jest pozytywny i ma wartość 0,86. 

## <a name="what-has-this-luis-app-accomplished"></a>Co wykonała ta aplikacja LUIS?
Ta aplikacja z włączoną funkcją analizy tonacji zidentyfikowała intencję zapytania języka naturalnego i zwróciła wyodrębnione dane zawierające ogólną tonację jako wynik. 

Twój czatbot ma teraz wystarczająco dużo informacji, aby określić następny krok w konwersacji. 

## <a name="where-is-this-luis-data-used"></a>Gdzie są używane te dane usługi LUIS? 
Usługa LUIS skończyła obsługiwać to żądanie. Aplikacja wywołująca, taka jak czatbot, może pobrać wynik topScoringIntent (najwyżej oceniana intencja) oraz dane tonacji z wypowiedzi, aby wykonać kolejny krok. Usługa LUIS nie wykonuje tej pracy programowej dla bota ani dla aplikacji wywołującej. Usługa LUIS określa jedynie intencję użytkownika. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Wybierz pozycję **My apps** (Moje aplikacje) z menu w lewym górnym rogu. Wybierz wielokropek (**...**) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie ***Delete*** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Przejrzyj wypowiedzi punktu końcowego w aplikacji HR](luis-tutorial-review-endpoint-utterances.md) 


---
title: 'Samouczek 9: analiza tonacji — pozytywnych, negatywnych i neutralnych — w usłudze LUIS'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz aplikację, która pokazuje, jak wyodrębniać pozytywne, negatywne i neutralne tonacje z wypowiedzi. Tonację określa się na podstawie całej wypowiedzi.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 95d1c4ffe76cf4c652f347014a838f1250c0ca15
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277481"
---
# <a name="tutorial-9--extract-sentiment-of-overall-utterance"></a>Samouczek 9: wyodrębnianie tonacji całej wypowiedzi
W tym samouczku utworzysz aplikację, która pokazuje, jak wyodrębniać pozytywne, negatywne i neutralne tonacje z wypowiedzi. Tonację określa się na podstawie całej wypowiedzi.

Analiza tonacji to możliwość określenia, czy wypowiedź użytkownika jest pozytywna, negatywna, czy neutralna. 

Następujące wypowiedzi przedstawiają przykłady tonacji:

|Opinia|Wynik|Wypowiedź|
|:--|:--|:--|
|pozytywna|0,91 |John W. Smith did a great job on the presentation in Paris.|
|pozytywna|0,84 |jill-jones@mycompany.com did fabulous work on the Parker sales pitch.|

Analiza tonacji to ustawienie publikowania, które ma zastosowanie do każdej wypowiedzi. Nie trzeba wyszukiwać wyrazów wskazujących tonację w wypowiedzi ani oznaczać ich etykietami, ponieważ analiza tonacji dotyczy całej wypowiedzi. 

Ponieważ jest to ustawienie publikowania, nie widać go na stronach intencji i jednostek. Jest ono widoczne w okienku [interactive test](luis-interactive-test.md#view-sentiment-results) (Test interaktywny) lub podczas testowania pod adresem URL punktu końcowego. 

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Korzystanie z istniejącej aplikacji samouczka 
> * Dodawanie analizy tonacji jako ustawienia publikowania
> * Szkolenie
> * Publikowanie
> * Uzyskiwanie tonacji wypowiedzi z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Korzystanie z istniejącej aplikacji

Przejdź do aplikacji o nazwie **HumanResources** utworzonej w ostatnim samouczku. 

Jeśli nie masz aplikacji HumanResources z poprzedniego samouczka, wykonaj następujące kroki:

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-keyphrase-HumanResources.json).

2. Zaimportuj plik JSON do nowej aplikacji.

3. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `sentiment`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

## <a name="employeefeedback-intent"></a>Intencja EmployeeFeedback 
Dodanie nowej intencji pozwala na przechwycenie opinii pracowników firmy. 

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Wybierz pozycję **Create new intent** (Utwórz nową intencję).

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

## <a name="train"></a>Szkolenie

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurowanie aplikacji pod kątem analizy tonacji
1. Wybierz pozycję **Manage** (Zarządzaj) w prawym górnym okienku nawigacji, a następnie wybierz pozycję **Publish settings** (Ustawienia publikowania) z menu po lewej stronie.

2. Przestaw przełącznik **Sentiment Analysis** (Analiza tonacji), aby włączyć to ustawienie. 

    ![](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish"></a>Publikowanie

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-sentiment-of-utterance-from-endpoint"></a>Uzyskiwanie tonacji wypowiedzi z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Jill Jones work with the media team on the public portal was amazing`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź jest inna niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić intencję `EmployeeFeedback` z wyodrębnioną analizą tonacji.
    
    ```JSON
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

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki
W tym samouczku dodaliśmy analizę tonacji jako ustawienie publikowania, aby wyodrębnić wartości tonacji z wypowiedzi jako całości.

> [!div class="nextstepaction"] 
> [Przejrzyj wypowiedzi punktu końcowego w aplikacji HR](luis-tutorial-review-endpoint-utterances.md) 


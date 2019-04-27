---
title: Przeglądanie wypowiedzi punktu końcowego
titleSuffix: Azure Cognitive Services
description: Ulepszysz przewidywanie aplikacji, weryfikując i poprawiając wypowiedzi odebrane za pośrednictwem punktu końcowego HTTP usługi LUIS i uznane za niepewne przez tę usługę. Niektóre wypowiedzi mogą wymagać weryfikacji pod kątem intencji, a inne — pod kątem jednostki.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: diberry
ms.openlocfilehash: 118ac858103776e880e7304199279a7d50ad71b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599569"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Samouczek: Rozwiązywanie problemów z niepewnymi przewidywaniami przez przeglądanie wypowiedzi punktu końcowego
W tym samouczku ulepszysz przewidywanie aplikacji, weryfikując i poprawiając wypowiedzi odebrane za pośrednictwem punktu końcowego HTTP usługi LUIS i uznane za niepewne przez tę usługę. Niektóre wypowiedzi mogą wymagać weryfikacji pod kątem intencji, a inne — pod kątem jednostki. Należy regularnie przeglądać wypowiedzi punktu końcowego w ramach zaplanowanej konserwacji usługi LUIS. 

Ten proces przeglądu to kolejny sposób, w który usługa LUIS może nauczyć się domeny aplikacji. Usługa LUIS wybrała wypowiedzi występujące na liście do przeglądu. Ta lista ma następujące cechy:

* Jest specyficzna dla aplikacji.
* Ma za zadanie zwiększyć dokładność przewidywania aplikacji. 
* Powinna być regularnie przeglądana. 

Przeglądając wypowiedzi punktu końcowego, weryfikujesz lub poprawiasz przewidzianą intencję wypowiedzi. Możesz też oznaczać etykietami jednostki niestandardowe, które nie zostały przewidziane lub zostały przewidziane niepoprawnie. 

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Przeglądanie wypowiedzi punktu końcowego
> * Aktualizowanie listy fraz
> * Szkolenie aplikacji
> * Publikowanie aplikacji
> * Wysyłanie zapytań do punktu końcowego aplikacji w celu wyświetlenia odpowiedzi JSON usługi LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importowanie aplikacji przykładowej

Przejdź do aplikacji o nazwie **HumanResources** utworzonej w ostatnim samouczku. 

Wykonaj następujące czynności:

1.  Pobierz i zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json).

1. Zaimportuj plik JSON do nowej aplikacji.

1. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `review`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

1. Wytrenuj i opublikuj nową aplikację.

1. Użyj punktu końcowego, aby dodać następujące wypowiedzi. Możesz to zrobić za pomocą [skryptu](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js) lub z punktu końcowego w przeglądarce. Należy dodać następujące wypowiedzi:

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

    Jeśli masz wszystkie wersje aplikacji z całej serii samouczków, może Cię zaskoczyć to, że lista **Review endpoint utterances** (Przeglądanie wypowiedzi punktu końcowego) nie ulega zmianie w zależności od wersji. Jest tylko jedna pula wypowiedzi do przejrzenia, niezależnie od aktywnie edytowanej wersji i od wersji aplikacji opublikowanej w punkcie końcowym. 

## <a name="review-endpoint-utterances"></a>Przeglądanie wypowiedzi punktu końcowego

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Wybierz pozycję **Review endpoint utterances** (Przejrzyj wypowiedzi punktu końcowego) w lewym obszarze nawigacji. Lista jest przefiltrowana pod kątem intencji **ApplyForJob**. 

    [![Zrzut ekranu przycisku Review endpoint utterances (Przejrzyj wypowiedzi punktu końcowego) w lewym obszarze nawigacji](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png#lightbox)

1. Przełącz pozycję **Entities view** (Widok jednostek), aby wyświetlić jednostki oznaczone etykietami. 
    
    [![Zrzut ekranu obszaru Review endpoint utterances (Przeglądanie wypowiedzi punktu końcowego) z wyróżnionym przełącznikiem Entities view (Widok jednostek)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png#lightbox)

    |Wypowiedź|Poprawna intencja|Brakujące jednostki|
    |:--|:--|:--|
    |Szukam zadania przy użyciu przetwarzania języka naturalnegoSzukam zadania przy użyciu przetwarzania języka naturalnego|GetJobInfo|Zadania — "Języka naturalnego procesu"|

    Ta wypowiedź ma nieprawidłową intencję i ocenę niższą niż 50%. Intencja **ApplyForJob** ma 21 wypowiedzi w porównaniu do siedmiu wypowiedzi w intencji **GetJobInformation**. Poza prawidłowym dopasowaniem punktu końcowego należy dodać więcej wypowiedzi do intencji **GetJobInformation**. Ta czynność zostanie zostawiona jako ćwiczenie do samodzielnego wykonania. Wszystkie intencje z wyjątkiem intencji **None** powinny mieć mniej więcej taką samą liczbę przykładowych wypowiedzi. Intencja **None** powinna mieć 10% wszystkich wypowiedzi w aplikacji. 

1. Dla wypowiedzi `I'm looking for a job with Natual Language Processing` wybierz poprawną intencję, **GetJobInformation**, w kolumnie **Aligned intent** (Dopasowana intencja). 

    [![Zrzut ekranu obszaru Review endpoint utterances (Przeglądanie wypowiedzi punktu końcowego) — dopasowywanie wypowiedzi do intencji](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

1. W tej samej wypowiedzi jednostka dla frazy `Natural Language Processing` to keyPhrase. Zamiast niej powinna to być jednostka **Job**. Wybierz frazę `Natural Language Processing`, a następnie jednostkę **Job** z listy.

    [![Zrzut ekranu obszaru Review endpoint utterances (Przeglądanie wypowiedzi punktu końcowego) — oznaczanie etykietą jednostek w wypowiedzi](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

1. W tym samym wierszu wybierz ikonę znacznika wyboru w okręgu w kolumnie **Add to aligned intent** (Dodaj do dopasowanej intencji). 

    [![Zrzut ekranu przedstawiający finalizowanie dopasowywania wypowiedzi do intencji](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    Ta akcja powoduje przeniesienie wypowiedzi z obszaru **Review endpoint utterances** (Przeglądanie wypowiedzi punktu końcowego) do intencji **GetJobInformation**. Wypowiedź punktu końcowego jest teraz przykładową wypowiedzią dla tej intencji. 

1. Przejrzyj pozostałe wypowiedzi w tej intencji, oznaczając je etykietami i poprawiając intencję **Aligned intent** (Dopasowana intencja), jeśli jest ona nieprawidłowa.

1. Po poprawieniu wszystkich wypowiedzi zaznacz pole wyboru w każdym wierszu, a następnie wybierz przycisk **Add selected** (Dodaj wybrane), aby poprawnie dopasować wypowiedzi. 

    [![Zrzut ekranu przedstawiający finalizowanie dopasowanej intencji pozostałych wypowiedzi](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

1. Te wypowiedzi powinny zniknąć z listy. Jeśli zostaną wyświetlone kolejne wypowiedzi, kontynuuj pracę z listą, poprawiając intencje i oznaczając etykietami wszelkie brakujące jednostki, dopóki lista nie będzie pusta. 

1. Wybierz kolejną intencję z listy Filter (Filtr) i kontynuuj poprawianie wypowiedzi oraz oznaczanie jednostek etykietami. Pamiętaj, że ostatnim krokiem dla każdej intencji jest wybranie opcji **Add to aligned intent** (Dodaj do dopasowanej intencji) w wierszu wypowiedzi lub zaznaczenie pola wyboru obok każdej intencji i wybranie przycisku **Add selected** (Dodaj wybrane) nad tabelą.

    Kontynuuj, aż wszystkie intencje i jednostki na liście filtru będą miały pustą listę. Ta aplikacja jest bardzo mała. Proces przeglądu zajmuje tylko kilka minut. 

## <a name="update-phrase-list"></a>Aktualizowanie listy fraz
Na bieżąco aktualizuj listę fraz, dodając do niej wszelkie nowo odnalezione nazwy stanowisk. 

1. Wybierz pozycję **Phrase lists** (Listy fraz) w lewym obszarze nawigacji.

2. Wybierz listę fraz **Jobs** (Stanowiska).

3. Dodaj wartość `Natural Language Processing`, a następnie wybierz pozycję **Save** (Zapisz). 

## <a name="train"></a>Szkolenie

Usługa LUIS nie wie o zmianach, dopóki nie zostanie nauczona. 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikowanie

Jeśli zaimportowano tę aplikację, musisz wybrać pozycję **Sentiment analysis** (Analiza tonacji).

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Pobieranie intencji i jednostek z punktu końcowego

Spróbuj użyć wypowiedzi podobnej do poprawionej wypowiedzi. 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Are there any natural language processing jobs in my department right now?`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. 

   ```json
   {
    "query": "are there any natural language processing jobs in my department right now?",
    "topScoringIntent": {
      "intent": "GetJobInformation",
      "score": 0.9247605
    },
    "intents": [
      {
        "intent": "GetJobInformation",
        "score": 0.9247605
      },
      {
        "intent": "ApplyForJob",
        "score": 0.129989788
      },
      {
        "intent": "FindForm",
        "score": 0.006438211
      },
      {
        "intent": "EmployeeFeedback",
        "score": 0.00408575451
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00194211153
      },
      {
        "intent": "None",
        "score": 0.00166400627
      },
      {
        "intent": "Utilities.Help",
        "score": 0.00118593348
      },
      {
        "intent": "MoveEmployee",
        "score": 0.0007885918
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.0006373631
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0005980781
      },
      {
        "intent": "Utilities.Confirm",
        "score": 3.719905E-05
      }
    ],
    "entities": [
      {
        "entity": "right now",
        "type": "builtin.datetimeV2.datetime",
        "startIndex": 64,
        "endIndex": 72,
        "resolution": {
          "values": [
            {
              "timex": "PRESENT_REF",
              "type": "datetime",
              "value": "2018-07-05 15:23:18"
            }
          ]
        }
      },
      {
        "entity": "natural language processing",
        "type": "Job",
        "startIndex": 14,
        "endIndex": 40,
        "score": 0.9869922
      },
      {
        "entity": "natural language processing jobs",
        "type": "builtin.keyPhrase",
        "startIndex": 14,
        "endIndex": 45
      },
      {
        "entity": "department",
        "type": "builtin.keyPhrase",
        "startIndex": 53,
        "endIndex": 62
      }
    ],
    "sentimentAnalysis": {
      "label": "positive",
      "score": 0.8251864
    }
   }
   }
   ```

   Prawidłowa intencja została przewidziana i uzyskała wysoką ocenę oraz wykryto jednostkę typu **Job** `natural language processing`. 

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Czy zamiast przeglądania można dodać więcej wypowiedzi? 
Możesz się zastanawiać, dlaczego nie dodać więcej przykładowych wypowiedzi. Jaki jest cel przeglądania wypowiedzi punktu końcowego? W rzeczywistej aplikacji usługi LUIS wypowiedzi punktu końcowego pochodzą od użytkowników, a ich dobór i kolejność słów różnią się od użytych. Gdyby użyto tych samych słów w tej samej kolejności, pierwotne przewidywanie miałoby wyższą wartość procentową. 

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Dlaczego intencja o najwyższej ocenie znajduje się na liście wypowiedzi? 
Niektóre wypowiedzi punktu końcowego będą miały wysoki współczynnik przewidywania na liście do przeglądu. Mimo to należy przejrzeć i zweryfikować te wypowiedzi. Znajdują się one na liście, ponieważ różnica między intencją o najwyższej ocenie i intencją drugą w kolejności jest zbyt mała. Różnica między dwiema najważniejszymi intencjami powinna wynosić około 15%.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przejrzano wypowiedzi przesłane w punkcie końcowym, które usługa LUIS uznała za niepewne. Zweryfikowanie i przeniesienie tych wypowiedzi do poprawnych intencji jako przykładowych wypowiedzi spowoduje, że dokładność przewidywania usługi LUIS zwiększy się.

> [!div class="nextstepaction"]
> [Dowiedz się, jak używać wzorców](luis-tutorial-pattern.md)

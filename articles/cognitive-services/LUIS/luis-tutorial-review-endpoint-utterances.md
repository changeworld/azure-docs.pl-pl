---
title: 'Samouczek: przeglądanie punktu końcowego wyrażenia długości — LUIS'
titleSuffix: Azure Cognitive Services
description: Ulepszysz przewidywanie aplikacji, weryfikując i poprawiając wypowiedzi odebrane za pośrednictwem punktu końcowego HTTP usługi LUIS i uznane za niepewne przez tę usługę. Niektóre wypowiedzi mogą wymagać weryfikacji pod kątem intencji, a inne — pod kątem jednostki.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 04f30818e3c871d74d94bfd92bd3f73e4e6637a0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499410"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Samouczek: usuwanie prognoz przez przeglądanie punktu końcowego wyrażenia długości
W tym samouczku ulepszysz przewidywanie aplikacji, weryfikując i poprawiając wypowiedzi odebrane za pośrednictwem punktu końcowego HTTP usługi LUIS i uznane za niepewne przez tę usługę. Niektóre wypowiedzi mogą wymagać weryfikacji pod kątem intencji, a inne — pod kątem jednostki. Należy regularnie przeglądać wypowiedzi punktu końcowego w ramach zaplanowanej konserwacji usługi LUIS. 

Ten proces przeglądu to kolejny sposób, w który usługa LUIS może nauczyć się domeny aplikacji. Usługa LUIS wybrała wypowiedzi występujące na liście do przeglądu. Ta lista ma następujące cechy:

* Jest specyficzna dla aplikacji.
* Ma za zadanie zwiększyć dokładność przewidywania aplikacji. 
* Powinna być regularnie przeglądana. 

Przeglądając wypowiedzi punktu końcowego, weryfikujesz lub poprawiasz przewidzianą intencję wypowiedzi. Możesz też oznaczać etykietami jednostki niestandardowe, które nie zostały przewidziane lub zostały przewidziane niepoprawnie. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Przeglądanie wypowiedzi punktu końcowego
> * Aktualizowanie listy fraz
> * Szkolenie aplikacji
> * Publikowanie aplikacji
> * Wysyłanie zapytania do punktu końcowego aplikacji w celu wyświetlenia odpowiedzi JSON usługi LUIS

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


    Ta wypowiedź `I'm looking for a job with Natural Language Processing`nie jest w poprawnym zamiarem. 

    Przyczyną nieoczekiwanej przewidywania wypowiedź jest to, że intencja **ApplyForJob** ma 21 wyrażenia długości w porównaniu do 7 wyrażenia długości w **GetJobInformation**. Cel o większej wyrażenia długości będzie miał wyższą prognozę. Należy pamiętać, że ilość i jakość wyrażenia długości w ramach intencji są zrównoważone.

1.  Aby wyrównać tę wypowiedź, wybierz odpowiednie przeznaczenie i Oznacz w niej jednostkę zadania. Dodaj zmienione wypowiedź do aplikacji, zaznaczając zieloną wartość pola wyboru. 

    |Wypowiedź|Poprawna intencja|Brakujące jednostki|
    |:--|:--|:--|
    |`I'm looking for a job with Natural Language Processing`|GetJobInfo|Zadania — "Języka naturalnego procesu"|

    Aby zmienić `natural language processing` z jednostki keyPhrase na jednostkę zadania, wybierz frazę, a następnie wybierz z listy pozycję **zadanie** . Jeśli chcesz wybrać tylko część tekstu keyPhrase dla innej jednostki, musisz usunąć keyPhrase jako jednostkę, etykietę z inną jednostką, a następnie ponownie zastosować jednostkę keyPhrase do aplikacji. 

    Dodanie wypowiedź przenosi wypowiedź z **punktu końcowego przeglądu wyrażenia długości** do zamiaru **GetJobInformation** . Wypowiedź punktu końcowego jest teraz przykładową wypowiedzią dla tej intencji. 

    Wraz z prawidłowym dostosowaniem tego wypowiedź do zamiaru **GetJobInformation** należy dodać więcej wyrażenia długości. Ta czynność zostanie zostawiona jako ćwiczenie do samodzielnego wykonania. Wszystkie intencje z wyjątkiem intencji **None** powinny mieć mniej więcej taką samą liczbę przykładowych wypowiedzi. Intencja **None** powinna mieć 10% wszystkich wypowiedzi w aplikacji. 

    Przejrzyj pozostałe wypowiedzi w tej intencji, oznaczając je etykietami i poprawiając intencję **Aligned intent** (Dopasowana intencja), jeśli jest ona nieprawidłowa.

    Lista **wyrażenia długości punktów końcowych recenzji** nie powinna już mieć tych wyrażenia długości. Jeśli zostaną wyświetlone kolejne wypowiedzi, kontynuuj pracę z listą, poprawiając intencje i oznaczając etykietami wszelkie brakujące jednostki, dopóki lista nie będzie pusta. 

    Wybierz kolejną intencję z listy Filter (Filtr) i kontynuuj poprawianie wypowiedzi oraz oznaczanie jednostek etykietami. Pamiętaj, że ostatnim krokiem dla każdej intencji jest wybranie opcji **Add to aligned intent** (Dodaj do dopasowanej intencji) w wierszu wypowiedzi lub zaznaczenie pola wyboru obok każdej intencji i wybranie przycisku **Add selected** (Dodaj wybrane) nad tabelą.

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

## <a name="next-steps"></a>Następne kroki
W tym samouczku przejrzano wypowiedzi przesłane w punkcie końcowym, które usługa LUIS uznała za niepewne. Zweryfikowanie i przeniesienie tych wypowiedzi do poprawnych intencji jako przykładowych wypowiedzi spowoduje, że dokładność przewidywania usługi LUIS zwiększy się.

> [!div class="nextstepaction"]
> [Dowiedz się, jak używać wzorców](luis-tutorial-pattern.md)

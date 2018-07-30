---
title: Samouczek przeglądania wypowiedzi punktu końcowego w usłudze Language Understanding (LUIS) — Azure | Microsoft Docs
description: W tym samouczku dowiesz się, jak przeglądać wypowiedzi punktu końcowego w domenie zasobów ludzkich (HR, Human Resources) w usłudze LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 07/03/2018
ms.author: diberry
ms.openlocfilehash: 1f1e3310e0d02983aaecc3f87ba9c116d65b751b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237217"
---
# <a name="tutorial-review-endpoint-utterances"></a>Samouczek: przeglądanie wypowiedzi punktu końcowego
W tym samouczku ulepszysz przewidywanie aplikacji, weryfikując i poprawiając wypowiedzi odebrane za pośrednictwem punktu końcowego HTTP usługi LUIS. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Informacje o przeglądaniu wypowiedzi punktu końcowego 
> * Korzystanie z aplikacji LUIS dla domeny zasobów ludzkich (HR, Human Resources) 
> * Przeglądanie wypowiedzi punktu końcowego
> * Uczenie i publikowanie aplikacji
> * Wysyłanie zapytań do punktu końcowego aplikacji w celu wyświetlenia odpowiedzi JSON usługi LUIS

Na potrzeby tego artykułu wymagane jest bezpłatne konto usługi [LUIS](luis-reference-regions.md#luis-website) w celu tworzenia aplikacji LUIS.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli nie masz aplikacji Human Resources z samouczka dotyczącego [opinii](luis-quickstart-intent-and-sentiment-analysis.md), zaimportuj ją z repozytorium GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-sentiment-HumanResources.json). Jeśli w tym samouczku używasz nowej, zaimportowanej aplikacji, musisz także przeprowadzić uczenie i opublikować ją, a następnie dodać wypowiedzi do punktu końcowego za pomocą [skryptu](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js) lub z poziomu punktu końcowego w przeglądarce. Należy dodać następujące wypowiedzi:

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

Jeśli chcesz zachować oryginalną aplikację Human Resources, sklonuj tę wersję na stronie [Settings](luis-how-to-manage-versions.md#clone-a-version) (Ustawienia) i nadaj jej nazwę `review`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. 

Jeśli masz wszystkie wersje aplikacji z całej serii samouczków, może Cię zaskoczyć to, że lista **Review endpoint utterances** (Przeglądanie wypowiedzi punktu końcowego) nie ulega zmianie w zależności od wersji. Jest tylko jedna pula wypowiedzi do przejrzenia, niezależnie od aktywnie edytowanej wersji wypowiedzi ani od wersji aplikacji opublikowanej w punkcie końcowym. 

## <a name="purpose-of-reviewing-endpoint-utterances"></a>Cel przeglądania wypowiedzi punktu końcowego
Ten proces przeglądu to kolejny sposób, w który usługa LUIS może nauczyć się domeny aplikacji. Usługa LUIS wybrała wypowiedzi na liście do przeglądu. Ta lista ma następujące cechy:

* Jest specyficzna dla aplikacji.
* Ma za zadanie zwiększyć dokładność przewidywania aplikacji. 
* Powinna być regularnie przeglądana. 

Przeglądając wypowiedzi punktu końcowego, weryfikujesz lub poprawiasz przewidzianą intencję wypowiedzi. Ponadto dodajesz etykiety do jednostek niestandardowych, które nie zostały przewidziane. 

## <a name="review-endpoint-utterances"></a>Przeglądanie wypowiedzi punktu końcowego

1. Upewnij się, że aplikacja Human Resources znajduje się w sekcji **Build** (Kompilacja) aplikacji LUIS. Możesz przejść do tej sekcji, wybierając pozycję **Build** (Kompilacja) na górnym pasku menu po prawej stronie. 

    [ ![Zrzut ekranu aplikacji LUIS z wyróżnioną pozycją Build (Kompilacja) na górnym prawym pasku nawigacyjnym](./media/luis-tutorial-review-endpoint-utterances/first-image.png)](./media/luis-tutorial-review-endpoint-utterances/first-image.png#lightbox)

1. Wybierz pozycję **Review endpoint utterances** (Przejrzyj wypowiedzi punktu końcowego) w lewym obszarze nawigacji. Lista jest przefiltrowana pod kątem intencji **ApplyForJob**. 

    [ ![Zrzut ekranu przycisku Review endpoint utterances (Przejrzyj wypowiedzi punktu końcowego) w lewym obszarze nawigacji](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png)](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png#lightbox)

2. Przełącz pozycję **Entities view** (Widok jednostek), aby wyświetlić jednostki oznaczone etykietami. 
    
    [ ![Zrzut ekranu obszaru Review endpoint utterances (Przeglądanie wypowiedzi punktu końcowego) z wyróżnionym przełącznikiem Entities view (Widok jednostek)](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png)](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png#lightbox)

    |Wypowiedź|Poprawna intencja|Brakujące jednostki|
    |:--|:--|:--|
    |I'm looking for a job with Natural Language Processing|GetJobInfo|Job — „Natural Language Process”|

    Ta wypowiedź ma nieprawidłową intencję i ocenę niższą niż 50%. Intencja **ApplyForJob** ma 21 wypowiedzi w porównaniu do siedmiu wypowiedzi w intencji **GetJobInformation**. Poza prawidłowym dopasowaniem punktu końcowego należy dodać więcej wypowiedzi do intencji **GetJobInformation**. Ta czynność zostanie zostawiona jako ćwiczenie do samodzielnego wykonania. Wszystkie intencje z wyjątkiem intencji **None** powinny mieć mniej więcej taką samą liczbę przykładowych wypowiedzi. Intencja **None** powinna mieć 10% wszystkich wypowiedzi w aplikacji. 

    W obszarze **Tokens View** (Widok tokenów) możesz zatrzymać wskaźnik myszy na dowolnym niebieskim tekście w wypowiedzi, aby zobaczyć przewidywaną nazwę jednostki. 

3. Dla wypowiedzi `I'm looking for a job with Natual Language Processing` wybierz poprawną intencję, **GetJobInformation**, w kolumnie **Aligned intent** (Dopasowana intencja). 

    [ ![Zrzut ekranu obszaru Review endpoint utterances (Przeglądanie wypowiedzi punktu końcowego) — dopasowywanie wypowiedzi do intencji](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

4. W tej samej wypowiedzi jednostka dla frazy `Natural Language Processing` to keyPhrase. Zamiast niej powinna to być jednostka **Job**. Wybierz frazę `Natural Language Processing`, a następnie jednostkę **Job** z listy.

    [ ![Zrzut ekranu obszaru Review endpoint utterances (Przeglądanie wypowiedzi punktu końcowego) — oznaczanie etykietą jednostek w wypowiedzi](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

5. W tym samym wierszu wybierz ikonę znacznika wyboru w okręgu w kolumnie **Add to aligned intent** (Dodaj do dopasowanej intencji). 

    [ ![Zrzut ekranu przedstawiający finalizowanie dopasowywania wypowiedzi do intencji](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    Ta akcja powoduje przeniesienie wypowiedzi z obszaru **Review endpoint utterances** (Przeglądanie wypowiedzi punktu końcowego) do intencji **GetJobInformation**. Wypowiedź punktu końcowego jest teraz przykładową wypowiedzią dla tej intencji. 

6. Przejrzyj pozostałe wypowiedzi w tej intencji, oznaczając je etykietami i poprawiając intencję **Aligned intent** (Dopasowana intencja), jeśli jest ona nieprawidłowa.

7. Po poprawieniu wszystkich wypowiedzi zaznacz pole wyboru w każdym wierszu, a następnie wybierz przycisk **Add selected** (Dodaj wybrane), aby poprawnie dopasować wypowiedzi. 

    [ ![Zrzut ekranu przedstawiający finalizowanie dopasowanej intencji pozostałych wypowiedzi](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

8. Te wypowiedzi powinny zniknąć z listy. Jeśli zostaną wyświetlone kolejne wypowiedzi, kontynuuj pracę z listą, poprawiając intencje i oznaczając etykietami wszelkie brakujące jednostki, dopóki lista nie będzie pusta. Wybierz kolejną intencję z listy Filter (Filtr) i kontynuuj poprawianie wypowiedzi oraz oznaczanie jednostek etykietami. Pamiętaj, że ostatnim krokiem dla każdej intencji jest wybranie opcji **Add to aligned intent** (Dodaj do dopasowanej intencji) w wierszu wypowiedzi lub zaznaczenie pola wyboru obok każdej intencji i wybranie przycisku **Add selected** (Dodaj wybrane) nad tabelą. 

    Ta aplikacja jest bardzo mała. Proces przeglądu zajmuje tylko kilka minut.

## <a name="add-new-job-name-to-phrase-list"></a>Dodawanie nowej nazwy stanowiska do list fraz
Na bieżąco aktualizuj listę fraz, dodając do niej wszelkie nowo odnalezione nazwy stanowisk. 

1. Wybierz pozycję **Phrase lists** (Listy fraz) w lewym obszarze nawigacji.

2. Wybierz listę fraz **Jobs** (Stanowiska).

3. Dodaj wartość `Natural Language Processing`, a następnie wybierz pozycję **Save** (Zapisz). 

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS
Usługa LUIS nie wie o zmianach, dopóki nie zostanie nauczona. 

1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz).

2. Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego
Aby uzyskać zaktualizowany model aplikacji usługi LUIS w czatbocie lub innej aplikacji, należy opublikować aplikację. 

1. W górnej części witryny usługi LUIS po prawej stronie wybierz przycisk **Publish** (Publikuj). 

2. Jeśli zaimportowano tę aplikację, musisz wybrać pozycję **Sentiment analysis** (Analiza tonacji). 

3. Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj).

4. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

## <a name="query-the-endpoint-with-an-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą wypowiedzi
Spróbuj użyć wypowiedzi podobnej do poprawionej wypowiedzi. 

1. Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. 

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Are there any natural language processing jobs in my department right now?`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. 

```JSON
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
Niektóre wypowiedzi punktu końcowego będą miały wysoką wartość procentową na liście do przeglądu. Mimo to należy przejrzeć i zweryfikować te wypowiedzi. Znajdują się one na liście, ponieważ różnica między intencją o najwyższej ocenie i intencją drugą w kolejności jest zbyt mała. 

## <a name="what-has-this-tutorial-accomplished"></a>Co osiągnięto w ramach tego samouczka?
Dokładność przewidywania aplikacji została zwiększona przez przejrzenie wypowiedzi z punktu końcowego. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Wybierz pozycję **My apps** (Moje aplikacje) z menu w lewym górnym rogu. Wybierz wielokropek (**...**) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie **Delete** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak używać wzorców](luis-tutorial-pattern.md)

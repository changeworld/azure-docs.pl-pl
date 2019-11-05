---
title: 'Samouczek: przewidywanie zamiarów — LUIS'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz niestandardową aplikację, która będzie przewidywać intencje użytkownika. Ta aplikacja to najprostszy rodzaj aplikacji usługi LUIS, ponieważ nie wyodrębnia ona z tekstu wypowiedzi różnych elementów danych, takich jak adresy e-mail czy daty.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 83ecf0767f2b21065c698421e3ad8f07f31d5b16
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465291"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Samouczek: kompilowanie aplikacji LUIS w celu określenia zamiarów użytkownika

W tym samouczku utworzysz niestandardową aplikację Human Resources (HR), która na podstawie wypowiedzi (tekstu) będzie przewidywała intencje użytkownika. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie nowej aplikacji 
> * Tworzenie intencji
> * Dodawanie przykładowych wypowiedzi
> * Szkolenie aplikacji
> * Publikowanie aplikacji
> * Pobieranie intencji z punktu końcowego


[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="user-intentions-as-intents"></a>Zamiary użytkownika jako intencje

Zadaniem aplikacji jest ustalenie intencji konwersacyjnego tekstu w języku naturalnym: 

`Are there any new positions in the Seattle office?`

Intencje są podzielone na kategorie **intencji**. 

Ta aplikacja ma kilka intencji. 

|Intencja|Przeznaczenie|
|--|--|
|`ApplyForJob`|Określa, czy użytkownik ubiega się o pracę.|
|`GetJobInformation`|Określa, czy użytkownik szuka informacji dotyczących pracy w ogóle, czy jakiejś konkretnej pracy.|
|`None`|Określa, czy użytkownik zadaje pytanie, na które aplikacja nie powinna odpowiadać. Ta intencja jest dostarczana jako część procesu tworzenia aplikacji i nie można jej usunąć. |

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Tworzenie intencji dotyczącej informacji o pracy

1. Wybierz pozycję**Create new intent** (Utwórz nową intencję). Wprowadź nazwę nowej intencji: `GetJobInformation`. Ta intencja jest przewidywana, gdy użytkownik chce uzyskać informacje o stanowiskach dostępnych w firmie. 

    ![Zrzut ekranu przedstawiający okno dialogowe nowego zamiaru Language Understanding (LUIS)](media/luis-quickstart-intents-only/create-intent.png "Zrzut ekranu przedstawiający okno dialogowe nowego zamiaru Language Understanding (LUIS)")

1. Wybierz pozycję **Done** (Gotowe).

2. Dodaj kilka przykładowych wypowiedzi do tej intencji — takich, których spodziewasz się ze strony użytkownika:

    | Przykładowe wypowiedzi|
    |--|
    |`Any new jobs posted today?`|
    |`Are there any new positions in the Seattle office?`|
    |`Are there any remote worker or telecommute jobs open for engineers?`|
    |`Is there any work with databases?`|
    |`I'm looking for a co-working situation in the tampa office.`|
    |`Is there an internship in the san francisco office?`|
    |`Is there any part-time work for people in college?`|
    |`Looking for a new situation with responsibilities in accounting`|
    |`Looking for a job in new york city for bilingual speakers.`|
    |`Looking for a new situation with responsibilities in accounting.`|
    |`New jobs?`|
    |`Show me all the jobs for engineers that were added in the last 2 days.`|
    |`Today's job postings?`|
    |`What accounting positions are open in the london office?`|
    |`What positions are available for Senior Engineers?`|
    |`Where is the job listings`|

    [![Zrzut ekranu przedstawiający wprowadzanie nowych wyrażenia długości dla zamiaru elementu mójMagazyn](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Zrzut ekranu przedstawiający wprowadzanie nowych wyrażenia długości dla zamiaru elementu mójMagazyn")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    Dostarczając _przykładowe wypowiedzi_, trenujesz usługę LUIS, jakiego rodzaju wypowiedzi powinny być przewidywane dla tej intencji. 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>Dodawanie przykładowych wypowiedzi do intencji None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Trenowanie aplikacji przed testowaniem lub publikowaniem

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikowanie aplikacji w celu wysyłania zapytań z punktu końcowego

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>Pobieranie przewidywania intencji z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Przejdź na koniec adresu URL na pasku adresu i wprowadź ciąg `I'm looking for a job with Natural Language Processing`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź nie przypomina żadnej z przykładowych wypowiedzi. Jest to dobry test, który powinien zwrócić intencję `GetJobInformation` jako najwyżej ocenioną. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.9923871
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.9923871
        },
        {
          "intent": "None",
          "score": 0.007810574
        }
      ],
      "entities": []
    }
    ```

    Parametr ciągu zapytania `verbose=true` oznacza, że w wynikach zapytania aplikacji zostaną uwzględnione **wszystkie intencje**. Tablica jednostek jest pusta, ponieważ ta aplikacja obecnie nie ma żadnych jednostek. 

    Wynik w formacie JSON określa najwyżej ocenioną intencję jako właściwość **`topScoringIntent`** . Wszystkie wyniki należą do zakresu od 1 do 0, przy czym im bliżej 1, tym lepiej. 

## <a name="create-intent-for-job-applications"></a>Tworzenie intencji na potrzeby podań o pracę

Wróć do portalu usługi LUIS i utwórz nową intencję, która będzie określała, czy wypowiedź użytkownika dotyczy podania o pracę.

1. Wybierz pozycję **Build** (Twórz) z menu w prawym górnym rogu, aby powrócić do tworzenia aplikacji.

1. Wybierz pozycję **Intents** (Intencje) w menu po lewej stronie, aby przejść do listy intencji.

1. Wybierz polecenie **Create new intent** (Utwórz nową intencję) i podaj nazwę `ApplyForJob`. 

    ![Okno dialogowe usługi LUIS umożliwiające utworzenie nowej intencji](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. Dodaj kilka wypowiedzi do intencji — takich, których spodziewasz się ze strony użytkownika, na przykład:

    | Przykładowe wypowiedzi|
    |--|
    |`Fill out application for Job 123456`|
    |`Here is my c.v. for position 654234`|
    |`Here is my resume for the part-time receptionist post.`|
    |`I'm applying for the art desk job with this paperwork.`|
    |`I'm applying for the summer college internship in Research and Development in San Diego`|
    |`I'm requesting to submit my resume to the temporary position in the cafeteria.`|
    |`I'm submitting my resume for the new Autocar team in Columbus, OH`|
    |`I want to apply for the new accounting job`|
    |`Job 456789 accounting internship paperwork is here`|
    |`Job 567890 and my paperwork`|
    |`My papers for the tulsa accounting internship are attached.`|
    |`My paperwork for the holiday delivery position`|
    |`Please send my resume for the new accounting job in seattle`|
    |`Submit resume for engineering position`|
    |`This is my c.v. for post 234123 in Tampa.`|


## <a name="train-again"></a>Ponowne szkolenie

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Ponowne publikowanie

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>Ponowne pobieranie przewidywania intencji

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. W nowym oknie przeglądarki podaj ciąg `Can I submit my resume for job 235986` na końcu adresu URL. 

    ```json
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9634406
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9634406
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0171300638
        },
        {
          "intent": "None",
          "score": 0.00670867041
        }
      ],
      "entities": []
    }
    ```

    Wyniki zawierają nową intencję **ApplyForJob**, a także istniejące intencje. 

## <a name="client-application-next-steps"></a>Następne czynności dotyczące aplikacji klienckiej

Gdy usługa LUIS zwróci odpowiedź w formacie JSON, żądanie zostanie wykonane. Usługa LUIS nie odpowiada na wypowiedzi użytkowników, tylko określa, o jakie informacje proszą oni w języku naturalnym. Dalsza konwersacja jest udostępniana przez aplikację kliencką, na przykład Azure Bot. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Typy jednostek](luis-concept-entity-types.md)
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono aplikację Human Resources (HR), utworzono 2 intencje, dodano przykładowe wypowiedzi do każdej intencji, dodano przykładowe wypowiedzi do intencji None, przeprowadzono szkolenie, opublikowano i przetestowano w punkcie końcowym. Są to podstawowe kroki tworzenia modelu usługi LUIS. 

Kontynuuj pracę z tą aplikacją, [dodając prostą jednostkę i listę fraz](luis-quickstart-primary-and-secondary-data.md).

> [!div class="nextstepaction"]
> [Dodawanie wstępnie utworzonych intencji i jednostek do aplikacji](luis-tutorial-prebuilt-intents-entities.md)

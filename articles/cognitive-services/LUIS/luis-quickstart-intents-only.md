---
title: Przewidywanie intencji
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz niestandardową aplikację, która będzie przewidywać intencje użytkownika. Ta aplikacja to najprostszy rodzaj aplikacji usługi LUIS, ponieważ nie wyodrębnia ona z tekstu wypowiedzi różnych elementów danych, takich jak adresy e-mail czy daty.
services: cognitive-services
author: lingliw
manager: digimobile
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 04/19/19
ms.author: v-lingwu
ms.openlocfilehash: 067829a1d9425ede1320242e364eca7c30bb7053
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66123936"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Samouczek: Tworzenie aplikacji usługi LUIS umożliwiającej określanie intencji użytkownika

W tym samouczku utworzysz niestandardową aplikację Human Resources (HR), która na podstawie wypowiedzi (tekstu) będzie przewidywała intencje użytkownika. 

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
|ApplyForJob|Określa, czy użytkownik ubiega się o pracę.|
|GetJobInformation|Określa, czy użytkownik szuka informacji dotyczących pracy w ogóle, czy jakiejś konkretnej pracy.|
|Brak|Określa, czy użytkownik zadaje pytanie, na które aplikacja nie powinna odpowiadać. Ta intencja jest dostarczana jako część procesu tworzenia aplikacji i nie można jej usunąć. |

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Tworzenie intencji dotyczącej informacji o pracy

1. Wybierz pozycję **Create new intent** (Utwórz nową intencję). Wprowadź nazwę nowej intencji: `GetJobInformation`. Ta intencja jest przewidywana, gdy użytkownik chce uzyskać informacje o stanowiskach dostępnych w firmie. 

    ![Zrzut ekranu przedstawiający okno dialogowe New intent (Nowa intencja) usługi Language Understanding (LUIS)](media/luis-quickstart-intents-only/create-intent.png "Zrzut ekranu przedstawiający okno dialogowe New intent (Nowa intencja) usługi Language Understanding (LUIS)")

1. Wybierz pozycję **Done** (Gotowe).

2. Dodaj kilka przykładowych wypowiedzi do tej intencji — takich, których spodziewasz się ze strony użytkownika:

    | Przykładowe wypowiedzi|
    |--|
    |Any new jobs posted today? (Czy dzisiaj zostały opublikowane jakieś nowe oferty pracy?)|
    |Are there any new positions in the Seattle office? (Czy są dostępne jakieś nowe oferty pracy w biurze w Seattle?)|
    |Are there any remote worker or telecommute jobs open for engineers? (Czy są dostępne dla inżynierów oferty pracy dla pracowników zdalnych lub oferty telepracy?)|
    |Is there any work in databases? (Czy jest jakaś praca związana z bazami danych?)|
    |I'm looking for a co-working situation in the tampa office (Szukam pracy jako współpracownik w biurze w Tampa).|
    |Is there an internship in the san francisco office? (Czy jest możliwość odbycia stażu w biurze w San Francisco?)|
    |Is there any part-time work for people in college? (Czy jest dostępna jakaś praca niepełnoetatowa dla studentów?)|
    |Looking for a new situation with responsibilities in accounting (Szukam nowego wyzwania w obszarze księgowości)|
    |Looking for a job in new york city for bilingual speakers (Szukam pracy w Nowym Jorku dla osób mówiących dwoma językami).|
    |Looking for a new situation with responsibilities in accounting (Szukam nowego wyzwania w obszarze księgowości).|
    |New jobs? (Jakieś nowe oferty?)|
    |Show me all the jobs for engineers that were added in the last 2 days (Pokaż mi wszystkie oferty pracy dla inżynierów, które dodano w ciągu ostatnich 2 dni).|
    |Today's job postings? (Czy dodano dzisiaj jakieś ofery pracy?)|
    |What accounting positions are open in the london office? (Jakie stanowiska w obszarze księgowości są dostępne w biurze w Londynie?)|
    |What positions are available for Senior Engineers? (Jakie oferty są dostępne dla starszych inżynierów?)|
    |Where is the job listings (Gdzie są listy ofert pracy)|

    [![Zrzut ekranu przedstawiający wprowadzanie nowych wypowiedzi dla intencji MyStore](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Zrzut ekranu przedstawiający wprowadzanie nowych wypowiedzi dla intencji MyStore")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

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

    Wynik w formacie JSON określa najwyżej ocenioną intencję jako właściwość **`topScoringIntent`**. Wszystkie wyniki należą do zakresu od 1 do 0, przy czym im bliżej 1, tym lepiej. 

## <a name="create-intent-for-job-applications"></a>Tworzenie intencji na potrzeby podań o pracę

Wróć do portalu usługi LUIS i utwórz nową intencję, która będzie określała, czy wypowiedź użytkownika dotyczy podania o pracę.

1. Wybierz pozycję **Build** (Twórz) z menu w prawym górnym rogu, aby powrócić do tworzenia aplikacji.

1. Wybierz pozycję **Intents** (Intencje) w menu po lewej stronie, aby przejść do listy intencji.

1. Wybierz polecenie **Create new intent** (Utwórz nową intencję) i podaj nazwę `ApplyForJob`. 

    ![Okno dialogowe usługi LUIS umożliwiające utworzenie nowej intencji](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. Dodaj kilka wypowiedzi do intencji — takich, których spodziewasz się ze strony użytkownika, na przykład:

    | Przykładowe wypowiedzi|
    |--|
    |Fill out application for job 123456 (Wypełnij wniosek dotyczący oferty pracy 123456)|
    |This is my c.v. for position 654234 (To jest moje CV w odpowiedzi na ofertę pracy 654234)|
    |Here is my resume for the part-time receptionist post (To jest mój życiorys w odpowiedzi na ogłoszenie o niepełnoetatowej pracy dla recepcjonisty).|
    |I'm applying for the art desk job with this paperwork (To jest dokumentacja używana na potrzeby ubiegania się o stanowisko rysownika).|
    |I'm applying for the summer college internship in Research and Development in San Diego (Ubiegam się o letni staż w dziale Badania i projektowanie na uniwersytecie w San Diego)|
    |I'm requesting to submit my resume to the temporary position in the cafeteria (Przesyłam mój życiorys w związku z ofertą tymczasowej pracy w kafeterii).|
    |I'm submitting my resume for the new Autocar team in Columbus, OH (Przesyłam mój życiorys w związku z przyjęciem mnie do zespołu Autocar w Columbus, Ohio)|
    |I want to apply for the new accounting job (Chcę odpowiedzieć na nową ofertę pracy w księgowości)|
    |Job 456789 accounting internship paperwork is here (Tutaj znajdują się dokumenty dotyczące stażu w księgowości — oferta pracy 456789)|
    |Job 567890 and my paperwork (Oferta pracy 567890 i moje papiery)|
    |My papers for the tulsa accounting internship are attached (Dołączono dokumenty dotyczące stażu w księgowości w Tulsie).|
    |My paperwork for the holiday delivery position (Moje dokumenty dotyczące oferty pracy jako dostawcy w dni wolne od pracy)|
    |Please send my resume for the new accounting job in seattle (Wyślij mój życiorys na potrzeby nowej pracy w księgowości w Seattle)|
    |Submit resume for engineering position (Prześlij CV w odpowiedzi na ofertę pracy dla inżyniera)|
    |To jest Moja doświadczeń for post 234123 in Tampa (To jest moje CV w odpowiedzi na ofertę pracy 234123 w Tampie)|

<!--

    [![Screenshot of entering new utterances for ApplyForJob intent](media/luis-quickstart-intents-only/utterance-applyforjob.png "Screenshot of entering new utterances for ApplyForJob intent")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    The labeled intent is outlined in red because LUIS is currently uncertain the intent is correct. Training the app tells LUIS the utterances are on the correct intent. 

-->

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


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku utworzono aplikację Human Resources (HR), utworzono 2 intencje, dodano przykładowe wypowiedzi do każdej intencji, dodano przykładowe wypowiedzi do intencji None, przeprowadzono szkolenie, opublikowano i przetestowano w punkcie końcowym. Są to podstawowe kroki tworzenia modelu usługi LUIS. 

Kontynuuj pracę z tą aplikacją, [dodając prostą jednostkę i listę fraz](luis-quickstart-primary-and-secondary-data.md).

> [!div class="nextstepaction"]
> [Dodawanie wstępnie utworzonych intencji i jednostek do aplikacji](luis-tutorial-prebuilt-intents-entities.md)





---
title: 'Samouczek 1: odkrywanie intencji w niestandardowej aplikacji usługi LUIS'
titleSuffix: Azure Cognitive Services
description: Utwórz niestandardową aplikację, która będzie przewidywać intencje użytkownika. Ta aplikacja to najprostszy rodzaj aplikacji usługi LUIS, ponieważ nie wyodrębnia ona z tekstu wypowiedzi różnych elementów danych, takich jak adresy e-mail czy daty.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 30c9f572d77caacbeecf5f15d74fd8517e9fa883
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426863"
---
# <a name="tutorial-1-build-custom-app-to-determine-user-intentions"></a>Samouczek 1: tworzenie niestandardowej aplikacji umożliwiającej określanie intencji użytkownika

W tym samouczku utworzysz niestandardową aplikację Human Resources (HR), która na podstawie wypowiedzi (tekstu) będzie przewidywała intencje użytkownika. Po zakończeniu samouczka punkt końcowy usługi LUIS będzie działał w chmurze.

Zadaniem aplikacji jest ustalenie intencji konwersacyjnego tekstu w języku naturalnym. Intencje są podzielone na kategorie **intencji**. Ta aplikacja ma kilka intencji. Pierwsza intencja, **`GetJobInformation`**, umożliwia określenie, kiedy użytkownik chce otrzymać informacje o stanowiskach dostępnych w firmie. Druga intencja, **`None`**, jest używana w przypadku wszystkich wypowiedzi użytkownika, które wykraczają poza _domenę_ (zakres) tej aplikacji. Natomiast trzecia intencja, **`ApplyForJob`**, jest dodawana w przypadku wszystkich wypowiedzi będących podaniem o pracę. Ta trzecia intencja różni się od intencji `GetJobInformation`, ponieważ informacje o stanowisku powinny już być znane, gdy ktoś zgłasza swoją kandydaturę na dane stanowisko. Jednak w zależności od wybranych słów, określenie intencji może być trudne, ponieważ obie dotyczą pracy.

Gdy usługa LUIS zwróci odpowiedź w formacie JSON, żądanie zostanie wykonane. Usługa LUIS nie odpowiada na wypowiedzi użytkowników, tylko określa, o jakie informacje proszą oni w języku naturalnym. 

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie nowej aplikacji 
> * Tworzenie intencji
> * Dodawanie przykładowych wypowiedzi
> * Szkolenie aplikacji
> * Publikowanie aplikacji
> * Pobieranie intencji z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

1. Zaloguj się do portalu usługi LUIS za pomocą adresu URL [https://www.luis.ai](https://www.luis.ai). 

2. Wybierz opcję **Create new app** (Utwórz nową aplikację).  

    [![](media/luis-quickstart-intents-only/app-list.png "Zrzut ekranu przedstawiający stronę My Apps (Moje aplikacje) usługi Language Understanding (LUIS)")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. W wyskakującym oknie dialogowym wprowadź nazwę `HumanResources` i zachowaj domyślną kulturę **English** (Angielski). Opis pozostaw pusty.

    ![Nowa aplikacja usługi LUIS](./media/luis-quickstart-intents-only/create-app.png)

    Następnie aplikacja zostanie wyświetlona na stronie **Intents** (Intencje) z intencją **None**.

## <a name="getjobinformation-intent"></a>Intencja GetJobInformation

1. Wybierz pozycję **Create new intent** (Utwórz nową intencję). Wprowadź nazwę nowej intencji: `GetJobInformation`. Ta intencja jest przewidywana za każdym razem, gdy użytkownik chce uzyskać informacje o stanowiskach dostępnych w firmie.

    ![](media/luis-quickstart-intents-only/create-intent.png "Zrzut ekranu przedstawiający okno dialogowe New intent (Nowa intencja) usługi Language Understanding (LUIS)")

2. Dostarczając _przykładowe wypowiedzi_, uczysz usługę LUIS, jakiego rodzaju wypowiedzi powinny być przewidywane dla tej intencji. Dodaj kilka przykładowych wypowiedzi do tej intencji — takich, których spodziewasz się ze strony użytkownika, na przykład:

    | Przykładowe wypowiedzi|
    |--|
    |Any new jobs posted today? (Czy dzisiaj zostały opublikowane jakieś nowe oferty pracy?)|
    |What positions are available for Senior Engineers? (Jakie oferty są dostępne dla starszych inżynierów?)|
    |Is there any work in databases? (Czy jest jakaś praca związana z bazami danych?)|
    |Looking for a new situation with responsibilities in accounting (Szukam nowego wyzwania w obszarze księgowości)|
    |Where is the job listings (Gdzie są listy ofert pracy)|
    |New jobs? (Jakieś nowe oferty?)|
    |Are there any new positions in the Seattle office? (Czy są dostępne jakieś nowe oferty pracy w biurze w Seattle?)|

    [![](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Zrzut ekranu przedstawiający podawanie nowych wypowiedzi dla intencji MyStore")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    


## <a name="none-intent"></a>Intencja None 
Aplikacja kliencka musi wiedzieć, czy wypowiedź nie wykracza poza domenę podmiotu aplikacji. Jeśli usługa LUIS zwraca intencję wypowiedzi **None**, aplikacja kliencka może zapytać, czy użytkownik chce zakończyć konwersację. Aplikacja kliencka może również podać więcej wskazówek umożliwiających kontynuowanie konwersacji, jeśli użytkownik nie chce jej zakończyć. 

Te przykładowe wypowiedzi spoza domeny podmiotu są grupowane w intencję **None**. Nie zostawiaj jej pustej. 

1. Wybierz pozycję **Intents** (Intencje) na lewym panelu.

2. Wybierz intencję **None**. Dodaj trzy wypowiedzi, które może wprowadzić użytkownik, ale które nie są istotne dla tej aplikacji Human Resources. Jeśli aplikacja dotyczy ofert pracy, przykładowe wypowiedzi dla intencji **None** to:

    | Przykładowe wypowiedzi|
    |--|
    |Barking dogs are annoying (Szczekające psy są irytujące)|
    |Order a pizza for me (Zamów dla mnie pizzę)|
    |Penguins in the ocean (Pingwiny w oceanie)|


## <a name="train"></a>Szkolenie 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikowanie

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent"></a>Uzyskiwanie intencji

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Przejdź na koniec adresu URL na pasku adresu i wprowadź ciąg `I'm looking for a job with Natural Language Processing`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź nie przypomina żadnej z przykładowych wypowiedzi. Jest to dobry test, który powinien zwrócić intencję `GetJobInformation` jako najwyżej ocenioną. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.8965092
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.8965092
        },
        {
          "intent": "None",
          "score": 0.147104025
        }
      ],
      "entities": []
    }
    ```

    Wyniki obejmują **wszystkie intencje** w aplikacji, obecnie 2. Tablica jednostek jest pusta, ponieważ ta aplikacja obecnie nie ma żadnych jednostek. 

    Wynik w formacie JSON określa najwyżej ocenioną intencję jako właściwość **`topScoringIntent`**. Wszystkie wyniki należą do zakresu od 1 do 0, przy czym im bliżej 1, tym lepiej. 

## <a name="applyforjob-intent"></a>Intencja ApplyForJob
Wróć do witryny internetowej usługi LUIS i utwórz nową intencję, która będzie określała, czy wypowiedź użytkownika dotyczy podania o pracę.

1. Wybierz pozycję **Build** (Twórz) z menu w prawym górnym rogu, aby powrócić do tworzenia aplikacji.

2. Wybierz pozycję **Intents** (Intencje) z menu po lewej.

3. Wybierz polecenie **Create new intent** (Utwórz nową intencję) i podaj nazwę `ApplyForJob`. 

    ![Okno dialogowe usługi LUIS umożliwiające utworzenie nowej intencji](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

4. Dodaj kilka wypowiedzi do intencji — takich, których spodziewasz się ze strony użytkownika, na przykład:

    | Przykładowe wypowiedzi|
    |--|
    |I want to apply for the new accounting job (Chcę odpowiedzieć na nową ofertę pracy w księgowości)|
    |Fill out application for job 123456 (Wypełnij wniosek dotyczący oferty pracy 123456)|
    |Submit resume for engineering position (Prześlij CV w odpowiedzi na ofertę pracy dla inżyniera)|
    |This is my c.v. for position 654234 (To jest moje CV w odpowiedzi na ofertę pracy 654234)|
    |Job 567890 and my paperwork (Oferta pracy 567890 i moje papiery)|

    [![](media/luis-quickstart-intents-only/utterance-applyforjob.png "Zrzut ekranu przedstawiający podawanie nowych wypowiedzi dla intencji ApplyForJob")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    Intencja z etykietą jest oznaczona kolorem czerwonym, ponieważ usługa LUIS obecnie nie ma pewności, czy intencja jest poprawna. Proces uczenia aplikacji informuje usługę LUIS, że wypowiedzi dotyczą poprawnej intencji. 

## <a name="train-again"></a>Ponowne szkolenie

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Ponowne publikowanie

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-again"></a>Ponowne pobieranie intencji

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. W nowym oknie przeglądarki podaj ciąg `Can I submit my resume for job 235986` na końcu adresu URL. 

    ```JSON
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9166808
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9166808
        },
        {
          "intent": "GetJobInformation",
          "score": 0.07162977
        },
        {
          "intent": "None",
          "score": 0.0262826588
        }
      ],
      "entities": []
    }
    ```

    Wyniki zawierają nową intencję **ApplyForJob**, a także istniejące intencje. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono aplikację Human Resources (HR), utworzono 2 intencje, dodano przykładowe wypowiedzi do każdej intencji, dodano przykładowe wypowiedzi do intencji None, przeprowadzono szkolenie, opublikowano i przetestowano w punkcie końcowym. Są to podstawowe kroki tworzenia modelu usługi LUIS. 

> [!div class="nextstepaction"]
> [Dodawanie wstępnie utworzonych intencji i jednostek do aplikacji](luis-tutorial-prebuilt-intents-entities.md)

---
title: Tworzenie prostej aplikacji z dwiema intencjami — Azure | Microsoft Docs
description: Z tego przewodnika Szybki start możesz dowiedzieć się, jak utworzyć prostą aplikację usługi LUIS używającą dwóch intencji i niekorzystającą z jednostek na potrzeby identyfikowania wypowiedzi użytkownika.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: v-geberr
ms.openlocfilehash: fea311eb36c0654b45b2354cb283f1699be7257c
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109345"
---
# <a name="tutorial-1-build-app-with-custom-domain"></a>Samouczek: 1. Tworzenie aplikacji z domeną niestandardową
W tym samouczku jest tworzona aplikacja, która pokazuje sposób użycia **intencji** w celu określenia _zamiaru_ użytkownika na podstawie wypowiedzi (tekstu) przesyłanej do aplikacji. Po zakończeniu samouczka punkt końcowy usługi LUIS będzie działał w chmurze.

Ta aplikacja to najprostszy typ aplikacji usługi LUIS, ponieważ nie wyodrębnia danych z wypowiedzi. Określa tylko zamiar użytkownika zawarty w wypowiedzi.

<!-- green checkmark -->
> [!div class="checklist"]
> * Tworzenie nowej aplikacji dla domeny zarządzania zasobami ludzkimi (HR) 
> * Dodawanie intencji GetJobInformation
> * Dodawanie przykładowych wypowiedzi do intencji GetJobInformation 
> * Uczenie i publikowanie aplikacji
> * Wysyłanie zapytania do punktu końcowego aplikacji w celu wyświetlenia odpowiedzi JSON usługi LUIS
> * Dodawanie intencji ApplyForJob
> * Dodawanie przykładowych wypowiedzi do intencji ApplyForJob 
> * Uczenie, publikowanie i ponowne wykonywanie zapytań względem punktu końcowego 

Na potrzeby tego artykułu jest wymagane bezpłatne konto usługi [LUIS](luis-reference-regions.md#luis-website), które umożliwia utworzenie aplikacji usługi LUIS.

## <a name="purpose-of-the-app"></a>Przeznaczenie aplikacji
Ta aplikacja ma kilka intencji. Pierwsza intencja, **`GetJobInformation`**, umożliwia określenie, kiedy użytkownik chce otrzymać informacje o stanowiskach dostępnych w firmie. Druga intencja, **`None`**, identyfikuje wszystkie inne typy wypowiedzi. Później w przewodniku Szybki start zostanie dodana trzecia intencja, `ApplyForJob`. 

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji
1. Zaloguj się w witrynie internetowej usługi [LUIS](luis-reference-regions.md#luis-website). Pamiętaj, aby zalogować się w [regionie](luis-reference-regions.md#publishing-regions), w którym mają zostać opublikowane punkty końcowe usługi LUIS.

2. W witrynie internetowej usługi [LUIS](luis-reference-regions.md#luis-website) wybierz pozycję **Create new app** (Utwórz nową aplikację).  

    [![](media/luis-quickstart-intents-only/app-list.png "Zrzut ekranu strony My apps (Moje aplikacje)")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. W wyskakującym oknie dialogowym wprowadź nazwę `HumanResources`. Ta aplikacja obejmuje pytania dotyczące działu zasobów ludzkich w firmie. Dział tego typu obsługuje zagadnienia związane z zatrudnieniem, takie jak stanowiska w firmie, które mają zostać obsadzone.

    ![Nowa aplikacja usługi LUIS](./media/luis-quickstart-intents-only/create-app.png)

4. Po zakończeniu tego procesu aplikacja wyświetli stronę **Intents** (Intencje) z intencją **None**. 

    [![](media/luis-quickstart-intents-only/intents-list.png "Zrzut ekranu przedstawiający stronę Intents list (Lista intencji)")](media/luis-quickstart-intents-only/intents-list.png#lightbox)

## <a name="create-getjobinformation-intention"></a>Tworzenie intencji GetJobInformation
1. Wybierz pozycję **Create new intent** (Utwórz nową intencję). Wprowadź nazwę nowej intencji: `GetJobInformation`. Ta intencja stanowi prognozę za każdym razem, gdy użytkownik chce uzyskać informacje o stanowiskach dostępnych w firmie.

    ![](media/luis-quickstart-intents-only/create-intent.png "Zrzut ekranu okna dialogowego New intent (Nowa intencja)")

    Tworząc intencję, tworzysz kategorię informacji, którą chcesz identyfikować. Nadanie nazwy kategorii umożliwia każdej innej aplikacji, która używa wyników zapytania usługi LUIS, zastosowanie tej nazwy kategorii w celu znalezienia odpowiedniej odpowiedzi. Usługa LUIS nie będzie odpowiadać na te pytania — określi jedynie, jakiego typu informacji dotyczy pytanie w języku naturalnym. 

2. Dodaj siedem wypowiedzi do tej intencji — takich, których spodziewasz się ze strony użytkownika, na przykład:

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

3. Aplikacja LUIS obecnie nie zawiera żadnych wypowiedzi dla intencji **None**. Potrzebuje ona wypowiedzi, na które aplikacja nie odpowiada. Nie zostawiaj jej pustej. Wybierz pozycję **Intents** (Intencje) na lewym panelu. 

4. Wybierz intencję **None**. Dodaj trzy wypowiedzi, które może wprowadzić użytkownik, ale które nie są istotne dla tej aplikacji. Jeśli aplikacja dotyczy ofert pracy, przykładowe wypowiedzi dobre dla intencji **None** to:

    | Przykładowe wypowiedzi|
    |--|
    |Barking dogs are annoying (Szczekające psy są irytujące)|
    |Order a pizza for me (Zamów dla mnie pizzę)|
    |Penguins in the ocean (Pingwiny w oceanie)|

    W aplikacji wywołującej usługę LUIS (takiej jak czatbot), gdy usługa LUIS zwraca intencję **None** dla wypowiedzi, bot może zadać pytanie, czy użytkownik chce zakończyć konwersację. Czatbot może również podać więcej wskazówek umożliwiających kontynuowanie konwersacji, jeśli użytkownik nie chce jej zakończyć. 

## <a name="train-and-publish-the-app"></a>Uczenie i publikowanie aplikacji
1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz). 

    ![Przycisk Train (Ucz)](./media/luis-quickstart-intents-only/train-button.png)

    Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    ![Pasek stanu Trained (Nauczono)](./media/luis-quickstart-intents-only/trained.png)

2. W prawym górnym rogu witryny internetowej usługi LUIS wybierz przycisk **Publish** (Publikuj), aby otworzyć stronę Publish (Publikowanie). Domyślnie wybrane jest miejsce produkcyjne. Wybierz przycisk **Publish** (Publikuj) obok wybranego miejsca produkcyjnego. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    Nie musisz tworzyć klucza punktu końcowego usługi LUIS w witrynie Azure Portal przed opublikowaniem lub przetestowaniem adresu URL punktu końcowego. Każda aplikacja usługi LUIS ma bezpłatny klucz początkowy na potrzeby tworzenia. Zapewnia on nielimitowane funkcje tworzenia oraz [kilka trafień punktów końcowych](luis-boundaries.md#key-limits). 

## <a name="query-endpoint-for-getjobinformation-intent"></a>Wykonywanie zapytań względem punktu końcowego o intencję GetJobInformation
1. Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. 

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `I'm looking for a job with Natual Language Processing`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź jest inna niż wszystkie pozostałe przykładowe wypowiedzi w kroku 4, dlatego jest dobra do testowania i powinna zwrócić intencję `GetJobInformation` jako intencję z najwyższą oceną. 

    ```
    {
      "query": "I'm looking for a job with Natual Language Processing",
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

## <a name="create-applyforjob-intention"></a>Tworzenie intencji ApplyForJob
Wróć do karty przeglądarki z witryną internetową usługi LUIS i utwórz nową intencję do zastosowania dla zadania.

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

    Ponowne [uczenie i publikowanie](#train-and-publish-the-app). 

## <a name="query-endpoint-for-applyforjob-intent"></a>Punkt końcowy dla intencji ApplyForJob
Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. W nowym oknie przeglądarki podaj ciąg `Can I submit my resume for job 235986` na końcu adresu URL. 

    ```
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

## <a name="what-has-this-luis-app-accomplished"></a>Co wykonała ta aplikacja LUIS?
Ta aplikacja, obejmująca zaledwie kilka intencji, zidentyfikowała zapytanie w języku naturalnym, które wyraża ten sam zamiar, lecz przedstawiony w inny sposób. 

Wynik w formacie JSON określa najwyżej ocenioną intencję. Wszystkie wyniki należą do zakresu od 1 do 0, przy czym im bliżej 1, tym lepiej. Wyniki dla intencji `GetJobInformation` i `None` są o wiele bliższe zera. 

## <a name="where-is-this-luis-data-used"></a>Gdzie są używane te dane usługi LUIS? 
Usługa LUIS skończyła obsługiwać to żądanie. Aplikacja wywołująca, taka jak czatbot, może odebrać wynik topScoringIntent i wyszukać informacje (spoza usługi LUIS) w celu udzielenia odpowiedzi na pytanie lub zakończyć konwersację. Są to opcje programistyczne dla bota lub aplikacji wywołującej. Aplikacja LUIS nie wykonuje tej pracy. Usługa LUIS określa jedynie intencję użytkownika. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Aby to zrobić, wybierz pozycję **My apps** (Moje aplikacje) z menu w lewym górnym rogu. Wybierz menu z trzema kropkami (...) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie **Delete** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Czy usunąć aplikację?) wybierz przycisk **OK**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie wstępnie utworzonych intencji i jednostek do aplikacji](luis-tutorial-prebuilt-intents-entities.md)

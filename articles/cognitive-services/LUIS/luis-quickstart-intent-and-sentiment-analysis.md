---
title: Samouczek dotyczący tworzenia aplikacji LUIS zwracającej analizę tonacji — platforma Azure | Microsoft Docs
description: W tym samouczku nauczysz się, jak dodawać analizę tonacji do aplikacji LUIS, aby analizować wypowiedzi pod kątem pozytywnych, negatywnych i neutralnych uczuć.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: d000637312619fc493e2f7bad8e8edf0d8d0d94b
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265338"
---
# <a name="tutorial-create-app-that-returns-sentiment-along-with-intent-prediction"></a>Samouczek: Tworzenie aplikacji, która zwraca tonację wraz z przewidywaną intencją
W tym samouczku utworzysz aplikację, która pokazuje, jak wyodrębniać pozytywne, negatywne i neutralne tonacje z wypowiedzi.

<!-- green checkmark -->
> [!div class="checklist"]
> * Omówienie jednostek hierarchicznych i wywnioskowanych z kontekstu elementów podrzędnych 
> * Tworzenie nowej aplikacji LUIS dla domeny travel z intencją Bookflight
> * Dodawanie intencji _None_ i dodawanie przykładowych wypowiedzi
> * Dodawanie jednostki hierarchicznej lokalizacji z elementami podrzędnymi miejsca początkowego i docelowego
> * Uczenie i publikowanie aplikacji
> * Wykonywanie względem punktu końcowego zapytania o aplikację w celu sprawdzenia odpowiedzi JSON usługi LUIS, w tym hierarchicznych elementów podrzędnych 

Na potrzeby tego artykułu wymagane jest bezpłatne konto usługi [LUIS][LUIS] w celu tworzenia aplikacji LUIS.

## <a name="sentiment-analysis"></a>Analiza tonacji
Analiza tonacji to możliwość określenia, czy wypowiedź użytkownika jest pozytywna, negatywna, czy neutralna. 

Następujące wypowiedzi przedstawiają przykłady tonacji:

|Tonacja i wynik|Wypowiedź|
|:--|--|
|pozytywna: 0.89 |Połączenie zupy i sałatki było wyśmienite.|
|negatywna: 0.07 |Przystawka podana podczas kolacji mi nie smakowała.|

Analiza tonacji to ustawienie aplikacji, która ma zastosowanie do każdej wypowiedzi. Nie ma potrzeby odnajdywania słów wskazujących tonację w ramach wypowiedzi i oznaczania ich. Usługa LUIS zrobi to za Ciebie.

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji
1. Zaloguj się w witrynie internetowej usługi [LUIS][LUIS]. Pamiętaj, aby zalogować się w [regionie][LUIS-regions], w którym mają zostać opublikowane punkty końcowe usługi LUIS.

2. W witrynie internetowej usługi [LUIS][LUIS] wybierz pozycję **Create new app** (Utwórz nową aplikację). 

    [![](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png "Zrzut ekranu ze stroną App lists (Listy aplikacji)")](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png#lightbox)

3. W oknie dialogowym **Create new app** (Tworzenie nowej aplikacji) nadaj aplikacji nazwę `Restaurant Reservations With Sentiment`, a następnie wybierz pozycję **Done** (Gotowe). 

    ![Obraz przedstawiający okno dialogowe tworzenia nowej aplikacji](./media/luis-quickstart-intent-and-sentiment-analysis/create-app-ddl.png)

    Po ukończeniu procesu tworzenia aplikacji w usłudze LUIS wyświetlona zostanie lista intencji zawierająca intencję None.

    [![](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png "Zrzut ekranu przedstawiający stronę Intents lists (Listy intencji)")](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png#lightbox)

## <a name="add-a-prebuilt-domain"></a>Dodawanie wstępnie utworzonej domeny
Dodaj wstępnie utworzoną domenę, aby szybko dodawać intencje, jednostki i oznaczone wypowiedzi.

1. Wybierz pozycję **Prebuilt Domains** (Wstępnie utworzone domeny) z menu po lewej stronie.

    [ ![Zrzut ekranu przedstawiający przycisk Prebuilt Domains (Wstępnie utworzone domeny)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-expanded.png#lightbox)

2. Wybierz pozycję **Add domain** (Dodaj domenę) dla wstępnie utworzonej domeny **RestaurantReservation**. Poczekaj na dodanie domeny.

    [ ![Zrzut ekranu przedstawiający listę Prebuilt Domain (Wstępnie utworzone domeny)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-expanded.png#lightbox)

3. Wybierz pozycję **Intents** (Intencje) na lewym pasku nawigacyjnym. Ta wstępnie utworzona domena ma jedną intencję.

    [ ![Zrzut ekranu przedstawiający listę Prebuilt domain (Wstępnie utworzone domeny) z wyróżnioną pozycją Intents (Intencje) na lewym pasku nawigacyjnym](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png#lightbox)

4.  Wybierz intencję **RestaurantReservation.Reserve**. 

    [ ![Zrzut ekranu z listą Intents (Intencje) i wyróżnioną intencją RestaurantReservation.Reserve](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png#lightbox)

5. Przełącz **widok jednostek**, aby sprawdzić wypowiedzi podane z oznaczonymi jednostkami specyficznymi dla domeny.

    [ ![Zrzut ekranu z intencją RestaurantReservation.Reserve i widokiem Intents (Intencje) przełączonym na wyróżniony widok Token (Token)](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-expanded.png#lightbox)

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS
Usługa LUIS nie wie o zmianach intencji i jednostek (modelu), dopóki nie zostanie ich nauczona. 

1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz).

    ![Zrzut ekranu przedstawiający wyróżniony przycisk Train (Ucz)](./media/luis-quickstart-intent-and-sentiment-analysis/train-button-expanded.png)

2. Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    ![Zrzut ekranu przedstawiający pasek powiadomienia o powodzeniu uczenia ](./media/luis-quickstart-intent-and-sentiment-analysis/trained-expanded.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurowanie aplikacji do uwzględnienia analizy tonacji
Analiza tonacji jest włączona na stronie **Publish** (Publikowanie). 

1. Wybierz pozycję **Publish** (Publikuj) na prawym górnym pasku nawigacyjnym.

    ![Zrzut ekranu przedstawiający stronę Intent (Intencja) z rozwiniętym przyciskiem Publish (Publikuj) ](./media/luis-quickstart-intent-and-sentiment-analysis/publish-expanded.png)

2. Wybierz pozycję **Enable Sentiment Analysis** (Włącz analizę tonacji).

    ![Zrzut ekranu przedstawiający stronę publikowania z wyróżnioną pozycją Enable Sentiment Analysis (Włącz analizę tonacji) ](./media/luis-quickstart-intent-and-sentiment-analysis/enable-sentiment-expanded.png)

3. Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj).

    [![](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-inline.png "Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym przyciskiem publikowania w miejscu produkcyjnym")](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-expanded.png#lightbox)

4. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

## <a name="query-the-endpoint-with-an-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą wypowiedzi

1. Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. 

    ![Zrzut ekranu przedstawiający stronę publikowania z wyróżnionym adresem URL punktu końcowego](media/luis-quickstart-intent-and-sentiment-analysis/endpoint-url-inline.png)

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Reserve table for  10 on upper level away from kitchen`. Ostatni parametr ciągu zapytania to `q`, czyli **query** (zapytanie) wypowiedzi. Ta wypowiedź jest inna niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić intencję `RestaurantReservation.Reserve` z wyodrębnioną analizą tonacji.

```
{
  "query": "Reserve table for 10 on upper level away from kitchen",
  "topScoringIntent": {
    "intent": "RestaurantReservation.Reserve",
    "score": 0.9926384
  },
  "intents": [
    {
      "intent": "RestaurantReservation.Reserve",
      "score": 0.9926384
    },
    {
      "intent": "None",
      "score": 0.00961109251
    }
  ],
  "entities": [],
  "sentimentAnalysis": {
    "label": "neutral",
    "score": 0.5
  }
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Co wykonała ta aplikacja LUIS?
Ta aplikacja z włączoną funkcją analizy tonacji zidentyfikowała intencję zapytania języka naturalnego i zwróciła wyodrębnione dane zawierające ogólną tonację jako wynik. 

Twój czatbot ma teraz wystarczająco dużo informacji, aby określić następny krok w konwersacji. 

## <a name="where-is-this-luis-data-used"></a>Gdzie są używane te dane usługi LUIS? 
Usługa LUIS skończyła obsługiwać to żądanie. Aplikacja wywołująca, taka jak czatbot, może pobrać wynik topScoringIntent (najwyżej oceniana intencja) oraz dane tonacji z wypowiedzi, aby wykonać kolejny krok. Usługa LUIS nie wykonuje tej pracy programowej dla bota ani dla aplikacji wywołującej. Usługa LUIS określa jedynie intencję użytkownika. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy aplikacja LUIS nie będzie już potrzebna, usuń ją. Aby to zrobić, wybierz menu z trzema kropkami (...) po prawej stronie nazwy aplikacji na liście aplikacji i wybierz polecenie **Delete** (Usuń). W wyskakującym oknie dialogowym **Delete app?** (Usunąć aplikację?) wybierz pozycję **OK**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Wywoływanie interfejsu API punktu końcowego usługi LUIS za pomocą języka C#](luis-get-started-cs-get-intent.md) 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions

---
title: Tworzenie złożonego jednostki można wyodrębnić złożonych danych — Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia złożonych jednostki w aplikacją usługi LUIS do wyodrębniania różnych typów danych jednostki.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 03/28/2018
ms.author: v-geberr
ms.openlocfilehash: 375b52f9206f55e620d5e664844b8fa1d7249a07
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888749"
---
# <a name="use-composite-entity-to-extract-complex-data"></a>Wyodrębnianie danych złożonych przy użyciu złożonego jednostki
Ta prosta aplikacja ma dwa [intencji](luis-concept-intent.md) i jednostki. Jej celem jest Zarezerwuj lotów, takie jak "1 ticket z Seattle do Cairo w piątek" i zwracać wszystkie szczegółowe informacje na temat rezerwacji jako pojedynczy element danych. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
* Dodaj numer i datetimeV2 ze wstępnie utworzonych jednostek
* Tworzenie złożonego jednostki
* Zapytanie usługi LUIS i odbierać dane jednostki złożone

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Aplikacją usługi LUIS z  **[hierarchiczne szybkiego startu](luis-tutorial-composite-entity.md)**. 

> [!Tip]
> Jeśli nie masz już subskrypcję, możesz zarejestrować [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="composite-entity-is-a-logical-grouping"></a>Złożone jednostki to logiczna grupa 
Celem jednostki jest znalezienie fragmentów tekstu w wypowiedzi i przypisanie im kategorii. A [złożonego](luis-concept-entity-types.md) jednostki składa się z innych typów jednostek z kontekstu. Dla tej aplikacji podróży, która przyjmuje Rezerwacje lotów istnieje kilka rodzajów informacji, takich jak daty, lokalizacji i liczbę stanowisk. 

Taka informacja istnieje jako osobne jednostki, przed utworzeniem złożonego. Tworzenie złożonego jednostki, gdy osobne jednostki mogą być grupowane logicznie to logiczne grupowanie jest przydatne do chatbot lub inne aplikacje korzystające z usługi LUIS. 

Proste przykładowe wypowiedzi użytkowników mogą być następujące:

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Reserve a seat from New York to Paris on the first of April
```
 
Złożone jednostki dopasowuje liczba stanowisk, lokalizację pochodzenia, miejsce docelowe i daty. 

## <a name="what-luis-does"></a>Jak działa usługa LUIS
Gdy intencje i jednostki wypowiedzi zostaną zidentyfikowane, [wyodrębnione](luis-concept-data-extraction.md#list-entity-data) i zwrócone w formacie JSON z [punktu końcowego](https://aka.ms/luis-endpoint-apis), działanie usługi LUIS kończy się. Aplikacja wywołująca lub czatbot pobiera tę odpowiedź JSON i spełnia żądanie — w taki sposób, jaki został zaprojektowany. 

## <a name="add-prebuilt-entities-number-and-datetimev2"></a>Dodaj numer ze wstępnie utworzonych jednostek i datetimeV2
1. Wybierz `MyTravelApp` aplikacji z listy aplikacji na [LUIS](luis-reference-regions.md#luis-website) witryny sieci Web.

2. Po otwarciu aplikacji wybierz **jednostek** łącze nawigacji po lewej stronie.

    ![Wybierz jednostki przycisku](./media/luis-tutorial-composite-entity/intents-page-select-entities.png)    

3. Wybierz pozycję **Manage prebuilt entities** (Zarządzaj wstępnie utworzonymi jednostkami).

    ![Wybierz jednostki przycisku](./media/luis-tutorial-composite-entity/manage-prebuilt-entities-button.png)

4. W oknie podręcznym wybierz **numer** i **datetimeV2**.

    ![Wybierz jednostki przycisku](./media/luis-tutorial-composite-entity/prebuilt-entity-ddl.png)

5. Aby nowe jednostki do wyodrębnienia, wybierz **Train** w górnym pasku nawigacyjnym.

    ![Wybieranie przycisku Train (Ucz)](./media/luis-tutorial-composite-entity/train.png)

## <a name="use-existing-intent-to-create-composite-entity"></a>Użyj istniejących przeznaczenie, aby utworzyć złożone jednostki
1. Wybierz **intencji** w lewym obszarze nawigacji. 

    ![Wybierz stronę intencji](./media/luis-tutorial-composite-entity/intents-from-entities-page.png)

2. Wybierz `BookFlight` z **intencji** listy.  

    ![Z listy wybierz BookFlight intencji](./media/luis-tutorial-composite-entity/intent-page-with-prebuilt-entities-labeled.png)

    Liczba i datetimeV2 ze wstępnie utworzonych jednostek są oznaczone etykietami na wypowiedzi.

3. Dla wypowiedź `book 2 flights from seattle to cairo next monday`, wybierz niebieski `number` jednostki, następnie wybierz pozycję **opakować w jednostce złożone** z listy. Zieloną linię w obszarze wyrazów, następuje kursor, kiedy przesuwa się on do prawej, wskazujący złożonego jednostki. Następnie przesuń w prawo, aby wybrać ostatnich wstępnie utworzone jednostki `datetimeV2`, wprowadź `FlightReservation` w polu tekstowym w oknie podręcznym, a następnie zaznacz **Utwórz nowe złożone**. 

    ![Tworzenie złożonego jednostki na stronie intencji](./media/luis-tutorial-composite-entity/create-new-composite.png)

4. Wyskakującego okna dialogowego pojawia się, co pozwoli zweryfikować dzieci złożonego jednostki. Wybierz pozycję **Done** (Gotowe).

    ![Tworzenie złożonego jednostki na stronie intencji](./media/luis-tutorial-composite-entity/validate-composite-entity.png)

## <a name="wrap-the-entities-in-the-composite-entity"></a>OPAKOWYWANIE jednostek w jednostce złożone
Po utworzeniu obiektu złożonego etykiety pozostałe wypowiedzi w jednostce złożone. Aby zawijać frazy jako obiekt złożony, konieczne będzie dokonanie zaznacz wyraz najdalej po lewej stronie, a następnie wybierz **opakować w jednostce złożone** z wyświetlonej listy następnie zaznacz wyraz najdalej z prawej strony, a następnie wybierz nazwanych jednostek złożonego `FlightReservation`. Jest to szybkie i bezproblemowe kroku wybory, podzielone na następujące czynności:

1. W polu wypowiedź `schedule 4 seats from paris to london for april 1`, wybierz opcję 4 jako liczba wstępnie utworzone jednostki.

    ![Zaznacz wyraz skrajnie po lewej](./media/luis-tutorial-composite-entity/wrap-composite-step-1.png)

2. Wybierz **opakować w jednostce złożone** z wyświetlonej listy.

    ![Wybierz opcję zawijania z listy](./media/luis-tutorial-composite-entity/wrap-composite-step-2.png)

3. Zaznacz wyraz najdalej z prawej strony. Zielona linia jest wyświetlany w obszarze frazy, wskazujący złożonego jednostki.

    ![Zaznacz wyraz najdalej z prawej strony](./media/luis-tutorial-composite-entity/wrap-composite-step-3.png)

4. Wybierz nazwę złożonego `FlightReservation` z wyświetlonej listy.

    ![Wybierz nazwanych jednostek złożone](./media/luis-tutorial-composite-entity/wrap-composite-step-4.png)

    Ostatnie wypowiedź, można opakować `London` i `tomorrow` w jednostce złożone przy użyciu tych samych instrukcji. 

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS
Usługa LUIS nie wie o zmianach intencji i jednostek (modelu), dopóki nie zostanie ich nauczona. 

1. W górnej części witryny internetowej usługi LUIS po prawej stronie wybierz przycisk **Train** (Ucz).

    ![Uczenie aplikacji](./media/luis-tutorial-composite-entity/train-button.png)

2. Uczenie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

    ![Uczenie powiodło się](./media/luis-tutorial-composite-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego
Aby uzyskać przewidywania usługi LUIS w czatbocie lub innej aplikacji, należy opublikować aplikację. 

1. W górnej części witryny usługi LUIS po prawej stronie wybierz przycisk **Publish** (Publikuj). 

2. Wybierz miejsce produkcyjne i przycisk **Publish** (Publikuj).

    ![Publikowanie aplikacji](./media/luis-tutorial-composite-entity/publish-to-production.png)

3. Publikowanie jest ukończone, gdy w górnej części witryny internetowej jest widoczny zielony pasek stanu potwierdzający powodzenie.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Wysyłanie zapytania do punktu końcowego za pomocą różnych wypowiedzi
1. Na stronie **Publish** (Publikowanie) wybierz link **endpoint** (punkt końcowy) u dołu strony. Ta czynność spowoduje otwarcie nowego okna przeglądarki z adresem URL punktu końcowego na pasku adresu. 

    ![Wybierz adres URL punktu końcowego](./media/luis-tutorial-composite-entity/publish-select-endpoint.png)

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `reserve 3 seats from London to Cairo on Sunday`. Ostatni parametr querystring jest `q`, zapytanie wypowiedź. Ta wypowiedź jest inna niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić intencję `BookFlight` z wyodrębnioną jednostką hierarchiczną.

```
{
  "query": "reserve 3 seats from London to Cairo on Sunday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.999999046
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.999999046
    },
    {
      "intent": "None",
      "score": 0.227036044
    }
  ],
  "entities": [
    {
      "entity": "sunday",
      "type": "builtin.datetimeV2.date",
      "startIndex": 40,
      "endIndex": 45,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-03-25"
          },
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-04-01"
          }
        ]
      }
    },
    {
      "entity": "3 seats from london to cairo on sunday",
      "type": "flightreservation",
      "startIndex": 8,
      "endIndex": 45,
      "score": 0.6892485
    },
    {
      "entity": "cairo",
      "type": "Location::Destination",
      "startIndex": 31,
      "endIndex": 35,
      "score": 0.557570755
    },
    {
      "entity": "london",
      "type": "Location::Origin",
      "startIndex": 21,
      "endIndex": 26,
      "score": 0.8933808
    },
    {
      "entity": "3",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "3"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "flightreservation",
      "value": "3 seats from london to cairo on sunday",
      "children": [
        {
          "type": "builtin.datetimeV2.date",
          "value": "sunday"
        },
        {
          "type": "Location::Destination",
          "value": "cairo"
        },
        {
          "type": "builtin.number",
          "value": "3"
        },
        {
          "type": "Location::Origin",
          "value": "london"
        }
      ]
    }
  ]
}
```

Ten wypowiedź zwraca tym tablicy złożonego jednostek **flightreservation** obiektu z danymi wyodrębnione.  

## <a name="what-has-this-luis-app-accomplished"></a>Co wykonała ta aplikacja LUIS?
Tej aplikacji, za pomocą zaledwie dwóch intencje i jednostką złożonego zidentyfikowane zamiar zapytań języka naturalnego i zwrócony wyodrębnione dane. 

Twoje chatbot ma teraz informacje wystarczające do wyznaczenia Akcja podstawowa `BookFlight`i informacje o rezerwacji w wypowiedź. 

## <a name="where-is-this-luis-data-used"></a>Gdzie są używane te dane usługi LUIS? 
Usługa LUIS skończyła obsługiwać to żądanie. Aplikacja wywołująca, taka jak czatbot, może pobrać wynik topScoringIntent (najwyżej oceniana intencja) oraz dane z jednostki, aby wykonać kolejny krok. Usługa LUIS nie wykonuje tej pracy programowej dla bota ani dla aplikacji wywołującej. Usługa LUIS określa jedynie intencję użytkownika. 

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej na temat jednostek](luis-concept-entity-types.md). 

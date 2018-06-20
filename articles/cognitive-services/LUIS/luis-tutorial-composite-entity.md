---
title: Utwórz jednostkę złożonego wyodrębnić dane złożone - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć złożone jednostki LUIS aplikacji w celu wyodrębnienia różnych typów danych jednostki.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 03/28/2018
ms.author: v-geberr
ms.openlocfilehash: cb581ee60dea2b0810332933455a03a8b68e16ea
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264389"
---
# <a name="use-composite-entity-to-extract-complex-data"></a>Użyj jednostki złożone, aby wyodrębnić dane złożone
Ta prosta aplikacja ma dwa [intencje](luis-concept-intent.md) i kilka jednostek. Jego celem jest zarezerwować lotach, takich jak "1 bilet z Seattle Kair w piątek" i zwracać wszystkie szczegółowe informacje na temat rezerwacji jako pojedynczy element danych. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
* Dodaj datetimeV2 wbudowane jednostek i numer
* Utwórz jednostkę złożone
* Zapytanie LUIS i odbierać dane jednostki złożone

## <a name="before-you-begin"></a>Przed rozpoczęciem
* LUIS aplikacji z  **[hierarchiczna szybkiego startu](luis-tutorial-composite-entity.md)**. 

> [!Tip]
> Jeśli nie masz już subskrypcję, możesz zarejestrować dla [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="composite-entity-is-a-logical-grouping"></a>Złożone jednostki to logiczne grupowanie 
Jednostka ma na celu Znajdź i kategoryzowanie fragmenty tekstu w utterance. A [złożonego](luis-concept-entity-types.md) jednostki składa się z innych typów jednostek, które zostały uzyskane na podstawie kontekstu. Dla tej aplikacji podróży pobierającej zastrzeżenia transmitowane istnieją kilku informacji takich jak daty, lokalizacji i liczbę miejsc. 

Informacje istnieje jako osobne jednostki, przed utworzeniem złożonym. Utwórz jednostkę złożone, gdy osobne jednostki można grupować logicznie i to logiczne grupowanie jest przydatne do chatbot lub innych aplikacji korzystających z LUIS. 

Prosty przykład zniesławiających od użytkowników obejmują:

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Reserve a seat from New York to Paris on the first of April
```
 
Obiekt złożony jest zgodny stanowisk, lokalizacja źródła lokalizacji docelowej i daty. 

## <a name="what-luis-does"></a>Jaki jest LUIS
Gdy są identyfikowane zamiar i jednostek utterance [wyodrębnione](luis-concept-data-extraction.md#list-entity-data)i zwracane w formacie JSON z [punktu końcowego](https://aka.ms/luis-endpoint-apis), LUIS jest wykonywane. Wywołanie aplikacji lub chatbot przyjmuje tę odpowiedź JSON i spełnia żądania — w dowolnie wybrany sposób aplikacji lub chatbot zaprojektowano w celu. 

## <a name="add-prebuilt-entities-number-and-datetimev2"></a>Dodaj liczbę jednostek wbudowane i datetimeV2
1. Wybierz `MyTravelApp` aplikacji z listy aplikacji na [LUIS] [ LUIS] witryny sieci Web.

2. Po otwarciu aplikacji wybierz **jednostek** łącza nawigacji po lewej stronie.

    ![Wybierz przycisk jednostek](./media/luis-tutorial-composite-entity/intents-page-select-entities.png)    

3. Wybierz **Zarządzanie wbudowane jednostek**.

    ![Wybierz przycisk jednostek](./media/luis-tutorial-composite-entity/manage-prebuilt-entities-button.png)

4. W polu podręcznym wybierz **numer** i **datetimeV2**.

    ![Wybierz przycisk jednostek](./media/luis-tutorial-composite-entity/prebuilt-entity-ddl.png)

5. Aby nowe jednostki do wyodrębnienia, wybierz **pociągu** w górnym pasku nawigacyjnym.

    ![Przycisk Wybierz pociągu](./media/luis-tutorial-composite-entity/train.png)

## <a name="use-existing-intent-to-create-composite-entity"></a>Istniejące opcje umożliwia tworzenie złożonego jednostki
1. Wybierz **intencje** z lewym pasku nawigacyjnym. 

    ![Wybierz stronę opcji](./media/luis-tutorial-composite-entity/intents-from-entities-page.png)

2. Wybierz `BookFlight` z **intencje** listy.  

    ![Wybierz opcje BookFlight z listy](./media/luis-tutorial-composite-entity/intent-page-with-prebuilt-entities-labeled.png)

    Liczba i datetimeV2 wbudowane jednostki są oznaczane etykietami na zniesławiających.

3. Dla utterance `book 2 flights from seattle to cairo next monday`, wybierz niebieski `number` jednostki, następnie wybierz **zawijanie w złożonych jednostki** z listy. Zielony wiersza, w obszarze wyrazy, następuje kursor przesyłane do prawej, wskazującą złożonego jednostki. Następnie można przejść do prawej, aby wybrać ostatniego jednostki wbudowane `datetimeV2`, wprowadź `FlightReservation` w polu tekstowym w oknie podręcznym wybierz **Utwórz nowy złożone**. 

    ![Utwórz jednostkę złożonego na stronie opcji](./media/luis-tutorial-composite-entity/create-new-composite.png)

4. Okno podręczne pojawi się sprawdź elementy podrzędne jednostki złożonego. Wybierz **gotowe**.

    ![Utwórz jednostkę złożonego na stronie opcji](./media/luis-tutorial-composite-entity/validate-composite-entity.png)

## <a name="wrap-the-entities-in-the-composite-entity"></a>Zawijanie jednostek w złożonych jednostki
Po utworzeniu obiektu złożonego etykiety pozostałych zniesławiających w jednostce złożonego. Aby zawijać frazy jako obiekt złożony, trzeba zaznacz słowo lewej, a następnie wybierz **zawijanie w złożonych jednostki** na liście zaznacz słowo prawej, następnie wybrać nazwanym jednostkę złożonego `FlightReservation`. Jest to krok szybkie i sprawne wyborów, podzielić na następujące czynności:

1. W utterance `schedule 4 seats from paris to london for april 1`, wybierz opcję 4 jako liczba jednostek wbudowane.

    ![Zaznacz słowo z lewej](./media/luis-tutorial-composite-entity/wrap-composite-step-1.png)

2. Wybierz **zawijanie w złożonych jednostki** z wyświetlonej listy.

    ![Wybierz opcję zawijania z listy](./media/luis-tutorial-composite-entity/wrap-composite-step-2.png)

3. Zaznacz słowo prawej krawędzi. Zielony pozycji zostanie wyrażenie wskazujące złożonego jednostki.

    ![Zaznacz słowo z prawej](./media/luis-tutorial-composite-entity/wrap-composite-step-3.png)

4. Wybierz nazwę złożonego `FlightReservation` z wyświetlonej listy.

    ![Wybierz nazwanej jednostki złożone](./media/luis-tutorial-composite-entity/wrap-composite-step-4.png)

    Ostatni utterance, można zawijać `London` i `tomorrow` w jednostce złożonego przy użyciu tej samej instrukcji. 

## <a name="train-the-luis-app"></a>Szkolenie LUIS aplikacji
LUIS nie ma informacji dotyczących zmiany lokalizacji docelowych i jednostek (model) do momentu jego przygotowaniu. 

1. W górnym rogu LUIS witryny sieci Web, wybierz **pociągu** przycisku.

    ![Szkolenie aplikacji](./media/luis-tutorial-composite-entity/train-button.png)

2. Szkolenie zostało ukończone, gdy zostanie wyświetlony pasek stanu zielonego w górnej części witryny sieci Web potwierdzeniem powodzenia.

    ![Szkolenie powiodło się.](./media/luis-tutorial-composite-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji, aby uzyskać adres URL punktu końcowego
Aby uzyskać Prognozowanie LUIS chatbot lub innej aplikacji, należy opublikować aplikację. 

1. W górnym rogu LUIS witryny sieci Web, wybierz **publikowania** przycisku. 

2. Wybierz miejsca produkcyjnego i **publikowania** przycisku.

    ![Publikowanie aplikacji](./media/luis-tutorial-composite-entity/publish-to-production.png)

3. Publikowanie została ukończona, gdy zostanie wyświetlony pasek stanu zielonego w górnej części witryny sieci Web potwierdzeniem powodzenia.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Punkt końcowy o różnych utterance zapytania
1. Na **publikowania** wybierz pozycję **punktu końcowego** łącze umieszczone u dołu strony. Akcja ta Otwiera inne okno przeglądarki z adresem URL punktu końcowego na pasku adresu. 

    ![Wybierz adres URL punktu końcowego](./media/luis-tutorial-composite-entity/publish-select-endpoint.png)

2. Przejdź do końca w adresie URL, a następnie wprowadź `reserve 3 seats from London to Cairo on Sunday`. Ostatni parametr querystring jest `q`, utterance zapytania. Ta utterance nie jest taka sama jak żadnego z etykietą zniesławiających, więc jest dobrym testu i powinna zostać zwrócona `BookFlight` konwersji z jednostką hierarchiczna wyodrębnione.

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

Zwraca ten utterance tym tablicy złożonego jednostek **flightreservation** obiektu z danych wyodrębnionych.  

## <a name="what-has-this-luis-app-accomplished"></a>Co ma osiągnąć tej aplikacji LUIS?
Tę aplikację z dwóch lokalizacji docelowych i złożone jednostki, identyfikowane zamiar zapytania języka naturalnego i zwrócił wyodrębnione dane. 

Twoje chatbot ma teraz zebranie informacji umożliwiających ustalenie akcji głównej `BookFlight`oraz informacje o rezerwacji w utterance. 

## <a name="where-is-this-luis-data-used"></a>Gdzie są używane te dane LUIS? 
LUIS odbywa się z tym żądaniem. Aplikacja wywołująca, takich jak chatbot, możliwe jest wynik topScoringIntent i danych z obiektu do wykonania kolejnego kroku. LUIS nie programowe pracy bot lub aplikacja wywołująca. LUIS określa tylko jest zamiar użytkownika. 

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej na temat jednostek](luis-concept-entity-types.md). 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions

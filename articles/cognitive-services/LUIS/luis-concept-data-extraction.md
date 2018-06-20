---
title: Pojęciami wyodrębniania danych w LUIS - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jakiego rodzaju dane można wyodrębnić z opis języka (LUIS)
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 28fde09fa9291fbcd64ce4542a008f48dd0018d1
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265256"
---
# <a name="data-extraction"></a>Wyodrębnianie danych
LUIS daje możliwość pobrania informacji z zniesławiających języka naturalnego użytkownika. Informacje są wyodrębniane w sposób, że może służyć przez program, aplikacji lub chatbot podejmowania żadnych działań.

W poniższych sekcjach Dowiedz się, jakie dane są zwracane z lokalizacji docelowych i jednostek wraz z przykładami JSON. Najtrudniejsze danych, aby wyodrębnić jest rozpoznawane maszyny danych nie jest odpowiednikiem dokładny tekst. Wyodrębniania danych z rozpoznane maszyny [jednostek](luis-concept-entity-types.md) musi być częścią [tworzenia cyklu](luis-concept-app-iteration.md) dopóki upewnieniu odbierania danych spodziewasz się. 

## <a name="data-location-and-key-usage"></a>Wykorzystanie danych lokalizacji i klucz
LUIS zawiera dane publikowane [punktu końcowego](luis-glossary.md#endpoint). **Żądanie HTTPS** (POST lub GET) zawiera utterance, a także niektóre konfiguracje opcjonalne, takie jak środowisk przemieszczania i produkcji. 

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

`appID` Jest dostępna w **ustawienia** strony z LUIS aplikacji, a także część adresu URL (po `/apps/`) podczas edytowania, aplikacja LUIS. `subscription-key` Jest punktu końcowego używany do wykonywania zapytań w aplikacji. Za pomocą klucza tworzenia/starter wolnego podczas nauki obsługi LUIS, ważne jest, aby zmienić wartość klucza subskrypcji do klucza, który obsługuje Twojej [oczekiwano użycia LUIS](luis-boundaries.md#key-limits). `timezoneOffset` Jednostka jest minut.

**Odpowiedzi HTTPS** zawiera wszystkie informacje zamiar i jednostek, LUIS można określić oparte na bieżący model publikowanych albo tymczasowym czy produkcyjnym punktu końcowego. Punkt końcowy adres URL znajduje się na [LUIS] [ LUIS] witryny sieci Web **publikowania** strony. 

## <a name="data-from-intents"></a>Dane z lokalizacji docelowych
Danych podstawowych jest najwyższym oceniania **konwersji nazwy**. Przy użyciu `MyStore` [szybkiego startu](luis-quickstart-intents-only.md), odpowiedź punktu końcowego jest:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|
|--|--|--|--|
|Celem|Ciąg|topScoringIntent.intent|"GetStoreInfo"|

Jeśli z chatbot lub aplikacji telefonicznej LUIS sprawia, że decyzje, w oparciu o więcej niż jeden wynik konwersji, zwróć wyniki wszystkich opcji przez ustawienie dla parametru querystring `verbose=true`. Odpowiedź punktu końcowego jest:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

Profile są uporządkowane z najwyższym do najmniejszej liczby punktów.

|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|Wynik|
|--|--|--|--|:--|
|Celem|Ciąg|.intent intencje [0]|"GetStoreInfo"|0.984749258|
|Celem|Ciąg|.intent intencje [1]|"None"|0.0168218873|

Po dodaniu domen wbudowane konwersji wskazuje nazwa domeny, takie jak `Utilties` lub `Communication` oraz zamiar:

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```
    
|Domena|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|
|--|--|--|--|--|
|Usługi użyteczności publicznej|Celem|Ciąg|.intent intencje [0]|"<b>Narzędzia</b>. ShowNext"|
|Komunikacja|Celem|Ciąg|.intent intencje [1]|<b>Komunikacja</b>. StartOver"|
||Celem|Ciąg|.intent intencje [2]|"None"|


## <a name="data-from-entities"></a>Dane z jednostki
Większość chatbots i aplikacje wymagają więcej niż nazwa konwersji. To dodatkowe, opcjonalne dane pochodzą z odnalezionych w utterance jednostek. Każdy typ jednostki zwraca różne informacje o zgodności. 

Pojedynczy wyraz lub frazę w utterance może dopasować więcej niż jednej jednostki. W takim przypadku każdy obiekt pasującego zostało zwrócone z jego wynik. 

Wszystkie obiekty są zwracane w **jednostek** tablicy odpowiedzi z punktu końcowego:

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

## <a name="tokenized-entity-returned"></a>Jednostka tokenami zwracana
Kilka [kultur](luis-supported-languages.md#tokenization) zwracać obiekt jednostki z `entity` wartość [stokenizowanego](luis-glossary.md#token). StartIndex i endIndex zwrócony przez LUIS w obiekcie jednostki nie zostaną zamapowani na wartość nowego, tokenami tylko oryginalne zapytanie w kolejności, można programowo wyodrębnić raw jednostki. 

Na przykład w języku niemieckim, wyraz `das Bauernbrot` jest stokenizowana do `das bauern brot`. Wartość tokenami `das bauern brot`, jest zwracany i oryginalne wartości można programowo ustalić na podstawie startIndex i endIndex oryginalne zapytanie, umożliwiając `das Bauernbrot`.

## <a name="simple-entity-data"></a>Proste jednostek danych

A [jednostki prostej](luis-concept-entity-types.md) jest wartością rozpoznane maszyny. Może być słowo lub frazę. 

`Bob Jones wants 3 meatball pho`

W poprzednich utterance `Bob Jones` jest oznaczona jako prosty `Customer` jednostki.

Dane zwrócone z punktu końcowego obejmuje nazwa jednostki, odnalezionych tekst ze utterance, lokalizację odnalezionych tekst i wynik:

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Obiekt danych|Nazwa jednostki|Wartość|
|--|--|--|
|Proste jednostki|"Klient"|"bob Nowak"|

## <a name="hierarchical-entity-data"></a>Hierarchiczna jednostek danych

[Hierarchiczna](luis-concept-entity-types.md) jednostki są rozpoznawane maszyny i mogą zawierać słowo lub frazę. Elementy podrzędne są identyfikowane przez kontekst. Jeśli szukasz relacji nadrzędny podrzędny z tekstu dokładnego dopasowania, użyj [listy](#list-entity-data) jednostki. 

`book 2 tickets to paris`

W poprzednich utterance `paris` etykietą `Location::ToLocation` podrzędnym `Location` hierarchiczna jednostki. 

Dane zwrócone z punktu końcowego obejmuje nazwa jednostki i nazwa elementu podrzędnego, odnalezionych tekst ze utterance, lokalizację odnalezionych tekst i wynik: 

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Location::ToLocation",
    "startIndex": 18,
    "endIndex": 22,
    "score": 0.6866132
  }
]
```

|Obiekt danych|Nadrzędny|Podrzędne|Wartość|
|--|--|--|--|--|
|Hierarchiczna jednostki|Lokalizacja|ToLocation|"Paryża"|

## <a name="composite-entity-data"></a>Dane złożone jednostki
[Złożone](luis-concept-entity-types.md) jednostki są rozpoznawane maszyny i mogą zawierać słowo lub frazę. Rozważmy na przykład złożonego jednostki wbudowane `number` i `Location::ToLocation` z następujących utterance:

`book 2 tickets to paris`

Zwróć uwagę, że `2`, liczba, i `paris`, ToLocation ma słowa między nimi, które nie są częścią dowolne jednostki. Zielony podkreślenia, używany w etykietą utterance w [LUIS] [ LUIS] witryny sieci Web, wskazuje złożonego jednostki.

![Złożone jednostki](./media/luis-concept-data-extraction/composite-entity.png)

Złożone jednostki są zwracane w `compositeEntities` tablicy a wszystkimi jednostkami w złożone są także zwracany w `entities` tablicy:

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 18,
      "endIndex": 22,
      "score": 0.956998169
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "2 tickets to paris",
      "type": "Order",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.7714499
    }
  ],
  "compositeEntities": [
    {
      "parentType": "Order",
      "value": "2 tickets to paris",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Location::ToLocation",
          "value": "paris"
        }
      ]
    }
  ]
```    

|Obiekt danych|Nazwa jednostki|Wartość|
|--|--|--|
|Wbudowane jednostki — numer|"builtin.number"|"2"|
|Hierarchiczna jednostki - lokalizacji|"Location::ToLocation"|"Paryża"|

## <a name="list-entity-data"></a>Listy danych jednostki

A [listy](luis-concept-entity-types.md) jednostki jest nie maszyny rozpoznana. Konieczne jest dopasowanie dokładne tekstu. Lista reprezentuje elementy na liście oraz synonimy dla tych elementów. LUIS oznacza wszystkie dopasowania do elementu w żadnej listy jako jednostki w odpowiedzi. Synonim może znajdować się na liście więcej niż jeden. 

Załóżmy, że aplikacja ma listę o nazwie `Cities`, dzięki czemu dla zmian nazwy Miasto, w tym miejscowość lotnisku (Sea ODP), kod lotnisku (SEA), kod pocztowy (98101) i telefonu numer kierunkowy (206). 

|Element listy|Element synonimy|
|---|---|
|Seattle|Odp SEA, sea, 98101, 206, + 1 |
|Paryż|cdg, roissy historii, 75001, 1, +33|

`book 2 tickets to paris`

W poprzednich utterance, słowa `paris` jest zamapowany do elementu Paryża jako część `Cities` listy jednostki. Jednostka listy jest zgodny zarówno znormalizowaną nazwę elementu, a także synonimy elementu. 

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 22,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

Inny utterance przykład, przy użyciu synonim Paryża:

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

## <a name="prebuilt-entity-data"></a>Wbudowane jednostek danych
[Wbudowane](luis-concept-entity-types.md) jednostek są odnajdywane w oparciu o dopasowanie wyrażenia regularnego przy użyciu open source [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text) projektu. Wbudowane jednostki są zwracane w tablicy jednostek i użyj nazwy typu i jest poprzedzony prefiksem `builtin::`. Następujący tekst jest utterance przykład z zwrócone jednostki wbudowane:

`Dec 5th send to +1 360-555-1212`

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
``` 

## <a name="regular-expression-entity-data"></a>Wyrażenie regularne danych dotyczących jednostki
[Wyrażenie regularne](luis-concept-entity-types.md) jednostek są odnajdywane w oparciu o dopasowanie wyrażenia regularnego przy użyciu wyrażenia podawane podczas tworzenia jednostki. Korzystając z `kb[0-9]{6}` jako wyrażenie regularne definicji jednostki, następującą odpowiedź JSON jest utterance przykład z jednostek zwracanych wyrażeń regularnych dla zapytania `When was kb123456 published?`:

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="extracting-names"></a>Wyodrębnianie nazw
Pobieranie nazwy z utterance jest trudne, ponieważ nazwa może być praktycznie dowolnej kombinacji literami i wyrazami. W zależności od tego, jakiego typu o nazwie zostaną wyodrębnione masz kilka opcji. Nie są reguły, ale dodatkowe wskazówki. 

### <a name="names-of-people"></a>Nazwy użytkowników
Nazwy użytkowników może mieć niektóre nieznaczne format, w zależności od języka i kultury. Użyj hierarchiczna jednostki z imiona i nazwiska jako elementy podrzędne lub jednostki prostej z rolami imię i nazwisko. Upewnij się, że znajdują się przykłady używających imię i nazwisko w różnych części utterance, zniesławiających różne długości i zniesławiających we wszystkich opcji tym brak konwersji. [Przegląd](label-suggested-utterances.md) zniesławiających punktu końcowego na bieżąco etykietę żadnych nazw, które nie zostały poprawnie przewidzieć. 

### <a name="names-of-places"></a>Nazwy miejsca
Ustaw nazwy lokalizacji i znany, takich jak miast, powiatów stanów, prowincje i krajów. Jeśli aplikacja korzysta z zestawu znanych lokalizacji, należy wziąć pod uwagę jednostki listy. Aby dowiedzieć się, że wszystkie umieścić nazwy należy utworzyć jednostki prostej i udostępniać różne przykłady. Dodaj listę frazy nazwy miejsce do wymuszania sytuacji, w jakie miejsce nazwy przypominają w aplikacji. [Przegląd](label-suggested-utterances.md) zniesławiających punktu końcowego na bieżąco etykietę żadnych nazw, które nie zostały poprawnie przewidzieć. 

### <a name="new-and-emerging-names"></a>Nazwy nowych i pojawiających się
Niektóre aplikacje wymagają można było znaleźć nowych i pojawiających się nazwy, takie jak produkty lub firmy. Jest to typ najtrudniejsze wyodrębniania danych. Rozpoczyna się od podmiotu prostego i dodać listę frazę. [Przegląd](label-suggested-utterances.md) zniesławiających punktu końcowego na bieżąco etykietę żadnych nazw, które nie zostały poprawnie przewidzieć. 

## <a name="pattern-roles-data"></a>Dane ról wzorca
Role są kontekstowe różnice jednostek. 

```JSON
{
  "query": "move bob jones from seattle to redmond",
  "topScoringIntent": {
    "intent": "MoveAssetsOrPeople",
    "score": 0.9999998
  },
  "intents": [
    {
      "intent": "MoveAssetsOrPeople",
      "score": 0.9999998
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 6.12244548E-07
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 6.12244548E-07
    },
    {
      "intent": "FindForm",
      "score": 1.1E-09
    }
  ],
  "entities": [
    {
      "entity": "bob jones",
      "type": "Employee",
      "startIndex": 5,
      "endIndex": 13,
      "score": 0.922820568,
      "role": ""
    },
    {
      "entity": "seattle",
      "type": "Location",
      "startIndex": 20,
      "endIndex": 26,
      "score": 0.948008537,
      "role": "Origin"
    },
    {
      "entity": "redmond",
      "type": "Location",
      "startIndex": 31,
      "endIndex": 37,
      "score": 0.7047979,
      "role": "Destination"
    }
  ]
}
```

## <a name="patternany-entity-data"></a>Pattern.any danych dotyczących jednostki
Pattern.any jednostki to jednostki o zmiennej długości, używany w szablonie zniesławiających z [wzorzec](luis-concept-patterns.md). 

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```


## <a name="sentiment-analysis"></a>Analiza opinii
Jeśli skonfigurowano analizy wskaźniki nastrojów klientów, odpowiedź w formacie json LUIS obejmuje analizy wskaźniki nastrojów klientów. Dowiedz się więcej na temat analizy wskaźniki nastrojów klientów w [Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) dokumentacji.

### <a name="sentiment-data"></a>Wskaźniki nastrojów klientów danych
Wskaźniki nastrojów klientów danych jest wynikiem między 1 i wskazujący dodatnich 0 (bliżej 1) lub ujemną (bliżej 0) wskaźniki nastrojów klientów danych.

Gdy jest kultury `en-us`, odpowiedź jest:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Dla wszystkich innych kultur odpowiedź jest:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Hasło klucza wyodrębniania danych dotyczących jednostki
Hasło klucza jednostki wyodrębniania zwraca fraz klucza w utterance, dostarczone przez [Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

<!-- TBD: verify JSON-->
```JSON
"keyPhrases": [
    "places",
    "beautiful views",
    "favorite trail"
]
```

## <a name="data-matching-multiple-entities"></a>Dopasowywanie wiele jednostek danych
LUIS zwraca wszystkich jednostek w utterance. W związku z tym chatbot Twojego może być konieczne podjęcie decyzji na podstawie wyników. Utterance może mieć wiele jednostek w utterance:

`book me 2 adult business tickets to paris tomorrow on air france`

Punkt końcowy LUIS umożliwia odnalezienie tych samych danych w różnych obiektów: 

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Zobacz [Dodaj jednostki](luis-how-to-add-entities.md) Aby dowiedzieć się więcej o sposobie dodawania do aplikacji LUIS jednostek.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
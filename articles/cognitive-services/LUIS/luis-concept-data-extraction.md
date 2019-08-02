---
title: Wyodrębnianie danych — LUIS
titleSuffix: Azure Cognitive Services
description: Wyodrębnij dane z wypowiedź tekstu z intencjami i jednostkami. Dowiedz się, jakiego rodzaju dane mogą zostać wyodrębnione z Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 055cd25f534de5d3cc3ccbe44df88e7111e101a3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560754"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Wyodrębnij dane z wypowiedź tekstu z intencjami i jednostkami
Usługa LUIS daje możliwość pobrać informacje z wypowiedzi języka naturalnego użytkownika. Informacje są wyodrębniane w sposób, że może służyć przez program, aplikacji lub czatbot podjąć działania. W poniższych sekcjach Dowiedz się, jakie dane są zwracane z intencje i podmioty, wraz z przykładami JSON.

Najtrudniejsze dane do wyodrębnienia to dane zdobyte na maszynie, ponieważ nie jest to dokładne dopasowanie tekstu. Wyodrębnianie danych [jednostek](luis-concept-entity-types.md) , które są poznanie maszynowe, musi być częścią [cyklu tworzenia](luis-concept-app-iteration.md) , dopóki nie uzyskasz pewności, że otrzymasz oczekiwane dane.

## <a name="data-location-and-key-usage"></a>Użycie danych lokalizacji i klucz
Usługa LUIS udostępnia dane z opublikowanego [punktu końcowego](luis-glossary.md#endpoint). **Żądanie HTTPS** (POST lub GET) zawiera wypowiedź, a także niektóre konfiguracje opcjonalne, takie jak środowisk przejściowych lub produkcyjnych.

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

Jest ona dostępna na stronie **Ustawienia** aplikacji Luis, a także jako część adresu URL (po `/apps/`) podczas edytowania tej aplikacji Luis. `appID` `subscription-key` Jest klucza punktu końcowego używanego na potrzeby zapytań Twojej aplikacji. Chociaż możesz użyć bezpłatnego klucza autorstwa/początkowego podczas uczenia się LUIS, ważne jest, aby zmienić klucz punktu końcowego na klucz, który obsługuje [oczekiwane użycie Luis](luis-boundaries.md#key-limits). `timezoneOffset` Jednostka jest minut.

**Odpowiedzi HTTPS** zawiera wszystkie informacje na temat intencji i jednostki usługi LUIS można określić na podstawie bieżącego opublikowanego modelu albo punktu końcowego przejściowych lub produkcyjnych. Punkt końcowy adres URL znajduje się na [LUIS](luis-reference-regions.md) witryny sieci Web w **Zarządzaj** sekcji na **kluczy i punktów końcowych** strony.

## <a name="data-from-intents"></a>Dane z opcjami
Danych podstawowych jest najwyższym oceniania **intencji nazwa**. Za pomocą `MyStore` [Szybki Start](luis-quickstart-intents-only.md), odpowiedź na punkt końcowy jest:

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
|Intencja|Ciąg|topScoringIntent.intent|"GetStoreInfo"|

Jeśli swoją aplikacją usługi LUIS wywołania lub chatbot podejmuje decyzję oparciu o więcej niż jeden wynik konwersji, zwracają wyniki wszystkich intencji, ustawiając parametr querystring `verbose=true`. Odpowiedź na punkt końcowy jest:

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

Intencji są uporządkowane od najwyższego do najniższego wyniku.

|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|Wynik|
|--|--|--|--|:--|
|Intencja|Ciąg|.intent intencji [0]|"GetStoreInfo"|0.984749258|
|Intencja|Ciąg|.intent intencji [1]|"None"|0.0168218873|

Jeśli dodasz ze wstępnie utworzonych domen, intencji wskazuje nazwa domeny, takich jak `Utilties` lub `Communication` oraz zamiar:

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

|Domain|Obiekt danych|Typ danych|Lokalizacja danych|Wartość|
|--|--|--|--|--|
|Usługi użyteczności publicznej|Intencja|Ciąg|.intent intencji [0]|"<b>Narzędzia</b>. ShowNext"|
|Komunikacja|Intencja|Ciąg|.intent intencji [1]|<b>Komunikacja</b>. StartOver"|
||Intencja|Ciąg|.intent intencji [2]|"None"|


## <a name="data-from-entities"></a>Dane z jednostki
Większość czatbotów i aplikacje muszą mieć więcej niż nazwa metody konwersji. To dodatkowe, opcjonalne dane pochodzą z odnalezionych w wypowiedź jednostek. Każdy typ jednostki, zwraca różne informacje o zgodności.

Więcej niż jednej jednostki może odnosić się pojedynczego wyrazu lub frazy w wypowiedź. W takim przypadku każdego pasującego obiektu jest zwracany za pomocą jego wynik.

Wszystkie jednostki są zwracane w **jednostek** tablicy odpowiedzi z punktu końcowego:

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
Kilka [kultur](luis-language-support.md#tokenization) zwracają obiekt jednostki za pomocą `entity` wartość [stokenizowana](luis-glossary.md#token). StartIndex i endIndex zwrócony przez usługi LUIS w obiekcie jednostki nie są mapowane na nowe, tokenami wartość tylko do oryginalnego zapytania w celu uzyskania programowo Wyodrębnij nieprzetworzona jednostki. 

Na przykład w języku niemieckim, wyraz `das Bauernbrot` jest stokenizowana do `das bauern brot`. Wartość tokenami `das bauern brot`, jest zwracana i oryginalne wartości można programowo ustalić na podstawie startIndex i endIndex oryginalne zapytanie, zapewniając `das Bauernbrot`.

## <a name="simple-entity-data"></a>Proste jednostki danych

A [jednostki prostej](reference-entity-simple.md) jest wartością maszyny do opanowania. Można go wyrazu lub frazy.

## <a name="composite-entity-data"></a>Dane złożone jednostki

[Jednostka złożona](reference-entity-composite.md) składa się z innych jednostek, takich jak wstępnie zbudowane jednostki, proste, wyrażenie regularne i jednostki listy. Osobne jednostki tworzą całej jednostki. 

## <a name="list-entity-data"></a>Lista danych jednostki

[Jednostki listy](reference-entity-list.md) reprezentują stały, zamknięty zestaw powiązanych słów wraz z ich synonimami. Usługa LUIS nie wykrywa dodatkowe wartości dla jednostek z listy. Użyj **zaleca się** funkcji, aby zobaczyć sugestie dotyczące nowych słów na podstawie bieżącej listy. Jeśli istnieje więcej niż jednej jednostki listy z taką samą wartość, każdy obiekt jest zwracany w kwerendy punktu końcowego. 

## <a name="prebuilt-entity-data"></a>Wstępnie utworzone jednostki danych
[Wstępnie utworzone](luis-concept-entity-types.md) jednostki są wykrywane na podstawie dopasowania wyrażenia regularnego przy użyciu typu open-source [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text) projektu. Wstępnie utworzone jednostki są zwracane w tablicy jednostek i użyj nazwy typu prefiksem `builtin::`. Poniższy tekst to wypowiedź przykład za pomocą zwrócone ze wstępnie utworzonych jednostek:

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

[Jednostka wyrażenia regularnego](reference-entity-regular-expression.md) wyodrębnia jednostkę na podstawie podania wzorca wyrażenia regularnego.

## <a name="extracting-names"></a>Trwa wyodrębnianie nazw
Pobieranie nazwy z wypowiedź jest trudne, ponieważ nazwa może zawierać niemal dowolną kombinację literami i wyrazami. W zależności od typu wyodrębnianych nazw można korzystać z kilku opcji. Poniższe sugestie nie są regułami, ale są bardziej szczegółowe.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Dodawanie wstępnie utworzonych jednostek PersonName i GeographyV2

[](luis-reference-prebuilt-person.md) Jednostki PersonName i [GeographyV2](luis-reference-prebuilt-geographyV2.md) są dostępne w niektórych [kulturach językowych](luis-reference-prebuilt-entities.md). 

### <a name="names-of-people"></a>Nazwiska osób

Nazwa osób może mieć pewne niewielkie format, w zależności od języka i kultury. Użyj wstępnie utworzonej jednostki PersonName lub **[prostej jednostki](luis-concept-entity-types.md#simple-entity)** z [rolami](luis-concept-roles.md) imię i nazwisko. **[](luis-reference-prebuilt-person.md)** 

Jeśli używasz prostej jednostki, upewnij się, że są podane przykłady, które używają pierwszej i ostatniej nazwy w różnych częściach wypowiedź, w wyrażenia długości o różnej długości i wyrażenia długości we wszystkich intencjach, w tym dla opcji Brak. [Przegląd](luis-how-to-review-endoint-utt.md) wypowiedzi punktu końcowego w regularnych odstępach czasu, aby dodać etykietę żadnych nazw, które nie zostały poprawnie przewidzieć.

### <a name="names-of-places"></a>Nazwy miejsc

Nazwy lokalizacji są ustawiane i znane, takie jak miasta, powiaty, Stany, prowincje i kraje/regiony. Aby wyodrębnić informacje o lokalizacji, Użyj wstępnie skompilowanej jednostki **[geographyV2](luis-reference-prebuilt-geographyv2.md)** .

### <a name="new-and-emerging-names"></a>Nowe i rozwijające się nazwy

Niektóre aplikacje wymagają można było znaleźć nowe i rozwijające się nazwy, takie jak produkty lub firmy. Te typy nazw są najbardziej trudnym typem wyodrębniania danych. Zacznij od **[prostej jednostki](luis-concept-entity-types.md#simple-entity)** i Dodaj [listę fraz](luis-concept-feature.md). [Przegląd](luis-how-to-review-endoint-utt.md) wypowiedzi punktu końcowego w regularnych odstępach czasu, aby dodać etykietę żadnych nazw, które nie zostały poprawnie przewidzieć.

## <a name="pattern-roles-data"></a>Wzorzec role danych
Role różnią się kontekstowych jednostek.

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

[Wzorzec. any](reference-entity-pattern-any.md) jest symbolem zastępczym o zmiennej długości używanym tylko w szablonie wzorca wypowiedź, aby oznaczyć, gdzie rozpoczyna się i kończą.  

## <a name="sentiment-analysis"></a>Analiza tonacji
Jeśli jest skonfigurowana analiza tonacji, odpowiedź w formacie json usługi LUIS obejmuje analizę tonacji. Dowiedz się więcej na temat analizy tonacji w [analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) dokumentacji.

### <a name="sentiment-data"></a>Dane opinii
Dane opinii jest wynik w zakresie od 1 i 0, wskazując pozytywny (bliżej 1) lub ujemną (bliżej 0) tonacji danych.

Gdy jest kultura `en-us`, odpowiedź jest:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Dla wszystkich innych języków odpowiedź jest:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Dane jednostki wyodrębnianie kluczowych fraz
Jednostki wyodrębnianie kluczowych fraz zwraca kluczowych fraz w wypowiedź, dostarczone przez [analizy tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

## <a name="data-matching-multiple-entities"></a>Dopasowywanie wiele jednostek danych

Usługa LUIS zwraca wszystkie jednostki w wypowiedź. Co w efekcie Twoja chatbot może być konieczne podejmowanie decyzji na podstawie wyników. Wypowiedź może mieć wiele jednostek w wypowiedź:

`book me 2 adult business tickets to paris tomorrow on air france`

Punkt końcowy usługi LUIS można odnajdywanie tych samych danych w różnych jednostkach:

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

## <a name="data-matching-multiple-list-entities"></a>Dane zgodne z wieloma jednostkami listy

Jeśli wyraz lub frazę, pasuje do więcej niż jednej jednostki listy, kwerendy punktu końcowego zwraca każdy obiekt listy.

Dla zapytania `when is the best time to go to red rock?`, a aplikacja ma słowo `red` w więcej niż jednej listy LUIS rozpoznaje wszystkie jednostki i zwraca tablicę jednostki jako część odpowiedzi JSON punktu końcowego: 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

Zobacz [Dodaj jednostki](luis-how-to-add-entities.md) Aby dowiedzieć się więcej o sposobie dodawania jednostki z aplikacją usługi LUIS.

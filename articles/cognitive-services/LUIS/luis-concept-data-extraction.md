---
title: Wyodrębnianie danych
titleSuffix: Language Understanding - Azure Cognitive Services
description: Wyodrębnianie danych z tekstem wypowiedź intencje i podmioty. Dowiedz się, jakiego rodzaju dane można wyodrębnić z Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 35f1521884de3a4a0971b6e1c00f92a9094a8550
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526293"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Wyodrębnianie danych z tekstem wypowiedź intencje i podmioty
Usługa LUIS daje możliwość pobrać informacje z wypowiedzi języka naturalnego użytkownika. Informacje są wyodrębniane w sposób, że może służyć przez program, aplikacji lub czatbot podjąć działania. W poniższych sekcjach Dowiedz się, jakie dane są zwracane z intencje i podmioty, wraz z przykładami JSON.

Najtrudniejsze dane do wyodrębnienia to dane maszyny do opanowania, ponieważ nie znajduje się dopasowania tekstu do dokładnego dopasowania. Wyodrębnianie danych z maszyny przedstawiono [jednostek](luis-concept-entity-types.md) musi być częścią [cyklu tworzenia](luis-concept-app-iteration.md) aż masz pewność, odbieranie danych, o których oczekujesz.

## <a name="data-location-and-key-usage"></a>Użycie danych lokalizacji i klucz
Usługa LUIS udostępnia dane z opublikowanego [punktu końcowego](luis-glossary.md#endpoint). **Żądanie HTTPS** (POST lub GET) zawiera wypowiedź, a także niektóre konfiguracje opcjonalne, takie jak środowisk przejściowych lub produkcyjnych.

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

`appID` Jest dostępny na **ustawienia** strony z aplikacją usługi LUIS, jak również w adresie URL (po `/apps/`) podczas edytowania aplikacji usługi LUIS. `subscription-key` Jest klucza punktu końcowego używanego na potrzeby zapytań Twojej aplikacji. Za pomocą klucza usługi bezpłatne Tworzenie/modułu uruchamiającego podczas zapoznawania się LUIS, jest ważne, aby zmienić wartość klucza punktu końcowego z kluczem, który obsługuje Twoja [oczekiwane użycie usługi LUIS](luis-boundaries.md#key-limits). `timezoneOffset` Jednostka jest minut.

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

A [jednostki prostej](luis-concept-entity-types.md) jest wartością maszyny do opanowania. Można go wyrazu lub frazy.

`Bob Jones wants 3 meatball pho`

W poprzednim wypowiedź `Bob Jones` jest oznaczona jako prosty `Customer` jednostki.

Dane zwrócone z punktu końcowego obejmuje nazwa jednostki, odnalezione tekst z wypowiedź, lokalizacja odnalezionych tekstu i oceny:

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
|Proste jednostki|`Customer`|`bob jones`|

## <a name="hierarchical-entity-data"></a>Dane hierarchiczne jednostki

**Hierarchiczna jednostki po pewnym czasie zostaną wycofane. Użyj [ról jednostki](luis-concept-roles.md) Aby określić podtypy jednostki, zamiast hierarchiczne jednostek.**

[Hierarchiczna](luis-concept-entity-types.md) jednostki są rozpoznane przez maszynę i może zawierać słowo lub frazę. Elementy podrzędne są identyfikowane przez kontekst. Jeśli szukasz relacji nadrzędny podrzędny z dopasowaniem tekstu do dokładnego dopasowania użyj [listy](#list-entity-data) jednostki.

`book 2 tickets to paris`

W poprzednim wypowiedź `paris` jest oznaczona etykietą `Location::ToLocation` podrzędnym `Location` hierarchiczne jednostki.

Dane zwrócone z punktu końcowego obejmuje nazwa jednostki i nazwa elementu podrzędnego, odnalezionych tekst z wypowiedź, lokalizacja odnalezionych tekstu i oceny:

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
|--|--|--|--|
|Hierarchiczna jednostki|Lokalizacja|ToLocation|"Paryż"|

## <a name="composite-entity-data"></a>Dane złożone jednostki
[Złożone](luis-concept-entity-types.md) jednostki są rozpoznane przez maszynę i może zawierać słowo lub frazę. Na przykład, należy wziąć pod uwagę złożonego jednostki wstępnie `number` i `Location::ToLocation` za pomocą następujących wypowiedź:

`book 2 tickets to paris`

Należy zauważyć, że `2`, liczba i `paris`, ToLocation mają słowa między nimi, które nie należą do żadnej z jednostki. Zielonym podkreśleniem, używane w etykietami wypowiedź w [LUIS](luis-reference-regions.md) witryny sieci Web, wskazuje obiekt złożony.

![Złożone jednostki](./media/luis-concept-data-extraction/composite-entity.png)

Złożone jednostki są zwracane w `compositeEntities` tablicy i wszystkich jednostek w ramach złożonego jest także zwracany w `entities` tablicy:

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
|Wstępnie utworzone jednostki — liczba|"builtin.number"|"2"|
|Hierarchiczna jednostce — lokalizacji|"Location::ToLocation"|"Paryż"|

## <a name="list-entity-data"></a>Lista danych jednostki

A [listy](luis-concept-entity-types.md) jednostki nie jest maszyny do opanowania. Konieczne jest dopasowanie tekstu do dokładnego dopasowania. Lista reprezentuje elementy na liście wraz z synonimami tych elementów. Usługa LUIS oznacza jakiegokolwiek dopasowania do elementu w dowolnej listy jako jednostki w odpowiedzi. Jest to synonim mogą mieć więcej niż jednej listy.

Załóżmy, że aplikacja ma listę o nazwie `Cities`, dzięki czemu dla zmian nazwy miast, w tym mieście Kuwejcie (Sea tac), kod lotniska (SEA), kod pocztowy zip (98101) i numer kierunkowy telefonu (206).

|Element listy|Synonimy — element|
|---|---|
|Seattle|Odp morza, Morza, 98101, 206 + 1 |
|Paryż|cdg, roissy historii, 75001, 1, +33|

`book 2 tickets to paris`

W poprzednim wypowiedź, wyraz `paris` jest mapowany na element Paryż jako część `Cities` listy jednostek. Jednostka listy dopasowuje zarówno znormalizowaną nazwę elementu, jak i synonimy elementu.

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

Inny wypowiedź przykładzie, za pomocą synonim dla Paryż:

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
[Wyrażenie regularne](luis-concept-entity-types.md) jednostki są wykrywane na podstawie dopasowania wyrażenia regularnego przy użyciu wyrażenia podane podczas tworzenia jednostki. Korzystając z `kb[0-9]{6}` jako jej definicję wyrażenia regularnego, następującą odpowiedź JSON jest wypowiedź przykład z jednostkami zwrócone wyrażenie regularne dla zapytania `When was kb123456 published?`:

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

## <a name="extracting-names"></a>Trwa wyodrębnianie nazw
Pobieranie nazwy z wypowiedź jest trudne, ponieważ nazwa może zawierać niemal dowolną kombinację literami i wyrazami. W zależności od tego, jakiego rodzaju nazw można teraz wyodrębniania masz kilka opcji. Poniższe sugestie nie są reguły, ale dodatkowe wskazówki.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Dodaj wstępnie utworzone jednostki PersonName i GeographyV2

[PersonName](luis-reference-prebuilt-person.md) i [GeographyV2](luis-reference-prebuilt-geographyV2.md) jednostki są dostępne w niektórych [kultur języka](luis-reference-prebuilt-entities.md). 

### <a name="names-of-people"></a>Nazwiska osób

Nazwa osób może mieć pewne niewielkie format, w zależności od języka i kultury. Użyj jednej ze wstępnie utworzonych **[personName](luis-reference-prebuilt-person.md)** jednostki lub **[jednostki prostej](luis-concept-entity-types.md#simple-entity)** z [role](luis-concept-roles.md) pierwszych i nazwisko. 

Korzystając z prostego jednostki, upewnij się, zapewnienie przykłady z zastosowaniem imię i nazwisko w różnych częściach wypowiedź, wypowiedzi o różnej długości i wypowiedzi we wszystkich intencji, łącznie z Brak intencji. [Przegląd](luis-how-to-review-endoint-utt.md) wypowiedzi punktu końcowego w regularnych odstępach czasu, aby dodać etykietę żadnych nazw, które nie zostały poprawnie przewidzieć.

### <a name="names-of-places"></a>Nazwy miejsc

Nazwy lokalizacji są ustawiane i znany, takich jak miast, powiaty, Stany, prowincje i krajów. Użyj wstępnie utworzone jednostki **[geographyV2](luis-reference-prebuilt-geographyv2.md)** można wyodrębnić informacji o lokalizacji.

### <a name="new-and-emerging-names"></a>Nowe i rozwijające się nazwy

Niektóre aplikacje wymagają można było znaleźć nowe i rozwijające się nazwy, takie jak produkty lub firmy. Te typy nazw są najtrudniejsze typu wyodrębnianie danych. Zaczynają się od **[jednostki prostej](luis-concept-entity-types.md#simple-entity)** i Dodaj [listy fraz](luis-concept-feature.md). [Przegląd](luis-how-to-review-endoint-utt.md) wypowiedzi punktu końcowego w regularnych odstępach czasu, aby dodać etykietę żadnych nazw, które nie zostały poprawnie przewidzieć.

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
Jednostki pattern.any to jednostki o zmiennej długości, używane w wypowiedzi szablonu z [wzorzec](luis-concept-patterns.md).

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

## <a name="data-matching-multiple-list-entities"></a>Dane dopasowania wielu jednostek listy

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

## <a name="next-steps"></a>Kolejne kroki

Zobacz [Dodaj jednostki](luis-how-to-add-entities.md) Aby dowiedzieć się więcej o sposobie dodawania jednostki z aplikacją usługi LUIS.

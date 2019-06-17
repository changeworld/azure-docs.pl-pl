---
title: Umiejętności wyszukiwania kognitywnego shaper — usługa Azure Search
description: Wyodrębnianie metadanych i ustrukturyzowanych informacji z danych bez struktury i kształtować je jako typ złożony w usłudze Azure Search wzbogacony potok.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 058b6c979346d9dcce36940432d0e222e919dba9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540829"
---
#   <a name="shaper-cognitive-skill"></a>Shaper umiejętności cognitive

**Shaper** umiejętności konsoliduje kilku wejść do [typu złożonego](search-howto-complex-data-types.md) mogą być przywoływane w dalszej części wzbogacony potok. **Shaper** umiejętności umożliwia zasadniczo tworzenia struktury definiuje nazwy elementów członkowskich tej struktury i przypisania wartości do każdego elementu członkowskiego. Skonsolidowane pola przydatne w scenariuszach wyszukiwania przykładami łączenie imię i nazwisko w struktura single, miasta i stanu do pojedynczego struktury lub nazwa i Data urodzenia w jednym strukturę nawiązać unikatową tożsamość.

Wersja interfejsu API Określa głębokość kształtowania można osiągnąć. 

| Wersja interfejsu API | Kształtowanie zachowania | 
|-------------|-------------------|
| 2019-05-06-preview wersję interfejsu API REST (zestaw SDK platformy .NET nie jest obsługiwany) | Złożone obiekty, wiele poziomy głębokich, w jednym **Shaper** definicji umiejętności. |
| 2019-05-06 ** 2017-11-11-Preview (dostępne ogólnie)| Złożone obiekty głębokości jeden poziom. Wielopoziomowe kształt wymaga podjęcia kilku czynności shaper Łączenie łańcuchowe.|

Zgodnie z informacjami od `api-version=2019-05-06-Preview`, **Shaper** przedstawionych umiejętności [Scenariusz 3](#nested-complex-types) dodaje nowy opcjonalne *sourceContext* właściwości do danych wejściowych. *Źródła* i *sourceContext* właściwości wzajemnie się wykluczają. W przypadku danych wejściowych w kontekście umiejętności, po prostu użyć *źródła*. Jeśli dane wejściowe znajduje się na *różnych* kontekście niż kontekst umiejętności, użyj *sourceContext*. *SourceContext* wymaga zdefiniowania zagnieżdżone dane wejściowe z określonego elementu traktowane jako źródło. 

W odpowiedzi dla wszystkich wersji interfejsu API Nazwa wyjściowego jest zawsze "output". Wewnętrznie potoku można mapować innej nazwy, takie jak "analyzedText", jak pokazano w poniższych przykładach, ale **Shaper** umiejętności, sama zwraca "Wyjście" w odpowiedzi. Może to być ważne debugowania wzbogaconego dokumentów i zwróć uwagę, niezgodność nazw lub jeśli Tworzenie niestandardowych umiejętności i struktury odpowiedzi samodzielnie.

> [!NOTE]
> **Shaper** umiejętności nie jest powiązany z interfejsu API usług Cognitive Services i nie są naliczane dotyczące korzystania z niego. Powinna nadal [dołączenia zasobu usług Cognitive Services](cognitive-search-attach-cognitive-services.md), jednak aby zastąpić **bezpłatna** resource — Opcja ograniczająca na niewielką liczbę dziennych wzbogacenia dziennie.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Scenariusz 1: typy złożone

Rozważmy scenariusz, w którym chcesz utworzyć strukturę o nazwie *analyzedText* zawierający dwa elementy członkowskie: *tekstu* i *tonacji*odpowiednio. W ramach indeksu usługi Azure Search jest wywoływana wieloczęściowy polu możliwym do przeszukania *typu złożonego* i często jest tworzony, gdy źródło danych ma odpowiedniej struktury złożone, która mapuje do niego.

Innym podejściem do tworzenia złożonych typów jest jednak za pomocą **Shaper** umiejętności. Przez dołączenie tej umiejętności do zestawu umiejętności, operacje w pamięci podczas przetwarzania zestawu umiejętności mogą przesyłać dane wyjściowe dane kształty zagnieżdżonych struktur, które następnie mogą zostać zmapowane do typu złożonego w indeksie. 

Następująca definicja umiejętności przykład zawiera element członkowski nazw jako dane wejściowe. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Przykładowy indeks

Zestawu umiejętności jest wywoływany przez indeksator i indeksatora wymaga indeksu. Reprezentacja złożonych pola w indeksie może wyglądać jak w poniższym przykładzie. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Dane wejściowe umiejętności

Podanie można używać danych wejściowych dla tego dokumentu JSON przychodzącego **Shaper** umiejętności mogą być:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>Dane wyjściowe umiejętności

**Shaper** umiejętności generuje nowy element o nazwie *analyzedText* z elementami połączone *tekstu* i *tonacji*. Te dane wyjściowe jest zgodny ze schematem indeksu. Jego zostaną zaimportowane i indeksowane w indeksie usługi Azure Search.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>Scenariusz 2: konsolidacji danych wejściowych

W kolejnym przykładzie załóżmy, że na różnych etapach cyklu przetwarzania potokowego w programie, zostały wyodrębnione tytuł książki i oddzielenia na różnych stronach w książce. Można teraz utworzyć struktura single składa się z różnych danych wejściowych.

**Shaper** definicji umiejętności, w tym scenariuszu może wyglądać następująco:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Dane wyjściowe umiejętności
W tym przypadku **Shaper** Spłaszcza wszystkie tytuły rozdział, aby utworzyć pojedynczą tablicę. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Scenariusz 3: konsolidacji danych wejściowych z kontekstów zagnieżdżonych

> [!NOTE]
> Zagnieżdżone konstrukcje obsługiwane w [wersji interfejsu API REST 2019-05-06-Preview](search-api-preview.md) mogą być używane w [magazynu wiedzy](knowledge-store-concept-intro.md) lub w ramach indeksu usługi Azure Search.

Wyobraź sobie, tytuł, rozdziały i zawartość książki i uruchomiono jednostki wyrażeń rozpoznawanie i klucz zawartości i konieczność agregowanie wyników z różnych umiejętności do pojedynczego kształtu z nazwą działu, jednostek i fraz kluczowych.

**Shaper** definicji umiejętności, w tym scenariuszu może wyglądać następująco:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Dane wyjściowe umiejętności
W tym przypadku **Shaper** tworzy typu złożonego. Ta struktura istnieje w pamięci. Jeśli chcesz go zapisać w magazynie wiedzy, należy utworzyć projekcji w swojej zestawu umiejętności, która definiuje właściwości magazynu.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Jak używać typów złożonych](search-howto-complex-data-types.md)
+ [Omówienie magazynu wiedzy](knowledge-store-concept-intro.md)
+ [Jak rozpocząć pracę z magazynem wiedzy](knowledge-store-howto.md)
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
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 627c53f7339dbc35d822a0bf6038ca0f1ea5e653
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313840"
---
#   <a name="shaper-cognitive-skill"></a>Shaper umiejętności cognitive

**Shaper** umiejętności tworzy typu złożonego do obsługi złożonych pola (nazywane także wieloczęściowy pola). Pole Typ złożony ma wiele części, ale jest traktowany jako pojedynczy element w indeksie usługi Azure Search. Skonsolidowane pola przydatne w scenariuszach wyszukiwania przykładami łączenie imię i nazwisko w jedno pole, miasta i stanu do pojedynczego pola lub nazwa i Data urodzenia na pojedyncze pole, aby ustanowić unikatową tożsamość.

Umiejętności Shaper umożliwia zasadniczo tworzenia struktury definiuje nazwy elementów członkowskich tej struktury i przypisania wartości do każdego elementu członkowskiego.

Domyślnie ta technika obsługuje obiekty, które są szczegółowe o jeden poziom. W przypadku bardziej złożonych obiektów można połączyć kilka kroków Shaper.

W odpowiedzi Nazwa wyjściowego jest zawsze "output". Wewnętrznie potoku można mapować innej nazwy, takie jak "analyzedText" w poniższych przykładach "wyjściowe", ale Shaper umiejętności, sama zwraca "output" w odpowiedzi. Może to być ważne debugowania wzbogaconego dokumentów i zwróć uwagę, niezgodność nazw lub jeśli Tworzenie niestandardowych umiejętności i struktury odpowiedzi samodzielnie.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>Przykład 1: typy złożone

Rozważmy scenariusz, w którym chcesz utworzyć strukturę o nazwie *analyzedText* zawierający dwa elementy członkowskie: *tekstu* i *tonacji*odpowiednio. W usłudze Azure Search jest wywoływana wieloczęściowy polu możliwym do przeszukania *typu złożonego*, i nie jest jeszcze obsługiwany poza pakietem. W tej wersji zapoznawczej umiejętności Shaper może służyć do generowania pola typu złożonego w indeksie. 

W poniższym przykładzie przedstawiono elementu członkowskiego nazw jako dane wejściowe. Struktura danych wyjściowych (złożonych pola w usłudze Azure Search) jest określona za pomocą *targetName*. 


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
      "targetName": analyzedText"
    }
  ]
}
```

### <a name="sample-input"></a>Przykładowe dane wejściowe
Dokument JSON, zapewniając użyteczne dane wejściowe dla tego umiejętności Shaper może być:

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


### <a name="sample-output"></a>Przykładowe dane wyjściowe
Umiejętności Shaper generuje nowy element o nazwie *analyzedText* z elementami połączone *tekstu* i *tonacji*. 

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

## <a name="sample-2-input-consolidation"></a>Przykład 2: konsolidacji danych wejściowych

W kolejnym przykładzie załóżmy, że na różnych etapach cyklu przetwarzania potokowego w programie, zostały wyodrębnione tytuł książki i oddzielenia na różnych stronach w książce. Można teraz utworzyć struktura single składa się z różnych danych wejściowych.

Definicja Shaper umiejętności, w tym scenariuszu może wyglądać następująco:

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
            "source": "/document/content/pages/*/chapterTitles/*"
        }
    ],
    "outputs": [
        {
            "output": "titlesAndChapters",
            "targetName": "analyzedText"
        }
    ]
}
```

### <a name="sample-output"></a>Przykładowe dane wyjściowe
W tym przypadku Shaper Spłaszcza wszystkie tytuły rozdział, aby utworzyć pojedynczą tablicę. 

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

## <a name="see-also"></a>Zobacz także

+ [Wstępnie zdefiniowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)


---
title: Korektorze kognitywnych wyszukiwania umiejętności (Azure Search) | Dokumentacja firmy Microsoft
description: Wyodrębnianie metadanych i informacji z danych bez struktury i kształtu go jako typ złożony w potoku wzbogacenia usługi Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 311f4bd67081de567763783a9d86540eda36d9f8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33791007"
---
#   <a name="shaper-cognitive-skill"></a>Umiejętności kognitywnych korektorze

**Korektorze** umiejętności tworzy typu złożonego do obsługi złożonych pola (znanej także jako wieloczęściowego). Pole typu złożonego wiele części, ale jest traktowany jako pojedynczy element w indeksie usługi wyszukiwanie Azure. Skonsolidowane pola przydatne w scenariuszach wyszukiwania przykładami łączenie imię i nazwisko w jedno pole, miejscowość i stan jako pojedyncze pole lub nazwę i Data urodzenia w jedno pole do ustalenia tożsamości.

Umiejętności korektorze umożliwia zasadniczo Utwórz strukturę definiuje nazwy elementów członkowskich tej struktury i przypisać wartości do każdego elementu członkowskiego.

Domyślnie ta metoda obsługuje obiekty, które są głębokości jeden poziom. W przypadku bardziej złożonych obiektów tworzenia łańcucha kilka kroków korektorze.

W odpowiedzi nazwy wyjściowego jest zawsze "output". Wewnętrznie potoku można mapować na inną nazwę, takie jak "analyzedText" w poniższych przykładach "output", ale korektorze umiejętności sam "output" w odpowiedzi. Może to być ważne debugowania wzbogaconego dokumentów i zwróć uwagę, niezgodność nazw lub jeśli Tworzenie niestandardowych umiejętności i struktury odpowiedź samodzielnie.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>Przykład 1: typy złożone

Rozważmy scenariusz, w którym ma zostać utworzona struktura wywołuje *analyzedText* która ma dwa elementy członkowskie: *tekst* i *wskaźniki nastrojów klientów*odpowiednio. W usłudze Azure Search jest nazywany wieloczęściowych pole wyszukiwania *typu złożonego*, i nie jest nie jest jeszcze obsługiwane poza pole. W tej wersji zapoznawczej umiejętności korektorze można wygenerować pola typu złożonego w indeksie. 

W poniższym przykładzie przedstawiono składnik nazwy jako dane wejściowe. Struktura danych wyjściowych (złożone pola w usłudze Azure Search) jest określany za pośrednictwem *targetName*. 


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
Dokument JSON, zapewniając danych użytecznego wejściowych dla tego umiejętności korektorze może być:

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
Umiejętności korektorze generuje nowy element o nazwie *analyzedText* Scalonej elementami *tekst* i *wskaźniki nastrojów klientów*. 

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

## <a name="sample-2-input-consolidation"></a>Przykład 2: wejściowy konsolidacji

W kolejnym przykładzie załóżmy, że na różnych etapach przetwarzania potokowego zostały wyodrębnione tytuł książki i oddzielenia na różnych stronach książki. Można teraz utworzyć struktura single składa się z tych różnych danych wejściowych.

Korektorze definicji umiejętności w tym scenariuszu może wyglądać następująco:

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
W takim przypadku korektorze Spłaszcza wszystkie tytuły rozdział, aby utworzyć pojedynczą tablicę. 

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
+ [Sposób definiowania skillset](cognitive-search-defining-skillset.md)


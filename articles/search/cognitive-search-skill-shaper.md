---
title: Umiejętności poznawcze z zakresu kształtowania
titleSuffix: Azure Cognitive Search
description: Wyodrębnij metadane i informacje strukturalne z nieustrukturyzowanych danych i ukształtuj go jako typ złożony w potoku wzbogacania sztucznej inteligencji w usłudze Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 384b79037bb30656934c5e4b596dac2b776593b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754121"
---
# <a name="shaper-cognitive-skill"></a>Umiejętności poznawcze z zakresu kształtowania

**Shaper** umiejętności konsoliduje kilka danych wejściowych do [typu złożonego,](search-howto-complex-data-types.md) który może być odwołuje się później w potoku wzbogacania. **Shaper** umiejętności pozwala zasadniczo utworzyć strukturę, zdefiniować nazwę elementów członkowskich tej struktury i przypisać wartości do każdego elementu członkowskiego. Przykłady pól skonsolidowanych przydatnych w scenariuszach wyszukiwania obejmują łączenie imienia i nazwiska w jedną strukturę, miasto i stan w jedną strukturę lub nazwę i data urodzenia w jedną strukturę w celu ustanowienia unikatowej tożsamości.

Ponadto **Shaper** umiejętności zilustrowane w [scenariuszu 3](#nested-complex-types) dodaje opcjonalne *sourceContext* właściwości do danych wejściowych. Właściwości *źródła* i *sourceContext* wzajemnie się wykluczają. Jeśli dane wejściowe są w kontekście umiejętności, po prostu użyj *źródła*. Jeśli dane wejściowe są w *innym* kontekście niż kontekst umiejętności, użyj *sourceContext*. *SourceContext* wymaga zdefiniowania zagnieżdżonych danych wejściowych z określonym elementem, który jest adresowany jako źródło. 

Nazwa danych wyjściowych jest zawsze "wyjście". Wewnętrznie potoku można mapować inną nazwę, takich jak "analyzedText", jak pokazano w poniższych przykładach, ale **shaper** sama umiejętność zwraca "dane wyjściowe" w odpowiedzi. Może to być ważne, jeśli debugowanie wzbogaconych dokumentów i zauważyć rozbieżność nazewnictwa lub jeśli tworzysz umiejętności niestandardowe i strukturyzowania odpowiedzi samodzielnie.

> [!NOTE]
> **Umiejętność Shaper** nie jest powiązana z interfejsem API usług Cognitive Services i nie są naliczane opłaty za korzystanie z niej. Należy jednak [dołączyć zasób usług Cognitive Services,](cognitive-search-attach-cognitive-services.md)aby zastąpić opcję **Wolny** zasób, która ogranicza liczbę wzbogacenia dziennie.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Scenariusz 1: typy złożone

Rozważmy scenariusz, w którym chcesz utworzyć strukturę o nazwie *analyzedText,* który ma dwa elementy członkowskie: *tekst* i *tonację*, odpowiednio. W indeksie wieloczęściowe pole z przeszukiwania jest nazywane *typem złożonym* i często jest tworzone, gdy dane źródłowe mają odpowiednią złożoną strukturę, która jest do niego mapowana.

Jednak inne podejście do tworzenia typów złożonych jest za pośrednictwem **Shaper** umiejętności. Dołączając tę umiejętność do zestawu umiejętności, operacje w pamięci podczas przetwarzania zestawu umiejętności mogą wyprowadzać kształty danych za pomocą struktur zagnieżdżonych, które następnie można mapować na złożony typ w indeksie. 

Poniższa definicja umiejętności przykład zawiera nazwy elementów członkowskich jako dane wejściowe. 


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

Zestaw umiejętności jest wywoływany przez indeksatora, a indeksator wymaga indeksu. Złożona reprezentacja pola w indeksie może wyglądać jak w poniższym przykładzie. 

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

### <a name="skill-input"></a>Wprowadzanie umiejętności

Przychodzący dokument JSON zawierający użyteczne dane wejściowe dla tej umiejętności **Shaper** może być:

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


### <a name="skill-output"></a>Wydajność umiejętności

Umiejętność **Shaper** generuje nowy element o nazwie *analyzedText* z połączonymi elementami *tekstu* i *tonacji.* To dane wyjściowe jest zgodne ze schematem indeksu. Zostanie zaimportowany i zindeksowany w indeksie usługi Azure Cognitive Search.

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

## <a name="scenario-2-input-consolidation"></a>Scenariusz 2: konsolidacja wejściowa

W innym przykładzie wyobraź sobie, że na różnych etapach przetwarzania potoku wyodrębniłeś tytuł książki i tytuły rozdziałów na różnych stronach książki. Można teraz utworzyć pojedynczą strukturę składającą się z tych różnych danych wejściowych.

Definicja umiejętności **Shaper** dla tego scenariusza może wyglądać następująco:

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

### <a name="skill-output"></a>Wydajność umiejętności
W takim przypadku **Shaper** spłaszcza wszystkie tytuły rozdziałów, aby utworzyć jedną tablicę. 

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

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Scenariusz 3: konsolidacja danych wejściowych z kontekstów zagnieżdżonych

Wyobraź sobie, że masz tytuł, rozdziały i zawartość książki i uruchamiasz rozpoznawanie jednostek i kluczowe frazy dotyczące zawartości, a teraz musisz agregować wyniki z różnych umiejętności w jeden kształt z nazwą rozdziału, encjami i kluczowymi frazami.

Definicja umiejętności **Shaper** dla tego scenariusza może wyglądać następująco:

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

### <a name="skill-output"></a>Wydajność umiejętności
W takim przypadku **Shaper** tworzy typ złożony. Ta struktura istnieje w pamięci. Jeśli chcesz zapisać go w [magazynie wiedzy,](knowledge-store-concept-intro.md)należy utworzyć projekcję w swoim zestawem umiejętności, który definiuje charakterystykę pamięci masowej.

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

## <a name="see-also"></a>Zobacz też

+ [Wbudowane umiejętności](cognitive-search-predefined-skills.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Jak używać typów złożonych](search-howto-complex-data-types.md)
+ [Magazyn wiedzy (wersja zapoznawcza)](knowledge-store-concept-intro.md)
+ [Tworzenie magazynu wiedzy w rest](knowledge-store-create-rest.md)
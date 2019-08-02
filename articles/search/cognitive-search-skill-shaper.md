---
title: Umiejętność wyszukiwania poznawczego kształtu — Azure Search
description: Wyodrębnij metadane i informacje strukturalne z danych bez struktury i kształtj je jako typ złożony w potoku wzbogacenia Azure Search.
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
ms.openlocfilehash: 1a970bb2c33db1ad78dca088b7d9b2430984df96
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698867"
---
#   <a name="shaper-cognitive-skill"></a>Umiejętności poznawcze kształtu

Umiejętność **kształtu** konsoliduje kilka wejść do [typu złożonego](search-howto-complex-data-types.md) , do którego można przystąpić później w potoku wzbogacania. Umiejętność **kształtu** pozwala na zasadniczo utworzyć strukturę, zdefiniować nazwę elementów członkowskich tej struktury i przypisać wartości do każdego elementu członkowskiego. Przykłady skonsolidowanych pól przydatnych w scenariuszach wyszukiwania obejmują łączenie imion i nazwisk w jedną strukturę, miasto i stan w jedną strukturę, a także nazwę i DataUrodzenia w jednej strukturze w celu ustanowienia unikatowej tożsamości.

Ponadto umiejętność **kształtowania** zilustrowana w [scenariuszu 3](#nested-complex-types) dodaje opcjonalną Właściwość *sourceContext* do danych wejściowych. Właściwości *Source* i *sourceContext* wzajemnie się wykluczają. Jeśli dane wejściowe są w kontekście umiejętności, wystarczy użyć *źródła*. Jeśli dane wejściowe są w *innym* kontekście niż kontekst umiejętności, użyj *sourceContext*. *SourceContext* wymaga zdefiniowania zagnieżdżonych danych wejściowych z określonym elementem, który jest adresowany jako źródło. 

Nazwa wyjściowa jest zawsze "output". Wewnętrznie potok może mapować inną nazwę, taką jak "analyzedText", jak pokazano w poniższych przykładach, ale umiejętność **kształtowania** zwraca wartość "output" w odpowiedzi. Taka sytuacja może być ważna, jeśli debugujesz wzbogacone dokumenty i zauważysz rozbieżność nazewnictwa, lub jeśli tworzysz umiejętność niestandardową i utworzysz swoją samą strukturę.

> [!NOTE]
> Umiejętność **kształtu** nie jest powiązana z interfejsem API Cognitive Services i nie jest naliczana opłata za korzystanie z niego. Mimo to należy [dołączyć zasób Cognitive Services](cognitive-search-attach-cognitive-services.md), aby zastąpić opcję bezpłatnych zasobów , która ogranicza liczbę codziennych wzbogacań dziennie.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Scenariusz 1: typy złożone

Rozważmy scenariusz, w którym chcesz utworzyć strukturę o nazwie *analyzedText* , która ma dwa elementy członkowskie: odpowiednio *tekst* i *tonacji*. W indeksie Azure Search pole z możliwością wyszukiwania wieloczęściowego nosi nazwę *typu złożonego* i często jest tworzone, gdy dane źródłowe mają odpowiednią strukturę złożoną, która jest mapowana na ten element.

Jednak inne podejście do tworzenia typów złożonych polega na użyciu umiejętności **kształtu** . Uwzględniając te umiejętności w zestawu umiejętności, operacje w pamięci podczas przetwarzania zestawu umiejętności mogą wyprowadzać kształty danych z zagnieżdżonymi strukturami, które następnie można mapować na typ złożony w indeksie. 

Poniższa przykładowa definicja umiejętności zawiera nazwy elementów członkowskich jako dane wejściowe. 


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

Element zestawu umiejętności jest wywoływany przez indeksator, a indeksator wymaga indeksu. Złożona reprezentacja pola w indeksie może wyglądać podobnie do poniższego przykładu. 

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

### <a name="skill-input"></a>Dane wejściowe kwalifikacji

Dokument przychodzącego JSON udostępniający użyteczne dane wejściowe dla tej umiejętności **kształtu** może być następujący:

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


### <a name="skill-output"></a>Dane wyjściowe kwalifikacji

Umiejętność **kształtu** generuje nowy element o nazwie *analyzedText* z połączonymi elementami *tekstu* i *tonacji*. Dane wyjściowe są zgodne ze schematem indeksu. Zostanie ona zaimportowana i zindeksowana w indeksie Azure Search.

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

## <a name="scenario-2-input-consolidation"></a>Scenariusz 2: Konsolidacja danych wejściowych

W innym przykładzie Załóżmy, że na różnych etapach przetwarzania potoku wyodrębniono tytuł książki oraz tytuły rozdziałów na różnych stronach książki. Teraz można utworzyć pojedynczą strukturę składającą się z tych różnych danych wejściowych.

Definicja umiejętności **kształtu** w tym scenariuszu może wyglądać podobnie do poniższego przykładu:

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

### <a name="skill-output"></a>Dane wyjściowe kwalifikacji
W takim przypadku **kształt** Spłaszcza wszystkie tytuły rozdziałów, aby utworzyć pojedynczą tablicę. 

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

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Scenariusz 3: wprowadzanie konsolidacji z zagnieżdżonych kontekstów

Załóżmy, że masz tytuł, rozdziały i zawartość książki oraz uruchomiono rozpoznawanie jednostek i kluczowe frazy dla zawartości, a teraz trzeba agregować wyniki z różnych umiejętności w jednym kształcie z nazwą rozdziału, jednostkami i frazami kluczowymi.

Definicja umiejętności **kształtu** w tym scenariuszu może wyglądać podobnie do poniższego przykładu:

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

### <a name="skill-output"></a>Dane wyjściowe kwalifikacji
W takim przypadku **kształt** tworzy typ złożony. Ta struktura istnieje w pamięci. Jeśli chcesz zapisać go w [sklepie](knowledge-store-concept-intro.md)z bazami danych, należy utworzyć projekcję w zestawu umiejętności, która definiuje charakterystykę magazynu.

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
+ [Jak zdefiniować zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Jak używać typów złożonych](search-howto-complex-data-types.md)
+ [Przegląd sklepu merytorycznego](knowledge-store-concept-intro.md)
+ [Jak rozpocząć pracę ze sklepem merytorycznym](knowledge-store-howto.md)
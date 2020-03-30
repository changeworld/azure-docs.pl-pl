---
title: Mapowanie danych wejściowych na pola wyjściowe
titleSuffix: Azure Cognitive Search
description: Wyodrębnij i wzbogać pola danych źródłowych i mapuj do pól wyjściowych w indeksie usługi Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f0537af684632a08a39e3e681900d62238365073
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74280981"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Jak mapować pola wzbogacone o AI do indeksu z wyszukujalnym

W tym artykule dowiesz się, jak mapować wzbogacone pola wejściowe do pól wyjściowych w indeksie z wyszukujalnym. Po [zdefiniowaniu zestawu umiejętności](cognitive-search-defining-skillset.md)należy zamapować pola wyjściowe dowolnej umiejętności, która bezpośrednio przyczynia się do wartości w danym polu w indeksie wyszukiwania. 

Mapowania pól wyjściowych są wymagane do przenoszenia zawartości z wzbogaconych dokumentów do indeksu.  Wzbogacony dokument jest naprawdę drzewem informacji i mimo że istnieje obsługa złożonych typów w indeksie, czasami można przekształcić informacje z wzbogaconego drzewa w bardziej prosty typ (na przykład tablica ciągów). Mapowania pól wyjściowych umożliwiają wykonywanie przekształceń kształtów danych przez spłaszczenie informacji.

## <a name="use-outputfieldmappings"></a>Użyj outputFieldMappings
Aby zamapować `outputFieldMappings` pola, dodaj do definicji indeksatora, jak pokazano poniżej:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

Treść wniosku jest skonstruowana w następujący sposób:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions"
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

Dla każdego mapowania pól wyjściowych ustaw lokalizację danych w drzewie wzbogaconego dokumentu (sourceFieldName) i nazwę pola, do którego odwołuje się indeks (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Spłaszczanie informacji z typów złożonych 

Ścieżka w sourceFieldName może reprezentować jeden element lub wiele elementów. W powyższym ```/document/content/sentiment``` przykładzie reprezentuje pojedynczą ```/document/content/organizations/*/description``` wartość liczbową, podczas gdy reprezentuje kilka opisów organizacji. 

W przypadkach, gdy istnieje kilka elementów, są one "spłaszczone" do tablicy, która zawiera każdy z elementów. 

Bardziej konkretnie, w ```/document/content/organizations/*/description``` przykładzie dane w polu *opisy* będą wyglądać jak płaska tablica opisów, zanim zostanie zindeksowana:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Jest to ważna zasada, dlatego przedstawimy kolejny przykład. Wyobraź sobie, że masz tablicę typów złożonych jako część drzewa wzbogacania. Załóżmy, że istnieje element członkowski o nazwie customEntities, który ma tablicę złożonych typów, takich jak ten opisany poniżej.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

Załóżmy, że indeks ma pole o nazwie "choroby" typu Kolekcja(Edm.String), w którym chcesz przechowywać każdą z nazw jednostek. 

Można to łatwo zrobić za\*pomocą symbolu " " w następujący sposób:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Ta operacja będzie po prostu "spłaszczyć" każdą z nazw elementów customEntities w jednej tablicy ciągów w ten sposób:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Następne kroki
Po zamapowania wzbogaconych pól na pola z wyszukujem można ustawić atrybuty pola dla każdego pola z wyszukujalnymi [jako część definicji indeksu](search-what-is-an-index.md).

Aby uzyskać więcej informacji na temat mapowania pól, zobacz [Mapowania pól w indeksatorach usługi Azure Cognitive Search](search-indexer-field-mappings.md).

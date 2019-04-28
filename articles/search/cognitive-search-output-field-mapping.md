---
title: Usługa cognitive search mapy wzbogacony pól wejściowych na pól wyjściowych — usługa Azure Search
description: Wyodrębnianie i wzbogacanie pola danych źródła i mapowane na pola danych wyjściowych do indeksu usługi Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: dd62119b01465392a92c7e68231fed8027b04da2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342624"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Jak zamapować pola wzbogaconego przeszukiwanie indeksu

W tym artykule dowiesz się, jak zamapować wzbogaconego pól wejściowych danych wyjściowych pola w indeksie wyszukiwania. Po utworzeniu [zdefiniowane zestawu umiejętności](cognitive-search-defining-skillset.md), musisz dokonać mapowania pól danych wyjściowych dowolnego umiejętności, która bezpośrednio wpływa wartości do danego pola w indeksie wyszukiwania. Mapowania pól są wymagane do przenoszenia zawartości z wzbogaconego dokumentów do indeksu.


## <a name="use-outputfieldmappings"></a>Użyj outputFieldMappings
Aby zamapować pola, Dodaj `outputFieldMappings` do swojej definicji indeksatora, jak pokazano poniżej:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

Treść żądania jest zbudowany w następujący sposób:

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
Dla każdego mapowania pola danych wyjściowych, należy ustawić nazwę pola wzbogaconego (sourceFieldName) i nazwy pola zgodnie z odwołaniem w indeksie (targetFieldName).

Ścieżka w sourceFieldName może reprezentować jeden element lub wiele elementów. W przykładzie powyżej ```/document/content/sentiment``` reprezentuje jedną wartość liczbową, podczas gdy ```/document/content/organizations/*/description``` reprezentuje kilka opisy organizacji. W przypadkach, w których istnieje wiele elementów one są "jako spłaszczone" do tablicę zawierającą każdy z elementów. Więcej konkretnie dla ```/document/content/organizations/*/description``` przykład, dane w *opisy* pola będzie wyglądać prostego tablicę opisy, przed rozpoczęciem ich indeksowania:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Kolejne kroki
Po dokonaniu mapowania pól wzbogaconego do pola z możliwością wyszukiwania, można ustawić atrybutów pola dla każdego pola z możliwością wyszukiwania [jako część definicji indeksu](search-what-is-an-index.md).

Aby uzyskać więcej informacji na temat mapowania pól, zobacz [mapowania pól w indeksatorach usługi Azure Search](search-indexer-field-mappings.md).

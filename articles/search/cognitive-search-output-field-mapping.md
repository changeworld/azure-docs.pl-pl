---
title: Usługa cognitive search mapy wzbogacony pól wejściowych na pól wyjściowych — usługa Azure Search
description: Wyodrębnianie i wzbogacanie pola danych źródła i mapowane na pola danych wyjściowych do indeksu usługi Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 506acee6cd9cd3c50e10f1c45768230564eeaaf1
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65022091"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Jak zamapować pola wzbogaconego przeszukiwanie indeksu

W tym artykule dowiesz się, jak zamapować wzbogaconego pól wejściowych danych wyjściowych pola w indeksie wyszukiwania. Po utworzeniu [zdefiniowane zestawu umiejętności](cognitive-search-defining-skillset.md), musisz dokonać mapowania pól danych wyjściowych dowolnego umiejętności, która bezpośrednio wpływa wartości do danego pola w indeksie wyszukiwania. Mapowania pól są wymagane do przenoszenia zawartości z wzbogaconego dokumentów do indeksu.


## <a name="use-outputfieldmappings"></a>Użyj outputFieldMappings
Aby zamapować pola, Dodaj `outputFieldMappings` do swojej definicji indeksatora, jak pokazano poniżej:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
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

---
title: Mapowanie pól wejściowych wzbogaconych AI do pól wyjściowych
titleSuffix: Azure Cognitive Search
description: Wyodrębnij i Wzbogacaj pola danych źródłowych i Mapuj do pól wyjściowych w indeksie Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 77f378f4e0c4a8e4827523e244f7b18c2a9ba336
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792110"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Jak mapować pola wzbogacone AI na indeks wyszukiwania

Ten artykuł zawiera informacje na temat mapowania ulepszonych pól wejściowych do pól wyjściowych w indeksie, który można przeszukiwać. Po [zdefiniowaniu zestawu umiejętności](cognitive-search-defining-skillset.md)należy zamapować pola danych wyjściowych o wszelkie umiejętności, które bezpośrednio współtworzą wartości w danym polu w indeksie wyszukiwania. Mapowania pól są wymagane do przeniesienia zawartości z ulepszonych dokumentów do indeksu.


## <a name="use-outputfieldmappings"></a>Użyj outputFieldMappings
Aby zmapować pola, Dodaj `outputFieldMappings` do definicji indeksatora, jak pokazano poniżej:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

Treść żądania jest uporządkowana w następujący sposób:

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
Dla każdego mapowania pola danych wyjściowych Ustaw nazwę wzbogaconego pola (sourceFieldName) i nazwę pola jako odwołanie w indeksie (targetFieldName).

Ścieżka w sourceFieldName może reprezentować jeden element lub wiele elementów. W powyższym przykładzie ```/document/content/sentiment``` reprezentuje pojedynczą wartość liczbową, a ```/document/content/organizations/*/description``` reprezentuje kilka opisów organizacji. W przypadkach, gdy istnieje kilka elementów, są one "spłaszczone" do tablicy zawierającej poszczególne elementy. Dokładniej, w przypadku ```/document/content/organizations/*/description``` przykładu dane w polu *opisy* będą wyglądały jak płaska tablica opisów przed rozpoczęciem indeksowania:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Następne kroki
Po zmapowaniu ulepszonych pól do pól z możliwością wyszukiwania można ustawić atrybuty pola dla każdego z pól z możliwością wyszukiwania [jako część definicji indeksu](search-what-is-an-index.md).

Aby uzyskać więcej informacji na temat mapowania pól, zobacz [mapowania pól w usłudze Azure wyszukiwanie poznawcze indeksatory](search-indexer-field-mappings.md).

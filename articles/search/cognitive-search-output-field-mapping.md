---
title: Mapuj przeszukiwanie poznawczych pól wejściowych do pól wyjściowych — Azure Search
description: Wyodrębnij i Wzbogacaj pola danych źródłowych i Mapuj do pól wyjściowych w indeksie Azure Search.
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 0e422d2453fe12280da9e9b0b5dc7aa391f97b9f
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186408"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Jak zmapować wzbogacone pola na indeks wyszukiwania

Ten artykuł zawiera informacje na temat mapowania ulepszonych pól wejściowych do pól wyjściowych w indeksie, który można przeszukiwać. Po zdefiniowaniu [zestawu umiejętności](cognitive-search-defining-skillset.md)należy zamapować pola danych wyjściowych o wszelkie umiejętności, które bezpośrednio współtworzą wartości w danym polu w indeksie wyszukiwania. Mapowania pól są wymagane do przeniesienia zawartości z ulepszonych dokumentów do indeksu.


## <a name="use-outputfieldmappings"></a>Użyj outputFieldMappings
Aby zmapować pola, Dodaj `outputFieldMappings` je do definicji indeksatora, jak pokazano poniżej:

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

Ścieżka w sourceFieldName może reprezentować jeden element lub wiele elementów. W powyższym ```/document/content/sentiment``` przykładzie reprezentuje pojedynczą wartość liczbową, ```/document/content/organizations/*/description``` a jednocześnie reprezentuje kilka opisów organizacji. W przypadkach, gdy istnieje kilka elementów, są one "spłaszczone" do tablicy zawierającej poszczególne elementy. Dokładniej, na ```/document/content/organizations/*/description``` przykład dane w polu *opisy* będą wyglądały jak płaska tablica opisów przed rozpoczęciem indeksowania:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Następne kroki
Po zmapowaniu ulepszonych pól do pól z możliwością wyszukiwania można ustawić atrybuty pola dla każdego z pól z możliwością wyszukiwania [jako część definicji indeksu](search-what-is-an-index.md).

Aby uzyskać więcej informacji na temat mapowania pól, zobacz [mapowania pól w indeksatorach Azure Search](search-indexer-field-mappings.md).

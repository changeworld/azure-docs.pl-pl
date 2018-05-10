---
title: Mapa kognitywnych wyszukiwania wzbogacone pól wejściowych do pól danych wyjściowych indeksów usługi Azure Search | Dokumentacja firmy Microsoft
description: Wyodrębnij uzupełnić pola dane źródłowe i mapowanie do pól danych wyjściowych do indeksu usługi Azure Search.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 67e4798070a73eebb8f61b0b260e3104e9ae6237
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Sposób mapowania pól wzbogaconego do indeksu wyszukiwania

W tym artykule dowiesz sposób mapowania pól wejściowych wzbogaconego do pól dane wyjściowe wyszukiwania indeksu. Po utworzeniu [zdefiniowane skillset](cognitive-search-defining-skillset.md), musisz zamapować pola danych wyjściowych żadnych umiejętności, która wspiera bezpośrednio wartości do danego pola w indeksie wyszukiwania. Mapowania pól są wymagane do przenoszenia zawartości z wzbogaconego dokumenty do indeksu.


## <a name="use-outputfieldmappings"></a>Użyj outputFieldMappings
Mapowania pól, Dodaj `outputFieldMappings` do definicji indeksatora w sposób przedstawiony poniżej:

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
Dla każdego mapowania pól danych wyjściowych, należy ustawić nazwę pola wzbogaconego (sourceFieldName) i nazwy pola zgodnie z informacjami zawartymi w indeksie (targetFieldName).

Ścieżka w sourceFieldName może reprezentować elementu jednego lub wielu elementów. W powyższym przykładzie ```/document/content/sentiment``` reprezentuje pojedynczą wartość liczbową, gdy ```/document/content/organizations/*/description``` reprezentuje kilka opisy organizacji. W przypadkach, w których istnieje wiele elementów one są "spłaszczane" do tablica zawierająca poszczególnych elementów. Więcej konkretnie dla ```/document/content/organizations/*/description``` przykład, dane w *opisy* pole będzie wyglądała płaskiej tablicę opisy przed jego pobiera indeksowane:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Kolejne kroki
Polom wzbogaconego zmapowaniu do pól z możliwością wyszukiwania można ustawić atrybuty pól dla każdego pola z możliwością przeszukiwania [jako część definicji indeksu](search-what-is-an-index.md).

Aby uzyskać więcej informacji dotyczących mapowania pól, zobacz [pola mapowania w indeksatory usługi Azure Search](search-indexer-field-mappings.md).
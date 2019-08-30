---
title: Indeksowanie obiektów BLOB zawierających wiele dokumentów indeksu wyszukiwania z usługi Azure Blob indeksator na potrzeby wyszukiwania pełnotekstowego — Azure Search
description: Przeszukiwanie obiektów blob platformy Azure pod kątem zawartości tekstowej przy użyciu indeksatora Azure Search obiektów BLOB. Każdy obiekt BLOB może zawierać jeden lub więcej Azure Searchych dokumentów indeksu.
ms.date: 05/02/2019
author: arv100kri
manager: nitinme
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: 2c2a17d006f65854a89b9fac1818fcec420c07dc
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182311"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>Indeksowanie obiektów BLOB generujących wiele dokumentów wyszukiwania
Domyślnie indeksator obiektów BLOB będzie traktować zawartość obiektu BLOB jako pojedynczy dokument wyszukiwania. Niektóre wartości typu analizowaniemode obsługują scenariusze, w których pojedynczy obiekt BLOB może spowodować wielokrotne wyszukiwanie dokumentów. Różne typy analizmode , które umożliwiają Indeksatorowi wyodrębnienie więcej niż jednego dokumentu wyszukiwania z obiektu BLOB:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Klucz dokumentu "jeden do wielu"
Każdy dokument, który jest wyświetlany w indeksie Azure Search, jest jednoznacznie identyfikowany przez klucz dokumentu. 

Gdy nie jest określony tryb analizowania i jeśli nie istnieje jawne mapowanie dla pola klucza w indeksie, Azure Search automatycznie mapuje [](search-indexer-field-mappings.md) `metadata_storage_path` właściwość jako klucz. To mapowanie zapewnia, że każdy obiekt BLOB będzie wyświetlany jako odrębny dokument wyszukiwania.

W przypadku korzystania z dowolnego z wymienionych powyżej trybów analizowania jeden obiekt BLOB mapuje do "wielu" dokumentów wyszukiwania, co sprawia, że klucz dokumentu opiera się wyłącznie na metadanych obiektów BLOB. Aby można było obejść to ograniczenie, Azure Search może wygenerować klucz dokumentu "jeden do wielu" dla każdej jednostki wyodrębnionej z obiektu BLOB. Ta właściwość ma nazwę `AzureSearch_DocumentKey` i jest dodawana do każdej pojedynczej jednostki wyodrębnionej z obiektu BLOB. Wartość tej właściwości gwarantuje, że jest unikatowa dla każdej jednostki w obiektach _BLOB_ , a jednostki będą widoczne jako osobne dokumenty wyszukiwania.

Domyślnie, gdy nie są określone żadne jawne mapowania pól dla pola indeks klucza, `AzureSearch_DocumentKey` jest ono mapowane na ten element `base64Encode` przy użyciu funkcji mapowania pól.

## <a name="example"></a>Przykład
Załóżmy, że masz definicję indeksu z następującymi polami:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

I kontener obiektów BLOB zawiera obiekty blob o następującej strukturze:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Gdy tworzysz indeksator i ustawisz dla opcji `jsonLines` -bez określania wszystkich jawnych mapowań pól klucza, następujące mapowanie zostanie zastosowane niejawnie
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Ta konfiguracja spowoduje, że indeks Azure Search zawierający następujące informacje (skrócony identyfikator kodowany algorytmem Base64 dla zwięzłości)

| id | temperature | pressure | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapowanie pola niestandardowego dla pola klucza indeksu

Przy założeniu, że ta sama definicja indeksu jest taka sama jak w poprzednim przykładzie, Załóżmy, że kontener obiektów BLOB zawiera obiekty blob o następującej strukturze:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Podczas tworzenia indeksatora przy użyciu `delimitedText` elementu **analizowaniamode**może zajść konieczność skonfigurowania funkcji mapowania pól do pola klucza w następujący sposób:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Jednak to mapowanie _nie_ powoduje, że 4 dokumenty wyświetlane w indeksie, ponieważ `recordid` pole nie jest unikatowe w obiektach _BLOB_. W związku z tym zalecamy użycie niejawnego mapowania pól stosowanego z `AzureSearch_DocumentKey` właściwości do pola indeksu klucza dla trybów analizy "jeden do wielu".

Jeśli chcesz skonfigurować jawne Mapowanie pól, upewnij się, że _sourceField_ różni się dla poszczególnych jednostek **we wszystkich**obiektach Blob.

> [!NOTE]
> Podejście używane w `AzureSearch_DocumentKey` celu zapewnienia unikatowości dla wyodrębnionej jednostki może ulec zmianie, dlatego nie należy polegać na jej wartości dla potrzeb aplikacji.

## <a name="see-also"></a>Zobacz także

+ [Indeksatory w Azure Search](search-indexer-overview.md)
+ [Indeksowanie Blob Storage platformy Azure z Azure Search](search-howto-index-json-blobs.md)
+ [Indeksowanie obiektów BLOB woluminów CSV za pomocą indeksatora Azure Search obiektów BLOB](search-howto-index-csv-blobs.md)
+ [Indeksowanie obiektów BLOB JSON przy użyciu indeksatora Azure Search obiektów BLOB](search-howto-index-json-blobs.md)

## <a name="NextSteps"></a>Następne kroki
* Aby dowiedzieć się więcej na temat Azure Search, zobacz [stronę usługi wyszukiwania](https://azure.microsoft.com/services/search/).
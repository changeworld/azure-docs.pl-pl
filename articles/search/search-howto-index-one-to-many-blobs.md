---
title: Indeksowanie jednego obiektu BLOB w wielu dokumentach indeksu wyszukiwania z usługi Azure Blob indeksator do wyszukiwania pełnotekstowego
description: Przeszukiwanie obiektów blob platformy Azure pod kątem zawartości tekstowej przy użyciu indeksatora obiektów BLOB usługi Azure Cognitive Search. Każdy obiekt BLOB może przynieść jeden lub więcej dokumentów indeksu wyszukiwania.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ec7796f19df8d58831b442adeae02b54223799c1
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793724"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indeksowanie obiektów BLOB w celu utworzenia wielu dokumentów wyszukiwania
Domyślnie indeksator obiektów BLOB będzie traktować zawartość obiektu BLOB jako pojedynczy dokument wyszukiwania. Niektóre wartości typu **analizowaniemode** obsługują scenariusze, w których pojedynczy obiekt BLOB może spowodować wielokrotne wyszukiwanie dokumentów. Różne typy **analizmode** , które umożliwiają Indeksatorowi wyodrębnienie więcej niż jednego dokumentu wyszukiwania z obiektu BLOB:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Klucz dokumentu "jeden do wielu"
Każdy dokument, który jest wyświetlany w indeksie Wyszukiwanie poznawcze platformy Azure, jest jednoznacznie identyfikowany przez klucz dokumentu. 

Gdy nie określono żadnego trybu analizowania i jeśli nie istnieje jawne mapowanie dla pola klucza w indeksie Azure Wyszukiwanie poznawcze automatycznie [mapuje](search-indexer-field-mappings.md) Właściwość `metadata_storage_path` jako klucz. To mapowanie zapewnia, że każdy obiekt BLOB będzie wyświetlany jako odrębny dokument wyszukiwania.

W przypadku korzystania z dowolnego z wymienionych powyżej trybów analizowania jeden obiekt BLOB mapuje do "wielu" dokumentów wyszukiwania, co sprawia, że klucz dokumentu opiera się wyłącznie na metadanych obiektów BLOB. Aby można było obejść to ograniczenie, usługa Azure Wyszukiwanie poznawcze może wygenerować klucz dokumentu "jeden do wielu" dla każdej jednostki wyodrębnionej z obiektu BLOB. Ta właściwość ma nazwę `AzureSearch_DocumentKey` i jest dodawana do poszczególnych jednostek wyodrębnionych z obiektu BLOB. Wartość tej właściwości gwarantuje, że jest unikatowa dla każdej jednostki w obiektach _BLOB_ , a jednostki będą widoczne jako osobne dokumenty wyszukiwania.

Domyślnie, gdy nie są określone żadne jawne mapowania pól dla pola indeks klucza, `AzureSearch_DocumentKey` jest mapowany do niego przy użyciu funkcji mapowania pól `base64Encode`.

## <a name="example"></a>Przykład
Załóżmy, że masz definicję indeksu z następującymi polami:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

I kontener obiektów BLOB zawiera obiekty blob o następującej strukturze:

_Blob1. JSON_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2. JSON_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Gdy tworzysz indeksator i ustawisz na **`jsonLines`** — bez określania wszystkich jawnych mapowań pól klucza, następujące mapowanie zostanie zastosowane niejawnie
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Ta konfiguracja spowoduje, że indeks usługi Azure Wyszukiwanie poznawcze zawierający następujące informacje (skrócony identyfikator kodowany algorytmem Base64 dla zwięzłości)

| id | temperature | pressure | sygnatura czasowa |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018 R-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013 – 05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapowanie pola niestandardowego dla pola klucza indeksu

Przy założeniu, że ta sama definicja indeksu jest taka sama jak w poprzednim przykładzie, Załóżmy, że kontener obiektów BLOB zawiera obiekty blob o następującej strukturze:

_Blob1. JSON_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2. JSON_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Podczas tworzenia indeksatora przy użyciu `delimitedText` **przeanalizowanie**może być w naturalny sposób skonfigurować funkcję mapowania pól do pola klucza w następujący sposób:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Jednak to mapowanie _nie_ powoduje, że 4 dokumenty wyświetlane w indeksie, ponieważ pole `recordid` nie jest unikatowe w obiektach _BLOB_. W związku z tym zaleca się użycie niejawnego mapowania pól zastosowanej ze właściwości `AzureSearch_DocumentKey` do pola indeksu klucza w przypadku trybów analizy "jeden do wielu".

Jeśli chcesz skonfigurować jawne Mapowanie pól, upewnij się, że _sourceField_ różni się dla poszczególnych jednostek **we wszystkich**obiektach Blob.

> [!NOTE]
> Podejście używane przez `AzureSearch_DocumentKey` zapewnienia unikatowości dla wyodrębnionej jednostki może ulec zmianie, dlatego nie należy polegać na jej wartości dla potrzeb aplikacji.

## <a name="next-steps"></a>Następne kroki

Jeśli nie znasz już podstawowej struktury i przepływu pracy indeksowania obiektów blob, należy najpierw przejrzeć [indeksowanie BLOB Storage Azure przy użyciu usługi Azure Cognitive Search](search-howto-index-json-blobs.md) . Aby uzyskać więcej informacji na temat trybów analizowania dla różnych typów conten obiektów blob, zapoznaj się z następującymi artykułami.

> [!div class="nextstepaction"]
> [Indeksowanie obiektów BLOB woluminów CSV](search-howto-index-csv-blobs.md) 
> [indeksowania obiektów BLOB JSON](search-howto-index-json-blobs.md)

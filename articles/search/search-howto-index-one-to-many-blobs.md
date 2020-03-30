---
title: Indeksowane obiekty blob zawierające wiele dokumentów
titleSuffix: Azure Cognitive Search
description: Przeszukiwanie obiektów BLOB platformy Azure dla zawartości tekstowej przy użyciu indeksatora obiektów blob usługi Azure Congitive Search, w którym każdy obiekt blob może przynieść jeden lub więcej dokumentów indeksu wyszukiwania.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1840bda0ecc9462a5d8f796b616d728d0bb412f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112268"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indeksowanie obiektów blob w celu tworzenia wielu dokumentów wyszukiwania
Domyślnie indeksator obiektów blob będzie traktować zawartość obiektu blob jako pojedynczy dokument wyszukiwania. Niektóre wartości **parsingMode** obsługują scenariusze, w których pojedynczy obiekt blob może spowodować wiele dokumentów wyszukiwania. Różne typy **parsingMode,** które umożliwiają indeksatorowi wyodrębnić więcej niż jeden dokument wyszukiwania z obiektu blob są:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Klucz dokumentu jeden do wielu
Każdy dokument, który pojawia się w indeksie usługi Azure Cognitive Search jest jednoznacznie identyfikowany przez klucz dokumentu. 

Gdy nie określono trybu analizy i jeśli nie ma jawnego mapowania pola klucza w `metadata_storage_path` indeksie Azure Cognitive Search automatycznie [mapuje](search-indexer-field-mappings.md) właściwość jako klucz. To mapowanie zapewnia, że każdy obiekt blob jest wyświetlany jako odrębny dokument wyszukiwania.

Podczas korzystania z dowolnego z trybów analizy wymienionych powyżej, jeden obiekt blob mapuje do "wielu" dokumentów wyszukiwania, co klucz dokumentu wyłącznie na podstawie metadanych obiektu blob nie jest odpowiedni. Aby przezwyciężyć to ograniczenie, usługa Azure Cognitive Search jest w stanie wygenerować klucz dokumentu "jeden do wielu" dla każdej jednostki wyodrębnione z obiektu blob. Ta właściwość `AzureSearch_DocumentKey` jest nazwany i jest dodawany do każdej jednostki wyodrębnione z obiektu blob. Wartość tej właściwości jest gwarantowana jest unikatowa dla każdej jednostki _w obiektach blob_ i jednostki będą wyświetlane jako oddzielne dokumenty wyszukiwania.

Domyślnie, gdy nie określono jawnych mapowań pól `AzureSearch_DocumentKey` dla pola indeksu klucza, jest on mapowany do niego przy użyciu funkcji mapowania `base64Encode` pól.

## <a name="example"></a>Przykład
Załóżmy, że masz definicję indeksu z następującymi polami:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

A kontener obiektów blob ma obiekty blob o następującej strukturze:

_Plik Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Plik Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Podczas tworzenia indeksatora i **ustawiania parsingMode** do `jsonLines` - bez określania żadnych jawnych mapowań pól dla pola klucza, następujące mapowanie zostanie zastosowane niejawnie
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Ta konfiguracja spowoduje, że indeks usługi Azure Cognitive Search będzie zawierał następujące informacje (identyfikator zakodowany w kodowaniu base64 skrócony dla zwięzłości)

| id | temperature | pressure | sygnatura czasowa |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx ( YjEuanNvbjsx ) | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy ( YjEuanNvbjsy ) | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx ( YjIuanNvbjsx ) | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy (YjIuanNvbjsy) | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Niestandardowe mapowanie pól dla pola klucza indeksu

Zakładając taką samą definicję indeksu jak w poprzednim przykładzie, załóżmy, że kontener obiektów blob ma obiekty blob o następującej strukturze:

_Plik Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Plik Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Podczas tworzenia indeksatora `delimitedText` z **analizowaniemMode**może wydawać się naturalne skonfigurowanie funkcji mapowania pola do pola klucza w następujący sposób:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Jednak to mapowanie _nie_ spowoduje 4 dokumentów wyświetlanych `recordid` w indeksie, ponieważ pole nie jest _unikatowe w obiektach blob_. W związku z tym zaleca się, aby użyć `AzureSearch_DocumentKey` niejawnego mapowania pola stosowane z właściwości do pola indeksu klucza dla trybów analizy "jeden do wielu".

Jeśli chcesz skonfigurować jawne mapowanie pól, upewnij się, że _sourceField_ jest odrębny dla każdej jednostki **we wszystkich obiektach blob**.

> [!NOTE]
> Podejście używane `AzureSearch_DocumentKey` przez zapewnienie unikatowości na wyodrębniony podmiot może ulec zmianie i dlatego nie należy polegać na jego wartość dla potrzeb aplikacji.

## <a name="next-steps"></a>Następne kroki

Jeśli nie jesteś jeszcze zaznajomiony z podstawowej struktury i przepływu pracy indeksowania obiektów blob, należy przejrzeć [indeksowanie usługi Azure Blob Storage z usługi Azure Cognitive Search](search-howto-index-json-blobs.md) pierwszy. Aby uzyskać więcej informacji na temat trybów analizowania dla różnych typów zawartości obiektu blob, zapoznaj się z poniższymi artykułami.

> [!div class="nextstepaction"]
> [Indeksowanie obiektów BLOB indeksowania](search-howto-index-csv-blobs.md)
> [obiektów blob JSON](search-howto-index-json-blobs.md)

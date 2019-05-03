---
title: Obiekty BLOB indeksu zawierające wiele wyszukiwania indeksować dokumenty z indeksatora obiektów Blob platformy Azure w celu wyszukiwania pełnotekstowego — usługa Azure Search
description: Można przeszukiwać w obiektach blob platformy Azure w przypadku zawartości tekstowej za pomocą indeksatora obiektów Blob platformy Azure Search. Każdy obiekt blob może zawierać jeden lub więcej dokumentów indeksu usługi Azure Search.
ms.date: 05/02/2019
author: arv100kri
manager: briansmi
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: 628ced069c9d32c6e874c2e36a1e3b752c476003
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024648"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>Indeksowanie obiektów blob, tworzyć wiele dokumentów wyszukiwania plików
Domyślnie indeksatora obiektów blob będzie traktowany zawartość obiektu blob jako wyszukiwania pojedynczego dokumentu. Niektóre **parsingMode** wartości obsługi scenariuszy, w którym poszczególnych obiektów blob może spowodować wiele dokumentów wyszukiwania. Różne rodzaje **parsingMode** umożliwiające indeksator do wyodrębniania więcej niż jednego wyszukiwania dokumentów z obiektu blob są:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Klucz dokumentu jeden do wielu
Każdy dokument, który pojawia się w indeksie usługi Azure Search jest unikatowo identyfikowane za pomocą klucza dokumentu. 

Jeśli nie określono tryb bez analizy, a w przypadku nie jawnego mapowania pola klucza w indeksie usługi Azure Search automatycznie [mapuje](search-indexer-field-mappings.md) `metadata_storage_path` właściwości jako klucz. To mapowanie gwarantuje, czy każdy obiekt blob jest wyświetlana jako odrębne wyszukiwania dokumentu.

W przypadku stosowania analizy trybów wymienionych powyżej jeden obiekt blob jest mapowany na "many" dokumentów wyszukiwania, dzięki czemu klucz dokumentu, wyłącznie na podstawie obiektu blob metadanych nie nadaje się. Aby wyeliminować to ograniczenie, usługa Azure Search jest zdolny do generowania klucza dokumentu "jeden do wielu" dla każdej jednostki poszczególnych wyodrębnić z obiektu blob. Ta właściwość jest o nazwie `AzureSearch_DocumentKey` i jest dodawana do każdej jednostki poszczególnych wyodrębnione z obiektu blob. Wartość tej właściwości jest musi być unikatowy dla każdej jednostki poszczególnych _między obiektami blob_ i jednostki pojawią się jako osobne wyszukiwania dokumentów.

Domyślnie, gdy nie określono żadnych mapowań pól jawne, dla pola klucza indeksu `AzureSearch_DocumentKey` jest mapowany do niego przy użyciu `base64Encode` funkcji mapowania pól.

## <a name="example"></a>Przykład
Przyjęto założenie, że masz definicji indeksu przy użyciu następujących pól:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

I kontenera obiektów blob ma obiektów blob o następującej strukturze:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Po utworzeniu indeksatora i ustawianie **parsingMode** do `jsonLines` — bez określania dowolnego pola jawnego mapowania pola klucza następującego mapowania zostaną zastosowane niejawnie
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Ten Instalator będzie skutkować indeksu usługi Azure Search, zawierający poniższe informacje (zakodowany w formacie base64 identyfikator skrócony dla zwięzłości)

| id | temperature | pressure | timestamp |
|----|-------------|----------|-----------|
| aHR0... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapowanie niestandardowego pola dla pola klucza indeksu

Zakładając, że tej samej definicji indeksu, jak w poprzednim przykładzie, załóżmy, że kontener obiektów blob ma obiektów blob o następującej strukturze:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Po utworzeniu indeksatora z `delimitedText` **parsingMode**, mogą czuć się fizyczną, aby skonfigurować funkcję mapowania pól do pola klucza w następujący sposób:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Jednak to mapowanie będzie _nie_ spowodować 4 dokumenty, które pojawią się w indeksie, ponieważ `recordid` pole nie jest unikatowa _między obiektami blob_. Dzięki temu firma Microsoft zaleca się korzystanie z mapowania niejawne pole z `AzureSearch_DocumentKey` właściwości pola klucza indeksu tryby analizy "jeden do wielu".

Jeśli chcesz skonfigurować mapowanie pola jawne, upewnij się, że _sourceField_ różni się dla każdej jednostki poszczególnych **we wszystkich obiektów blob**.

> [!NOTE]
> Metoda używana przez `AzureSearch_DocumentKey` zapewnienia unikatowości na jednostkę wyodrębniony i mogą ulec zmianie w związku z tym nie należy polegać na wartości dla potrzeb aplikacji.

## <a name="see-also"></a>Zobacz także

+ [Indeksatory w usłudze Azure Search](search-indexer-overview.md)
+ [Indeksowanie usługi Azure Blob Storage z usługą Azure Search](search-howto-index-json-blobs.md)
+ [Indeksowanie obiektów blob CSV za pomocą indeksatora obiektów blob usługi Azure Search](search-howto-index-csv-blobs.md)
+ [Indeksowanie obiektów blob JSON za pomocą indeksatora obiektów blob usługi Azure Search](search-howto-index-json-blobs.md)

## <a name="NextSteps"></a>Następne kroki
* Aby dowiedzieć się więcej o usłudze Azure Search, zobacz [stronę usługi wyszukiwania](https://azure.microsoft.com/services/search/).
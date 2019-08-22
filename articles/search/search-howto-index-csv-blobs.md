---
title: Indeksowanie obiektów BLOB woluminów CSV za pomocą indeksatora Azure Search blob — Azure Search
description: Przeszukiwanie obiektów BLOB CSV w usłudze Azure Blob Storage na potrzeby wyszukiwania pełnotekstowego przy użyciu indeksu Azure Search. Indeksatory automatyzują pozyskiwanie danych dla wybranych źródeł danych, takich jak Azure Blob Storage.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: b135fd1a0758567a7b504996bf442a913741fe59
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656754"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indeksowanie obiektów BLOB woluminów CSV za pomocą indeksatora Azure Search obiektów BLOB

> [!Note]
> Tryb analizy delimitedText jest w wersji zapoznawczej i nie jest przeznaczony do użycia w środowisku produkcyjnym. [Interfejs API REST w wersji 2019-05-06 —](search-api-preview.md) wersja zapoznawcza zawiera tę funkcję. W tej chwili nie ma obsługi zestawu SDK platformy .NET.
>

Domyślnie [indeksator Azure Search obiektów BLOB](search-howto-indexing-azure-blob-storage.md) analizuje rozdzielone tekstowe obiekty blob jako pojedynczy fragment tekstu. Jednak z obiektami BLOB zawierającymi dane CSV często warto traktować każdy wiersz w obiekcie BLOB jako oddzielny dokument. Na przykład, uwzględniając następujący rozdzielony tekst, możesz chcieć przeanalizować go w dwóch dokumentach, z których każdy zawiera pola "ID", "datePublished" i "Tags": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

W tym artykule dowiesz się, jak analizować obiekty blob w formacie CSV za pomocą Azure Search obiektu BLOB `delimitedText` indexerby ustawienie trybu analizy. 

> [!NOTE]
> Postępuj zgodnie z zaleceniami dotyczącymi konfiguracji indeksatora w [indeksie jeden-do-wielu](search-howto-index-one-to-many-blobs.md) , aby wyprowadzić wiele dokumentów wyszukiwania z jednego obiektu blob platformy Azure.

## <a name="setting-up-csv-indexing"></a>Konfigurowanie indeksowania woluminów CSV
Aby indeksować obiekty blob w formacie CSV, Utwórz lub zaktualizuj definicję indeksatora przy użyciu `delimitedText` trybu analizowania w żądaniu [tworzenia indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer) :

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders`wskazuje, że pierwsza (niepusta) linia każdego obiektu BLOB zawiera nagłówki.
Jeśli obiekty blob nie zawierają początkowego wiersza nagłówka, nagłówki należy określić w konfiguracji indeksatora: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Można dostosować znak ogranicznika przy użyciu `delimitedTextDelimiter` ustawienia konfiguracji. Na przykład:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Obecnie obsługiwane jest tylko kodowanie UTF-8. Jeśli potrzebujesz pomocy technicznej dotyczącej innych kodowań, zagłosuj na nią w usłudze [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> W przypadku korzystania z rozdzielonego trybu analizowania tekstu Azure Search zakłada, że wszystkie obiekty blob w źródle danych będą CSV. Jeśli potrzebujesz obsługi kombinacji obiektów BLOB w formacie CSV i innych niż woluminy woluminów CSV w tym samym źródle danych, zapoznaj się z nim w usłudze [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Przykłady żądań
Wszystkie te przykłady są kompletne. 

DataSource 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indeksatora

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Pomóż nam w ulepszaniu Azure Search
Jeśli masz żądania funkcji lub pomysły dotyczące ulepszeń, podaj dane wejściowe w usłudze [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).


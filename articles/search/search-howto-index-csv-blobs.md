---
title: Indeksowanie obiektów BLOB CSV za pomocą indeksatora usługi Azure Wyszukiwanie poznawcze BLOB
titleSuffix: Azure Cognitive Search
description: Przeszukiwanie obiektów BLOB CSV w usłudze Azure Blob Storage na potrzeby wyszukiwania pełnotekstowego przy użyciu indeksu Wyszukiwanie poznawcze platformy Azure. Indeksatory automatyzują pozyskiwanie danych dla wybranych źródeł danych, takich jak Azure Blob Storage.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 18d0eb704deba80bf83b5cae0a598f47181700f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793777"
---
# <a name="how-to-index-csv-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Jak indeksować obiekty blob w formacie CSV za pomocą indeksatora obiektów BLOB na platformie Azure Wyszukiwanie poznawcze 

> [!Note]
> Tryb analizy delimitedText jest w wersji zapoznawczej i nie jest przeznaczony do użycia w środowisku produkcyjnym. [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. W tej chwili nie ma obsługi zestawu SDK platformy .NET.
>

Domyślnie [indeksator usługi Azure wyszukiwanie poznawcze BLOB](search-howto-indexing-azure-blob-storage.md) analizuje rozdzielony tekst obiektów BLOB jako pojedynczy fragment tekstu. Jednak z obiektami BLOB zawierającymi dane CSV często warto traktować każdy wiersz w obiekcie BLOB jako oddzielny dokument. Na przykład, uwzględniając następujący rozdzielony tekst, możesz chcieć przeanalizować go w dwóch dokumentach, z których każdy zawiera pola "ID", "datePublished" i "Tags": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

W tym artykule dowiesz się, jak analizować obiekty blob w formacie CSV za pomocą usługi Azure Wyszukiwanie poznawcze BLOB indexerby ustawienie trybu analizy `delimitedText`. 

> [!NOTE]
> Postępuj zgodnie z zaleceniami dotyczącymi konfiguracji indeksatora w [indeksie jeden-do-wielu](search-howto-index-one-to-many-blobs.md) , aby wyprowadzić wiele dokumentów wyszukiwania z jednego obiektu blob platformy Azure.

## <a name="setting-up-csv-indexing"></a>Konfigurowanie indeksowania woluminów CSV
Aby zindeksować obiekty blob w formacie CSV, Utwórz lub zaktualizuj definicję indeksatora z trybem analizy `delimitedText` na żądanie [utworzenia indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer) :

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` wskazuje, że pierwszy (niepusty) wiersz każdego obiektu BLOB zawiera nagłówki.
Jeśli obiekty blob nie zawierają początkowego wiersza nagłówka, nagłówki należy określić w konfiguracji indeksatora: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Można dostosować znak ogranicznika przy użyciu ustawienia konfiguracji `delimitedTextDelimiter`. Na przykład:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Obecnie obsługiwane jest tylko kodowanie UTF-8. Jeśli potrzebujesz pomocy technicznej dotyczącej innych kodowań, zagłosuj na nią w usłudze [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> W przypadku korzystania z rozdzielonego trybu analizowania tekstu usługa Azure Wyszukiwanie poznawcze zakłada, że wszystkie obiekty blob w źródle danych będą CSV. Jeśli potrzebujesz obsługi kombinacji obiektów BLOB w formacie CSV i innych niż woluminy woluminów CSV w tym samym źródle danych, zapoznaj się z nim w usłudze [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
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

## <a name="help-us-make-azure-cognitive-search-better"></a>Pomóż nam ulepszyć platformę Azure Wyszukiwanie poznawcze
Jeśli masz żądania funkcji lub pomysły dotyczące ulepszeń, podaj dane wejściowe w usłudze [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).


---
title: Wyszukiwanie obiektów BLOB w formacie CSV
titleSuffix: Azure Cognitive Search
description: Wyodrębnij i zaimportuj plik CSV z usługi Azure Blob Storage za pomocą trybu analizy delimitedText, który jest obecnie w publicznej wersji zapoznawczej.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2166e100f03f21c218618d19dc37ee70c6ab29ef
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113029"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Jak indeksować obiekty blob w formacie CSV przy użyciu trybu analizowania delimitedText i indeksatorów obiektów BLOB na platformie Azure Wyszukiwanie poznawcze 

> [!IMPORTANT] 
> Tryb analizy delimitedText jest obecnie w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. Obecnie nie ma obsługi portalu lub zestawu SDK platformy .NET.

Domyślnie [indeksator usługi Azure wyszukiwanie poznawcze BLOB](search-howto-indexing-azure-blob-storage.md) analizuje rozdzielony tekst obiektów BLOB jako pojedynczy fragment tekstu. Jednak z obiektami BLOB zawierającymi dane CSV często warto traktować każdy wiersz w obiekcie BLOB jako oddzielny dokument. Na przykład, uwzględniając następujący rozdzielony tekst, możesz chcieć przeanalizować go w dwóch dokumentach, z których każdy zawiera pola "ID", "datePublished" i "Tags": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

W tym artykule dowiesz się, jak analizować obiekty blob w formacie CSV za pomocą indeksatora usługi Azure Wyszukiwanie poznawcze BLOB, ustawiając tryb analizy `delimitedText`. 

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


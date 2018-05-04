---
title: Indeksowanie obiektów blob CSV z indeksatora obiektów blob Azure Search | Dokumentacja firmy Microsoft
description: Dowiedz się, jak indeksowanie obiektów blob CSV z usługi Azure Search
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 12/28/2017
ms.author: eugenesh
ms.openlocfilehash: dfb1bd48a47e45363e8761a3d79901e5171b37d1
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indeksowanie obiektów blob CSV z indeksatora obiektów blob Azure Search
Domyślnie [indeksatora obiektów blob Azure Search](search-howto-indexing-azure-blob-storage.md) analizuje rozdzielany tekst obiekty BLOB jako pojedynczy fragmentów tekstu. Jednak z obiektami blob zawierający dane w formacie CSV, często zachodzi potrzeba Traktuj każdego wiersza w obiekcie blob jako osobny dokument. Na przykład, dla danego tekstu rozdzielanego następujące: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

należy przeanalizować go do 2 dokumentów zawierających każdego pola "tagi", "datePublished" i "id".

W tym artykule dowiesz się, jak analizować obiektów blob CSV z indeksatora obiektów blob Azure Search. 

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Jest on dostępny tylko w wersji interfejsu API REST **2015-02-28-Preview**. Należy pamiętać, Podgląd interfejsy API są przeznaczone do testowania i ocenie, a nie powinna być używana w środowisku produkcyjnym. 
> 
> 

## <a name="setting-up-csv-indexing"></a>Konfigurowanie indeksowania CSV
Do indeksu CSV obiektów blob, Utwórz lub zaktualizuj definicję indeksatora `delimitedText` podczas analizowania trybu:  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Aby uzyskać więcej szczegółów na tworzenie interfejsu API indeksatora, zapoznaj się [Utwórz indeksator](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

`firstLineContainsHeaders` Wskazuje, że w pierwszym wierszu (niepustych) każdy obiekt blob zawiera nagłówki.
Jeśli obiekty BLOB nie zawierają wiersz nagłówka początkowej, nagłówki powinny określony w konfiguracji indeksatora: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Można dostosować przy użyciu znaku ogranicznika `delimitedTextDelimiter` ustawienia konfiguracji. Na przykład:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Aktualnie obsługiwana jest tylko kodowania UTF-8. Jeśli potrzebujesz pomocy technicznej dla innych kodowań, prosimy o kontakt na [witryny UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Gdy używasz tekstu rozdzielanego podczas analizowania trybu usługi Azure Search zakłada, że wszystkie obiekty BLOB w źródle danych będą CSV. Jeśli potrzebujesz do obsługi różnych CSV i CSV z systemem innym niż obiekty BLOB w tym samym źródle danych, prosimy o kontakt na [witryny UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Przykładowe żądanie
Umieszczanie to wszystkie ze sobą, poniżej przedstawiono przykłady pełną ładunku. 

Źródło danych: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indeksator:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Pomóż nam udoskonalić usługę Azure Search
Jeśli masz żądania funkcji lub pomysły dotyczące ulepszenia należy dotrzeć do nas w naszej [witryny UserVoice](https://feedback.azure.com/forums/263029-azure-search/).


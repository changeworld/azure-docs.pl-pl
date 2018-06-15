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
ms.openlocfilehash: bf65ab7858ba792418e325e7a025ee1bd88bbb27
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363040"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indeksowanie obiektów blob CSV z indeksatora obiektów blob Azure Search
Domyślnie [indeksatora obiektów blob Azure Search](search-howto-indexing-azure-blob-storage.md) analizuje rozdzielany tekst obiekty BLOB jako pojedynczy fragmentów tekstu. Jednak z obiektami blob zawierający dane w formacie CSV, często zachodzi potrzeba Traktuj każdego wiersza w obiekcie blob jako osobny dokument. Na przykład, biorąc pod uwagę następujące tekstu rozdzielanego, można przeanalizować go na dwa dokumenty zawierające każdego pola "tagi", "datePublished" i "id": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

W tym artykule dowiesz się, jak analizować obiektów blob CSV z indeksatora obiektów blob Azure Search. 

> [!IMPORTANT]
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej i nie powinna być używana w środowisku produkcyjnym. Aby uzyskać więcej informacji, zobacz [REST api-version = 2017-11-11-Preview](search-api-2017-11-11-preview.md). 
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
> Aktualnie obsługiwana jest tylko kodowania UTF-8. Jeśli potrzebujesz pomocy technicznej dla innych kodowań głosowania dla niego w [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Gdy używasz tekstu rozdzielanego podczas analizowania trybu usługi Azure Search zakłada, że wszystkie obiekty BLOB w źródle danych będą CSV. Jeśli potrzebujesz do obsługi różnych CSV i CSV z systemem innym niż obiekty BLOB w tym samym źródle danych, sprawdź głosowania dla niego w [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Przykładowe żądanie
Umieszczanie to wszystkie ze sobą, poniżej przedstawiono przykłady pełną ładunku. 

Źródło danych: 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indeksator:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Pomóż nam udoskonalić usługę Azure Search
Jeśli masz żądania funkcji lub pomysły dotyczące ulepszeń, podaj dane wejściowe na [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).


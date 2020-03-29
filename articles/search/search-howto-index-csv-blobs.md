---
title: Wyszukiwanie obiektów BLOB
titleSuffix: Azure Cognitive Search
description: Wyodrębnij i zaimportuj csv z magazynu obiektów blob platformy Azure przy użyciu trybu analizowania delimitedText.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bf600890bfed570e712a159005b8ef5267298cc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122325"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Jak indeksować obiekty BLOB przy użyciu trybu analizowania rozdzielanychtekstowe i indeksatory obiektów blob w usłudze Azure Cognitive Search

Domyślnie [indeksator obiektów blob usługi Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md) analizuje rozdzielone obiekty blob jako pojedynczy fragment tekstu. Jednak w przypadku obiektów blob zawierających dane CSV często należy traktować każdy wiersz w obiekcie blob jako oddzielny dokument. Na przykład, biorąc pod uwagę następujący tekst rozdzielany, można przeanalizować go w dwóch dokumentach, z których każdy zawiera pola "id", "datePublished" i "tagi": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

W tym artykule dowiesz się, jak analizować obiekty BLOB csv za `delimitedText` pomocą indeksatora obiektów blob usługi Azure Cognitive Search, ustawiając tryb analizy. 

> [!NOTE]
> Postępuj zgodnie z zaleceniami konfiguracji indeksatora w [indeksowaniu jeden do wielu,](search-howto-index-one-to-many-blobs.md) aby wyprowadzić wiele dokumentów wyszukiwania z jednego obiektu blob platformy Azure.

## <a name="setting-up-csv-indexing"></a>Konfigurowanie indeksowania CSV
Aby indeksować obiekty BLOB, należy utworzyć lub `delimitedText` zaktualizować definicję indeksatora za pomocą trybu analizy w [żądaniu tworzenia indeksatora:](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders`wskazuje, że pierwszy (niepusty) wiersz każdego obiektu blob zawiera nagłówki.
Jeśli obiekty BLOB nie zawierają początkowego wiersza nagłówka, nagłówki powinny być określone w konfiguracji indeksatora: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Znak ogranicznika można dostosować `delimitedTextDelimiter` za pomocą ustawienia konfiguracji. Przykład:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Obecnie obsługiwane jest tylko kodowanie UTF-8. Jeśli potrzebujesz wsparcia dla innych kodowania, zagłosuj na to na [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Korzystając z trybu analizowania tekstu rozdzielanych usługa Azure Cognitive Search zakłada, że wszystkie obiekty BLOB w źródle danych będą csv. Jeśli chcesz obsługiwać kombinację obiektów BLOB CSV i innych niż CSV w tym samym źródle danych, zagłosuj na nią na [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Przykłady żądania
Łącząc to wszystko, oto pełne przykłady ładunku. 

Datasource: 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indeksatora:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-cognitive-search-better"></a>Pomóż nam poprawić usługę Azure Cognitive Search
Jeśli masz prośby o ulepszenia lub pomysły dotyczące ulepszeń, podaj swoje dane wejściowe w [uservoice](https://feedback.azure.com/forums/263029-azure-search/).


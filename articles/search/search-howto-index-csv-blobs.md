---
title: Indeksowanie obiektów blob CSV za pomocą indeksatora obiektów Blob platformy Azure Search — usługa Azure Search
description: Można przeszukiwać obiektów blob CSV w usłudze Azure Blob storage w celu wyszukiwania pełnotekstowego przy użyciu indeksu usługi Azure Search. Indeksatory zautomatyzować pozyskiwanie danych dla wybranych źródeł danych takich jak usługi Azure Blob storage.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: e7d959e77d27fb04b18f402e4056d4dea1607039
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522889"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indeksowanie obiektów blob CSV za pomocą indeksatora obiektów blob usługi Azure Search

> [!Note]
> Tryb analizy delimitedText znajduje się w wersji zapoznawczej i nie przeznaczonych do użycia w środowisku produkcyjnym. [Wersji interfejsu API REST 2019-05-06-Preview](search-api-preview.md) zapewnia tę funkcję. Brak obsługi zestawu SDK platformy .NET w tej chwili.
>

Domyślnie [indeksatora obiektów blob usługi Azure Search](search-howto-indexing-azure-blob-storage.md) analizuje rozdzielany tekst obiektów blob jako jeden fragment tekstu. Jednak za pomocą obiektów blob zawierający dane w formacie CSV, często zachodzi potrzeba traktują każdy wiersz w obiekcie blob jako oddzielny dokument. Na przykład, biorąc pod uwagę następujący tekst rozdzielany, warto go przeanalizować pod dwa dokumenty, zawierających każdego pola "tags", "datePublished" i "id": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

W tym artykule nauczysz przeanalizować obiektów blob CSV za pomocą ustawień indexerby obiektów blob usługi Azure Search `delimitedText` tryb analizy. 

> [!NOTE]
> Postępuj zgodnie z zaleceniami konfiguracji indeksatora w [indeksowania jeden do wielu](search-howto-index-one-to-many-blobs.md) służący do wypełniania wyjściowego wiele dokumentów wyszukiwania z jednego obiektu blob platformy Azure.

## <a name="setting-up-csv-indexing"></a>Konfigurowanie indeksowania CSV
Aby indeksowanie obiektów blob CSV, Utwórz lub zaktualizuj definicję indeksatora z `delimitedText` tryb analizy na [tworzenie indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer) żądania:

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` Wskazuje, że pierwszy wiersz (niepustych) każdy obiekt blob zawiera nagłówki.
Jeśli obiekty BLOB nie zawierają wiersz nagłówka początkowej, należy określić nagłówków w konfiguracji indeksatora: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Można dostosować przy użyciu znaku ogranicznika `delimitedTextDelimiter` ustawienia konfiguracji. Na przykład:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Aktualnie obsługiwana jest tylko kodowanie UTF-8. Jeśli potrzebujesz pomocy technicznej dla innego kodowania głosować na na [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Korzystając z tekstu rozdzielanego tryb analizy usługi Azure Search przyjęto założenie, że wszystkie obiekty BLOB ze źródła danych będą CSV. Jeśli potrzebujesz do obsługi różnych obiektów blob CSV i niebędący woluminem CSV, w tym samym źródle danych, można głosować na na [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Przykładowe żądanie
Umieszczenie to wszystkie ze sobą, poniżej przedstawiono przykłady pełny ładunek. 

Źródło danych: 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indeksator:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Pomóż nam ulepszyć usługę Azure Search
Jeśli masz sugestie funkcji lub pomysły dotyczące ulepszeń, podaj dane wejściowe na [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).


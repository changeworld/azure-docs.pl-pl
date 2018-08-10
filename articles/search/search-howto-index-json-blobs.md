---
title: Indeksowanie obiektów blob JSON za pomocą indeksatora obiektów blob usługi Azure Search
description: Indeksowanie obiektów blob JSON za pomocą indeksatora obiektów blob usługi Azure Search
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
ms.openlocfilehash: 5b4cd1c592c4cd965a0b5d9e4fb8eef84a6bea91
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003285"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indeksowanie obiektów blob JSON za pomocą indeksatora obiektów blob usługi Azure Search
W tym artykule pokazano, jak skonfigurować indeksator obiektów blob usługi Azure Search można wyodrębnić zawartości ze strukturą z obiektów blob JSON w usłudze Azure Blob storage.

Obiektów blob JSON w usłudze Azure Blob storage są zazwyczaj pojedynczego dokumentu JSON lub tablicę JSON. Indeksowanie obiektów blob w usłudze Azure Search może przeanalizować albo konstrukcji, w zależności od tego, jak ustawić **parsingMode** parametru w żądaniu.

| Dokument JSON | parsingMode | Opis | Dostępność |
|--------------|-------------|--------------|--------------|
| Jeden na obiekt blob | `json` | Analizuje obiektów blob JSON jako jeden fragment tekstu. Każdy obiekt blob JSON staje się pojedynczym dokumencie usługi Azure Search. | Ogólnie dostępna w obu [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) i [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) interfejsów API. |
| Wiele na obiekt blob | `jsonArray` | Analizuje tablicę JSON w obiekcie blob, w którym każdy element tablicy staje się oddzielny dokument usługi Azure Search.  | W wersji zapoznawczej w [REST api-version =`2017-11-11-Preview` ](search-api-2017-11-11-preview.md) i [.NET SDK w wersji zapoznawczej](https://aka.ms/search-sdk-preview). |

> [!Note]
> Interfejsy API (wersja zapoznawcza) są przeznaczone do testowania i oceny, a nie powinny być używane w środowiskach produkcyjnych.
>

## <a name="setting-up-json-indexing"></a>Konfigurowanie indeksowania JSON
Indeksowanie obiektów blob JSON jest podobny do wyodrębniania zwykłego dokumentu w przepływie pracy trzyczęściowej wspólne dla wszystkich indeksatorów w usłudze Azure Search.

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych

Pierwszym krokiem jest ustanowienie połączenia informacje o źródle danych używane przez indeksator. Typ, określone źródła danych tutaj jako `azureblob`, określa, które zachowania wyodrębniania danych są wywoływane przez indeksator. Indeksowanie obiektów blob JSON, aby uzyskać definicję źródła danych jest taka sama, zarówno dla dokumentów JSON i tablic. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>Krok 2: Tworzenie docelowym indeksem wyszukiwania 

Indeksatory są skojarzone ze schematem indeksu. Korzystając z interfejsu API (zamiast portalu), przygotuj indeksu z wyprzedzeniem, aby można je określić na indeksatora. 

> [!Note]
> Indeksatory są widoczne w portalu za pomocą **importu** akcji dla ograniczonej liczby ogólnie dostępnych indeksatorów. Często Importowanie przepływu pracy, często można skonstruować wstępny indeks na podstawie metadanych w źródle. Aby uzyskać więcej informacji, zobacz [importowanie danych do usługi Azure Search w portalu](search-import-data-portal.md).

### <a name="step-3-configure-and-run-the-indexer"></a>Krok 3: Skonfigurować i uruchomić indeksatora

Do tej pory definicje dla źródła danych i indeksu zostały parsingMode niezależny od. Jednak w kroku 3 dla konfiguracji indeksatora, ścieżka diverges w zależności od sposobu obiektu blob JSON zawartości przeanalizowano i ze strukturą w indeksie usługi Azure Search.

Podczas wywoływania indeksatora, wykonaj następujące czynności:

+ Ustaw **parsingMode** parametr `json` (do indeksowania każdy obiekt blob jako pojedynczy dokument) lub `jsonArray` (jeżeli obiektów blob zawierają tablice notacji JSON i potrzebny każdego elementu tablicy, powinien być traktowany jako oddzielny dokument).

+ Opcjonalnie użyj **mapowania pól** wyboru właściwości dokumentu JSON źródła, które są używane do wypełnienia z docelowym indeksem wyszukiwania. Dla tablic JSON Jeśli tablica istnieje jako właściwością niższa poziomu można ustawić katalog główny dokumentów wskazująca, gdzie tablicy jest umieszczany w ramach obiektu blob.

> [!IMPORTANT]
> Kiedy używasz `json` lub `jsonArray` tryb analizy, usługa Azure Search przyjęto założenie, że wszystkie obiekty BLOB ze źródła danych zawierają JSON. Daj nam znać, jeśli potrzebujesz do obsługi różnych obiektów blob JSON i -JSON, w tym samym źródle danych, na [naszą witrynę UserVoice](https://feedback.azure.com/forums/263029-azure-search).


## <a name="how-to-parse-single-json-blobs"></a>Jak analizować pojedynczego obiektów blob JSON

Domyślnie [indeksatora obiektów blob usługi Azure Search](search-howto-indexing-azure-blob-storage.md) analizuje obiektów blob JSON jako jeden fragment tekstu. Często chcesz zachować strukturę dokumentów JSON. Załóżmy na przykład, że masz następujący dokument JSON w usłudze Azure Blob storage:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>Definicja indeksatora dla pojedynczego obiektów blob JSON

Za pomocą indeksatora obiektów blob usługi Azure Search, podobny do poprzedniego przykładu dokument JSON jest analizowany w jednym dokumencie usługi Azure Search. Indeksator ładuje indeks, dopasowując "text", "datePublished" i "tags" ze źródła względem pola identycznie nazwane i wpisane docelowej.

Konfiguracja jest dostarczana w treści indeksatora. Odwołania, który wcześniej zdefiniowany obiekt źródła danych określa typ i połączenia informacje o źródle danych. Ponadto indeksu docelowego musi również istnieć pustego kontenera w usłudze. Harmonogram i parametry są opcjonalne, ale jeśli je pominiesz, indeksator uruchamia natychmiast, za pomocą `json` jako tryb analizy.

W pełni określonego żądania może wyglądać w następujący sposób:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Jak wspomniano, mapowań pól nie są wymagane. Podany indeks z "text", "datePublished i"tags"pola, obiekt blob indeksator może wywnioskować poprawne mapowania bez pola mapowania znajduje się w żądaniu.

## <a name="how-to-parse-json-arrays-preview"></a>Jak analizować tablice notacji JSON (wersja zapoznawcza)

Alternatywnie możesz zdecydować się na funkcję w wersji zapoznawczej tablicy JSON. Ta możliwość jest przydatna, gdy zawierają obiekty BLOB *Tablica obiektów JSON*, i chcesz, aby każdy element, aby stać się oddzielny dokument usługi Azure Search. Na przykład biorąc pod uwagę następujący obiekt blob JSON, które możesz wypełnić indeksu usługi Azure Search przy użyciu trzech oddzielnych dokumentów, każdy z pola "id" i "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>Definicja indeksatora dla tablicy JSON

Dla tablicy JSON, żądanie indexer używa interfejsu API w wersji zapoznawczej i `jsonArray` analizatora. Są to tylko dwa wymagania specyficzne dla tablicy indeksowanie obiektów blob JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Ponownie Zwróć uwagę, że mapowań pól nie są wymagane. Podany indeks z polami "id" i "text", indeksatora obiektów blob można wywnioskować poprawne mapowania bez listy mapowania pól.

<a name="nested-json-arrays"></a>

### <a name="nested-json-arrays"></a>Zagnieżdżone tablice notacji JSON
Co zrobić, jeśli chcesz indeksu tablicy obiektów JSON, ale ta tablica jest zagnieżdżona gdzieś w obrębie dokumentu? Możesz wybrać właściwość, która zawiera tablicę przy użyciu `documentRoot` właściwość konfiguracji. Na przykład, jeśli obiekty BLOB wyglądać następująco:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Indeks tablicy zawarte w przy użyciu tej konfiguracji `level2` właściwości:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="using-field-mappings-to-build-search-documents"></a>Tworzenie dokumentów wyszukiwania przy użyciu mapowań pól

Gdy pola źródłowe i docelowe nie są dokładnie wyrównana, można zdefiniować sekcję mapowania pola w treści żądania dla jawnego skojarzeń pola do pola.

Obecnie usługa Azure Search nie może indeksować dowolnych dokumentów JSON bezpośrednio, ponieważ obsługuje on typy danych tylko typy pierwotne, tablice parametrów i GeoJSON punktów. Można jednak użyć **mapowania pól** wybrać części dokumentu JSON, a "lift" je do pól najwyższego poziomu dokumentu wyszukiwania. Aby poznać podstawy mapowania pól, zobacz temat [mapowania pól w indeksatorach usługi Azure Search](search-indexer-field-mappings.md).

Ponowne spojrzenie na dokument JSON w naszym przykładzie:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Przyjęto założenie, indeks wyszukiwania przy użyciu następujących pól: `text` typu `Edm.String`, `date` typu `Edm.DateTimeOffset`, i `tags` typu `Collection(Edm.String)`. Zwróć uwagę rozbieżność między "datePublished" w źródle i `date` pola w indeksie. Aby zamapować JSON do żądanego kształtu, użyj następujące mapowania pola:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Źródło nazwy pól w mapowania są określane za pomocą [wskaźnik JSON](http://tools.ietf.org/html/rfc6901) notacji. Możesz zaczynać się ukośnikiem do odwoływania się do katalogu głównego dokumentu JSON, a następnie wybierz żądaną właściwość (na dowolny poziom zagnieżdżenia) przy użyciu ścieżki oddzielone ukośnikiem do przodu.

Może również odnosić się do elementów tablicy poszczególnych przy użyciu indeksu zaczynającego się od zera. Na przykład wybierz pierwszy element tablicy "tags" z powyższego przykładu, należy użyć mapowania pól w następujący sposób:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Jeśli nazwa pola źródła, w ścieżce mapowania pola odwołuje się do właściwości, która nie istnieje w formacie JSON, mapowanie jest pomijane bez błędów. Można to zrobić, aby firma Microsoft może obsługiwać dokumenty z innym schematem (jest to typowy przypadek użycia). Ponieważ nie ma możliwości weryfikacji, należy uważać, aby uniknąć błędów w specyfikacji mapowania pól.
>
>

## <a name="example-indexer-request-with-field-mappings"></a>Przykład: Indeksator żądania przy użyciu mapowań pól

Poniższy przykład to ładunek pełni określonego indeksatora, łącznie z mapowania pól:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


## <a name="help-us-make-azure-search-better"></a>Pomóż nam ulepszyć usługę Azure Search
Jeśli masz sugestie funkcji lub pomysły dotyczące poprawy się z nami na naszych [witryny UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="see-also"></a>Zobacz także

+ [Indeksatory w usłudze Azure Search](search-indexer-overview.md)
+ [Indeksowanie usługi Azure Blob Storage z usługą Azure Search](search-howto-index-json-blobs.md)
+ [Indeksowanie obiektów blob CSV za pomocą indeksatora obiektów blob usługi Azure Search](search-howto-index-csv-blobs.md)
+ [Samouczek: Przeszukiwanie częściowo ustrukturyzowanych danych z magazynu obiektów Blob platformy Azure ](search-semi-structured-data.md)

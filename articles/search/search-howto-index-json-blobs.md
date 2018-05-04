---
title: Indeksowanie obiektów blob JSON z indeksatora obiektów blob Azure Search
description: Indeksowanie obiektów blob JSON z indeksatora obiektów blob Azure Search
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
ms.openlocfilehash: 64d16182ce1992ec312ad1620d9d5cf11e0ddea8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indeksowanie obiektów blob JSON z indeksatora obiektów blob Azure Search
W tym artykule przedstawiono sposób skonfigurować indeksator usługi Azure Search obiektu blob do wyodrębniania zawartości strukturalnych z obiektów blob JSON w magazynie obiektów Blob Azure.

Obiekty BLOB JSON w magazynie obiektów Blob platformy Azure są zazwyczaj pojedynczego dokumentu JSON lub tablicy JSON. Indeksator obiektów blob w usłudze Azure Search można analizować albo konstrukcji, w zależności od tego, jak ustawić **parsingMode** parametru w żądaniu.

| Dokument JSON | parsingMode | Opis | Dostępność |
|--------------|-------------|--------------|--------------|
| Po jednym dla każdego obiektu blob | `json` | Analizuje obiekty BLOB JSON jako pojedynczy fragmentów tekstu. Każdy obiekt blob JSON staje się pojedynczego dokumentu usługi Azure Search. | Ogólnie dostępna zarówno w [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) i [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) interfejsów API. |
| Wiele poszczególnych obiektów blob | `jsonArray` | Analizuje tablica JSON w obiekcie blob, w którym każdy element tablicy staje się oddzielny dokument usługi Azure Search.  | W wersji zapoznawczej w [REST api-version =`2016-09-01-Preview` ](search-api-2016-09-01-preview.md) i [.NET SDK Preview](https://aka.ms/search-sdk-preview). |

> [!Note]
> Interfejsy API w wersji zapoznawczej są przeznaczone do testowania i ocenie, a nie powinna być używana w środowisku produkcyjnym.
>

## <a name="setting-up-json-indexing"></a>Konfigurowanie indeksowania JSON
Indeksowanie obiektów blob JSON jest podobny do wyodrębniania zwykłego dokumentu w typowych trzech części przepływu pracy do wszystkich indeksatorów w usłudze Azure Search.

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych

Pierwszym krokiem jest ustanowienie połączenia informacje o źródle danych używane przez indeksatora. Typ określony źródła danych jako `azureblob`, określa, które zachowania wyodrębniania danych są wywoływane przez indeksatora. Dla obiektu blob JSON indeksowania źródło danych jest definicja jest taka sama dla dokumentów JSON i tablic. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>Krok 2: Tworzenie indeksu wyszukiwania docelowego 

Indeksatory są skojarzone z schematu indeksu. Jeśli korzystasz z interfejsu API (zamiast portalu), przygotuj indeksu z wyprzedzeniem, aby je określić w indeksatora. 

> [!Note]
> Indeksatory są widoczne w portalu za pośrednictwem **importu** akcji dla ograniczonej liczby indeksatory ogólnie dostępna. Często przepływu pracy importowania często utworzyć indeks wstępne na podstawie metadanych w źródle. Aby uzyskać więcej informacji, zobacz [importowania danych do usługi Azure Search w portalu](search-import-data-portal.md).

### <a name="step-3-configure-and-run-the-indexer"></a>Krok 3: Konfigurowanie i uruchomić indeksatora

Do tej pory definicje dla źródła danych i indeks zostały niezależny od parsingMode. Jednak w kroku 3 dla konfiguracji indeksatora, ścieżka diverges w zależności od tego, jaki ma obiektu blob JSON zawartości przeanalizowano i struktura indeksu usługi Azure Search.

Podczas wywoływania metody indeksatora, wykonaj następujące czynności:

+ Ustaw **parsingMode** parametr `json` (do indeksowania każdy obiekt blob jako pojedynczego dokumentu) lub `jsonArray` (jeśli obiektów blob zawiera tablice notacji JSON, należy każdy element tablicy powinien być traktowany jako osobny dokument).

+ Można też użyć **mapowań pól** wybrać właściwości dokumentu JSON źródła, które są używane do wypełnienia indeksu wyszukiwania docelowej. Dla tablic JSON Jeśli istnieje tablicy jako właściwość niższy poziom, można ustawić wskazujący rozmieszczenia tablicy w obiekcie blob głównego dokumentu.

> [!IMPORTANT]
> Jeśli używasz `json` lub `jsonArray` analizy tryb usługi Azure Search przyjęto założenie, że wszystkie obiekty BLOB w źródle danych zawierają JSON. Daj nam znać, jeśli zachodzi potrzeba obsługi różnych obiektów blob JSON i z systemem innym niż JSON w tym samym źródle danych, na [witryny UserVoice](https://feedback.azure.com/forums/263029-azure-search).


## <a name="how-to-parse-single-json-blobs"></a>Jak analizować pojedynczego blob JSON

Domyślnie [indeksatora obiektów blob Azure Search](search-howto-indexing-azure-blob-storage.md) analizuje obiekty BLOB JSON jako pojedynczy fragmentów tekstu. Często chcesz zachować struktury dokumentów JSON. Na przykład założono, że ten dokument JSON w magazynie obiektów Blob platformy Azure:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>Indeksator definicji dla pojedynczego blob JSON

Przy użyciu indeksatora obiektów blob Azure Search, podobnie jak w poprzednim przykładzie dokumentu JSON jest analizowana w jednym dokumencie usługi Azure Search. Indeksatora ładuje indeks, dopasowując "text", "datePublished" i "tagów" ze źródła względem pola tak samo nazwane i typu docelowego.

Konfiguracja jest dostarczana w treści indeksatora. Odwołaj się, że obiekt źródła danych, w uprzednio zdefiniowany określa informacje typu i połączenia źródła danych. Ponadto indeksu docelowego musi również istnieć pusty kontener w usłudze. Harmonogram i parametry są opcjonalne, ale w przypadku pominięcia ich uruchomieniu indeksatora natychmiast, za pomocą `json` jako tryb przetwarzania.

Pełni określone żądanie może wyglądać następująco:

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

Jak wspomniano, mapowań pól nie są wymagane. Podany indeks z "text", "datePublished i"tagów"pól obiektu blob indeksatora może wnioskować poprawne mapowania bez pola mapowania znajduje się w żądaniu.

## <a name="how-to-parse-json-arrays-preview"></a>Jak analizować tablice notacji JSON (wersja zapoznawcza)

Alternatywnie można włączyć dla funkcji w wersji zapoznawczej tablicy JSON. Ta funkcja jest przydatna, gdy zawiera obiekty BLOB *Tablica obiektów JSON*, i chcesz, aby każdy element, aby stać się oddzielny dokument usługi Azure Search. Na przykład biorąc pod uwagę następujące obiektu blob JSON, można go wypełnić indeks usługi Azure Search, z trzech oddzielnych dokumentów, każda z pola "id" i "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>Definicja indeksatora tablicy JSON

Dla tablicy JSON, żądanie indeksatora używa interfejsu API w wersji zapoznawczej i `jsonArray` analizatora. Są to tylko dwa tablicy specyficzne wymagania dotyczące indeksowanie obiektów blob JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Ponownie Zwróć uwagę, że mapowań pól nie są wymagane. Podany indeks z pola "id" i "tekst", indeksatora obiektu blob można wnioskować o poprawne mapowania bez mapowania pola listy.

### <a name="nested-json-arrays"></a>Tablice zagnieżdżone JSON
Co zrobić, jeśli chcesz indeksu jest Tablica obiektów JSON, ale tablicy jest zagnieżdżony gdzieś w dokumencie? Można wybrać, które właściwości zawiera przy użyciu tablicy `documentRoot` właściwości konfiguracji. Jeśli na przykład obiektów blob wyglądać następująco:

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

Podczas pola źródłowego i docelowego nie są wyrównane doskonale, można zdefiniować sekcję mapowania pola w treści żądania jawne skojarzenia do pola.

Obecnie usługi Azure Search nie można indeksować dowolnych dokumentów JSON bezpośrednio, ponieważ obsługuje on typów danych tylko typy pierwotne, tablic ciągów i punkty GeoJSON. Można jednak użyć **mapowań pól** wybrać części dokumentu JSON, a "Podnieś" je do pól najwyższego poziomu dokumentu wyszukiwania. Aby poznać podstawy mapowania pól, zobacz temat [pola mapowania w indeksatory usługi Azure Search](search-indexer-field-mappings.md).

Ponowne odwiedzanie dokumentu JSON naszym przykładzie:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Załóżmy indeksem wyszukiwania z następującymi polami: `text` typu `Edm.String`, `date` typu `Edm.DateTimeOffset`, i `tags` typu `Collection(Edm.String)`. Zwróć uwagę rozbieżność między "datePublished" w źródle i `date` pola w indeksie. Do mapowania użytkownika JSON do żądanego kształtu, użyj następującego mapowania pól:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Nazwy pól źródła w mapowania są określane za pomocą [wskaźnika JSON](http://tools.ietf.org/html/rfc6901) notacji. Można rozpoczynać się kreską ukośną do odwoływania się do katalogu głównego dokumentu JSON, a następnie wybierz odpowiednie właściwości (na poziomie dowolnego zagnieżdżenia) przy użyciu ścieżki rozdzielonych ukośnikiem do przodu.

Ponadto mogą odwoływać się do poszczególnych elementów przy użyciu liczony od zera indeks. Na przykład wybierz pierwszy element tablicy "tagi" w powyższym przykładzie, należy użyć mapowania pól, jak to:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Jeśli nazwa pola źródła w ścieżce mapowania pola odwołuje się do właściwości, która nie istnieje w formacie JSON, że to mapowanie zostanie pominięty bez błędu. Można to zrobić, aby firma Microsoft obsługuje dokumenty z innym schematem (co jest typowe przypadek użycia). Ponieważ nie ma żadnych weryfikacji, należy zwrócić uwagę, aby uniknąć błędów pisowni w specyfikacji mapowania pól.
>
>

## <a name="example-indexer-request-with-field-mappings"></a>Przykład: Indeksator żądania z mapowania pól

Poniższy przykład jest ładunku pełni określonego indeksatora, łącznie z mapowania pól:

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


## <a name="help-us-make-azure-search-better"></a>Pomóż nam udoskonalić usługę Azure Search
Jeśli masz żądania funkcji lub pomysły dotyczące ulepszeń dotrzeć do nas w naszej [witryny UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="see-also"></a>Zobacz także

+ [Indeksatory w usłudze Azure Search](search-indexer-overview.md)
+ [Indeksowanie magazynu obiektów Blob Azure o usłudze Azure Search](search-howto-index-json-blobs.md)
+ [Indeksowanie obiektów blob CSV z indeksatora obiektów blob Azure Search](search-howto-index-csv-blobs.md)
+ [Samouczek: Wyszukiwanie częściowo ustrukturyzowanych danych z magazynu obiektów Blob platformy Azure ](search-semi-structured-data.md)
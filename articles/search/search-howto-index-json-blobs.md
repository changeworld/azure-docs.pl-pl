---
title: Indeksowanie obiektów blob JSON z indeksatora obiektów Blob platformy Azure w celu wyszukiwania pełnotekstowego — usługa Azure Search
description: Można przeszukiwać JSON usługi Azure blob dla zawartości tekstu za pomocą indeksatora obiektów Blob platformy Azure Search. Indeksatory zautomatyzować pozyskiwanie danych dla wybranych źródeł danych takich jak usługi Azure Blob storage.
ms.date: 12/21/2018
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 6df8d9a5c1ca1e587834ea08f73b3dd9498f8537
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753153"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indeksowanie obiektów blob JSON za pomocą indeksatora obiektów Blob platformy Azure Search
W tym artykule pokazano, jak skonfigurować indeksator obiektów blob usługi Azure Search można wyodrębnić zawartości ze strukturą z obiektów blob JSON w usłudze Azure Blob storage.

Możesz użyć [portal](#json-indexer-portal), [interfejsów API REST](#json-indexer-rest), lub [zestawu .NET SDK](#json-indexer-dotnet) do indeksu zawartość JSON. Wspólne wszystkich metod to dokumenty JSON znajduje się w kontenerze obiektów blob na koncie usługi Azure Storage. Aby uzyskać wskazówki dotyczące wypychania dokumentów JSON z innych platform spoza platformy Azure, zobacz [importowanie danych w usłudze Azure Search](search-what-is-data-import.md).

Obiektów blob JSON w usłudze Azure Blob storage są zazwyczaj pojedynczego dokumentu JSON lub tablicę JSON. Indeksowanie obiektów blob w usłudze Azure Search może przeanalizować albo konstrukcji, w zależności od tego, jak ustawić **parsingMode** parametru w żądaniu.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Używanie portalu

Najprostsza metoda dla indeksować dokumenty JSON jest użycie kreatora w [witryny Azure portal](https://portal.azure.com/). Analizując metadanych w kontenerze obiektów blob platformy Azure, [ **importowania danych** ](search-import-data-portal.md) kreatora można utworzyć indeks domyślny mapują pola źródłowe na pola indeksu docelowego, a ładowanie indeksu w ramach jednej operacji. W zależności od rozmiaru i stopnia złożoności danych źródła może mieć indeks wyszukiwania operacyjnej pełny tekst w ciągu kilku minut.

### <a name="1---prepare-source-data"></a>1 — Przygotowanie danych źródłowych

Należy mieć konto magazynu platformy Azure przy użyciu usługi Blob storage i kontenerem dokumentów JSON. Jeśli nie jesteś zaznajomiony z dowolnym z tych zadań, zapoznaj się z wstępnie wymaganego składnika "Konfigurowanie obiektów Blob platformy Azure usługi i ładowanie danych przykładowych" w [cognitive search-quickstart](cognitive-search-quickstart-blob.md#set-up-azure-blob-service-and-load-sample-data).

### <a name="2---start-import-data-wizard"></a>2 — Uruchom Kreatora importu danych

Możesz [uruchom kreatora](search-import-data-portal.md) na pasku poleceń, na stronie usługi Azure Search lub przez kliknięcie przycisku **Dodaj usługę Azure Search** w **usługi Blob service** sekcji konta magazynu w okienku nawigacji po lewej stronie.

### <a name="3---set-the-data-source"></a>3 — Ustaw źródło danych

W **źródła danych** stronie źródłowy musi być **usługi Azure Blob Storage**, z następującymi specyfikacjami:

+ **Dane do wyodrębnienia** powinien być *zawartości i metadanych*. Wybranie tej opcji pozwala kreatorowi rozpoznać schemat indeksu i mapowania pól do zaimportowania.
   
+ **Tryb analizy** powinna być równa *JSON* lub *tablicę JSON*. 

  *JSON* articulates każdy obiekt blob jako dokument jednego wyszukiwania, pojawią się jako niezależny element w wynikach wyszukiwania. 

  *Tablica JSON* to dla obiektów blob, który składa się z wielu elementów, w której ma być przegubowe jako autonomiczny, niezależny wyszukiwania dokumentów każdego z elementów. Jeśli obiekty BLOB są złożone i nie klikaj *tablicę JSON* całego obiektu blob są pozyskiwane jako pojedynczego dokumentu.
   
+ **Kontener magazynu** należy określić konto magazynu i kontener lub parametry połączenia, który jest rozpoznawany jako kontenera. Parametry połączenia można uzyskać na stronie portalu usługi obiektów Blob.

   ![Definicję źródła danych obiektu blob](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - Pomiń stronę "Dodaj cognitive search" w Kreatorze

Dodawanie umiejętności poznawcze nie jest konieczne do importowania dokumentu JSON. Jeśli nie ma określonych specjalnych potrzeb [obejmują interfejsy API usług Cognitive Services i przekształcenia](cognitive-search-concept-intro.md) do potoku indeksowania, należy pominąć ten krok.

Aby pominąć ten krok, kliknij przycisk następnej strony **Dostosuj indeks docelowy**.

### <a name="5---set-index-attributes"></a>5 — Ustaw atrybuty indeksu

W **indeksu** stronie powinien zostać wyświetlony listy pól z typem danych i serii pola wyboru do ustawiania atrybuty indeksu. Kreator może wygenerować domyślnego indeksu na podstawie metadanych i przez próbkowanie źródła danych. 

Ustawienia domyślne często tworzyć rozwiązania zwiększą: zaznaczania pola (rzutowany jako ciąg), aby służyć jako klucz lub dokumentów identyfikator do unikatowego identyfikowania każdego dokumentu, a także pola, które dobrze nadają się dla wyszukiwania pełnotekstowego i pobieranie w zestawie wyników. W przypadku obiektów blob `content` pole jest najlepsze Release candidate programu zawartość do przeszukiwania.

Możesz zaakceptować wartości domyślne lub przeczytaj jego opis [atrybutami indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) i [analizatory języka](https://docs.microsoft.com/rest/api/searchservice/language-support) do przesłaniające lub uzupełniające wartości początkowe. 

Poświęć chwilę, aby przejrzeć wybrane opcje. Po uruchomieniu kreatora, struktur danych fizycznych są tworzone i nie będzie można edytować tych pól bez porzucenie i ponowne utworzenie wszystkich obiektów.

   ![Definicja indeksu obiektu blob](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6. Tworzenie indeksatora

W pełni określona, Kreator tworzy trzy różne obiekty w usłudze wyszukiwania. Obiekt źródła danych i indeksu obiektu są zapisywane jako o nazwie zasoby w usłudze Azure Search. Ostatnim krokiem tworzy obiekt indeksatora. Nazewnictwo indeksatora umożliwia mu istnieje jako zasób autonomicznych, której możliwe jest planowanie i zarządzanie nimi niezależnie od obiektu źródłowego indeksu i danych, tworzone po kolei kreatora.

Jeśli nie jesteś zaznajomiony z indeksatorów, *indeksatora* jest zasobem w usłudze Azure Search, która przeszukuje zewnętrzne źródło danych dla zawartość do przeszukiwania. Dane wyjściowe **importowania danych** Kreator ma indeksatora przeszukiwania źródła danych JSON, który wyodrębnia zawartość, którą można przeszukiwać i importuje je do indeksu usługi Azure Search.

   ![Definicja indeksatora obiektów blob](media/search-howto-index-json/import-wizard-json-indexer.png)

Kliknij przycisk **OK** uruchom kreatora do tworzenia wszystkich obiektów. Indeksowanie rozpoczyna się natychmiast.

Można monitorować importowanie danych na stronach portalu. Powiadomienia o postępie wskazują stan indeksowania i liczby dokumentów są przekazywane. Po zakończeniu indeksowania, można użyć [Eksploratora wyszukiwania](search-explorer.md) wykonywać zapytania w indeksie.

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Używanie interfejsów API REST

Indeksowanie obiektów blob JSON jest podobny do wyodrębniania zwykłego dokumentu w przepływie pracy trzyczęściowej wspólne dla wszystkich indeksatorów w usłudze Azure Search: Utwórz źródło danych, Utwórz indeks, Utwórz indeksator.

W oparciu o kod JSON jest indeksowania, można użyć interfejsu API REST z interfejsami API w [indeksatory](https://docs.microsoft.com/rest/api/searchservice/create-indexer), [źródeł danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source), i [indeksów](https://docs.microsoft.com/rest/api/searchservice/create-index). W przeciwieństwie do kreatora portalu podejście kodu wymaga, która ma indeks w miejscu, gotowy do akceptowania dokumentów JSON, gdy wysyłasz **tworzenie indeksatora** żądania.

Obiektów blob JSON w usłudze Azure Blob storage są zazwyczaj pojedynczego dokumentu JSON lub tablicę JSON. Indeksowanie obiektów blob w usłudze Azure Search może przeanalizować albo konstrukcji, w zależności od tego, jak ustawić **parsingMode** parametru w żądaniu.

| Dokument JSON | parsingMode | Opis | Dostępność |
|--------------|-------------|--------------|--------------|
| Jeden na obiekt blob | `json` | Analizuje obiektów blob JSON jako jeden fragment tekstu. Każdy obiekt blob JSON staje się pojedynczym dokumencie usługi Azure Search. | Ogólnie dostępna w obu [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) i [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) interfejsów API. |
| Wiele na obiekt blob | `jsonArray` | Analizuje tablicę JSON w obiekcie blob, w którym każdy element tablicy staje się oddzielny dokument usługi Azure Search.  | Ogólnie dostępna w obu [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) i [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) interfejsów API. |


### <a name="step-1-create-a-data-source"></a>Krok 1: Tworzenie źródła danych

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

### <a name="step-2-create-a-target-search-index"></a>Krok 2: Utwórz docelowym indeksem wyszukiwania 

Indeksatory są skojarzone ze schematem indeksu. Korzystając z interfejsu API (zamiast portalu), przygotuj indeksu z wyprzedzeniem, aby można je określić na indeksatora.

Zawartość do przeszukiwania indeksu są przechowywane w usłudze Azure Search. Aby utworzyć indeks, zapewniają schematu, która określa pola w dokumencie, atrybuty i innych konstrukcji, które kształt środowisko wyszukiwania. Jeśli utworzysz indeks, który ma te same nazwy pól i typów danych jako źródła, indeksator będzie zgodna pola źródłowego i docelowego zaoszczędzić pracy konieczności jawnego mapowania pól.

W poniższym przykładzie przedstawiono [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) żądania. Indeks będzie miał wyszukiwanie `content` pola do przechowywania tekstu wyodrębnionego z obiektów blob:   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="step-3-configure-and-run-the-indexer"></a>Krok 3: Konfigurowanie i uruchamianie indeksatora

Do tej pory definicje dla źródła danych i indeksu zostały parsingMode niezależny od. Jednak w kroku 3 dla konfiguracji indeksatora, ścieżka diverges w zależności od sposobu obiektu blob JSON zawartości przeanalizowano i ze strukturą w indeksie usługi Azure Search. Można także wybrać opcję `json` lub `jsonArray`:

+ Ustaw **parsingMode** do `json` do indeksowania każdy obiekt blob jako pojedynczy dokument.

+ Ustaw **parsingMode** do `jsonArray` Jeśli obiekty BLOB składają się z tablice notacji JSON i potrzebny każdego elementu tablicy, aby stać się oddzielny dokument w usłudze Azure Search. Dokumentu można traktować jako pojedynczy element w wynikach wyszukiwania. Jeśli chcesz, aby każdy element w tablicy, które były wyświetlane w wynikach wyszukiwania jako niezależny element, należy użyć `jsonArray` opcji.

W ramach definicji indeksatora możesz opcjonalnie użyć **mapowania pól** wyboru właściwości dokumentu JSON źródła, które są używane do wypełnienia z docelowym indeksem wyszukiwania. Dla tablic JSON Jeśli tablica istnieje jako właściwość niższego poziomu, można ustawić katalog główny dokumentów wskazująca, gdzie tablicy jest umieszczany w ramach obiektu blob.

> [!IMPORTANT]
> Kiedy używasz `json` lub `jsonArray` tryb analizy, usługa Azure Search przyjęto założenie, że wszystkie obiekty BLOB ze źródła danych zawierają JSON. Daj nam znać, jeśli potrzebujesz do obsługi różnych obiektów blob JSON i -JSON, w tym samym źródle danych, na [naszą witrynę UserVoice](https://feedback.azure.com/forums/263029-azure-search).


### <a name="how-to-parse-single-json-blobs"></a>Jak analizować pojedynczego obiektów blob JSON

Domyślnie [indeksatora obiektów blob usługi Azure Search](search-howto-indexing-azure-blob-storage.md) analizuje obiektów blob JSON jako jeden fragment tekstu. Często chcesz zachować strukturę dokumentów JSON. Załóżmy na przykład, że masz następujący dokument JSON w usłudze Azure Blob storage:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Indeksowanie obiektów blob analizuje dokument JSON w jednym dokumencie usługi Azure Search. Indeksator ładuje indeks, dopasowując "text", "datePublished" i "tags" ze źródła względem pola indeksu docelowego identycznie nazwane i wpisane.

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

### <a name="how-to-parse-json-arrays"></a>Jak analizować tablice notacji JSON

Alternatywnie można wybrać funkcję tablicy JSON. Ta możliwość jest przydatna, gdy zawierają obiekty BLOB *Tablica obiektów JSON*, i chcesz, aby każdy element, aby stać się oddzielny dokument usługi Azure Search. Na przykład biorąc pod uwagę następujący obiekt blob JSON, które możesz wypełnić indeksu usługi Azure Search przy użyciu trzech oddzielnych dokumentów, każdy z pola "id" i "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Dla tablicy JSON definicja indeksatora powinien wyglądać podobnie do poniższego przykładu. Należy zauważyć, że parametr parsingMode Określa `jsonArray` analizatora. Określanie prawo analizator i posiadające odpowiednie dane dane wejściowe są tylko dwa wymagania specyficzne dla tablicy indeksowanie obiektów blob JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Ponownie Zwróć uwagę, że mapowania pól można pominąć. Zakładając, że indeks o nazwach "id" i "text" pól, indeksatora obiektów blob można wywnioskować poprawne mapowania bez jawnego pola mapowania listy.

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

### <a name="using-field-mappings-to-build-search-documents"></a>Tworzenie dokumentów wyszukiwania przy użyciu mapowań pól

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

Źródło nazwy pól w mapowania są określane za pomocą [wskaźnik JSON](https://tools.ietf.org/html/rfc6901) notacji. Możesz zaczynać się ukośnikiem do odwoływania się do katalogu głównego dokumentu JSON, a następnie wybierz żądaną właściwość (na dowolny poziom zagnieżdżenia) przy użyciu ścieżki oddzielone ukośnikiem do przodu.

Może również odnosić się do elementów tablicy poszczególnych przy użyciu indeksu zaczynającego się od zera. Na przykład wybierz pierwszy element tablicy "tags" z powyższego przykładu, należy użyć mapowania pól w następujący sposób:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Jeśli nazwa pola źródła, w ścieżce mapowania pola odwołuje się do właściwości, która nie istnieje w formacie JSON, mapowanie jest pomijane bez błędów. Można to zrobić, aby firma Microsoft może obsługiwać dokumenty z innym schematem (jest to typowy przypadek użycia). Ponieważ nie ma możliwości weryfikacji, należy uważać, aby uniknąć błędów w specyfikacji mapowania pól.
>
>

### <a name="rest-example-indexer-request-with-field-mappings"></a>Przykład REST: Żądania indeksowania przy użyciu mapowań pól

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

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET

Zestaw SDK platformy .NET ma pełni parzystości przy użyciu interfejsu API REST. Firma Microsoft zaleca przejrzenie poprzedniej sekcji interfejsu API REST, aby dowiedzieć się, pojęcia i przepływu pracy oraz wymagania. Następnie można się odwoływać do poniższej dokumentacji interfejsu API platformy .NET do zaimplementowania indeksatora JSON w kodzie zarządzanym.

+ [Microsoft.Azure.Search.models.DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [Microsoft.Azure.Search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [Microsoft.Azure.Search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [Microsoft.Azure.Search.models.Indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

## <a name="see-also"></a>Zobacz także

+ [Indeksatory w usłudze Azure Search](search-indexer-overview.md)
+ [Indeksowanie usługi Azure Blob Storage z usługą Azure Search](search-howto-index-json-blobs.md)
+ [Indeksowanie obiektów blob CSV za pomocą indeksatora obiektów blob usługi Azure Search](search-howto-index-csv-blobs.md)
+ [Samouczek: Przeszukiwanie częściowo ustrukturyzowanych danych z magazynu obiektów Blob platformy Azure](search-semi-structured-data.md)

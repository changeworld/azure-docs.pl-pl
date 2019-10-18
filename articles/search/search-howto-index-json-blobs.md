---
title: Indeksowanie obiektów BLOB JSON z usługi Azure Blob Indexer na potrzeby wyszukiwania pełnotekstowego — Azure Search
description: Przeszukiwanie obiektów BLOB usługi Azure JSON dla zawartości tekstowej przy użyciu indeksatora Azure Search obiektów BLOB. Indeksatory automatyzują pozyskiwanie danych dla wybranych źródeł danych, takich jak Azure Blob Storage.
ms.date: 05/02/2019
author: HeidiSteen
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 147a2b690139aff546d82fc89a2fbcdefed03e01
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533754"
---
# <a name="how-to-index-json-blobs-using-azure-search-blob-indexer"></a>Jak indeksować obiekty blob JSON przy użyciu Azure Search indeksatora obiektów BLOB
W tym artykule opisano sposób konfigurowania Azure Search obiektu BLOB [Indexer](search-indexer-overview.md) w celu wyodrębnienia zawartości strukturalnej z dokumentów JSON w usłudze Azure Blob Storage i przeszukiwania jej w programie Azure Search. Ten przepływ pracy tworzy indeks Azure Search i ładuje go z istniejącym tekstem wyodrębnionym z obiektów BLOB JSON. 

Za pomocą [portalu](#json-indexer-portal), [interfejsów API REST](#json-indexer-rest)lub [zestawu .NET SDK](#json-indexer-dotnet) można indeksować zawartość JSON. Wspólne dla wszystkich metod to, że dokumenty JSON znajdują się w kontenerze obiektów BLOB na koncie usługi Azure Storage. Aby uzyskać wskazówki dotyczące wypychania dokumentów JSON z innych platform spoza platformy Azure, zobacz [Importowanie danych w Azure Search](search-what-is-data-import.md).

Obiekty blob JSON w usłudze Azure Blob Storage to zwykle pojedynczy dokument JSON (tryb analizowania jest `json`) lub kolekcja jednostek JSON. W przypadku kolekcji obiekt BLOB może mieć **tablicę** poprawnie sformułowanych elementów JSON (tryb analizowania jest `jsonArray`). Obiekty blob mogą również składać się z wielu pojedynczych jednostek JSON oddzielonych znakiem nowego wiersza (tryb analizowania jest `jsonLines`). Parametr **analizowaniemode** żądania określa struktury wyjściowe.

> [!NOTE]
> Aby uzyskać więcej informacji na temat indeksowania wielu dokumentów wyszukiwania z pojedynczego obiektu BLOB, zobacz [indeksowanie "jeden do wielu"](search-howto-index-one-to-many-blobs.md).

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Używanie portalu

Najprostszym sposobem indeksowania dokumentów JSON jest użycie Kreatora w [Azure Portal](https://portal.azure.com/). Po analizie metadanych w kontenerze obiektów blob platformy Azure Kreator [**importu danych**](search-import-data-portal.md) może utworzyć domyślny indeks, zmapować pola źródłowe na docelowe pola indeksu i załadować indeks w ramach jednej operacji. W zależności od rozmiaru i stopnia złożoności danych źródłowych można mieć indeks wyszukiwania pełnotekstowego w ciągu kilku minut.

Zalecamy korzystanie z tej samej subskrypcji platformy Azure dla Azure Search i usługi Azure Storage, najlepiej w tym samym regionie.

### <a name="1---prepare-source-data"></a>1 — Przygotowywanie danych źródłowych

[Zaloguj się do Azure Portal](https://portal.azure.com/) i [Utwórz kontener obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) , aby zawierał dane. Poziom dostępu publicznego można ustawić na dowolną z jej prawidłowych wartości.

Do pobrania danych w kreatorze **importu danych** potrzebna jest nazwa konta magazynu, nazwa kontenera i klucz dostępu.

### <a name="2---start-import-data-wizard"></a>2 — Uruchom Kreatora importu danych

Na stronie Przegląd usługi Azure Search można [uruchomić Kreatora](search-import-data-portal.md) z poziomu paska poleceń.

   ![Importuj dane — polecenie w portalu](./media/search-import-data-portal/import-data-cmd2.png "Uruchamianie kreatora importu danych")

### <a name="3---set-the-data-source"></a>3 — Ustawianie źródła danych

Na stronie **Źródło danych** Źródło musi być **BLOB Storage platformy Azure**z następującymi specyfikacjami:

+ **Dane do wyodrębnienia** powinny być *zawartością i metadanymi*. Wybranie tej opcji umożliwia kreatorowi wywnioskowanie schematu indeksu i zamapowanie pól do zaimportowania.
   
+ **Tryb analizowania** powinien mieć wartość *JSON*, *tablicę JSON* lub *linie JSON*. 

  W formacie *JSON* każdy obiekt BLOB jest określany jako pojedynczy dokument wyszukiwania, wyświetlany jako niezależny element w wynikach wyszukiwania. 

  *Tablica JSON* jest przeznaczona dla obiektów blob, które zawierają poprawnie sformułowane dane JSON — poprawnie sformułowany kod JSON odpowiada tablicy obiektów lub ma właściwość, która jest tablicą obiektów i że każdy element ma być przegubem jako autonomiczną, niezależnym dokumentem wyszukiwania. Jeśli obiekty blob są złożone i nie wybrano *tablicy JSON* , cały obiekt BLOB jest pozyskiwany jako pojedynczy dokument.

  *Linie JSON* są przeznaczone dla obiektów BLOB składających się z wielu jednostek JSON rozdzielonych przez nowy wiersz, w którym każda jednostka ma być przegubem jako autonomiczny dokument wyszukiwania niezależnego. Jeśli obiekty blob są złożone i nie wybrano trybu analizowania *linii JSON* , cały obiekt BLOB jest pozyskiwany jako pojedynczy dokument.
   
+ **Kontener magazynu** musi określać Twoje konto magazynu i kontener albo parametry połączenia, które są rozpoznawane jako kontener. Parametry połączenia można uzyskać na stronie portalu Blob service.

   ![Definicja źródła danych obiektu BLOB](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 — Pomiń stronę "Dodawanie wyszukiwania poznawczego" w Kreatorze

Dodawanie umiejętności poznawczych nie jest konieczne w przypadku importu dokumentu JSON. O ile nie trzeba [uwzględniać interfejsy API usług Cognitive Services i transformacji](cognitive-search-concept-intro.md) do potoku indeksowania, należy pominąć ten krok.

Aby pominąć ten krok, najpierw przejdź do następnej strony.

   ![Przycisk następnej strony dla wyszukiwania poznawczego](media/search-get-started-portal/next-button-add-cog-search.png)

Na tej stronie możesz przejść do dostosowywania indeksu.

   ![Pomijanie kroku Umiejętności poznawcze](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 — Ustawianie atrybutów indeksu

Na stronie **indeks** powinna zostać wyświetlona lista pól z typem danych oraz seria pola wyboru służących do ustawiania atrybutów indeksu. Kreator może wygenerować listę pól na podstawie metadanych i przez próbkowanie danych źródłowych. 

Możesz wybrać atrybuty zbiorcze, klikając pole wyboru u góry kolumny atrybutu. Wybierz opcję **pobierania** i **wyszukiwania** dla każdego pola, które ma zostać zwrócone do aplikacji klienckiej i podlegające przetwarzaniu wyszukiwania pełnotekstowego. Zauważ, że liczby całkowite nie są pełnymi tekstami ani rozmyte wyszukiwania (liczby są oceniane Verbatim i często są przydatne w filtrach).

Przejrzyj opis [atrybutów indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) i [analizatorów języka](https://docs.microsoft.com/rest/api/searchservice/language-support) , aby uzyskać więcej informacji. 

Poświęć chwilę, aby przejrzeć wybrane opcje. Po uruchomieniu kreatora są tworzone fizyczne struktury danych i nie będzie można edytować tych pól bez porzucania i ponownego tworzenia wszystkich obiektów.

   ![Definicja indeksu obiektów BLOB](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 — Tworzenie indeksatora

W pełni określony Kreator tworzy trzy odrębne obiekty w usłudze wyszukiwania. Obiekt źródła danych i obiekt indeksu są zapisywane jako zasoby nazwane w usłudze Azure Search. Ostatnim krokiem jest utworzenie obiektu indeksatora. Nazwa indeksatora pozwala na jego istnienie jako zasób autonomiczny, który można zaplanować i zarządzać niezależnie od obiektu indeksu i źródła danych, utworzonego w tej samej sekwencji kreatora.

Jeśli nie znasz indeksatorów, *indeksator* jest zasobem w Azure Search przeszukiwanym zewnętrznym źródłem danych dla zawartości możliwej do przeszukania. Dane wyjściowe kreatora **importu danych** to indeksator, który przeszukuje źródło danych JSON, wyodrębnia zawartość z możliwością przeszukiwania i importuje go do indeksu na Azure Search.

   ![Definicja indeksatora obiektów BLOB](media/search-howto-index-json/import-wizard-json-indexer.png)

Kliknij przycisk **OK** , aby uruchomić kreatora i utworzyć wszystkie obiekty. Indeksowanie rozpocznie się natychmiast.

Import danych można monitorować na stronach portalu. Powiadomienia o postępie wskazują stan indeksowania oraz liczbę przekazywanych dokumentów. 

Po zakończeniu indeksowania można użyć [Eksploratora wyszukiwania](search-explorer.md) do wykonywania zapytań względem indeksu.

> [!NOTE]
> Jeśli nie widzisz danych, których oczekujesz, może być konieczne ustawienie więcej atrybutów na więcej pól. Usuń indeks i indeksator, który właśnie został utworzony, a następnie ponownie wykonaj kroki kreatora, modyfikując wybrane opcje dla atrybutów indeksu w kroku 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Używanie interfejsów API REST

Interfejsu API REST można używać do indeksowania obiektów BLOB w formacie JSON, które są wspólne dla wszystkich indeksatorów w Azure Search: tworzenie źródła danych, tworzenie indeksu i tworzenie indeksatora. Wyodrębnianie danych z magazynu obiektów BLOB odbywa się po przesłaniu żądania utworzenia indeksatora. Po zakończeniu tego żądania będzie miał indeks queryable. 

Możesz przejrzeć [przykład kodu REST](#rest-example) na końcu tej sekcji, który pokazuje, jak utworzyć wszystkie trzy obiekty. Ta sekcja zawiera również szczegółowe informacje na temat [trybów analizy JSON](#parsing-modes), [pojedynczych obiektów BLOB](#parsing-single-blobs), [tablic JSON](#parsing-arrays)i [tablic zagnieżdżonych](#nested-json-arrays).

W przypadku indeksowania JSON opartego na kodzie należy utworzyć następujące obiekty przy użyciu programu [Poster](search-get-started-postman.md) i interfejsu API REST:

+ [indeks](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Źródło danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [indeksatora](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Kolejność operacji wymaga utworzenia i wywołania obiektów w tej kolejności. W przeciwieństwie do przepływu pracy portalu podejście do kodu wymaga, aby dostępny indeks akceptował dokumenty JSON wysyłane przez żądanie **utworzenia indeksatora** .

Obiekty blob JSON w usłudze Azure Blob Storage są zwykle pojedynczym dokumentem JSON lub tablicą JSON. Indeksator obiektu BLOB w Azure Search może analizować każdą konstrukcję, w zależności od sposobu **Ustawienia parametru** ParseMode w żądaniu.

| Dokument JSON | przeanalizowanie | Opis | Dostępność |
|--------------|-------------|--------------|--------------|
| Jeden na obiekt BLOB | `json` | Analizuje obiekty blob JSON jako pojedynczy fragment tekstu. Każdy obiekt BLOB JSON zostaje przekształcony w jeden Azure Search. | Ogólnie dostępne zarówno w interfejsie API [rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) , jak i w zestawie SDK [platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) . |
| Wiele na obiekt BLOB | `jsonArray` | Analizuje tablicę JSON w obiekcie blob, gdzie każdy element tablicy jest oddzielnym dokumentem Azure Search.  | Ogólnie dostępne zarówno w interfejsie API [rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) , jak i w zestawie SDK [platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) . |
| Wiele na obiekt BLOB | `jsonLines` | Analizuje obiekt BLOB, który zawiera wiele jednostek JSON ("Array") oddzielonych znakiem nowego wiersza, gdzie każda jednostka zostaje oddzielnym Azure Searchm dokumentem. | Ogólnie dostępne zarówno w interfejsie API [rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) , jak i w zestawie SDK [platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) . |

### <a name="1---assemble-inputs-for-the-request"></a>1 — Tworzenie danych wejściowych dla żądania

Dla każdego żądania należy podać nazwę usługi i klucz administratora dla Azure Search (w nagłówku) oraz nazwę i klucz konta magazynu dla usługi BLOB Storage. Można użyć programu [Poster](search-get-started-postman.md) do wysyłania żądań HTTP do Azure Search.

Skopiuj następujące cztery wartości do Notatnika, aby można było je wkleić do żądania:

+ Nazwa usługi Azure Search
+ Klucz administratora Azure Search
+ Nazwa konta usługi Azure Storage
+ Klucz konta usługi Azure Storage

Te wartości można znaleźć w portalu:

1. Na stronach portalu Azure Search Skopiuj adres URL usługi Search na stronie Przegląd.

2. W okienku nawigacji po lewej stronie kliknij pozycję **klucze** , a następnie skopiuj klucz podstawowy lub pomocniczy (są one równoważne).

3. Przejdź do strony portalu dla konta magazynu. W okienku nawigacji po lewej stronie w obszarze **Ustawienia**kliknij pozycję **klucze dostępu**. Ta strona zawiera zarówno nazwę konta, jak i klucz. Skopiuj nazwę konta magazynu i jeden z kluczy do Notatnika.

### <a name="2---create-a-data-source"></a>2 — Tworzenie źródła danych

Ten krok zapewnia informacje o połączeniu ze źródłem danych używane przez indeksator. Źródło danych to nazwany obiekt w Azure Search, który utrzymuje informacje o połączeniu. Typ źródła danych, `azureblob` określa, które zachowania wyodrębniania danych są wywoływane przez indeksator. 

Zastąp prawidłowe wartości w obszarze symbole zastępcze nazwa usługi, klucz administratora, konto magazynu i klucz konta.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 — Tworzenie docelowego indeksu wyszukiwania 

Indeksatory są sparowane ze schematem indeksu. Jeśli używasz interfejsu API (a nie portalu), przygotuj indeks z góry, aby można było go określić na operacji indeksatora.

Indeks przechowuje zawartość przeszukiwaną w Azure Search. Aby utworzyć indeks, podaj schemat, który określa pola w dokumencie, atrybuty i inne konstrukcje, które tworzą kształt środowiska wyszukiwania. Jeśli tworzysz indeks, który ma takie same nazwy pól i typy danych jak źródło, indeksator będzie pasował do pól źródłowych i docelowych, co pozwala zaoszczędzić miejsce na jawne Mapowanie pól.

Poniższy przykład przedstawia żądanie [utworzenia indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index) . Indeks będzie miał pole `content` z możliwością wyszukiwania do przechowywania tekstu wyodrębnionego z obiektów blob:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 — Konfigurowanie i uruchamianie indeksatora

Podobnie jak w przypadku indeksu i źródła danych, a indeksator jest również nazwanym obiektem, który można utworzyć i użyć ponownie w usłudze Azure Search. W pełni określone żądanie utworzenia indeksatora może wyglądać następująco:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Konfiguracja indeksatora znajduje się w treści żądania. Wymaga źródła danych i pustego indeksu docelowego, który istnieje już w Azure Search. 

Harmonogram i parametry są opcjonalne. Jeśli zostaną pominięte, indeksator zostanie uruchomiony natychmiast, przy użyciu `json` jako tryb analizy.

Ten konkretny indeksator nie obejmuje mapowań pól. W definicji indeksatora można pozostawić **mapowania pól** , jeśli właściwości źródłowego dokumentu JSON pasują do pól docelowego indeksu wyszukiwania. 


### <a name="rest-example"></a>Przykład REST

Ta sekcja to podsumowanie wszystkich żądań używanych do tworzenia obiektów. Dyskusje dotyczące części składników można znaleźć w poprzednich sekcjach tego artykułu.

### <a name="data-source-request"></a>Żądanie źródła danych

Wszystkie indeksatory wymagają obiektu źródła danych, który zawiera informacje o połączeniu z istniejącymi danymi. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Żądanie indeksu

Wszystkie indeksatory wymagają indeksu docelowego, który odbiera dane. Treść żądania definiuje schemat indeksu, składający się z pól, przypisanych do obsługi żądanych zachowań w indeksie, który można przeszukiwać. Ten indeks powinien być pusty podczas uruchamiania indeksatora. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Żądanie indeksatora

To żądanie pokazuje w pełni określony indeksator. Zawiera ona mapowania pól, które zostały pominięte w poprzednich przykładach. Odwołaj te "Schedule", "Parameters" i "fieldMappings" są opcjonalne, o ile jest dostępna wartość domyślna. Pominięcie "harmonogramu" spowoduje natychmiastowe uruchomienie indeksatora. Pominięcie elementu "analizowaniemode" spowoduje, że indeks używa domyślnego elementu "JSON".

Tworzenie indeksatora na Azure Search Importuj dane. Jest on uruchamiany natychmiast, a następnie zgodnie z harmonogramem, jeśli został podany.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

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

Zestaw SDK platformy .NET ma pełną zgodność z interfejsem API REST. Zalecamy zapoznanie się z poprzednią sekcją interfejsu API REST, aby poznać koncepcje, przepływ pracy i wymagania. Następnie można zapoznać się z następującą dokumentacją interfejsu API platformy .NET, aby zaimplementować indeksator JSON w kodzie zarządzanym.

+ [Microsoft. Azure. Search. models. DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [Microsoft. Azure. Search. models. DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [Microsoft. Azure. Search. models. index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [Microsoft. Azure. Search. models. indeksator](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Tryby analizy

Obiekty blob JSON mogą przyjmować wiele formularzy. Parametr ParseMode indeksatora JSON określa sposób analizowania zawartości obiektu BLOB JSON i jej struktury w indeksie Azure Search:

| przeanalizowanie | Opis |
|-------------|-------------|
| `json`  | Indeksowanie każdego obiektu BLOB jako jednego dokumentu. Jest to wartość domyślna. |
| `jsonArray` | Wybierz ten tryb, jeśli obiekty blob składają się z tablic JSON i potrzebujesz każdego elementu tablicy, aby stał się osobnym dokumentem w Azure Search. |
|`jsonLines` | Wybierz ten tryb, jeśli obiekty blob składają się z wielu jednostek JSON, które są rozdzielone nowym wierszem, a każda jednostka ma być osobnym dokumentem w Azure Search. |

Dokument można traktować jako pojedynczy element w wynikach wyszukiwania. Jeśli chcesz, aby każdy element w tablicy był wyświetlany w wynikach wyszukiwania jako niezależny element, użyj odpowiednio opcji `jsonArray` lub `jsonLines`.

W definicji indeksatora można opcjonalnie użyć [mapowań pól](search-indexer-field-mappings.md) , aby określić, które właściwości źródłowego dokumentu JSON są używane do wypełniania docelowego indeksu wyszukiwania. W przypadku trybu analizowania `jsonArray`, jeśli tablica istnieje jako właściwość niższego poziomu, można ustawić katalog główny dokumentu wskazujący, gdzie tablica jest umieszczana w obiekcie blob.

> [!IMPORTANT]
> Gdy używasz `json`, `jsonArray` lub `jsonLines` tryb analizowania, Azure Search zakłada, że wszystkie obiekty blob w źródle danych zawierają kod JSON. Jeśli potrzebujesz obsługi kombinacji obiektów BLOB w formacie JSON i innych niż JSON w tym samym źródle danych, poinformuj nas o [naszej witrynie UserVoice](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Analizowanie pojedynczych obiektów BLOB JSON

Domyślnie [indeksator Azure Search obiektów BLOB](search-howto-indexing-azure-blob-storage.md) analizuje obiekty blob JSON jako pojedynczy fragment tekstu. Często chcesz zachować strukturę dokumentów JSON. Załóżmy na przykład, że masz następujący dokument JSON w usłudze Azure Blob Storage:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Indeksator obiektu BLOB analizuje dokument JSON w pojedynczym Azure Search dokumencie. Indeksator ładuje indeks, dopasowując wartości "text", "datePublished" i "Tags" ze źródła względem pól indeksowanych o identycznej nazwie i typach.

Jak wspomniano, mapowania pól nie są wymagane. Mając indeks z polami "text", "datePublished" i "Tags", indeksator obiektów BLOB może wywnioskować poprawne mapowanie bez mapowania pola obecnego w żądaniu.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analizowanie tablic JSON

Alternatywnie możesz użyć opcji tablicy JSON. Ta opcja jest przydatna, gdy obiekty blob zawierają *tablicę poprawnie sformułowanych obiektów JSON*, a każdy element ma zostać oddzielnym dokumentem Azure Search. Na przykład, mając następujący obiekt BLOB JSON, można wypełnić indeks Azure Search przy użyciu trzech oddzielnych dokumentów, z których każdy ma pola "ID" i "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

W przypadku tablicy JSON definicja indeksatora powinna wyglądać podobnie do poniższego przykładu. Należy zauważyć, że parametr analizymode określa `jsonArray` parser. Określenie właściwego analizatora i posiadanie właściwych danych wejściowych jest jedyne wymagania dotyczące tablicy dla indeksowania obiektów BLOB JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Zwróć uwagę, że mapowania pól można pominąć. Przy założeniu, że indeks ma takie same nazwy jak pola "ID" i "text", indeksator obiektu BLOB może wywnioskować poprawne mapowanie bez jawnej listy mapowania pól.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Analizowanie zagnieżdżonych tablic
W przypadku tablic JSON mających elementy zagnieżdżone można określić `documentRoot`, aby wskazać strukturę wielopoziomową. Na przykład jeśli obiekty blob wyglądają następująco:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Ta konfiguracja służy do indeksowania tablicy zawartej we właściwości `level2`:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Analizowanie obiektów BLOB rozdzielonych znakami nowego wiersza

Jeśli obiekt BLOB zawiera wiele jednostek JSON rozdzielonych znakiem nowego wiersza, a każdy element ma zostać oddzielnym dokumentem Azure Search, możesz wybrać opcję wierszy JSON. Na przykład, uwzględniając następujący obiekt BLOB (gdzie istnieją trzy różne jednostki JSON), można wypełnić indeks Azure Search przy użyciu trzech oddzielnych dokumentów, z których każda zawiera pola "ID" i "text".

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

W przypadku linii JSON definicja indeksatora powinna wyglądać podobnie do poniższego przykładu. Należy zauważyć, że parametr analizymode określa `jsonLines` parser. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Zwróć uwagę, że mapowania pól można pominąć, podobnie jak w trybie analizy `jsonArray`.

## <a name="add-field-mappings"></a>Dodaj mapowania pól

Gdy pola źródłowe i docelowe nie są idealnie wyrównane, można zdefiniować sekcję mapowanie pola w treści żądania dla jawnych powiązań pola-pola.

Obecnie Azure Search nie może indeksować dowolnych dokumentów JSON bezpośrednio, ponieważ obsługuje tylko pierwotne typy danych, tablice ciągów i punkty GEOJSON. Można jednak użyć **mapowań pól** , aby wybrać fragmenty dokumentu JSON i "podnieść" je do pól najwyższego poziomu w dokumencie wyszukiwania. Aby dowiedzieć się więcej o mapowaniu pól, zobacz [mapowania pól w indeksatorach Azure Search](search-indexer-field-mappings.md).

Odwiedzanie przykładowego dokumentu JSON:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Przyjmij indeks wyszukiwania o następujące pola: `text` typu `Edm.String`, `date` typu `Edm.DateTimeOffset` i `tags` typu `Collection(Edm.String)`. Zwróć uwagę na różnice między "datePublished" w polu źródłowym i `date`m w indeksie. Aby zmapować kod JSON do żądanego kształtu, użyj następujących mapowań pól:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Nazwy pól źródłowych w mapowaniach są określane przy użyciu notacji [wskaźnika JSON](https://tools.ietf.org/html/rfc6901) . Zacznij od ukośnika, aby odwołać się do katalogu głównego dokumentu JSON, a następnie wybierz odpowiednią właściwość (na dowolnym poziomie zagnieżdżenia), używając ścieżki rozdzielanej ukośnikiem.

Można również odwoływać się do poszczególnych elementów tablicy przy użyciu indeksu od zera. Na przykład aby wybrać pierwszy element tablicy "Tags" z powyższego przykładu, użyj mapowania pola, takiego jak:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Jeśli nazwa pola źródłowego w ścieżce mapowania pola odwołuje się do właściwości, która nie istnieje w formacie JSON, to mapowanie jest pomijane bez błędu. Dzieje się tak, aby umożliwić obsługę dokumentów z innym schematem (który jest typowym przypadkiem użycia). Ponieważ nie ma weryfikacji, należy zadbać o uniknięcie literówki w specyfikacji mapowania pól.
>
>

## <a name="see-also"></a>Zobacz także

+ [Indeksatory w Azure Search](search-indexer-overview.md)
+ [Indeksowanie Blob Storage platformy Azure z Azure Search](search-howto-index-json-blobs.md)
+ [Indeksowanie obiektów BLOB woluminów CSV za pomocą indeksatora Azure Search obiektów BLOB](search-howto-index-csv-blobs.md)
+ [Samouczek: wyszukiwanie danych z częściową strukturą z usługi Azure Blob Storage](search-semi-structured-data.md)
